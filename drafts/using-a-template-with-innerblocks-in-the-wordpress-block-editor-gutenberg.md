---

title: Using a Template with InnerBlocks in the WordPress Block Editor (Gutenberg)
post_date: 2021-03-04T00:03:31.000Z
post_status: publish

---

When you create a block in the WordPress Block Editor (Gutenberg) you can create an area that allows other block types to be nested inside your block. You do this with the `InnerBlocks` component.

In our other guides we previously explored [block patterns](https://wholesomecode.ltd/guides/register-block-pattern-wordpress-gutenberg/) and [block templates](https://wholesomecode.ltd/guides/register-block-template-wordpress-gutenberg/). This guide looks at creating a template for nested blocks.

Prerequisites
------------------

*   Be familiar with [creating plugins for WordPress Gutenberg](https://wholesomecode.ltd/guides/creating-plugin-wordpress-gutenberg/)

Create a Nested Block Plugin
-------------------------------------

After you have carried out the steps in the [creating a Gutenberg plugin](https://wholesomecode.ltd/guides/creating-plugin-wordpress-gutenberg/) tutorial, up to (but not including) **step 5**, you should have the framework in place to create the nested block plugin.

### Add InnerBlocks to `/src/edit.js`

Open up `/src/edit.js` in your editor and replace the import for `useBlockProps` with the following:

```
import { InnerBlocks, useBlockProps } from '@wordpress/block-editor';
```

This will let us use the InnerBlocks component in our edit render method.

Then change the entire Edit function to thefollowing:

```
export default function Edit() {
	return (
		<div { ...useBlockProps() }>
			<InnerBlocks/>
		</div>
	);
}
```

This registers the InnerBlock

### Output the Attributes in `/src/save.js`

Open up `/src/save.js` in your editor and replace the import for `useBlockProps` with the following:

```
import { InnerBlocks, useBlockProps } from '@wordpress/block-editor';
```

This will let us use the InnerBlocks component in our save render method.

Next output the content of the Innerblocks component by replacing the `save` function with the following:

```
export default function save() {
	return (
		<div { ...useBlockProps.save() }>
			<InnerBlocks.Content />
		</div>
	);
}
```

This simply outputs the value of the InnerBlocks into a `<div>`.

### Compiling the Plugin

Open the terminal, make sure you are in the root directory of your plugin and run the following command:

```
npm start
```

The script will compile. If you have any errors the terminal should make you aware of what they are.

Adding the InnerBlocks Template
------------------------------------

To keep things simple we are going to use the exact same template we previously created for the [block patterns](https://wholesomecode.ltd/guides/register-block-pattern-wordpress-gutenberg/) and the [block template](https://wholesomecode.ltd/guides/register-block-template-wordpress-gutenberg/) guide.

The template is very similar to the one we defined in our [block template guide](https://wholesomecode.ltd/guides/register-block-template-wordpress-gutenberg/), however the attributes are in an object instead of a multidimensional array.

Replace the `Edit` function with the following code:

```
export default function Edit() {
	return (
		<div { ...useBlockProps() }>
			<InnerBlocks
				template={ [
					[ 'core/heading', { level: 2, content: 'Example Nested Block Template' } ],
					[ 'core/paragraph', { content: 'Lorem ipsum dolor sit amet labore cras venenatis.' } ],
					[ 'core/columns', {},
						[
							[ 'core/column', {}, [
									[ 'core/heading', { level: 3, content: 'Sub Heading 1' } ],
									[ 'core/paragraph', { content: 'Lorem ipsum dolor sit amet id erat aliquet diam ullamcorper tempus massa eleifend vivamus.' } ],
								]
							],
							[ 'core/column', {}, [
									[ 'core/heading', { level: 3, content: 'Sub Heading 2' } ],
									[ 'core/paragraph', { content: 'Morbi augue cursus quam pulvinar eget volutpat suspendisse dictumst mattis id.' } ],
								]
							],
						]
					],
				] }
				templateLock="all"
			/>
		</div>
	);
}
```

This creates a template for the nested block. Note the use of `templateLock` which is set to all. This behaves the same as the [template lock for the block template](https://wholesomecode.ltd/guides/register-block-template-wordpress-gutenberg/#template-lock) and has the following two options:

*   `all` — prevents all operations. It is not possible to insert new blocks, move existing blocks, or delete blocks.
*   `insert` — prevents inserting or removing blocks, but allows moving existing blocks.

You can also [define which blocks are allowed within the nested blocks](https://wholesomecode.ltd/#allowed-blocks-for-nested-blocks).

4.  
Inserting and Viewing the InnerBlocks Template
---------------------------------------------------

You can insert the nested block the same way as any other block, by clicking the plus icon and searching for `Wholesome Plugin`.

![Inserting the Nested Block Template](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989810917/f3R3c7c4R.gif)Inserting the Nested Block Template

Note that as before, the nested blocks inside the nested blocks do not have the template lock applied to them.

There are of course workarounds for this, by being creative with how you create a combination of block templates and custom nested blocks.

### Viewing the Block

After a little bit of tinkering with the SCSS, our outputted block looks a little something like this:

![Inserting the Nested Block Template](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989816423/o5fqHdXRu.png)Inserting the Nested Block Template

Extra: Allowed Blocks for Nested Blocks
-----------------------------------------

Instead of locking the template, you could instead restrict it by stating which blocks you are allowed to insert into it. Simply replace the `Edit` function with the following code:

```
export default function Edit() {
	return (
		<div { ...useBlockProps() }>
			<InnerBlocks
				template={ [
					[ 'core/heading', { level: 2, content: 'Example Nested Block Template' } ],
					[ 'core/paragraph', { content: 'Lorem ipsum dolor sit amet labore cras venenatis.' } ],
					[ 'core/columns', {},
						[
							[ 'core/column', {}, [
									[ 'core/heading', { level: 3, content: 'Sub Heading 1' } ],
									[ 'core/paragraph', { content: 'Lorem ipsum dolor sit amet id erat aliquet diam ullamcorper tempus massa eleifend vivamus.' } ],
								]
							],
							[ 'core/column', {}, [
									[ 'core/heading', { level: 3, content: 'Sub Heading 2' } ],
									[ 'core/paragraph', { content: 'Morbi augue cursus quam pulvinar eget volutpat suspendisse dictumst mattis id.' } ],
								]
							],
						]
					],
				] }
				allowedBlocks={ [
					'core/column',
					'core/columns',
					'core/heading',
					'core/paragraph',
				] }
			/>
		</div>
	);
}
```

Here we omit the `templateLock` argument and instead restrict our nested block to the following blocks:

*   Column (`core/column`)
*   Columns (`core/column`s)
*   Group (`core/group`)
*   Heading (`core/heading`)
*   Paragraph (`core/paragraph`)

Again, this won’t work on any nested blocks within the template.

Extra: Taking it Further
--------------------------

*   Need something more flexible? Try [Registering a Block Pattern](https://wholesomecode.ltd/guides/register-block-pattern-wordpress-gutenberg/) instead
*   Need something more static? Try [Registering a Block Template](https://wholesomecode.ltd/guides/register-block-template-wordpress-gutenberg/)