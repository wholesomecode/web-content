---

title: Create a Custom Sidebar with PluginSidebar
post_date: 2021-03-30T15:06:30.000Z
post_status: publish

---

We have taken a look at custom [block sidebars](https://wholesomecode.ltd/guides/sidebar-control-wordpress-block-inspectorcontrols/), [third party block sidebars](https://wholesomecode.ltd/guides/block-sidebar-filters-higher-order-components/) via InspectorControls, and how to add controls to the [post sidebar](https://wholesomecode.ltd/guides/controls-post-sidebar-plugindocumentsettingpanel/). In this guide we are going to look at putting our controls in their very own custom sidebar.

![Our Custom Sidebar](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989585359/HH3mfVji9.png)Our Custom Sidebar

The screenshot above shows what we are going to build. For the most part we are just going to slightly alter the code we built in the [Post Sidebar guide](https://wholesomecode.ltd/guides/controls-post-sidebar-plugindocumentsettingpanel/), so make sure you have completed that guide first.

Prerequisites
------------------

*   Have followed the [Creating a Plugin for the WordPress Block Editor (Gutenberg)](https://wholesomecode.ltd/guides/creating-plugin-wordpress-gutenberg/) guide
*   Read the previous sidebar guides ([sidebar for a custom block](https://wholesomecode.ltd/guides/sidebar-control-wordpress-block-inspectorcontrols/), and [sidebar for an existing block](https://wholesomecode.ltd/guides/block-sidebar-filters-higher-order-components/))
*   Have followed the [Add Controls to the Post Sidebar with PluginDocumentSettingPanel](https://wholesomecode.ltd/guides/controls-post-sidebar-plugindocumentsettingpanel/) guide

Setting Up the Sidebar
---------------------------

We are going to use the code from the [Post Sidebar guide](https://wholesomecode.ltd/guides/controls-post-sidebar-plugindocumentsettingpanel/), so if you haven’t completed that guide, please do that. It’s ok, I’ll wait.

Open up the `/components/Sidebar.js` file that you created in the previous guide. We are going to replace the line that imports the `PluginDocumentSettingPanel` with the following code:

```
import { PluginSidebar, PluginSidebarMoreMenuItem } from '@wordpress/edit-post';
```

Now replace the markup `<PluginDocumentSettingPanel...` with `<PluginSidebar...` and job done! Right?

Well, yes and no. That does give us a sidebar, but it’s ugly! We need some panels so that the controls are formatted correctly.

Also the sidebar is missing from the ‘Options’ menu. To solve this we need to implement `PluginSidebarMoreMenuItem`.

Edit `/components/Sidebar.js` again, and paste in the following code:

```
import { Panel, PanelBody, ToggleControl } from '@wordpress/components';
import { dispatch, select } from '@wordpress/data';
import { PluginSidebar, PluginSidebarMoreMenuItem } from '@wordpress/edit-post';
import { Component, Fragment } from '@wordpress/element';
import { __ } from '@wordpress/i18n';

export default class Sidebar extends Component {
    render() {
        const meta = select( 'core/editor' ).getEditedPostAttribute( 'meta' );
        const exampleToggle = meta['_wholesomecode_wholesome_plugin_require_login'];

        return (
            <Fragment>
                <PluginSidebarMoreMenuItem target="wholesomecode-wholesome-plugin-sidebar">
                { __( 'Display Settings', 'wholesome-plugin' ) }
				</PluginSidebarMoreMenuItem>
                <PluginSidebar
                    name="wholesomecode-wholesome-plugin-sidebar"
                    title={ __( 'Display Settings', 'wholesome-plugin' ) }
                >
                    <Panel>
                        <PanelBody
                            title={ __( 'Display Settings', 'wholesome-plugin' ) }
                            icon="visibility"
                        >
                            <ToggleControl
                                checked={ exampleToggle }
                                help={ __( 'User must be logged-in in to view this post.', 'wholesome-plugin' ) }
                                label={ __( 'Require Login', 'wholesome-plugin' ) }
                                onChange={ ( value ) => {
                                    dispatch( 'core/editor' ).editPost( {
                                        meta: {
                                            '_wholesomecode_wholesome_plugin_require_login': value,
                                        },
                                    } );
                                    this.setState( { exampleToggle: value } );
                                } }
                            />
                        </PanelBody>
                    </Panel>
                </PluginSidebar>
            </Fragment>
        );
    }
}
```

That’s better, we have used the exact same code as in our previous post sidebar example, but now it looks the part.

Viewing the Custom Sidebar
-------------------------------

Now that we have our code in place, don’t forget to run `npm start` to compile it, and it should now look like the following:

![Our Custom Sidebar in Action](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989591149/-JD-58pwj.gif)Our Custom Sidebar in Action

As in our plugin sidebar guide, we switch the require login toggle, refresh a logged out post, turn it back off and refresh the post again. When toggled we cannot access the post.

Extra: Dynamically Switching the Sidebar
------------------------------------------

The name of our sidebar is `wholesomecode-wholesome-plugin-sidebar'`. If we wish to we can automatically open and close this sidebar from elsewhere in the editor. Let’s say we have a button with a click action that does this.

The code to open the sidebar dynamically is as follows:

```
wp.data.dispatch('core/edit-post').openGeneralSidebar( 'wholesomecode-wholesome-plugin-sidebar/wholesomecode-wholesome-plugin-sidebar' );
```

Likewise we can dynamically close the sidebar like so:

```
wp.data.dispatch('core/edit-post').closeGeneralSidebar( 'wholesomecode-wholesome-plugin-sidebar/wholesomecode-wholesome-plugin-sidebar' );
```

Extra: Taking it Further
--------------------------

*   Have a look at creating [nested child blocks with the `InnerBlocks` component](https://wholesomecode.ltd/guides/template-innerblocks-wordpress-gutenberg/)
*   Have a look at [using post meta fields in Gutenberg blocks](https://wholesomecode.ltd/guides/post-meta-fields-store-attributes-wordpress-gutenberg/) (if you haven’t already) and [Gutenberg Component Driven Custom Meta Boxes](https://wholesomecode.ltd/guides/custom-meta-boxes-wordpress-gutenberg/)
*   Have a look at [creating a settings page with Gutenberg Components](https://wholesomecode.ltd/guides/create-settings-page-wordpress-gutenberg-components/)