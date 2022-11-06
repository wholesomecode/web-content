---

title: Create Custom Meta Boxes Using the WordPress Block Editor (Gutenberg)
post_date: 2021-03-06T21:17:02.000Z
post_status: publish

---

The first question you might have is “_why would I need to create custom meta boxes with the Block Editor (Gutenberg)?_“, you can still implement meta boxes with PHP, and add controls to the block, post, or even a custom sidebar in Gutenberg.

There are a few reasons why you might want to do this, including:

*   Some blocks have complex settings, and viewing a meta box when in edit mode may make it easier to use for a client
*   Because you might want to change a certain layout aspect of the post, such as the header. You could hide the post title field and replace it with a custom meta box at the top of the page, and be able to control all the post meta that relates to it
*   Because you can use [block templates](https://wholesomecode.ltd/guides/register-block-template-wordpress-gutenberg/) to place the custom meta boxes where you like, so they make more sense in context
*   Because you can apply this learning to other aspects of block editor (Gutenberg) development

In this guide we are going to expand on what we have already built in our [Post Meta Fields guide](https://wholesomecode.ltd/guides/post-meta-fields-store-attributes-wordpress-gutenberg/). You could use block attributes, however because of the nature of meta boxes, we will use post meta fields in this guide.

Prerequisites
------------------

*   Be familiar with [creating plugins for WordPress Gutenberg](https://wholesomecode.ltd/guides/creating-plugin-wordpress-gutenberg/)
*   Be familiar [dynamic blocks and server side rendering](https://wholesomecode.ltd/guides/php-render-block-wordpress-gutenberg/)
*   Be familiar with using [post meta fields in WordPress Gutenberg](https://wholesomecode.ltd/guides/post-meta-fields-store-attributes-wordpress-gutenberg/)

Creating the Custom Meta Box in JavaScript
-----------------------------------------------

Because we are following on from the [Post Meta Fields guide](https://wholesomecode.ltd/guides/post-meta-fields-store-attributes-wordpress-gutenberg/), will will have already used `register_meta` to create our post meta field, ensuring we have set `show_in_rest` to true and we will have also set our JavaScript attribute’s source to `meta` and defined the meta key.

All that remains is to implement the interface.

Edit the `/src/edit.js` file, replacing the `@wordpress/components` import with the following:

```
import { Panel, PanelBody, TextControl } from '@wordpress/components';
```

Then replace the Edit code block with the following:

```
export default function Edit( { attributes, setAttributes } ) {
	const { blockText } = attributes;
	return (
		<div { ...useBlockProps() }>
			<Panel>
				<PanelBody
					title={ __( 'Example Meta Box', 'wholesome-plugin' ) }
					icon="admin-plugins"
				>
					<TextControl
						label={ __( 'Example Meta', 'wholesome-plugin' ) }
						help={ __( 'This is an example meta field.', 'wholesome-plugin' ) }
						onChange={ ( blockText ) => setAttributes( { blockText } ) }
                      	value={ blockText }
					/>
				</PanelBody>
			</Panel>
		</div>
	);
}
```

We now have a `Panel`, a `PanelBody` and a `TextControl`. Note that I have added the `admin-plugins` to the icon parameter of the `PanelBody` to keep it consistent with the plugin icon.

### Apply the Styles

Replace the SCSS in `/src/editor.scss` with the following:

```
.wp-block.wp-block-wholesomecode-wholesome-plugin {
	color: unset;
	background: unset;
	padding: unset;

	p {
		all: unset;
	}
}
```

Using the Custom Meta Box
------------------------------

If you are using a Custom Meta Box created in Gutenberg, I would recommend placing it with a [block template](https://wholesomecode.ltd/guides/register-block-template-wordpress-gutenberg/), however you can insert it in the normal way like so:

![Using a Gutenberg Custom Meta Box](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989777221/palZJUqI0.gif)Using a Gutenberg Custom Meta Box

Extra: Exploring Other Custom Meta Box Fields
-----------------------------------------------

Once you have created your meta box, there is no limit to what you can add into it. The entire library of WordPress Gutenberg React components is available to you, along with a vast library of external React components ([see my article on adding select2 into your project](https://wholesomecode.ltd/blog/select2-as-inspectorcontrol-wordpress-gutenberg/)).

The following example is the tip of the iceberg of what you can achive:

![Custom Meta Box with Additional Meta Fields](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989782194/WwhnVmWXN.png)Custom Meta Box with Additional Meta Fields

To build this, here is the code you need:

### Register Your Post Meta Fields in PHP

Open up the root PHP file of the plugin (in this case `wholesome-plugin.php`) and update the `register_meta` we added in the previous guide with the following:

```
function wholesomecode_wholesome_plugin_register_post_meta() {
	register_meta(
		'post',
		'_wholesomecode_wholesome_plugin_example_select',
		[
			'auth_callback' => '__return_true',
			'default'       => '',
			'show_in_rest'  => true,
			'single'        => true,
			'type'          => 'string',
		]
	);

	register_meta(
		'post',
		'_wholesomecode_wholesome_plugin_example_text',
		[
			'auth_callback' => '__return_true',
			'default'       => '',
			'show_in_rest'  => true,
			'single'        => true,
			'type'          => 'string',
		]
	);

	register_meta(
		'post',
		'_wholesomecode_wholesome_plugin_example_text_2',
		[
			'auth_callback' => '__return_true',
			'default'       => '',
			'show_in_rest'  => true,
			'single'        => true,
			'type'          => 'string',
		]
	);

	register_meta(
		'post',
		'_wholesomecode_wholesome_plugin_example_text_3',
		[
			'auth_callback' => '__return_true',
			'default'       => '',
			'show_in_rest'  => true,
			'single'        => true,
			'type'          => 'string',
		]
	);

	register_meta(
		'post',
		'_wholesomecode_wholesome_plugin_example_toggle',
		[
			'auth_callback' => '__return_true',
			'default'       => false,
			'show_in_rest'  => true,
			'single'        => true,
			'type'          => 'boolean',
		]
	);
}
add_action( 'init', 'wholesomecode_wholesome_plugin_register_post_meta' );
```
### Update the JavaScript Attributes

Open up `/src/index.js` and replace the attributes we added in the previous guide with the following:

```
attributes: {
	exampleSelect: {
		meta: '_wholesomecode_wholesome_plugin_example_select',
		source: 'meta',
	},
	exampleText: {
		meta: '_wholesomecode_wholesome_plugin_example_text',
		source: 'meta',
	},
	exampleText2: {
		meta: '_wholesomecode_wholesome_plugin_example_text_2',
		source: 'meta',
	},
	exampleText3: {
		meta: '_wholesomecode_wholesome_plugin_example_text_3',
		source: 'meta',
	},
	exampleToggle: {
		meta: '_wholesomecode_wholesome_plugin_example_toggle',
		source: 'meta',
	},
},
```

### Import the Components

Edit the `/src/edit.js` file, replacing the `@wordpress/components` import with the following:

```
import {
	Panel,
	PanelBody,
	PanelRow,
	SelectControl,
	TextControl,
	ToggleControl,
} from '@wordpress/components';
```

### Update the Edit Method

Continue to edit the `/src/edit.js` file, replacing `Edit` code block with the following:

```
export default function Edit( { attributes, setAttributes } ) {
	const {
		exampleSelect,
		exampleText,
		exampleText2,
		exampleText3,
		exampleToggle,
	} = attributes;

	return (
		<div { ...useBlockProps() }>
			<Panel>
				<PanelBody
					title={ __( 'Example Meta Box', 'wholesome-plugin' ) }
					icon="admin-plugins"
				>

						<SelectControl
							help={ __( 'An example dropdown field.', 'wholesome-plugin' ) }
							label={ __( 'Example Select','wholesome-plugin' ) }
							onChange={ ( exampleSelect ) => setAttributes( { exampleSelect } ) }
							options={ [
								{
									label: __( 'Please Select...', 'wholesome-plugin' ),
									value: '',
								},
								{
									label: __( 'Option 1', 'wholesome-plugin' ),
									value: 'option-1',
								},
								{
									label: __( 'Option 2', 'wholesome-plugin' ),
									value: 'option-2',
								},
							] }
							value={ exampleSelect }
						/>

						<TextControl
							help={ __( 'This is an example text field.', 'wholesome-plugin' ) }
							label={ __( 'Example Text', 'wholesome-plugin' ) }
							onChange={ ( exampleText ) => setAttributes( { exampleText } ) }
							value={ exampleText }
						/>

					<PanelRow>
						<TextControl
							help={ __( 'Use PanelRow to place controls inline.', 'wholesome-plugin' ) }
							label={ __( 'Example Text 2', 'wholesome-plugin' ) }
							onChange={ ( exampleText2 ) => setAttributes( { exampleText2 } ) }
							value={ exampleText2 }
						/>
						<TextControl
							help={ __( 'This control is inline.', 'wholesome-plugin' ) }
							label={ __( 'Example Text 3', 'wholesome-plugin' ) }
							onChange={ ( exampleText3 ) => setAttributes( { exampleText3 } ) }
							value={ exampleText3 }
						/>
					</PanelRow>

						<ToggleControl
							checked={ exampleToggle }
							help={ __( 'An example toggle.', 'wholesome-plugin' ) }
							label={ __( 'Example Toggle', 'wholesome-plugin' ) }
							onChange={ ( exampleToggle ) => setAttributes( { exampleToggle } ) }
						/>

				</PanelBody>
			</Panel>
		</div>
	);
}
```

### Add Some SCSS

Finally open up the `/src/editor.scss` file and replace its contents with the following:

```
.wp-block.wp-block-wholesomecode-wholesome-plugin {
	color: unset;
	background: unset;
	padding: unset;

	.components-base-control {
		margin-bottom: 1rem;

		select {
			height: 2rem;
			min-height: 2rem;
		}
	}

	p {
		all: unset;
	}
}
```

Extra: Taking it Further
--------------------------

*   Have a look at creating [nested child blocks with the `InnerBlocks` component](https://wholesomecode.ltd/guides/template-innerblocks-wordpress-gutenberg/)
*   Have a look at [using settings to store data in the options table in Gutenberg](https://wholesomecode.ltd/guides/options-settings-data-wordpress-gutenberg/)