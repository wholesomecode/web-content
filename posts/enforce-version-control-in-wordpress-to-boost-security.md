---

title: Enforce Version Control in WordPress to Boost Security
post_date: 2017-06-07T08:13:57.000Z
post_status: publish

---

If you have spent a long time working on your clients website, ensuring everything is in version control (such as git). Then you would be pretty gutted if your client edits a theme file, or adds a new plugin that lives outside of your version controlled workflow.

Adding code that lives outside of a tightly controlled workflow means:

*   It may not have been fully tested
*   It may contain malware
*   It goes directly onto the live site, potentially bringing down the whole site
*   You don’t have access to the code locally, so it makes it very difficult to debug

WordPress by default will let your clients add code directly to their sites. Let me explain why this is bad, and what you can do to prevent this from happening.

The Evil WordPress Code Editor
------------------------------

By default WordPress gives its users access to the editor. Here they can change files as they see fit.

![The Code Editor in WordPress](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990496318/7ZcP1PmuK.png)Seriously WordPress, why do you do this to us?

This is bad. It means that:

*   Code can be added / altered without proper version control
*   A potential malicious attacker that has managed to get into the backend can start tinkering with code without much in the way to stop them.

Fortunately we can (and you should) turn this off by adding this one piece of code to your `wp-config.php` file:

```
define( 'DISALLOW_FILE_EDIT', true );
```

Keeping Plugins (and Themes) inside Version Control
---------------------------------------------------

One of the best things about WordPress is its vast library of plugins and themes. Unfortunately if we let our clients add new plugins without the proper control it could mean:

*   Potentially untested code is activated on the live site
*   Plugin conflicts
*   Unstyled content is added to the site

So to prevent users from adding plugins directly to the site we can add this one bit of code to their live `wp-config.php` file:

```
define( 'DISALLOW\_FILE\_MODS', true );
```

This prevents users from adding new themes and plugins to the site, meaning that you can add them the proper way via version control, and we can all sleep a little easier at night.

Taking things further
---------------------

You can even disable WordPress updates (I certainly wouldn’t recommend turning off point updates however, as these often contain important security updates), force SSL and a whole bunch of other settings. [This WordPress Codex article about Editing wp-config.php](https://codex.wordpress.org/Editing_wp-config.php) documents a whole load of customisations you can make.