Date: 2011-12-31 12:31
Title: This is your blog, delivered by scriptogr.am

Thank you for using scriptogr.am. We hope you’ll enjoy the app. It’s designed to be fast, simple and to get the most creativity out of you.

scriptogr.am uses [Markdown](http://daringfireball.net/projects/markdown/ "Markdown"), a lightweight markup language, originally created by [John Gruber](http://daringfireball.net/ "Daring Fireball") and [Aaron Swartz](http://www.aaronsw.com/ "Aaron Swartz"). Markdown is a text-to-HTML conversion tool for web writers. Markdown allows you to write using an easy-to-read, easy-to-write plain text format, then convert it to structurally valid XHTML (or HTML). See the [Syntax](http://daringfireball.net/projects/markdown/syntax "Markdown syntax") page for details pertaining to Markdown’s formatting syntax. You can try it out, right now, using the online [Dingus](http://daringfireball.net/projects/markdown/dingus "Dingus").
scriptogr.am has also implemented support for features currently not available with the plain Markdown syntax. Read more in the [documentation](http://michelf.ca/projects/php-markdown/extra/ "documentation").
Note that these features are experimental, so we can't guarantee that they will always remain.

# Getting started

After connecting your Dropbox account to scriptogr.am, some necessary files and folders are added to your Dropbox at `Apps/scriptogram`. First the *GET_STARTED.txt* text file that pretty much explains the exact same as what you’re reading now. Next, we’ve added a `posts` folder. This is where you add your blog *post* (& *page*) files. These files are plain textfiles, but needs to be saved with the .md (markdown) extension like this: *yourfile.md*

![](http://dl.dropbox.com/u/35476/_scriptogram/folder.png)

Please use standard, non foreign filenames. Dropbox sometimes have problem syncing filenames that contains invalid HTML characters. 
We’ve added a post example page (this file) there for you to get familiar with.

## The template data
All files needs to contain "front block". The front block must be the first thing in the file and takes the form of:
	
	Date: 2012-04-17
	Title: My first post

You can set any of the predefined variables (see below for a reference). But, the *Title* is required. Without the title, the system will fail.

### Predefined global variables
_All the variable names below are case-sensitive:_

	 
`Required:`
	
	Title

The title of your post (or page)

	 
`Not required, but close to:`

	Date

The following date format is the correct one to use: *2011-12-31 12:31* or *2011-12-31*.
(The *Date* variable can be used to ensure correct sorting of posts.)


`Optional:`

	Published

Set to ’false’ if you don’t want a post to show up when the site is generated.

	Type

Set to ’page’ if you wan’t the post to act as a ’page’ instead of a ’post’.	

	Slug

Custom permalink (replaces the auto generated url based on the "Title:" variable), especially useful if titles tend to be long or they change frequently. E.g ’my-custom-permalink-url’

	Link

Links the "post" or "page" title to an external source. E.g ’http://google.com’

	Tags

Add tags to your posts and pages. Comma separated words. Once added, scriptogr.am will transform all tags to valid basic HTML characters.  	

	Excerpt

Add an excerpt[^1] to your post or page.

## Difference between ’posts’ and ’pages’

A `post` is a blog post.

A `page` is a similar as a *post*, but generates a link visible in the *menu* on your site that will lean to a page permalink.

## Publishing your posts

This is simple. Just head to your admin panel and hit the ”Synchronize” button, or enable autosync in your settings. When logged in to scriptogr.am and visiting your own page, you’ll see the scriptogr.am logotype symbol on the top right of the browser window. This is the link that leads to your admin panel. 

## Published vs Draft

There are two ways to mark posts and pages as drafts or published. The first way is to use the draft folder. Everything you put here will be treated as draft, and everything you put direct under the posts folder will be treated as published. The ”Publish” and ”Draft” buttons in the dashboard handles these file locations for you. You can also use the Published variable, mentioned above. The Published variable will ALWAYS take precedence over the file location. 

**Finally,** happy posting. If you have any questions, suggestions or thoughts just drop us an [e-mail](mailto:info@scriptogr.am) at any time.

[^1]: An [excerpt](http://en.wikipedia.org/wiki/Excerpt "Excerpt on Wikipedia") is a relatively small sample passage from a longer work, such as a book or article.