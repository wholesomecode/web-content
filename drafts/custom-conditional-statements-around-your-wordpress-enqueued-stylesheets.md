---

title: Custom conditional statements around your WordPress enqueued stylesheets
post_date: 2017-06-02T08:48:46.000Z
post_status: publish

---

You may be aware that you can add conditional statements around your WordPress enqueued CSS styles (such as `<!--[if gt IE 7]><link .../><![endif]-->)` to load the script for certain versions of Internet Explorer.

As handy as this is, it doesn’t cater for all situations. This tutorial will show you how you can expand WordPress to add any type of conditional around your enqueued CSS styles.

Lets start with a scenario. We have two scripts enqueued, one is for some styles that should only load for Internet Explorer 8 (IE8) and later, but it should also load for all modern browsers. The other script is for Google Fonts that we are already loading with JavaScript:

```
/**
 * Enqueue Scripts
 *
 * Some stylesheets for our example.
 */
function wholesomecode_example_wp_enqueue_scripts() {

	// A stylesheet targeted at IE8+ and new browsers.
	wp_enqueue_style( 'ie-8-plus', get_stylesheet_directory_uri() . '/styles/ie-8-plus.css' );

	// Load external Google Fonts.
	wp_enqueue_style( 'google-font', '//fonts.googleapis.com/css family=Open+Sans:400,300,600,400italic' );
}
add_filter( 'wp_enqueue_scripts', 'wholesomecode_example_wp_enqueue_scripts' );
```

WordPress gives us the handy [`wp_style_add_data()`](https://developer.wordpress.org/reference/functions/wp_style_add_data/) function to add default IE conditionals around our styles, however these do not allow the type of conditional ‘hack’ we need to let our stylesheet render for IE8+ and all modern browsers:

```
/**
 * Enqueue Scripts
 *
 * Some stylesheets for our example, with conditional loading for IE.
 */
function wholesomecode_example_wp_enqueue_scripts() {

	// A stylesheet targeted at IE8+ and new browsers.
	wp_enqueue_style( 'ie-8-plus', get_stylesheet_directory_uri() . '/styles/ie-8-plus.css' );

	// This will load the `ie-8-plus` script for IE8+, but will not load for modern browsers.
	wp_style_add_data( 'ie-8-plus', 'conditional', 'lt IE 7' ); 

	// Load external Google Fonts.
	wp_enqueue_style( 'google-font', '//fonts.googleapis.com/css?family=Open+Sans:400,300,600,400italic' );
}
add_filter( 'wp_enqueue_scripts', 'wholesomecode_example_wp_enqueue_scripts' );
```

As you can see, the code above will load the stylesheet for older versions of IE only, but it will still be commented out for modern browsers.

Introducing the [`style_loader_tag`](https://developer.wordpress.org/reference/hooks/style_loader_tag/) hook! Using this hook we can wrap custom conditional statements around any of our enqueued CSS stylesheets. So to put in the IE8+ and modern browsers, we would simply add the following code:

```
/**
 * Add custom conditional to styles
 *
 * If you want to load for IE8+ you need a custom conditional arround
 * your style declaration that WordPress does not cater for.
 *
 * @param  string $tag    The tag we want to wrap around.
 * @param  string $handle The handle of the tag.
 * @return string         The wrapped around tag.
 */
function wholesomecode_example_style_loader_tag( $tag, $handle ) {
	
	if ( 'ie-8-plus' === $handle  ) {
		$tag = "<!--[if gt IE 8]><!-->$tag<![endif]-->";
	}
	
	return $tag;
}
add_filter( 'style_loader_tag', 'wholesomecode_example_style_loader_tag', 10, 2 );
```

And if we want to do something a little different? Lets say we are loading our Google Fonts via JavaScript, but we want a `<noscript>` fallback so our fonts still render when JavaScript is turned off? Simple, here is an example of a `<noscript>` custom conditional statement:

```
/**
 * Add custom conditional to styles
 *
 * Lets say we are loading Google Fonts via JavaScript, but we still want
 * to use the font if JavaScript is disabled. We can add a custom conditional
 * Add a noscript around our style.
 *
 * @param  string $tag    The tag we want to wrap around.
 * @param  string $handle The handle of the tag.
 * @return string         The wrapped around tag.
 */
function wholesomecode_example_style_loader_tag( $tag, $handle ) {
	
	if ( 'google-font' === $handle  ) {
		$tag = '<noscript>' . $tag . '</noscript>';
	}
	
	return $tag;
}
add_filter( 'style_loader_tag', 'wholesomecode_example_style_loader_tag', 10, 2 );
```

There we go, easy custom conditional statements around WordPress enqueued CSS stylesheets!