---

title: Changing your WordPress site language (locale) dynamically
post_date: 2017-06-16T08:30:29.000Z
post_status: publish

---

There are some fantastic multilingual plugins for WordPress, however for one client project I needed something far more flexible, as this particular client needed to cater for several countries that each spoke several languages.

In short the client needed to be able to have a multisite instance that had a country and language set as default, and the ability to override the country and language on each page / post.

Building a Multilingual Plugin
------------------------------

If enough people [bug me to do it](https://wholesomecode.ltd/contact/), I may consider releasing the whole thing as a standalone plugin, however the heart of the multilingual plugin I build relies on the `locale` WordPress filter, and the heart of the plugin resides in the following code snippet:  

```
/**
 * Redefine the sites Locale (site langauge)
 *
 * In this example we try to get the country and the langauge from the page meta,
 * if that doesnt exist we fallback to the country and langauge set in the customizer,
 * which in turn fall back to gb and en.
 */
function wholesomecode_example_redefine_locale( $locale ) {

    $country = get_post_meta( get_the_id(), '_wholesomecode_example_global_country_override', true );
    $lang    = get_post_meta( get_the_id(), '_wholesomecode_example_global_language_override', true );

    if ( ( empty( $lang ) || ! ( is_page() || is_single() || is_singular() ) ) ) {
        $country = get_theme_mod( 'wholesomecode_example_multilingual_country', 'gb' );
        $lang    = get_theme_mod( 'wholesomecode_example_multilingual_language', 'en' );
    }

    if ( 'default' !== $country ) {
        $locale = $lang . '_' . strtoupper( $country );
    } else {
        $locale = $lang . '-' . $country;
    }

    return $locale;

}
add_filter( 'locale', 'wholesomecode_example_redefine_locale', 10 );
```

Tada! Your locale is now set, and your multilingual site should now be switching languages based on your page/site meta.

Wait! My WordPress Core Default Translations Are Not Working!
-------------------------------------------------------------

This is an issue I came across, and couldn’t for the life of me figure it out. My theme translated perfectly after I had added the relevant .mo and .po files, however WordPress core default translations (such as the ‘Leave a Reply’ message in the comments template) were not translating.

It turns out the solution was pretty simple! Although I had catered for custom domain translations, I had forgot to put one simple bit of code into my theme: `load_default_textdomain()`.  

```
/**
 * Load default text domain
 *
 * If default WordPress Core translations don't work, add this to your
 * `functions.php` file.
 */
load_default_textdomain();
```

If I have shaved a couple of hours off of your debugging with that ‘one weird tip’, you are welcome!