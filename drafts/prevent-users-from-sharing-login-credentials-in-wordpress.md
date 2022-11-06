---

title: Prevent users from sharing login credentials in WordPress
post_date: 2017-06-01T22:36:59.000Z
post_status: publish

---

As any Information Governance expert will tell you, sharing usernames and passwords is bad! Fortunately we can boost your WordPress security with this tip.

Although nothing is as good as educating your users as to why they shouldn’t be sharing their usernames and passwords, in WordPress we can certainly make it difficult for them to do so. Simply add this snippet to your `functions.php` file:

```
<?php
/**
 * Login One User Instance
 *
 * Only allow one instance of a user to be logged in at any one time.
 * Other browser sessions will be logged out other than the latest user to 
 * sign in with that username.
 */
function wholesomecode_login_one_user_instance() {
	global $sessions;
	$sessions = WP_Session+Tokens::get_instance( get_current_user_id() );
	$sessions->destroy_others( wp_get_session_token() );
}
add_action( 'setup_theme', 'wholesomecode_login_one_user_instance', 0 );
```

This snippet only allows one instance of a user credentials to be logged into to the site at once. So if your users are sharing their details, only one will be allowed in the system at a time (and the other people logging in with the same credentials will get kicked out).

Not only will this boost your sites security, but it should encourage your users to login with their own details (after a few times of being kicked out).