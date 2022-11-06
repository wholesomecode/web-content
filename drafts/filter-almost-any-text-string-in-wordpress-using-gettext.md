---

title: Filter (almost) any text string in WordPress using gettext
post_date: 2021-11-05T10:46:34.310Z
post_status: publish

---

I had an interesting job land on my desk today. A customer wanted to change a text string within a third party plugin, however there was no settings or filters to alter the string.

The string however was translatable via WordPresses own internationalisation (i18n) api, and was written in the following format.

`esc_html_e( 'String to translate', 'wholesomecode' );`

## Enter `gettext`

Because the string was translatable, I knew I could hook into the function somehow, and sure enough a quick google presented me with the `gettext` filter.

Using the filter in the following way, I could change the string without having to contact the plugin author.

```
add_filter( 'gettext', 'wholesomecode_filter_text', 0, 3);

/**
 * Filter Text.
 * 
 * Filter text by existing text and domain.
 *
 * @param string $translated_text   The translated version of the string.
 * @param string $untranslated_text The untranslated version of the string.
 * @param string $domain            The text domain of the i18n function.
 */
function wholesomecode_filter_text( $translated_text, $untranslated_text, $domain ) {
    if (  'String to translate' === $untranslated_text && 'wholesomecode' === $domain ) {
        return esc_html__( 'Translated text string', 'wholesomecode' );
    }
    return $translated_text;
}
```

