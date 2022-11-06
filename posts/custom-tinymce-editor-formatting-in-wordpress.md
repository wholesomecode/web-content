---

title: Custom TinyMCE Editor Formatting in WordPress
post_date: 2021-12-20T10:29:12.863Z
post_status: publish

---

Sometimes when writing a block of text using the WordPress editor, you need it to look a certain way, we usually do this by adding styles to pieces of text, but we don’t always want our clients to have to open up the text editor and do this manually.

In this example we will create a small filter that you can add to your `functions.php` file to give the user some extra formatting options, to add a custom ‘Lead’ paragraph style.

Using the Custom TinyMCE Formatting
-----------------------------------

Here is a super quick example of the filter in action. It shows you:

*   How to toggle the toolbar so that you can see the Formats menu
*   How to apply the formatting using the Formats menu
*   How to clear the styles

![TinyMCE Formatting in action](https://sp-ao.shortpixel.ai/client/to_webp,q_glossy,ret_img,w_2016,h_1044/https://wholesomecode.ltd/wp-content/uploads/2019/01/TinyMCE-Formatting-1.gif)TinyMCE Formatting in action

The Code
--------

The code is really simple, just hook into `tiny_mce_before_init` and add your custom style to the `style_formats` array. Be sure to include a title, the type of element you want to use, and most importantly the class.

```
/**
 * TinyMCE Formatting
 *
 * Adds a pagraph around selected text with a class of .lead.
 *
 * @param  array $settings Array of TinyMCE settings
 * @return array           Array of TinyMCE settings
 */
function wholesomecode_tinymce_formatting( $settings ) {
	$style_formats = array(
		array(
			'title'   => 'Lead Paragraph',
			'block'   => 'p',
			'classes' => 'lead',
			'wrapper' => false,
		),
	);
	$settings['style_formats'] = json_encode( $style_formats );
	return $settings;
}
add_filter( 'tiny_mce_before_init', 'wholesomecode_tinymce_formatting' );
```

### Styles

Of course, nothing will happen without a little styling. Be sure to add the styles to both your main WordPress `styles.css` so your Lead shows on the Front End, and also be sure to register an editor style sheet so that the code shows in the editor (You can use [`add_editor_style()`](https://developer.wordpress.org/reference/functions/add_editor_style/) to do this).

```
/**
 * The lead paragraph text
 *
 * If you choose the 'Lead Paragraph' formatting from the TinyMCE editor a <p>
 * with the class of .lead is applied to it.
 */
.lead {
	font-size: 120%;
}
```