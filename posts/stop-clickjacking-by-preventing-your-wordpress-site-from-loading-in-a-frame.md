---

title: Stop Clickjacking by preventing your WordPress site from Loading in a Frame
post_date: 2017-06-06T21:00:00.000Z
post_status: publish

---

Clickjacking is a potential security risk caused by someone loading your website inside a frame on another website and making it appear as if the website is legitimate, however they will be hijacking interactions you make with that site, such as form submissions.

For example if your website requires a user to login, the malicious website could be logging the username and passwords of users that log in via their framed site via a clickjacking attack.

Fortunately preventing clickjacking in WordPress is fairly straightforward. You can just add this little snippet of code in your `functions.php` file to set the `X-FRAME-OPTIONS` header to `SAMEORIGIN.`  

```
/**
 * Clickjacking protection
 *
 * Add header to stop site loading in an iFrame.
 **/
function wc_prevent_clickjacking() {
	header( 'X-FRAME-OPTIONS: SAMEORIGIN' );
}
add_action( 'send_headers', 'wc_prevent_clickjacking', 10 );
```

This will send a header along with WordPress that tells browsers to prevent pages showing up in frames that not hosted on the same domain as your website.

Clickjacking and Older Browsers
-------------------------------

Older browsers (browsers older than Internet Explorer 8 (IE8)) don’t understand the `X-FRAME-OPTIONS` header, so we have to fallback to JavaScript. For this you can use the following code:

```
/**
 * The HTTP header X-Frame-Options: SAMEORIGIN should be set.
 *
 * Older browsers do not support the X-Frame-Options header, so the following
 * JavaScript will act as a workaround.
 */
try { top.document.domain } catch (e) {
	var f = function() {
		document.body.innerHTML = '';
	};
	setInterval( f, 1 );
	if ( document.body ) {
		document.body.onload = f;
	};
}

```

Inside your theme create a folder named `JS` and cut and paste that code snippet into a file called `clickjack-protection.js`.

Of course we only want to enqueue the above script to load for browsers older than IE8, so we can use a good old conditional statement to do this, by placing the following code into your `functions.php` file:

```
/**
 * Clickjacking projection for old browsers
 *
 * Header not supported by older browsers, use JS fallback.
 **/
 function wc_prevent_clickjacking_older_browsers() {
	$script_url = get_stylesheet_directory_uri() . 'js/clickjack-protection.js';
	wp_enqueue_script(
		'clickjack-protection-js',
		$script_url
	);
	// Clickjack headers not supported below IE8.
	wp_script_add_data( 'clickjack-protection-js', 'conditional', 'lt IE 8' );
}
add_action( 'wp_enqueue_scripts', 'wc_prevent_clickjacking_older_browsers', 10 );
```