---

title: How to add items to robots.txt with WordPress
post_date: 2016-08-17T21:37:32.000Z
post_status: publish

---

If you wasn’t aware, WordPress auto-generates its own `robots.txt` file, and that there is a filter that you can hook into to allow you to add your own rules to the file.

The filter in question is `robots_txt` filter. Using this information I hand crafted this handy little snippet:

```
/**
 * Filter function used to disallow pdfs from robots.txt
 *
 * @param    string  $output     The output of the robots.txt file
 * @return   string  $public     If the site is public facing
 */
function disallow_pdf_index( $output, $public ) {

    if ( '1' === $public ) {
        $output .= "Disallow: *.pdf\n";
    }
    return $output;
}
add_filter( 'robots_txt', 'disallow_pdf_index', 0, 2 );
```

So there you go, a short, but handy little blog post detailing how you too can add entries to your `robots.txt` file programatically using WordPress.