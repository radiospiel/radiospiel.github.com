---
layout: post
title: The Pilotprojekt database
published: false

date: 2015-07-12
---

There is something special about the kinopilot data: individual entries rarely change. This includes
movie and theater descriptions, as well as individual showtimes: once a theater announces showtimes,
they usually don't change, and are of interest to a user until the actual day.

The current set of interesting data evolves over time. New showtimes are announced and 
must be added to that set, showtimes in the past must be removed from the set, as must descriptions 
of movies that have no future showtimes. Occasionally mistakes are
to be fixed, resulting in updates to existing documents.

The nature of such an dataset, especially:

- the limited size
- only rare updates
- automatic outdates
 
allows for an interesting optimization in a kinopilot client: The client keeps its current dataset
between runs, and only fetches a diff from the latest revision locally stored. This allows to 
drastically cut-down transmission times for updates.

In the case of a complete Berlin dataset we usually have ~70 theaters, ~150 movies, ~6000 showtimes.
Depending on the size of movie metadata this adds up to a datasize between 2 and 15 Megabyte. Would
a client transfer the entire package once per day, we would end up with 60 to 450 Megabyte per month,
which is quite a lot especially for mobile data rates. If the client only ever fetches showtimes that
were recebtly added to the dataset, the transmission volume would be, on average, close to ~1000 showtimes, 
and ~10 movies per day - resulting in only about ~15% of the required transmission volume. Also, this
allows for **quicker** updates, as a smaller update size obvioulsy reduces transmission time, and, again
in the case of a mobile transfer, increases the chance of successfully transferring the 
entire set of data.

## Algorithm

So, how do we end up with a chain of diffs between revisions? Lets recap 

The requirelemtns lend to a number of implemtations:

### Plain data

We could keep a copy of, say, the latest 10 releases. Whenever we run an update 
we would run it against each of these releases and track and record changes as we go.
This is obviously a bad idea.

### Using git as a backend

If we manage to write each change to the database, we could use git to create diffs. As each 
potential diff needs only be generated once - we can cache it and keep it around, as it never
changes - performace would be only of second order importance.

I tried this approach once, and it failed, for two reasons:

- After a certain number of revisions and changesets performace degraded horribly. 
- The git files must be stored somewhere in a file system, and we prefer an ephemeral file system,
  hence would not want to rely on FS storage. Yes, we could push those changes around, but
  this would probably add a huge bit of infrastrcture code on top of that.

### Using git objects as a backend

Using git objects - writing each change directly into the git database - might even be
a good idea, as it probably solves the performance problem. It does not, though, solve
the problem of where to store the data.

### Using some kind of database

We could buidl an application which is used to write into the database, and have some application
logic manage releases, which is then also able to generate diffs.

### Use CouchDB database

At a first glabce CouchDB seemed like a pretty good match. Not only is it (also) built for managing
and synchronising datasets over a multitude of nodes, it even offers a changes feed, allowing clients
to fetch only the changes from a former revision.

As it turned out back thwn (note: this might have changed since) a change feed is not a diff. The list 
of changes in the databse history allow a client to replay each individual revison. This is not needed
in our case, and it comes at huge costs: te CouchDB changes feed contains new objects and object
deletions for objects that were both created and destroyed between sync runs. However, we do not care
about those at all, so why fetch 6000 showtimes that we would never show.

### Use a SQL database to keep data and track revisions

A database server would solve the storage and network problems of the other approaches. If we 
could implement straight in SQL we would be fine. Again, here are different approaches:

In case you didn't know I am a big fan of Postgresql. I did build a SQLite version, but we will
use a Postgresql server, as it also stores all the data.

What we do is this:

We mark each insertion or change of a document with an (increasing) revision number. When deleting a
document the document will be marked as deleted instead, and that will be treated as a change (i.e.
marked with a revision number). 

Each document is stored as a document string in the database. We store these as JSONB entries; this
is quite fast, but more importantly might reduce the time to generate a JSON response from a query.
To mark a document as deleted we will set that string to NULL. 

So a client who wants to fetch a complete view of the data fetches all documents that are not deleted


```sql
SELECT data FROM documents WHERE data IS NOT NULL
```

Once a client fetched a dataset it can rebuild a current version using the data resulting from

```sql
SELECT id, data FROM documents WHERE revision > $client_revision
```

The client must then

- update all documents contained in the dataset, where data is set;
- delete all documents by id where data is NULL.

```sql
CREATE TABLE documents (
    dataset             VARCHAR(200),       -- the dataset this document belongs to; UUID
    id                  INTEGER,            -- id; STRING
    revision            INTEGER NOT NULL,   -- 
    expires_at          TIMESTAMP,          -- expiration timestamp
    data                JSONB,              -- the document
    PRIMARY KEY (dataset, id)
)
```


### How to manage the revision number? 

SO, what we do is to create triggers, that manage revision numbers on object creation, changes, and
deletions:

```sql
CREATE SEQUENCE revisions;

-- set revision on INSERT/UPDATE 
DROP TRIGGER IF EXISTS documents_set_revision ON documents;
CREATE OR REPLACE FUNCTION documents_set_revision() RETURNS trigger AS $code$
    BEGIN
        SELECT nextval('revisions') INTO NEW.revision;
        RETURN NEW;
    END;
$code$ LANGUAGE plpgsql;

CREATE TRIGGER documents_set_revision BEFORE INSERT OR UPDATE ON documents
    FOR EACH ROW EXECUTE PROCEDURE documents_set_revision();

-- set revision and data (to NULL) on DELETE 

DROP TRIGGER IF EXISTS documents_set_revision_on_delete ON documents;
CREATE OR REPLACE FUNCTION documents_set_revision_on_delete() RETURNS trigger AS $code$
    DECLARE
        new_revision int;
    BEGIN
        SELECT nextval('revisions') INTO new_revision;
        UPDATE documents SET data=NULL, revision=new_revision WHERE ctid=OLD.ctid;
        RETURN NULL;
    END;
$code$ LANGUAGE plpgsql;

CREATE TRIGGER documents_set_revision_on_delete
BEFORE DELETE
ON documents
FOR EACH ROW
EXECUTE PROCEDURE documents_set_revision_on_delete();        


-- record document update revisions

CREATE document_revisions
     CREATE RULE document_revisions AS 
     ON INSERT TO documents
         DO ALSO 
             UPDATE documents SET created_in_rev=updated_in_rev=fecth_revision
            WHERE id=NEW.id

-- record document change revisions

CREATE document_revisions
     CREATE RULE document_revisions AS 
     ON UPDATE TO documents
         WHERE NEW.document <> OLD.document
         DO INSTEAD 
             UPDATE documents SET data=NEW.data,
                                    revision=fecth_revision,
                                    current_timestamp
            WHERE id=NEW.id

-- record document change revisions

CREATE document_revisions
     CREATE RULE document_revisions AS 
     ON DELETE FROM documents
         DO INSTEAD 
             UPDATE documents SET document=NEW.document,
                                    revision=fecth_revision,
                                    current_timestamp
            WHERE id=NEW.id
```
