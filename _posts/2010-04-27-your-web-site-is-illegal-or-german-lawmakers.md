---
layout: post
title: ! 'Your web site is illegal, or: German lawmakers don''t understand the web'
published: true
date: 2010-04-27
categories:
- cookie
- germany
- internet
- IP address
- law
- legal
- society
- web 2.0
---

<p>In recent weeks online news sections across Germany discussed privacy implementations in social networks, mainly targeting the US-based social network <a href="http://www.google.com/search?q=facebook.com" target="_blank">facebook.com</a>. Triggered by a somewhat questionable (in my eyes at least) privacy assessment done by the usually well-established German consumer rating organization <a href="http://www.google.com/search?q=Stiftung+Warentest" target="_blank">Stiftung Warentest</a>, the story even hit mainstream politics, when German consumer protection minister <a href="http://www.google.com/search?q=Ilse+Aigner" target="_blank">Ilse Aigner</a> urged Facebook "to revise the privacy policy without delay." While one can hardly ignore the free promotion for struggling German social network site <a href="http://www.google.com/search?q=studivz.net" target="_blank">studivz.net</a> and the somewhat antiamerican smell in the ongoing discussion, lets just have a deeper look at some of the problems with German privacy laws.</p>

<h2>The German privacy law for a dummy (like me)</h2>

<p>Amongst other things German privacy laws <a href="http://de.wikipedia.org/wiki/Bundesdatenschutzgesetz">(Bundesdatenschutzgesetz)</a>
dictate that no website should store personal information without asking the individual for permission, should not pass along such information to third parties, and should allow the individual to retain control about content the he/she created. While this sounds all nice and shiny there is a problem with that: <em>what information</em> exactly constitutes personal information?</p>

<p>Apart from obviously personal data, like name, or date of birth, this includes data which "allows to identify an individual" even if not named explicitely. And prevailing case law includes even the IP address into that set. To see what is wrong with that lets illuminate the topic of IP address first.</p>

<h2>The IP address dilemma</h2>

<p>Wikipedia says that an <a href="http://www.google.com/search?q=IP+address" target="_blank">IP address</a> is a <em>"numerical label that is assigned to devices participating in the internet."</em> However, this number is not unique. While initially designed to assign an individual number to each internet device the prevailing version 4 of the IP protocl can handle only about 3 billion different IP addresses. Given that there are ~6 billion humans it is obvious that we are running out of IP addresses.</p>

<p>And until the upcoming IP version 6 will fix that shortage internet providers have to deploy ways to circumvent it. And this is what they use:</p>

<ul>
<li>
<a href="http://www.google.com/search?q=Network+Address+Translation" target="_blank">Network Address Translation</a> (NAT) allows a number of computers behind a router or firewall to share the same public IP address,</li>
<li>
<a href="http://www.google.com/search?q=Dynamic+Host+Configuration+Protocol" target="_blank">Dynamic Host Configuration Protocol</a> (DHCP) allows an internet provider to assign a dynamic IP address to an internet device only when that device is really online. If the device goes offline, and/or after a fixed amount of time the IP address gets reassigned to a potentially different device, and the next time you are online you will likely get a different IP number.</li>
</ul>
<p>That, however, means: as soon as you are behind a firewall, in a corporation, behind a router, on a DSL line, or behind a mobile phone dial-up link, you are affected from this. Or, in general terms: <strong>YOU DON'T HAVE A FIXED PUBLIC IP ADDRESS</strong>. You are likely to share your public IP address with somebody else. If not right now, somebody else will have your IP address in the next time. (Curios about your current public IP address? Check
<a href="http://www.whatismyip.com/">http://www.whatismyip.com/</a>)</p>

<h2>IP address usage: the do's and don'ts</h2>

<p>Even though this sounds quite unreliable a website can still put an IP address to a variety of sensible uses: because public IP addresses are assigned in ranges to the telcos and because these usually split up their IP ranges geographically one can identify a user's dialup location. Besides, a user is quite likely to use  the same IP in the next minutes. This allows to identify and fend off some kind of attacks.</p>

<p>However, you would never try to identify a user based on his/her IP address. And luckily you
don't have to, because people found better techniques to do that.</p>

<h2>Tracking crime by IP address</h2>

<p>If an internet provider lets you access his IP assignment logs - and german internet providers are (currently) required to keep logs for some time and to make these accessible for law enforcement agents - you might be able to identify the dialup link that was used at that time. As explained above, however, you cannot identify the responsible individual based solely on that information.</p>

<p>For identifying a criminal internet technology goes only so far. German law, however, knows a resort for problems like this: if the actual user cannot be identified the person operating the internet access point will be held responsible, for example when prosecuting pirated downloads.</p>

<p>I don't want to go into details here: some people argue, that laws like that render citoyen-sponsored   wireless community efforts impossible <a href="http://freifunkstattangst.de/">(see <a href="http://freifunkstattangst.de/)">http://freifunkstattangst.de/)</a></a>. Others argue, that WLAN encryption protocols can be broken comparatively easily. And then a logged IP address might be faked to begin with. Such facts get routinely ignored by German courts.</p>

<h2>The curious case of Google Analytics</h2>

<p>A problematic high profile case is <a href="http://www.google.com/search?q=Google+Analytics" target="_blank">Google Analytics</a>. Google itself states that they will store IP addresses (along with other information.) If you are running a website employing Google Analytics you must ask your users for permission. Now, this goes for a crappy user experience: imagine, whenever someone comes to your web site for the first time (s)he will be asked to give permission to use Google Analytics. But there are other problematic scenarios: as soon as you embed <em>anything</em> from someplace else - this includes a youtube videos, a web ad from an ad server, flickr photostreams, etc. - you must either know that this site doesn't store your user's IP address, or you <em>must ask</em> your users for permission. And it includes even yourself: you are not allowed to store a user's IP address without his/her permission! That would even include web server log files. Given that telcos are <em>required</em> to store your IP address this seems quite a strange policy to me.</p>

<h2>Cookies</h2>

<p>There are some sites that will remember your settings, without you ever to log in. (Google search as a prominent example) How do these web sites identify you, if they cannot use the IP address to do so?</p>

<p>The answer are "Cookies". A cookie is some small storage space that websites can use to store information in your browser. Whenever your browser opens a web site, it sends along all the cookies belonging to that website. The response transports not only the content that you will see, but some more non visible data including possible new settings. Usually a cookie contains a string of randomly chosen digits. Each time you get back to the web site you are having the same cookie in your basket.</p>

<p>If you are firing up Google search for the first time - say, on a freshly installed computer - google gives your browser a cookie, that holds some kind of user ID string like this "jhfg876anb218bdas". If you
ever get back to google your browser will send along this string with your search request, and the search engine is able to look up your search settings.</p>

<p>But not only google uses this technique. It is used by nearly all websites to track an individual guest between visits, and to remember you when you are logged in.</p>

<h2>How are your cookies safe (and how not?)</h2>

<p>The HTTP protocol states that a cookie "belongs" to a domain: A cookie set by "google.com" cannot be read by "yahoo.com", and, by the way, neither by "google.de". The idea is to set up some "private" storage that only a given web site can read. While this looks sound at a first glance, think twice.</p>

<p>Modern websites usually employ a number of third party Javascript applications, that are hosted elsewhere. This not only includes web site trackers a la "Google Analytics". <a href="radiospiel.org">radiospiel.org</a>, for example, currently links in third party code from <a href="http://google-analytics.com">google-analytics.com</a> <a href="http://smugmug.com">smugmug.com</a> and <a href="http://intensedebate.com">intensedebate.com</a>, and the latter then adds code from <a href="http://wordpress.com">wordpress.com</a>. And while the radiospiel.org cookies are safe from these parties any of these websites might set its own cookies in your browser.</p>

<p>This, however, means, a cookie set by "google-analytics.com" is still present when you are viewing a different google-analytics enabled web site. Which lets google track <em>you</em>, the individual, through the web. Which makes <strong>the cookie the universal piece of personal data</strong>, not any IP address.</p>

<p>Lucky us! Prevailing case law does not (yet) ban the use of cookies. Otherwise, no german web site could ever be useful in a web 2.0 context.  (And a few side notes: yes, you can disable cookies in your browser. No, browsing the web won't work any longer then. At least in any sensible way. Yes, if there were no cookies there are different technologies as easy to deploy.)</p>

<h2>SNAFU!</h2>

<p>The legal situation in Germany regarding online privacy is definitely crooked. German courts don't rule data as personal, when it is (cookies); they rule non personal data (IP addresses) as personal, when it is not, even as they recognize the opposite in other cases (i.e. prosecuting illegal downloads).</p>

<p>This looks crooked to me.</p>

<h2>Final Note</h2>

<p>Please don't mistake me with this post. I am not saying I would agree with the way google, or facebook, or someone else treats the privacy of myself and/or of others. I do see the dangers in that.</p>

<p>This post, however, is about these dangers being inherent to internet technologies. Therefore they cannot be fought by legal means alone.</p>
