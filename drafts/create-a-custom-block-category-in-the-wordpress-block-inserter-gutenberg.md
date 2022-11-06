---

title: Create a Custom Block Category in the WordPress Block Inserter (Gutenberg)
post_date: 2021-03-19T12:22:07.000Z
post_status: publish

---

Are you writing a lot of WordPress block editor (Gutenberg) blocks for just one client? Wouldnt it be good to just group them all together?

Simple, just create a custom block category. In the PHP of your plugin just add the following:

```
function wholesomecode_wholesome_plugin_block_categories( $categories ) {
	return array_merge(
		$categories,
		[
			[
				'slug'  => 'wholesome-blocks',
				'title' => __( 'Wholesome Blocks', 'wholesome-boilerplate' ),
			],
		]
	);
}
add_action( 'block_categories', 'wholesomecode_wholesome_plugin_block_categories', 10, 2 );
```

This will register a custom block category in your block inserter.

Adjust the `category` argument of the `registerBlockType` function for your block, like so:

```
/**
 * Blocks are grouped into categories to help users browse and discover them.
 * The categories provided by core are `text`, `media`, `design`, `widgets`, and `embed`.
 */
category: 'wholesome-blocks',
```

And there we have it, your block will appear in your newly created block category.

![Custom Block Category in the Block Inserter](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989659021/KaX7fvAGZ.png)Custom Block Category in the Block Inserter

If you would like more information about creating blocks with Gutenberg have a look at my [create a plugin for WordPress Gutenberg guide,](https://wholesomecode.ltd/guides/creating-plugin-wordpress-gutenberg/) which covers this topic in more details.