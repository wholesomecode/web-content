---

title: Using Post Meta Fields to Store Attributes in the WordPress Block Editor (Gutenberg)
post_date: 2021-03-06T15:40:58.000Z
post_status: publish

---

When you register and store attributes in the WordPress block editor (Gutenberg) you have the option to store them as post meta, instead of the usual block attributes.

Using this method, you could have a setting in your block that can control something post wide, or on the template.

**An Important Note About Rendering Post Meta Attributes**

*You cannot render post meta attributes using a JavaScript save method. A decision was taken by the Gutenberg team to prevent this, because blocks that render post meta fields can easily be broken due to the post meta changing elsewhere on the post.*

In this guide we are going to expand on what we have already built in our [WordPress Gutenberg plugin guide](https://wholesomecode.ltd/guides/creating-plugin-wordpress-gutenberg/), and also the [Dynamic Block guide](https://wholesomecode.ltd/guides/php-render-block-wordpress-gutenberg/).

Prerequisites
------------------

*   Be familiar with [creating plugins for WordPress Gutenberg](https://wholesomecode.ltd/guides/creating-plugin-wordpress-gutenberg/)
*   Be familiar [dynamic blocks and server side rendering](https://wholesomecode.ltd/guides/php-render-block-wordpress-gutenberg/)

Registering the Post Meta in PHP
-------------------------------------

Before we can use a post meta field in JavaScript, we have to make sure that we have registered it in PHP using `register_meta` and that the `show_in_rest` argument has been set to true.

Following on from the [Dynamic Block guide](https://wholesomecode.ltd/guides/php-render-block-wordpress-gutenberg/), open up the root PHP file of the plugin (in this case `wholesome-plugin.php`) and add the following code to the bottom of that file after all other functions:

```
function wholesomecode_wholesome_plugin_register_post_meta() {
	register_meta(
		'post',
		'_wholesomecode_wholesome_plugin_block_text',
		[
			'auth_callback' => '__return_true',
			'default'       => __( 'Wholesome Plugin – hello from the editor!', 'wholesome-plugin' ),
			'show_in_rest'  => true,
			'single'        => true,
			'type'          => 'string',
		]
	);
}
add_action( 'init', 'wholesomecode_wholesome_plugin_register_post_meta' );
```

This code registers a meta field called `_wholesomecode_wholesome_plugin_block_text` for the `post` post type. It also ensures the REST API can access this meta field with the `show_in_rest` value being set to true.

We are also passing the function `__return_true` to the `auth_callback` argument to ensure we have the permissions to access this parameter. If we wanted better security for this parameter we would alter this to a custom function that checks a certain user role.

Registering the Post Meta Attributes in JavaScript
-------------------------------------------------------

Again, following on from the previous guide, open up `/src/index.js` and replace the `attributes` with the following:

```
attributes: {
  blockText: {
    meta: '_wholesomecode_wholesome_plugin_block_text',
    source: 'meta',
  },
},
```

Instead of stating the type of attribute, and assigning a default value, we instead define the meta key that the data attribute will come from and indicating that the `source` of the attribute is `meta`.

As indicated in the warning at the top of this guide, you are not able to access post meta field attributes via the JavaScript `save` method, so compile the plugin with `npm start` and lets see how we can output the meta.

Rendering the Post Meta
----------------------------

Because we have saved our attribute as post meta, we could output this anywhere in WordPress using `get_post_meta` (provided we have the `$post_id`):

```
get_post_meta( $post_id, '_wholesomecode_wholesome_plugin_block_text', true );
```

Continuing the [Dynamic Block guide](https://wholesomecode.ltd/guides/php-render-block-wordpress-gutenberg/), let’s see how we can access this attribute on the server side in PHP.

The meta is not available via the `$attributes` parameter of the `render_callback`, as post meta attributes do not pass their value through to this parameter, but we can access the global `$post` object to get the post ID.

With this in mind, let’s update our `register_block_type` to output the post meta:

```
register_block_type(
  'wholesomecode/wholesome-plugin',
  array(
    'editor_script'   => 'wholesomecode-wholesome-plugin-block-editor',
    'editor_style'    => 'wholesomecode-wholesome-plugin-block-editor',
    'render_callback' => function( $attributes, $content ) {
      global $post;
      $block_text = get_post_meta( $post->ID, '_wholesomecode_wholesome_plugin_block_text', true );
      return "<p class='wp-block-wholesomecode-wholesome-plugin'>$block_text</p>";
    },
    'style'           => 'wholesomecode-wholesome-plugin-block',
  )
);
```

Note that we no longer need to register the `attributes` here, because we are only accessing the post meta field via the `get_post_meta` function.

### Using the Block

This isn’t the best use case of this kind of meta, however when we use the block, we can see that because we are storing the attributes as post meta, if we update the attribute in one block, it updates all blocks, as the post meta is stored against the post, not the individual block:

![Post Meta Attributes in Action](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989791305/Og4pRib_g.gif)Post Meta Attributes in Action

Extra: Taking it Further
--------------------------

*   Have a look at creating [nested child blocks with the `InnerBlocks` component](https://wholesomecode.ltd/guides/template-innerblocks-wordpress-gutenberg/)
*   Take using post meta in Gutenberg further by [creating Custom Meta Boxes in Gutenberg](https://wholesomecode.ltd/guides/create-custom-meta-boxes-using-the-wordpress-block-editor-gutenberg/)
*   Have a look at [using settings to store data in the options table in Gutenberg](https://wholesomecode.ltd/guides/options-settings-data-wordpress-gutenberg/)