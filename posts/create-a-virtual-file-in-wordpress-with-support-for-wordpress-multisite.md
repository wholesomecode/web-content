---

title: Create a Virtual File in WordPress with Support for WordPress Multisite
post_date: 2019-01-01T23:00:00.000Z
post_status: publish

---

Recently I was working on a WordPress Multisite instance, and I needed each site (multilingual in this instance) to have their own unique dynamically generated sitemap. I generated these sitemaps by creating **virtual files** within WordPress.

This article shows you how to create a virtual file, and then how to ensure that that virtual file is not accessible through every site in your WordPress Multisite instance.

Setting Up Rewrite Rules
------------------------

To create a virtual file, the first thing we need to do is create some rewrite rules, so that WordPress knows what to do with the virtual URL where we want our file will appear.

The following example creates two sitemap URLs sitemap.xml and sitemap-fr.xml.

```
function wholesomecode_sitemap_rewrites() {
	global $wp;
	$wp->add_query_var( 'map' );

	add_rewrite_rule( 'sitemap\.xml$', 'index.php?map=sitemap', 'top' );
	add_rewrite_rule( '^sitemap-fr\.xml$', 'index.php?map=sitemap-fr', 'top' );
}
add_action( 'init', 'wholesomecode_sitemap_rewrites', 99 );
```

Note how we add a query variable of ‘map’ and then we map the name of our sitemap to this query.

Prevent Canonical Redirects
---------------------------

Sometimes if you have canonical redirects enabled (say through a plugin such as Yoast SEO) WordPress will attempt to add a slash to the end of your URL. We can prevent this by using this function.

```
function wholesomecode_prevent_slash_on_map_variable( $redirect ) {
	if ( get_query_var( 'map' ) ) {
		return false;
	}
	return $redirect;
}
add_filter( 'redirect_canonical', 'wholesomecode_prevent_slash_on_map_variable' );
```

Creating the Virtual File Content
---------------------------------

Now finally, we need to setup the virtual files themselves. To do this we can hook into the `template_include` hook. Here is a very simplified version of what our sitemaps could look like.

Note how we use the map variable to determine which virtual file we wish to display.

```
function wholesomecode_sitemap_contents( $template ) {
	$map = get_query_var( 'map' );
	if ( ! empty( $map ) ) {
		header( 'Content-type: text/xml' );
		if ( 'sitemap' === $map ) {
			?>
			<?xml version="1.0" encoding="UTF-8"?>
			<sitemapindex xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
				<sitemap>
					<loc>https://mattwatson.codes/post-sitemap.xml</loc>
				</sitemap>
			</sitemapindex>
			<?php
		} elseif ( 'sitemap-fr' === $map ) {
			?>
			<sitemapindex xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
				<sitemap>
					<loc>https://mattwatson.codes/fr/post-sitemap.xml</loc>
				</sitemap>
			</sitemapindex>
			<?php
		}
		die();
	}
	return $template;
}
add_action( 'template_include', 'wholesomecode_sitemap_contents' );
```

### Bonus: Include Static Files in Your Virtual URL

The above example was very simple, but you could map static files into these virtual URLs by pulling the contents of them into the file. For example, here we are using sitemaps in the root directory ‘maps’ and dynamically including them based on the map variable.

```
function wholesomecode_sitemap_from_file( $template ) {
	$map = get_query_var( 'map' );
	if ( ! empty( $map ) ) {
		if ( ! function_exists( 'get_home_path' ) ) {
			include_once ABSPATH . '/wp-admin/includes/file.php';
		}
		header( 'Content-type: text/xml' );
		include get_home_path() . 'maps/' . $map . '.xml';
		die;
	}
	return $template;
}
add_action( 'template_include', 'wholesomecode_sitemap_from_file' );
```

Flushing Rewrite Rules
----------------------

The last thing you will need to do, before you see your virtual files is flush the rewrite rules. If you have pasted the above directing into `functions.php` the best course of action is to go to `Settings > Permalinks` in your WordPress dashboard and re-save your permalinks. But if you are building a plugin, you can hook into the activation hook so they automatically kick in when you activate your plugin:

```
register_activation_hook( __FILE__, 'flush_rewrite_rules' );
```

You can [read more about flushing rules in the WordPress Codex ](https://codex.wordpress.org/Function_Reference/flush_rewrite_rules) for other methods that may be relevant to your scenario.

![Our French Sitemap](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990321596/IK-k29Ex4.png)Our French Sitemap

This works great! The virtual files are created no problem, but if I then visited the french version of the site and looked for the sitemap (eg: https://mattwatson.codes/fr/sitemap.xml) I would be able to see the english version of the sitemap. Not ideal.

Unique Virtual Files in WordPress Multisite
-------------------------------------------

The solution to prevent virtual files in your other WordPress Multisite instances is pretty simply really. All you need to do when you are setting up your rewrites, is to check that site you are currently viewing is the one you want that virtual file to appear in.

```
function wholesomecode_sitemap_rewrites() {
	global $wp;
	$wp->add_query_var( 'map' );

	if ( get_current_blog_id() == '1' ) {
		add_rewrite_rule( 'sitemap\.xml$', 'index.php?map=sitemap', 'top' );
	}

	if ( get_current_blog_id() === get_id_from_blogname( 'fr' ) ) {
		add_rewrite_rule( '^sitemap-fr\.xml$', 'index.php?map=sitemap-fr', 'top' );
	}
}
add_action( 'init', 'wholesomecode_sitemap_rewrites', 99 );
```

If you have WordPress Multisite turned off, the above function will break, so you might want to check that the `get_current_blog_id` function exists before you try to use it.