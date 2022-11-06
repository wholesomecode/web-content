---

title: Import your Existing Website into a WordPress Install via RSS
post_date: 2019-01-18T19:42:32.000Z
post_status: publish

---

Do you want to import an existing website into WordPress? Does that existing site provide an RSS feed? Then this is the article for you.

The great news is WordPress has a default importer for RSS. Simply go to `Tools > Import` via your WordPress dashboard. If you haven’t already you will need to install the RSS importer.

![Install the RSS Importer](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990202538/TIT3_DfUs.png)Install the RSS Importer

For our example we will pick a random site with an RSS feed. I’m a big fan of Disney, so let’s use the Disney Tumblr RSS feed: [http://disney.tumblr.com/rss](http://disney.tumblr.com/rss).  

![Download the RSS Feed](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990208428/baPfYvGqA.png)Download the RSS Feed

Open this link in a new window, so you can see the XML (you may have to accept terms). Right Click, and download the XML File.

Via `Tools > Import`, under where it says ‘RSS’ click the ‘Run Importer’ link.

![The Import RSS Screen](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990213493/90nbb3jlA.png)The Import RSS Screen

On the RSS Importer page, select the RSS file you downloaded and click the ‘Upload File and Import’ button.

Using PHP 7?
------------

If you are using PHP 7 you may now be seeing an error in RSS Importer:

**`Fatal error`**`: Uncaught Error: Call to undefined function set_magic_quotes_runtime() in... ...wp-content/plugins/rss-importer/rss-importer.php`

Never fear, quite a few people have reported this issue, and you can see the responses in the official forum for the RSS Importer plugin.

The fix is simple, just go to Plugins in the WordPress dashboard, remove the RSS Importer plugin, and replace it with this [drop in replacement for RSS Importer by David Lynch](https://github.com/kemayo/wp-rss-importer) .

Importing the RSS Content
-------------------------

Once you have patched that issue (if you needed to patch it) can now go back to the RSS Importer page via the Tools > Import page.

Choose the RSS file you downloaded, and click the ‘Upload File and Import’ button.

You should be presented with a screen that shows that all the posts in the feed have been imported successfully.

![Successful Import of RSS Feed](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990218043/Wb8QyTJdr_.png)Successful Import of RSS Feed

If you have a look at your posts screen, you will see all the items in your post list. Note that all of the Categories have been imported automatically.

![Imported Posts on the Posts Screen](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990223113/BWCph5TUH.png)Imported Posts on the Posts Screen

If you click into one of these posts, you will see that the post content has also been migrated.

![Migrated Post Content](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990228256/mw1oLvirN.png)Migrated Post Content

Migrating the Images
--------------------

Now that all the content is migrated, there is one more thing to consider. If you expect the code of the imported content, you will note that the image URLs are still coming from the original site.

![Images Have Not Been Migrated](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990233182/vhXAMgfM9.png)Images Have Not Been Migrated

If you no longer require your previous website, this may be an issue for you. We will need ensure that any images in our posts are migrated into the media library.

There is a plugin that will do this for you ([Import External Images](https://wordpress.org/plugins/import-external-images/)), however it is outdated, and no longer works. Fortunately thanks to the the WordPress ecosystem being open source, [Lee from VR51 has made a working for of the Import External Images plugin](https://github.com/VR51/Import-External-Images-2).

When you install the working plugin, you will get a new sub menu item under Media in the WordPress Dashboard.

![Import Images Menu Item](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990237586/AvxwcHt53.png)Import Images Menu Item

Configure the plugin to exclude any files that you do not want to migrate, and click the ‘Import Images Now’ button.

![Images Importing](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990241533/m1GnlZdQS.png)Images Importing

The images will import for all of your posts, and when it has done you will be presented with something similar to the following:

![Import Complete](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990245389/LwyO859gV.png)Import Complete

Now if you check the source of your posts, the third party images are now all coming from the media library.

![The Image Sources Are Now Internal](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990249842/9HizfKcjD.png)The Image Sources Are Now Internal

If you check the media library, you can see all of the imported images.

![The Images are Now in the Media Library](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990254673/v4NwHv8-p.png)The Images are Now in the Media Library