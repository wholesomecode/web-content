---

title: Code Clinic – WordPress Sheffield June 2017
post_date: 2017-06-14T15:46:45.000Z
post_status: publish

---

I aim to help grow and support the WordPress community as a whole. Along with my code and plugin contributions to WordPress, I have also supported the [WordPress Sheffield](http://wpsheffield.com/) community group.

To cater to a wide verity of interests, the WordPress Sheffield format varies between speaker based ‘Mini conferences’, ‘Workshops’ and focussed code clinics, where I, along with other experts in attendance try to offer help and support to those who are having difficulty making their web applications and ideas into a reality.

This month, [Make Do](https://makedo.net/) provided the free pizza and drinks, and our wonderful hosts [Sheffield Hallam University](https://www.shu.ac.uk/) provided us with the space, and we settled in for a code clinic, or should I say ‘Lean Clinic’.

Lean Coffee Clinic
------------------

At our non-speaker focused sessions, we really love the [Lean Coffee](http://leancoffee.org/) format, as it lets us quickly get through lots of topics in one go, however we were really keen to see why our attendees were at WordPress Sheffield, and what specific issues they had, so we transformed the session into a ‘Lean Clinic’.

The basic format of a the session was:

1.  Give everybody time to write down an one or more topics that they would like to discuss (including specific issues) on post-it notes.
2.  Read out each issue and let the room take a vote on which ones we should cover
3.  Order the issues by vote number (higher votes will get talked about first, lower ones may get missed if we run out of time)
4.  Allow 5 minutes discussion of each issue, with a group vote of whether we should continue for another 5 minutes at the end of the allotted time period (repeat until the topic is exhausted).

![WordPress Sheffield Attendees - Sharing their ideas](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990483224/-YWqknmpI.jpeg)Some WordPress Sheffield Attendees sharing their WordPress Issues.

Clinic Topics this Month
------------------------

As usual a wide range of topics were up for discussion, including some old favourites that come up every session by new attendees, such as the difference between [WordPress.org](http://wordpress.org/) and [WordPress.com](http://wordpress.com/).

Here are the topics that were discussed in the clinic, and the answers and solutions that were given:

*   [What is Node.js?](#what-is-nodejs)
*   [Does Top Level Domain (TLD) Matter in Search Engine Optimisation (SEO)?](#does-tld-matter-in-seo)
*   [Why do Emails sent from my WordPress Website get Spam Blocked?](#emails-get-spam-blocked)
*   [What is the difference between WordPress.com and WordPress.org, and can I Monetise WordPress.com?](#can-i-monitise-wordpress)
*   [Can WordPress be used as a Workflow Application, with Readership Restrictions based on Status?](#workflow-application)
*   [How can I Exclude the Last Viewed Post from my Post Category Archive?](#exclude-last-viewed-post)
*   [How Can I Consume External REST API’s with WordPress?](#consume-rest-api)
*   [How can I use Custom Image Sizes in my WordPress theme?](#custom-image-sizes)
*   [Should I develop Widgets, Meta Boxes or the Customizer for my Client Projects?](#widgets-meta-boxes-or-customizer)

<a id="what-is-nodejs"/>What is Node.js?
----------------

[Node.js](https://nodejs.org/en/) uses the Chrome V8 JavaScript Engine to run JavaScript. It can be used as a server, and is sometimes used in the WordPress eco system to serve load HTML templates that render data that can be held in WordPress via the REST API.

A good [case study of this type of development is the ustwo website](https://hmn.md/what-we-do/projects/ustwo/) (the team behind the popular Monument Valley game).

<a id="does-tld-matter-in-seo"/>Does Top Level Domain (TLD) Matter in Search Engine Optimisation (SEO)?
-----------------------------------------------------------------------

[According to Google themselves, TLDs do not affect search rankings](https://webmasters.googleblog.com/2015/07/googles-handling-of-new-top-level.html), however debate was had, and there is some anecdotal evidence behind the fact that if a domain that has been established for longer (e.g. it is a .com or a .net, not a .agency) it would have a better ranking.

Ultimately though, it comes down to content. A .com domain with very poor content would rank less than the equivalent .io domain with fantastic content and structure.

<a id="emails-get-spam-blocked"/>Why do Emails sent from my WordPress Website get Spam Blocked?
--------------------------------------------------------------

There could be multiple factors to this issue.

One factor could be that the `wp_mail` function in WordPress which sends email via PHP (passing the mail off to the built in SMTP server on the host), by default does not include all the necessary headers that SMTP requires to send authenticated email.

Another factor could be that if you buy shared hosting from a hosting company, or if you host WordPress on your own servers, your servers IP may be on a blacklist due to previous abuse, or you it may be sending from a Dynamic IP, and or not be considered a ‘trusted’ mail server.

To remedy this you could look at SMTP plugins to route the email through a trusted server. For larger sites plugins such as [SendGrid](https://sendgrid.com/) will route emails to their trusted servers, and give you status updates for each email, so you can see exactly where it was blocked (if at all).

What is the difference between WordPress.com and WordPress.org, and can I Monetise WordPress.com?
-------------------------------------------------------------------------------------------------

This is our favourite question at our WordPress clinics, as understandably it does cause some confusion for those new to the platform.

In simple terms, WordPress.org is the self-hosted version of WordPress which you can extend however you see fit, whereas WordPress.com provides hosted version of WordPress that is managed externally, and has fewer options in terms of themes and plugins.

If you are new to WordPress we recommend trying out WordPress.com, and if your website needs grow beyond what .com can offer, then there are loads of hosts out there that offer a simply way to setup a WordPress.org instance of WordPress at your custom domain. It is important to note that you can extract your data from WordPress.org at any point.

For more in-depth reading, checkout this great [blog post from WordPress.com on the subject of .com vs .org](https://dailypost.wordpress.com/2013/11/14/com-or-org/).

### <a id="can-i-monitise-wordpress"/>Monetisation of WordPress

If you run your own WordPress.org based website, the monetisation options available to you are limitless. You can use one of the many affiliate plugins available on the Plugin repo, or even install your own custom advertising plugin.

On WordPress.com you still have the ability to manually add affiliate links to your blog posts, but you are not able to implement advertisements such as those provided by Google AdSense. You can however use the official WordAds platform to place adverts from these providers on your website.

You can [read more about monetising WordPress over in the WordPress.com support section](https://en.support.wordpress.com/monetize-your-site/).

<a id="workflow-application"/>Can WordPress be used as a Workflow Application, with Readership Restrictions based on Status?
----------------------------------------------------------------------------------------------

The question specifically was about data being sent publicly from web forms, which were turned into posts that had certain areas of them not editable based on what part of the workflow they had been through.

The answer to this is of course **yes**, _with some custom development_ to match the exact requirements, however plugins such as [GravityFlow](https://gravityflow.io/) and [PublishPress](https://en-gb.wordpress.org/plugins/publishpress/) may be a good step in the right direction to achieve what you need to do with less custom development.

<a id="exclude-last-viewed-post"/>How can I Exclude the Last Viewed Post from my Post Category Archive?
---------------------------------------------------------------------

If you are viewing a post that resides within a WordPress category, and you offer a link to the Post Category Archive, so that the visitor can view more posts in that category, how do you exclude the last post that was viewed from appearing in that list?

This can be achieved through some _custom development_. A plugin could be written that uses cookies or PHP server variables to get the ID of the last viewed post, and then the posts on the category page could be filtered to exclude the ID of the last viewed post.

<a id="consume-rest-api"/>How Can I Consume External REST API’s with WordPress?
-----------------------------------------------------

There are multiple answers to this question, and it all depends on what the data is, and what you want to do with data. Some of the options available are:

*   Write a plugin to pull the external data into posts (custom or otherwise), that you can then handle and render in the usual way through the use of a WordPress theme.
*   Use PHP to pull the data into the theme on the server, and render it into a theme.
*   Use JavaScript within your theme, to render the data on the client side.
*   Develop a web application, that pulls data from multiple sources, including the WordPress API, using a similar method to that described in the [answer to the Node.js question](https://wholesomecode.ltd/#what-is-nodejs) earlier. This could be rendered bother client side, and the server side (should JavaScript on the client side be unavailable) with the use of isomorphic app development.

<a id="custom-image-sizes"/>How can I use Custom Image Sizes in my WordPress theme?
-------------------------------------------------------

Many themes have additional sizes built into them, but if this is not enough, with some custom development you can use the [`add_image_size()` function](https://developer.wordpress.org/reference/functions/add_image_size/) to add multiple image sizes to your WordPress theme.

There are also plugins available that let you add custom image sizes to your site, such as the [Simple Image sizes plugin](https://en-gb.wordpress.org/plugins/simple-image-sizes/).

With regards to images within a gallery, custom development will give you exactly what you need, but many WordPress plugins, including [JetPack](https://jetpack.com/) with give you a great gallery with lots of configurable options.

<a id="widgets-meta-boxes-or-customizer"/>Should I develop Widgets, Meta Boxes or the Customizer for my Client Projects?
------------------------------------------------------------------------------

As with many of the answers given at the WordPress clinic, the answer depends on you and/or your clients specific needs.

Between us we came up with the following use case for each:

### Customizer

This is the most visually pleasing option for the client, however this should mainly be used for global site options, and visual changes. Per post changes are possible, but these are likely to be done at the same time as the post is written, so meta boxes may be more suitable for that.

Don’t forget that widgets can be managed via the customizer, so a widget approach will also tie into this approach.

The future of the customizer is bright, and it may one day fully replace the default editor, to allow visual editing of website data.

### Meta Boxes

Meta boxes sit right alongside the default WordPress post editor, and hold specific information about the post that is being written / edited. This is currently the best way to add post specific information.

### Widgets

Widgets can sit anywhere in the website where there is a registered widget area. The information within them is not usually tied to a specific post, and they can be added, removed and deleted by the your website administrators.

If widgets are used we recommend that the following steps are taken:

*   Certain widget areas should be restricted, so only certain widgets can be added to them (so layout doesn’t break)
*   All widgets have styles applied to them so they are in-keeping with the look and feel of the site (with special attention being given to widgets that come packaged with plugins)
*   Widgets that will not be used on the website are unregistered
*   Widget areas have appropriate styles / logic, so they do not look out of place if they contain no widgets

Widgets are the easiest way for a customer to break a design you have supplied for them, so use them with care.

### Site Builders

While we are listing out ways that allow your clients to add data to a site, an honourable mention should go to the site builders. These are the most flexible approach for a client with _good design skills_ is a site builder such as [Site Origin](https://siteorigin.com/) or [Beaver Builder](https://www.wpbeaverbuilder.com/).

Why Not Join a WordPress Community Meetup in Your Area?
-------------------------------------------------------

If you would like to get involved in WordPress community events, or even start one of your own, check out the [Make WordPress Community section of WordPress.org](https://make.wordpress.org/community/).