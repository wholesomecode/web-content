---

title: Register a Block Pattern in the WordPress Block Editor (Gutenberg)
post_date: 2021-12-21T11:21:40.676Z
post_status: publish

---

Block patterns in the WordPress block editor (Gutenberg) are a powerful feature that let you create varied layouts with very little effort.

Using `register_block_pattern` you can very quickly create a block pattern for you or your clients.

Prerequisites
------------------

*   WordPress installation
*   Code Editor

Create your Block Pattern
----------------------------------

You build block patterns out of the blocks you have registered on your site. These could be core blocks, or any number of third party or custom blocks.

Start by using the WordPress editor to build a block layout. You can use the settings to make this as complex or as simple as you like.

In this example we are going to keep it simple and simply create a block pattern that is structures as follows:

*   Group Block
    *   Heading
    *   Paragraph
    *   Columns
        *   Column 1
            *   Heading
            *   Paragraph
        *   Column 2
            *   Heading
            *   Paragraph

Once you have built your block pattern, simply copy the code that has been generated for you.

![Copy the Block Pattern Code](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989836961/6zFxpX_3C.gif)Copy the Block Pattern Code

To do this go to the three dots in the top right of the editor (labelled Options), and select ‘Code Editor’. This will show the HTML behind the block pattern you have just created.

Copy this code into your clipboard.

Register the Block Pattern
-------------------------------

Paste this block editor code into your code editor. If you are unsure where, you can use the `functions.php` of your theme.

Make sure you pass it into a variable by placing single quotes (`'`) around the pasted text, like so:

```
$block_pattern_content = '<!-- wp:group -->
<div class="wp-block-group"><div class="wp-block-group__inner-container"><!-- wp:heading -->
<h2>Example Block Pattern</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>Lorem ipsum dolor sit amet labore cras venenatis.</p>
<!-- /wp:paragraph -->

<!-- wp:columns -->
<div class="wp-block-columns"><!-- wp:column -->
<div class="wp-block-column"><!-- wp:heading {"level":3} -->
<h3>Sub Heading 1</h3>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>Lorem ipsum dolor sit amet id erat aliquet diam ullamcorper tempus massa eleifend vivamus.</p>
<!-- /wp:paragraph --></div>
<!-- /wp:column -->

<!-- wp:column -->
<div class="wp-block-column"><!-- wp:heading {"level":3} -->
<h3>Sub Heading 2</h3>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>Morbi augue cursus quam pulvinar eget volutpat suspendisse dictumst mattis id.</p>
<!-- /wp:paragraph --></div>
<!-- /wp:column --></div>
<!-- /wp:columns --></div></div>
<!-- /wp:group -->';
```

You may need to do some escaping of the characters, depending on the complexity of your block pattern.

Next we need to pass this into the `register_block_pattern` function:

```
register_block_pattern(
	'wholesomecode/example-block-pattern',
	[
		'categories'    => [
			'text',
		],
		'content'       => $block_pattern_content,
		'description'   => 'An example block pattern',
		'keywords'      => 'example',
		'title'         => 'Example Block Pattern',
		'viewportWidth' => 800,
	],
);
```

Thats it, the block pattern is registered.

### Breaking Down `register_block_pattern`

*   **Namespace** — (`wholesomecode/example-block-pattern`) is the namespace of the block pattern. Ensure that this is unique to your block pattern. If we ever need to unregister the block pattern, this is the string we will need.
*   **Categories** — This is the block pattern category that the block pattern will show up in, when we insert our block pattern. We can also [add our own categories](https://wholesomecode.ltd/#custom-block-pattern-category).
*   **Content** — This is HTML that generates the block pattern. This example uses the variable that we created from the editor.
*   **Description** — A description of the block pattern.
*   **Keywords** — Keywords that can be used to search for the block pattern.
*   **Title** — The title of the block pattern.
*   **Viewport Width** — The viewport of the block pattern, used for the preview area.

Insert the Block Pattern
-----------------------------

To insert the block pattern, simply use the plus sign on the top left of the editor, and select block patterns.

Select the category you registered your pattern into, and it should appear.

![Inserting the block pattern](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989843280/lTm-5RU7g.gif)Inserting the block pattern

Alternatively you can search by the keywords you registered.

Extra: Custom Block Pattern Category
--------------------------------------

To register a block category, just use the `register_block_pattern_category` function.

```
register_block_pattern_category(
	'my-client',
	[
		'label' => esc_html__( 'My Client Patterns', 'wholesome-plugin' ),
	]
);
```

Then you can use the category you have just registered in your block pattern code, like so:

```
register_block_pattern(
	'wholesomecode/example-block-pattern',
	[
		'categories'    => [
			'my-client',
		],
		'content'       => $block_pattern_content,
		'description'   => 'An example block pattern',
		'keywords'      => 'example',
		'title'         => 'Example Block Pattern',
		'viewportWidth' => 800,
	],
);
```

Now when you come to find your block pattern, it will be located in your custom category:

![Custom Block Pattern Category](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989847746/6dOk3m1li.png)Custom Block Pattern Category

Now you can group all the custom block patterns that you just made for your client together in one place.

Extra: Taking it Further
--------------------------

*   Need something more static? Try [Registering a Block Template](https://wholesomecode.ltd/guides/register-block-template-wordpress-gutenberg/) instead
*   Need something that applies to child blocks? Try [registering an InnerBlocks Template](https://wholesomecode.ltd/guides/template-innerblocks-wordpress-gutenberg/)