---

title: @wordpress/create-block  bug hunt
post_date: 2020-02-26T12:05:00.000Z
post_status: publish

---

Today I was writing my [@wordpress/create-block series](https://wholesomecode.ltd/articles/an-overview-of-the-wordpress-create-block-script/) over on Wholesome Code, and during a demo I was filming relating to this, I wanted to use both a Mac and a PC to to run the script to show how easy it is to create a WordPress Block Editor (Gutenberg) block with the correct setup.

I setup on a new install of Windows 10, installed all the tools I needed and… all was not well. The script would not run.

My immediate thought was that there was an issue with the script running on Windows, but it turns out I owed Microsoft an apology, because after trying to debug shell commands on Windows, I went back to the Mac and had the same issue!

Create an Issue
---------------

What should you do when you find an issue? Moan about it for a while? Well, if you like, but also why not create an issue?

Creating an issue is just what I did: [https://github.com/WordPress/gutenberg/issues/20450](https://github.com/WordPress/gutenberg/issues/20450)

> With version 0.8.1 of the @wordpress/create-block script, I always get the error “Minimum system requirements not met!”

I then immediately started trying to look for a fix (as you can see from the next comment in the issue thread).

Within moments however one of the contributors (Grzegorz (Greg) Ziółkowski) was on the case, and put up two potential fixes, squishing the bug once and for all.

Does that count as a contribution? I’m not sure, but I’ll certainly take it as a win!

Sorry Microsoft.