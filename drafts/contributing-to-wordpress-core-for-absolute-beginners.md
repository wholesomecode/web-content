---

title: Contributing to WordPress Core for Absolute Beginners
post_date: 2016-08-07T19:30:06.000Z
post_status: publish

---

This blog post details my first experience of contributing to WordPress Core, and hopefully it will provide a little help for any other budding contributors.

Where to Start
--------------

The [WordPress.org](http://wordpress.org/) website has a [fantastic section for contributors](http://make.wordpress.org/), the section I was looking for in particular was the [Make WordPress Core area](https://make.wordpress.org/core/) of the website. It was here that I found the [Core Contributors Handbook](https://make.wordpress.org/core/handbook/), which gives a great guide on how to get started.

### WordPress Development Environment

One of the first things that you need to do is configure a local WordPress environment. I referred directly to the handbook for advice on how to do this, and in particular, the section on installing [VVV](https://make.wordpress.org/core/handbook/tutorials/installing-a-local-server/installing-vvv/) (Varying Vagrant Variants). The [VVV GitHub repository](https://github.com/Varying-Vagrant-Vagrants/VVV) also has a great guide to help you get started.

You can choose whichever local development method you like, however VVV handily already pulls down the “WordPress Trunk” SVN (Subversion) repository for you, which is what you need to start contributing, so the rest of this guide will assume you are using Vagrant and VVV.

Finding a WordPress Issue
-------------------------

The WordPress Core team are great for newbies like me. On the Make WordPress Core site they handily link to a section of Trac (their issue management system) with tickets marked as [Good First Bugs](https://core.trac.wordpress.org/tickets/good-first-bugs). As this was my first time contributing, what better place to start?

![Make WordPress Core - Good First Bugs Page](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990534776/LynhT0iTR.png)The Make WordPress Core – Good First Bugs Page, with the bug that I was about to work on selected.

### Choosing an Issue

So lets have a look what tickets were on the ‘Good First Bugs’ list today:

*   **[#37529 — Links in the admin footer should be underlined](https://core.trac.wordpress.org/ticket/37529)** — hmmm, that one already has a patch, and I really want to “get my code on”, so that one is not for me.
*   **[#37448 — Primary and secondary buttons need to reset the outline property](https://core.trac.wordpress.org/ticket/37448)** — that one is all about the CSS, I came here for some PHP love, so nope, not that one either.
*   **[#27888 — Feature request:](https://core.trac.wordpress.org/ticket/27888) `get_current_admin_url()` and `get_current_admin_hook()`** — Bingo! Nobody has patched that one yet, so this looks like something I can really get my teeth into.

Preparing to Code
-----------------

I needed the latest version of WordPress trunk from SVN in order to develop my patch. To make sure I got this, I simply ran the following commands in the terminal.

```
cd ~/sites/www/wordpress-trunk
vagrant up --provision
```

I was prompted for my password, but this is essentially it! Lets step through those commands:

1.  Change directory (`cd`) to the path of my `wordpress-trunk` installation (this comes with VVV)
2.  Start Vagrant and provision it. This will automatically pull down the latest version of WordPress trunk from SVN

Simple right? All we need to do now is open up the `wordpress-trunk` directory in our favourite editor.

Writing the Code
----------------

First up I wanted to address the requested `get_current_admin_url()` function. The ticket had some suggested code, however I thought the proposed solution was a bit long winded as the same result could be achieved in less code by simply querying the query string using PHPs built in `$_SERVER['QUERY_STRING']` variable. Here is the code I wrote:

```
function get_current_admin_page_url() {

    if ( ! is_admin() ) {
        return false;
    }

    global $pagenow;

    $url = $pagenow;
    $query_string = $_SERVER['QUERY_STRING'];

    if ( ! empty( $query_string ) ) {
        $url .= '?' . $query_string;
    }

    return $url;
}
```

### Where to put the Code?

Handily, [Pascal Birchler](https://profiles.wordpress.org/swissspidy) (a WordPress Core contributor) had already [commented on the ticket](https://core.trac.wordpress.org/ticket/27888#comment:7) with the suggestion that it should be placed inside `wp-admin/includes/misc.php`. So I added my code to the bottom of that file.

Making the Patch
----------------

Making a patch is simple if you have been using VVV, although you do need to SSH into your VVV environment to do it. Doing that is as simple as writing the following code into your terminal:

```
vagrant ssh
```

Once you have done that you should see something a little like the following image:

![Entering the vagrant environment using SSH](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990540307/SZyWmYJ6h.png)Entering the vagrant environment using SSH

Now that you are inside your Vagrant environment you need to navigate to the `wordpress-trunk` directory, and then create your patch. As the ticket number for this patch is 27888, the name of the patch should reflect this. To create the patch you need to enter the following commands into your terminal:

```
cd ~/../../srv/www/wordpress-trunk
svn diff > 27888.diff
```

The first line just changes the directory to `wordpress-trunk` in your Vagrant instance.

The second line is a command to make an SVN `diff` file. This will create the file `27888.diff` in the root of your `wordpress-trunk` directory. This file contains all the differences between the latest version of `wordpress-trunk` in the SVN repository, and the changes you have made. This is our patch.

The beauty of VVV is that you now also have this file on your host machine in the same directory, so you can exit Vagrant now by entering the following command into your terminal:

```
exit
```

Submitting the Patch
--------------------

To submit the patch go back to the ticket page (in this case [ticket #27888](https://core.trac.wordpress.org/ticket/27888)), and if you haven’t already, login.

At the top of the page, under the initial ticket information, you will find a button called “Attach file”. Click this, upload the `diff` (the patch) and enter a comment. You will end up creating a comment that looks something like this:

![Patch to add a get_current_admin_page_url() function into wp-admin/includes/misc.php. This is my first patch, so be gentle with me when feeding back.](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990544527/ARFOJA_IA.png)](https://core.trac.wordpress.org/ticket/27888)My first patch

### Updating the Patch

You may have spotted that this patch only contains one of the two requested functions on this ticket, so I jumped back into the `wp-admin/includes/misc.php` file, and added in the code to make the `get_current_admin_hook()` function.

I then repeated patch creation and submit process. As I was updating the same patch, I simply appended a number to the end of the `diff` file to indicate that it had been updated, creating the file `27888.2.diff`.

![Patch to add a get_current_admin_hookl() function into wp-admin/includes/misc.php. This is only my second patch, so feedback appreciated.](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990549003/jRD-GChkI.png)My second patch

Updating the Ticket
-------------------

I now needed to update the ticket to show that a patch had been applied. At the bottom of the screen there is a comment section, along with some options to modify the ticket. It looks a little something like this:

![Adding a comment and modifying a ticket](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990554182/aPJ6e7Mju.png)Adding a comment and modifying a ticket

After filling this form in, the following comment was left on the ticket:

![Keywords has-patch needs-testing added; needs-patch removed I should note that 27888.2.diff contains both patches, including the one from 27888.diff.](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990558670/V4eYUWM7G.png)Updating the ticket

What Next?
----------

For now, we wait. The patches need testing, and in my experience its a good idea to let somebody else test your work (also writing WordPress Unit tests will be the subject of a future blog post). Finally the patch will need approving and possibly submitting into WordPress core by one of those lovely core contributors that have the power to do so.

**“But it might not get in. I don’t think I could take the pressure!”  —** I hear you cry.

For the most part, I don’t care if it gets into core or not, or even if my work gets rejected. Why? Because I did this to learn, and any feedback gained, negative or positive, will aid that learning.

In the meantime I plan to write more patches. These may get accepted (or not), and if I come across any really really good ones, I’ll let you know.