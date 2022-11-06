---

title: Disable the WordPress Multisite Activation Step
post_date: 2018-10-10T08:30:14.000Z
post_status: publish

---

So, you’ve created a WordPress Multisite installation, and you come to add your users. Simple right? 

Wrong! Multisite introduces additional authentication steps, whereby a user needs to activate their account by clicking a link that has been sent to them. A handy step for things such as self registration, however sometimes it does get in the way.

You see, before people have registered on the site via their activation key, they do not show up in the ‘users’ area of WordPress! This means that when you are managing a lot of users, it can quickly become frustrating. 

Removing the Activation Step in WordPress Multisite
---------------------------------------------------

Fortunately it is super simple to remove the activation step completely and have things behave like a regular old WordPress installation again. Simply just add the following code into your `functions.php` file:

```
function wholesomecode_remove_wpmu_activation( $user_login, $user_email, $key ) {
    wpmu_activate_signup( $key );
    return false;
}
add_filter( 'wpmu_signup_user_notification','wholesomecode_remove_wpmu_activation', 10, 3 );
```
