---

title: Add Controls to the Core and Third Party Block Sidebar with Filters and Higher Order Components
post_date: 2021-12-21T08:48:27.385Z
post_status: publish

---

In our last guide we looked at [adding `InspectorControls` to our custom blocks](https://wholesomecode.ltd/guides/sidebar-control-wordpress-block-inspectorcontrols/) to enable us to use controls in the block sidebar. In this guide we are going to build on that, and add a toggle control to the block sidebar of all of our blocks, including any core and third party blocks that we are using.

In this guide we are going to create the ‘draft block’ functionality that featured in my [Wholesome Publishing plugin](https://wholesomecode.ltd/plugins/wholesome-publishing/).

Prerequisites
------------------

*   Have followed the [Creating a Plugin for the WordPress Block Editor (Gutenberg)](https://wholesomecode.ltd/guides/creating-plugin-wordpress-gutenberg/) guide
*   Have followed the [Add a Sidebar to your Custom WordPress Block with InspectorControls](https://wholesomecode.ltd/guides/sidebar-control-wordpress-block-inspectorcontrols/) guide

Getting Ready for this Guide
---------------------------------

If you have followed the [Add a Sidebar guide](https://wholesomecode.ltd/guides/sidebar-control-wordpress-block-inspectorcontrols/), great. But I’m now going to have to ask you to undo the changes you made in step 4 of that guide (the Draft Block Toggle), because we are going to extrapolate that functionality and apply it to all of our blocks.

If you haven’t followed that guide, please ensure you follow it up to step 4 first, as this guide follows on directly from that one.

Create Higher Order Components
-----------------------------------

Higher Order Components (HoC) are used in React to wrap around an existing component and either pass functionality or props to that component. They are called HoCs because they are inserted into the DOM before the existing component, or you could say in a ‘Higher Order’.

We are going to create two new HoCs, so create a new folder in your plugin called `components` and create the following two files:

*   `/components/withInspectorControls.js`
*   `/components/withIsBlockDraft.js`

### withInspectorControls

Open up `/components/withInspectorControls.js` and add the following code:

```
import { InspectorControls } from '@wordpress/block-editor';
import { PanelBody, ToggleControl } from '@wordpress/components';
import { createHigherOrderComponent } from '@wordpress/compose';
import { Fragment }	from '@wordpress/element';
import { __ } from '@wordpress/i18n';

export default createHigherOrderComponent( ( BlockEdit ) => {
     return ( props ) => {
        const {
            attributes: {
                isBlockDraft,
            },
            setAttributes,
        } = props;

        return (
            <Fragment>
                <BlockEdit { ...props } />
                <InspectorControls>
                    <PanelBody
                        title={ __( 'Block Display Settings', 'wholesome-plugin' ) }
                        icon="visibility"
                        initialOpen={ false }
                    >
                        <ToggleControl
                            label={ __( 'Set Block as Draft', 'wholesome-plugin' ) }
                            checked={ isBlockDraft }
                            onChange={ ( isBlockDraft ) => setAttributes( { isBlockDraft } ) }
                            help={ __( 'If the block is set to draft it will not show on the front end.',
                                'wholesome-plugin' ) }
                        />
                    </PanelBody>
                </InspectorControls>
            </Fragment>
        );
    };
 }, 'withInspectorControls' );
```

You may recognise this code from step 4 of the [Sidebar Guide](https://wholesomecode.ltd/guides/sidebar-control-wordpress-block-inspectorcontrols/). It is pretty much the same code, apart from we have wrapped it in `createHigherOrderComponent`.

The `createHigherOrderComponent` allows us to access `BlockEdit` this is the original edit function of the block that this component will wrap. We will discover how this is passed in when we create the filters shortly.

### withIsBlockDraft

Open up `/components/withIsBlockDraft.js` and paste in the following code:

```
import { createHigherOrderComponent } from '@wordpress/compose';

export default createHigherOrderComponent( ( BlockListBlock ) => {
     return ( props ) => {
         const { attributes } = props;
         const { isBlockDraft } = attributes;

         return <BlockListBlock { ...props } className={ isBlockDraft ? 'draft-block' : '' } />;
     };
 }, 'withIsBlockDraft' );
```

This `createHigherOrderComponent` allows us to access `BlockListBlock` which lets us pass attributes into the editor block wrapper. We are using it here to set a ‘draft-block’ class to the wrapper if the Draft Block toggle has been set. This recreates what we had previously added to the edit method in step 4 of the [Sidebar Guide](https://wholesomecode.ltd/guides/sidebar-control-wordpress-block-inspectorcontrols/).

Again we need to setup a filter for the to be `BlockListBlock` to be passed into this control.

Setup the Filters
----------------------

At the top of the `/src/index.js` file add the following:

```
import { addFilter } from '@wordpress/hooks';

import withIsBlockDraft from './components/withIsBlockDraft';
import withInspectorControls from './components/withInspectorControls';
```

We really should think about altering the structure of our plugin so that the code that registers the block is imported from another file, but for simplicity we will use the same file.

### Filter the Attributes

In the same file add the following code:

```
addFilter(
	'blocks.registerBlockType',
	'wholesome-plugin/block-draft-attributes',
	( settings ) => {
		const { attributes } = settings;
		return {
			...settings,
			attributes: {
				...attributes,
				isBlockDraft: {
					default: false,
					type: 'boolean',
				},
			},
		};
	}
);
```

This filter adds additional attributes to all of the blocks in the build. In theory we could filter this to only be for certain blocks, but we are not going to for our example.

If we did want to limit this filter to certain blocks, we could do that like so:

```
const { attributes, name } = settings;
if ( 'core/paragraph' !== name ) {
  return;
}
```

### Filter the InspectorControls

Add the following code to the `/src/index.js` file:

```
addFilter(
	'editor.BlockEdit',
	'wholesome-plugin/block-draft-inspector',
	withInspectorControls,
);
```

Here we filter `BlockEdit` so that it passes this through to our HoC that we created earlier.

If we wanted to limit this filter to certain blocks we would do the following **within the HoC**:

```
if ( 'core/paragraph' === props.name ) {
  return <BlockEdit { ...props } />
}
```

### Filter the BlockListBlock

Finally let’s add the filter for our `BlockListBlock`:

```
addFilter(
	'editor.BlockListBlock',
	'wholesome-plugin/block-draft-class',
	withIsBlockDraft
);
```

Again, if you want to restrict this block you can do this inside the HoC.

Add the PHP and SCSS
-------------------------

Add the following to `/editor.scss` to make our Draft banner look pretty (this can be done much better, but this is just an example).

```
.editor-styles-wrapper {
	.draft-block {

		border-bottom: 1px dotted #cfcfcf;
		border-left: 1px solid #cfcfcf;
		border-right: 1px solid #cfcfcf;
		margin-top: 3em;

		&::before {
			background: #cfcfcf;
			background-image: linear-gradient(45deg, #efefef 8.33%, #cfcfcf 8.33%, #cfcfcf 50%, #efefef 50%, #efefef 58.33%, #cfcfcf 58.33%, #cfcfcf 100%);
			background-size: 8.49px 8.49px;
			border-left: 1px solid #cfcfcf;
			border-right: 1px solid #cfcfcf;
			border-top: 1px solid #cfcfcf;
			color: black;
			content: 'Draft Block';
			display: block;
			font-family: monospace;
			font-size: 1rem;
			left: 0;
			padding-left: 5px;
			position: absolute;
			top: -27px;
			width: 100%;
		}

		opacity: .4;

		&.is-selected {
			opacity: .8;
		}
	}
}
```

Add the following to the root file of the plugin (in our case `wholesome-plugin.php`).

```
function wholesomecode_wholesome_plugin_remove_blocks_in_draft( $pre_render, $block ) {

	// If we are in the admin interface, bail.
	if ( is_admin() ) {
		return $pre_render;
	}

	// If the block is draft, do not render.
	if (
		isset( $block['attrs'] ) &&
		isset( $block['attrs']['isBlockDraft'] ) &&
		$block['attrs']['isBlockDraft']
	) {
		return false;
	}

	// Otherwise, render the block.
	return $pre_render;
}
add_filter( 'pre_render_block', 'wholesomecode_wholesome_plugin_remove_blocks_in_draft', 0, 2 );
```

This will stop the draft blocks outputting on the frontend.

Seeing it in Action
------------------------

Remember to compile with `npm start` and we can now set any type of block to a draft block:

![Setting and Unsetting Draft Blocks](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989619820/HEwJanis0.gif)Setting and Unsetting Draft Blocks

Extra: Taking it Further
--------------------------

*   Have a look at creating [nested child blocks with the `InnerBlocks` component](https://wholesomecode.ltd/guides/template-innerblocks-wordpress-gutenberg/)
*   Have a look at [using post meta fields in Gutenberg blocks](https://wholesomecode.ltd/guides/post-meta-fields-store-attributes-wordpress-gutenberg/)
*   Have a go at [adding controls to the post sidebar](https://wholesomecode.ltd/guides/controls-post-sidebar-plugindocumentsettingpanel/)