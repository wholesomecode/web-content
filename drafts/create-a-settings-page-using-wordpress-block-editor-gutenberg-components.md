---

title: Create a Settings Page using WordPress Block Editor (Gutenberg) Components
post_date: 2021-03-14T20:52:17.000Z
post_status: publish

---

We have previously looked at [storing options and settings using the WordPress Block Editor (Gutenberg)](https://wholesomecode.ltd/guides/options-settings-data-wordpress-gutenberg/), and [expanding the Create Block Script to allow for extra end points](https://wholesomecode.ltd/guides/entry-points-wordpress-create-block-script/). In this guide we are going to put them all together to create a settings page using Gutenberg components.

![The settings page we are about to build](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989702620/3Ff80KRd7.png)The settings page we are about to build

But first, credit where credit is due, inspiration for this guide goes to the Code in WP article by Hardeep Asrani: [Making a “Plugin Options Page” With Gutenberg Components](https://www.codeinwp.com/blog/plugin-options-page-gutenberg/).

Prerequisites
------------------

*   Have followed the [Creating a Plugin for the WordPress Block Editor (Gutenberg)](https://wholesomecode.ltd/guides/creating-plugin-wordpress-gutenberg/) guide
*   Have followed the [Using Options to Store Data in the WordPress Block Editor (Gutenberg)](https://wholesomecode.ltd/guides/options-settings-data-wordpress-gutenberg/) guide
*   Have followed the [Add Entry Points to the WordPress Create Block Script](https://wholesomecode.ltd/guides/entry-points-wordpress-create-block-script/) guide

Create the Settings Page in PHP
------------------------------------

Following on from the guides in the prerequisites, open up the open up the root PHP file of the plugin (in this case `wholesome-plugin.php`) and add the following:

### Register the Settings

As in the [using options to store data guide](https://wholesomecode.ltd/guides/options-settings-data-wordpress-gutenberg/) add the following settings to the file:

```
function wholesomecode_wholesome_plugin_register_settings() {
	register_setting(
		'wholesomecode_wholesome_plugin_settings',
		'wholesomecode_wholesome_plugin_example_select',
		[
			'default'      => '',
			'show_in_rest' => true,
			'type'         => 'string',
		]
	);

	register_setting(
		'wholesomecode_wholesome_plugin_settings',
		'wholesomecode_wholesome_plugin_example_text',
		[
			'default'      => '',
			'show_in_rest' => true,
			'type'         => 'string',
		]
	);

	register_setting(
		'wholesomecode_wholesome_plugin_settings',
		'wholesomecode_wholesome_plugin_example_text_2',
		[
			'default'      => '',
			'show_in_rest' => true,
			'type'         => 'string',
		]
	);

	register_setting(
		'wholesomecode_wholesome_plugin_settings',
		'wholesomecode_wholesome_plugin_example_text_3',
		[
			'default'      => '',
			'show_in_rest' => true,
			'type'         => 'string',
		]
	);

	register_setting(
		'wholesomecode_wholesome_plugin_settings',
		'wholesomecode_wholesome_plugin_example_toggle',
		[
			'default'      => '',
			'show_in_rest' => true,
			'type'         => 'string',
		]
	);
}
add_action( 'init', 'wholesomecode_wholesome_plugin_register_settings', 10 );
```

Register the settings that we will access on the settings page. Be sure to set `show_in_rest` to `true` for each one, so that they can be accessed via Gutenberg.

### Register the Settings Page

Add the block of code to register the settings page:

```
function wholesomecode_wholesome_plugin_settings_page() {
	add_options_page(
		__( 'Wholesome Plugin Settings', 'wholesome-plugin' ),
		__( 'Wholesome Plugin Settings', 'wholesome-plugin' ),
		'manage_options',
		'wholesome_plugin_settings',
		function() {
			?>
			<div id="wholesome-plugin-settings"></div>
			<?php
		},
	);
}
add_action( 'admin_menu', 'wholesomecode_wholesome_plugin_settings_page', 10 );
```

The code above adds a new page to the settings menu. Note that all it does is output a `<div>`, this is what we will use to render the React based Gutenberg components.

![Settings Menu Item](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989706425/4Y7-d9uu4.png)Settings Menu Item

### Enqueue Admin Assets

In order for the next block of code, we need to have followed all the steps in the [Add Entry Points to the Create Block Script](https://wholesomecode.ltd/guides/entry-points-wordpress-create-block-script/) guide. Please ensure you follow all of the steps in that guide before this step, and come back and follow the rest of this guide.

```
function wholesomecode_wholesome_plugin_admin_scripts() {
	$dir = __DIR__;

	$script_asset_path = "$dir/build/admin.asset.php";
	if ( ! file_exists( $script_asset_path ) ) {
		throw new Error(
			'You need to run `npm start` or `npm run build` for the "wholesomecode/wholesome-plugin" block first.'
		);
	}
	$admin_js     = 'build/admin.js';
	$script_asset = require( $script_asset_path );
	wp_enqueue_script(
		'wholesomecode-wholesome-plugin-admin-editor',
		plugins_url( $admin_js, __FILE__ ),
		$script_asset['dependencies'],
		$script_asset['version']
	);
	wp_set_script_translations( 'wholesomecode-wholesome-plugin-block-editor', 'wholesome-plugin' );

	$admin_css = 'build/admin.css';
	wp_enqueue_style(
		'wholesomecode-wholesome-plugin-admin',
		plugins_url( $admin_css, __FILE__ ),
		['wp-components'],
		filemtime( "$dir/$admin_css" )
	);
}
add_action( 'admin_enqueue_scripts', 'wholesomecode_wholesome_plugin_admin_scripts', 10 );
```

Build the Admin Page in JavaScript
---------------------------------------

If you have followed all the steps in the [Add Entry Points to the Create Block Script](https://wholesomecode.ltd/guides/entry-points-wordpress-create-block-script/) guide, you should have a `/src/admin.js` file. Open up that file and delete its contents.

### Render the Component

Remember to run `npm start` in your terminal as per the [create plugin guide](https://wholesomecode.ltd/guides/entry-points-wordpress-create-block-script/), and add the below to your `/src/admin.js` file.

```
import './admin.scss';
import { Icon } from '@wordpress/components';
import {
    Fragment,
    render,
    Component,
} from '@wordpress/element';
import { __ } from '@wordpress/i18n';

class App extends Component {
    constructor() {
		super( ...arguments );
	}

	render() {
		return (
            <Fragment>
                <div className="wholesome-plugin__header">
                    <div className="wholesome-plugin__container">
                        <div className="wholesome-plugin__title">
                            <h1>{ __( 'Wholesome Plugin Settings', 'wholesome-plugin' ) } <Icon icon="admin-plugins" /></h1>
                        </div>
                    </div>
                </div>
            	<div className="wholesome-plugin__main"></div>
            </Fragment>
		)
	}
}

document.addEventListener( 'DOMContentLoaded', () => {
    const htmlOutput = document.getElementById( 'wholesome-plugin-settings' );

    if ( htmlOutput ) {
        render(
            <App />,
            htmlOutput
        );
    }
});

```

If you go to your settings page in the browser, you should now see the following:

![Rendering Components into the Settings Screen](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989710260/gc1cHhILl.png)Rendering Components into the Settings Screen

### Add the Setting Fields

Remember the extra steps in the ‘Using Options to Store Data’ guide? Well, we are pretty much going to paste those verbatim into this component, so you should end up with some code that looks a little like this:

```
import './admin.scss';

import api from '@wordpress/api';

import {
    Button,
    Icon,
    Panel,
    PanelBody,
    PanelRow,
    Placeholder,
    SelectControl,
    Spinner,
    TextControl,
    ToggleControl,
} from '@wordpress/components';

import {
    Fragment,
    render,
    Component,
} from '@wordpress/element';

import { __ } from '@wordpress/i18n';

class App extends Component {
    constructor() {
		super( ...arguments );

		this.state = {
			exampleSelect: '',
			exampleText: '',
			exampleText2: '',
			exampleText3: '',
			exampleToggle: false,
			isAPILoaded: false,
		};
	}

	componentDidMount() {

		api.loadPromise.then( () => {
			this.settings = new api.models.Settings();

			const { isAPILoaded } = this.state;

			if ( isAPILoaded === false ) {
				this.settings.fetch().then( ( response ) => {
					this.setState( {
						exampleSelect: response[ 'wholesomecode_wholesome_plugin_example_select' ],
						exampleText: response[ 'wholesomecode_wholesome_plugin_example_text' ],
						exampleText2: response[ 'wholesomecode_wholesome_plugin_example_text_2' ],
						exampleText3: response[ 'wholesomecode_wholesome_plugin_example_text_3' ],
						exampleToggle: Boolean( response[ 'wholesomecode_wholesome_plugin_example_toggle' ] ),
						isAPILoaded: true,
					} );
				} );
			}
		} );
	}

	render() {
		const {
			exampleSelect,
			exampleText,
			exampleText2,
			exampleText3,
			exampleToggle,
			isAPILoaded,
		} = this.state;

		if ( ! isAPILoaded ) {
			return (
				<Placeholder>
					<Spinner />
				</Placeholder>
			);
		}

		return (
            <Fragment>
                <div className="wholesome-plugin__header">
                    <div className="wholesome-plugin__container">
                        <div className="wholesome-plugin__title">
                            <h1>{ __( 'Wholesome Plugin Settings', 'wholesome-plugin' ) } <Icon icon="admin-plugins" /></h1>
                        </div>
                    </div>
                </div>

                <div className="wholesome-plugin__main">
                    <Panel>
                        <PanelBody
                            title={ __( 'Panel Body One', 'wholesome-plugin' ) }
                            icon="admin-plugins"
                        >
                            <SelectControl
                                help={ __( 'An example dropdown field.', 'wholesome-plugin' ) }
                                label={ __( 'Example Select', 'wholesome-plugin' ) }
                                onChange={ ( exampleSelect ) => this.setState( { exampleSelect } ) }
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
                        </PanelBody>
                        <PanelBody
                            title={ __( 'Panel Body Two', 'wholesome-plugin' ) }
                            icon="admin-plugins"
                        >
                            <TextControl
                                help={ __( 'This is an example text field.', 'wholesome-plugin' ) }
                                label={ __( 'Example Text', 'wholesome-plugin' ) }
                                onChange={ ( exampleText ) => this.setState( { exampleText } ) }
                                value={ exampleText }
                            />

                        </PanelBody>
                        <PanelBody
                            title={ __( 'Panel Body Three', 'wholesome-plugin' ) }
                            icon="admin-plugins"
                        >
                            <PanelRow>
                                <TextControl
                                    help={ __( 'Use PanelRow to place controls inline.', 'wholesome-plugin' ) }
                                    label={ __( 'Example Text 2', 'wholesome-plugin' ) }
                                    onChange={ ( exampleText2 ) => this.setState( { exampleText2 } ) }
                                    value={ exampleText2 }
                                />
                                <TextControl
                                    help={ __( 'This control is inline.', 'wholesome-plugin' ) }
                                    label={ __( 'Example Text 3', 'wholesome-plugin' ) }
                                    onChange={ ( exampleText3 ) => this.setState( { exampleText3 } ) }
                                    value={ exampleText3 }
                                />
                            </PanelRow>
                        </PanelBody>
                        <PanelBody
                            title={ __( 'Panel Body Four', 'wholesome-plugin' ) }
                            icon="admin-plugins"
                        >
                            <ToggleControl
                                checked={ exampleToggle }
                                help={ __( 'An example toggle.', 'wholesome-plugin' ) }
                                label={ __( 'Example Toggle', 'wholesome-plugin' ) }
                                onChange={ ( exampleToggle ) => this.setState( { exampleToggle } ) }
                            />
                        </PanelBody>
                        <Button
                            isPrimary
                            isLarge
                            onClick={ () => {}}
                        >
                            { __( 'Save', 'wholesome-plugin' ) }
                        </Button>
                    </Panel>
                </div>
            </Fragment>
		)
	}
}

document.addEventListener( 'DOMContentLoaded', () => {
    const htmlOutput = document.getElementById( 'wholesome-plugin-settings' );

    if ( htmlOutput ) {
        render(
            <App />,
            htmlOutput
        );
    }
});
```

Apart from the removal of the `subscribe` in `componentDidMount` that we previously used for saving in that guide, and the addition of the button, the code is pretty much copy and paste.

All being well our settings page should now look a little like this:

![Rendering the Setting Fields](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989714094/snzrDtgbY.png)Rendering the Setting Fields

Don’t worry, we will clean up the styles in section 4 of this guide.

### Handle the Save

In the `onClick` handler of the `<button>` component add the following code:

```
<Button
  isPrimary
  isLarge
  onClick={ () => {
    const {
      exampleSelect,
      exampleText,
      exampleText2,
      exampleText3,
      exampleToggle,
    } = this.state;

    const settings = new api.models.Settings( {
      [ 'wholesomecode_wholesome_plugin_example_select' ]: exampleSelect,
      [ 'wholesomecode_wholesome_plugin_example_text' ]: exampleText,
      [ 'wholesomecode_wholesome_plugin_example_text_2' ]: exampleText2,
      [ 'wholesomecode_wholesome_plugin_example_text_3' ]: exampleText3,
      [ 'wholesomecode_wholesome_plugin_example_toggle' ]: exampleToggle ? 'true' : '',
    } );
    settings.save();
  }}
  >
  { __( 'Save', 'wholesome-plugin' ) }
</Button>
```

This will save our options and settings when the button is clicked. However, there is no indication that the options have saved by default.

### Create the Notification

To give our user some feedback that the options and settings have been saved, lets implement a ‘snackbar’ notification. This is the same notification used in the main Post Editor screen that the block editor uses when the post has been saved.

In order to add this we need to port a core Gutenberg component into our build, as the notifications list is not available using the usual import statements.

We will need to add the following code into the file:

```
import { SnackbarList } from '@wordpress/components';

import {
    dispatch,
    useDispatch,
    useSelect,
} from '@wordpress/data';

import { store as noticesStore } from '@wordpress/notices';

const Notices = () => {
	const notices = useSelect(
		( select ) =>
			select( noticesStore )
				.getNotices()
				.filter( ( notice ) => notice.type === 'snackbar' ),
		[]
	);
	const { removeNotice } = useDispatch( noticesStore );
	return (
		<SnackbarList
			className="edit-site-notices"
			notices={ notices }
			onRemove={ removeNotice }
		/>
	);
};
```

Then in the main render of the `<App>` component add the following before the closing `</Fragment>`:

```
<div className="wholesome-plugin__notices">
  <Notices/>
</div>
```

Finally add the following to the `onClick` handler of the button:

```
dispatch('core/notices').createNotice(
  'success',
  __( 'Settings Saved', 'wholesome-plugin' ),
  {
    type: 'snackbar',
    isDismissible: true,
  }
);
```

This will create a small ‘snackbar’ popup whenever the settings are saved.

![Snackbar Notification in Action](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989718121/LnJV-iPzS.png)Snackbar Notification in Action

I know, I know, we still need to fix those styles.

### The Full `/src/admin.js` File

For reference here is the full `/src/admin.js` file code:

```
import './admin.scss';

import api from '@wordpress/api';

import {
    Button,
    Icon,
    Panel,
    PanelBody,
    PanelRow,
    Placeholder,
    SelectControl,
    SnackbarList,
    Spinner,
    TextControl,
    ToggleControl,
} from '@wordpress/components';

import {
    dispatch,
    useDispatch,
    useSelect,
} from '@wordpress/data';

import {
    Fragment,
    render,
    Component,
} from '@wordpress/element';

import { __ } from '@wordpress/i18n';

import { store as noticesStore } from '@wordpress/notices';

const Notices = () => {
	const notices = useSelect(
		( select ) =>
			select( noticesStore )
				.getNotices()
				.filter( ( notice ) => notice.type === 'snackbar' ),
		[]
	);
	const { removeNotice } = useDispatch( noticesStore );
	return (
		<SnackbarList
			className="edit-site-notices"
			notices={ notices }
			onRemove={ removeNotice }
		/>
	);
};

class App extends Component {
    constructor() {
		super( ...arguments );

		this.state = {
			exampleSelect: '',
			exampleText: '',
			exampleText2: '',
			exampleText3: '',
			exampleToggle: false,
			isAPILoaded: false,
		};
	}

	componentDidMount() {

		api.loadPromise.then( () => {
			this.settings = new api.models.Settings();

			const { isAPILoaded } = this.state;

			if ( isAPILoaded === false ) {
				this.settings.fetch().then( ( response ) => {
					this.setState( {
						exampleSelect: response[ 'wholesomecode_wholesome_plugin_example_select' ],
						exampleText: response[ 'wholesomecode_wholesome_plugin_example_text' ],
						exampleText2: response[ 'wholesomecode_wholesome_plugin_example_text_2' ],
						exampleText3: response[ 'wholesomecode_wholesome_plugin_example_text_3' ],
						exampleToggle: Boolean( response[ 'wholesomecode_wholesome_plugin_example_toggle' ] ),
						isAPILoaded: true,
					} );
				} );
			}
		} );
	}

	render() {
		const {
			exampleSelect,
			exampleText,
			exampleText2,
			exampleText3,
			exampleToggle,
			isAPILoaded,
		} = this.state;

		if ( ! isAPILoaded ) {
			return (
				<Placeholder>
					<Spinner />
				</Placeholder>
			);
		}

		return (
            <Fragment>
                <div className="wholesome-plugin__header">
                    <div className="wholesome-plugin__container">
                        <div className="wholesome-plugin__title">
                            <h1>{ __( 'Wholesome Plugin Settings', 'wholesome-plugin' ) } <Icon icon="admin-plugins" /></h1>
                        </div>
                    </div>
                </div>

                <div className="wholesome-plugin__main">
                    <Panel>
                        <PanelBody
                            title={ __( 'Panel Body One', 'wholesome-plugin' ) }
                            icon="admin-plugins"
                        >
                            <SelectControl
                                help={ __( 'An example dropdown field.', 'wholesome-plugin' ) }
                                label={ __( 'Example Select', 'wholesome-plugin' ) }
                                onChange={ ( exampleSelect ) => this.setState( { exampleSelect } ) }
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
                        </PanelBody>
                        <PanelBody
                            title={ __( 'Panel Body Two', 'wholesome-plugin' ) }
                            icon="admin-plugins"
                        >
                            <TextControl
                                help={ __( 'This is an example text field.', 'wholesome-plugin' ) }
                                label={ __( 'Example Text', 'wholesome-plugin' ) }
                                onChange={ ( exampleText ) => this.setState( { exampleText } ) }
                                value={ exampleText }
                            />

                        </PanelBody>
                        <PanelBody
                            title={ __( 'Panel Body Three', 'wholesome-plugin' ) }
                            icon="admin-plugins"
                        >
                            <PanelRow>
                                <TextControl
                                    help={ __( 'Use PanelRow to place controls inline.', 'wholesome-plugin' ) }
                                    label={ __( 'Example Text 2', 'wholesome-plugin' ) }
                                    onChange={ ( exampleText2 ) => this.setState( { exampleText2 } ) }
                                    value={ exampleText2 }
                                />
                                <TextControl
                                    help={ __( 'This control is inline.', 'wholesome-plugin' ) }
                                    label={ __( 'Example Text 3', 'wholesome-plugin' ) }
                                    onChange={ ( exampleText3 ) => this.setState( { exampleText3 } ) }
                                    value={ exampleText3 }
                                />
                            </PanelRow>
                        </PanelBody>
                        <PanelBody
                            title={ __( 'Panel Body Four', 'wholesome-plugin' ) }
                            icon="admin-plugins"
                        >
                            <ToggleControl
                                checked={ exampleToggle }
                                help={ __( 'An example toggle.', 'wholesome-plugin' ) }
                                label={ __( 'Example Toggle', 'wholesome-plugin' ) }
                                onChange={ ( exampleToggle ) => this.setState( { exampleToggle } ) }
                            />
                        </PanelBody>
                        <Button
                            isPrimary
                            isLarge
                            onClick={ () => {
                                const {
                                    exampleSelect,
                                    exampleText,
                                    exampleText2,
                                    exampleText3,
                                    exampleToggle,
                                } = this.state;

                                const settings = new api.models.Settings( {
                                    [ 'wholesomecode_wholesome_plugin_example_select' ]: exampleSelect,
                                    [ 'wholesomecode_wholesome_plugin_example_text' ]: exampleText,
                                    [ 'wholesomecode_wholesome_plugin_example_text_2' ]: exampleText2,
                                    [ 'wholesomecode_wholesome_plugin_example_text_3' ]: exampleText3,
                                    [ 'wholesomecode_wholesome_plugin_example_toggle' ]: exampleToggle ? 'true' : '',
                                } );
                                settings.save();

                                dispatch('core/notices').createNotice(
                                    'success',
                                    __( 'Settings Saved', 'wholesome-plugin' ),
                                    {
                                        type: 'snackbar',
                                        isDismissible: true,
                                    }
                                );
                            }}
                        >
                            { __( 'Save', 'wholesome-plugin' ) }
                        </Button>
                    </Panel>
                </div>

                <div className="wholesome-plugin__notices">
                    <Notices/>
                </div>

            </Fragment>
		)
	}
}

document.addEventListener( 'DOMContentLoaded', () => {
    const htmlOutput = document.getElementById( 'wholesome-plugin-settings' );

    if ( htmlOutput ) {
        render(
            <App />,
            htmlOutput
        );
    }
});
```

Add the SCSS
-----------------

Time to fix those styles. Just add the following into the `/src/admin.scss` file (which you would have created in the [Add Entry Points to the Create Block Script](https://wholesomecode.ltd/guides/entry-points-wordpress-create-block-script/) guide.

```
#wholesome-plugin-settings {

	.components-placeholder {
		background: #f1f1f1;
	}

	.wholesome-plugin__header {
		background-color: #ffffff;
		box-shadow: 0 1px 0 rgba(213, 213, 213, .5), 0 1px 2px #eeeeee;
		margin-left: -2em;
		padding: 20px 10px;

		.wholesome-plugin__container {
			margin: 0 auto;
			max-width: 750px;

			.wholesome-plugin__title {
				align-items: center;
				display: flex;
				justify-content: center;

                .dashicon {
                    color: #757575;
                }
			}
		}
	}

	.wholesome-plugin__main {
		margin-left: auto;
		margin-right: auto;
		max-width: 750px;

        .components-panel {
            background: none;
            border: none;
        }

		.components-panel__body {
            background: #ffffff;
			border: 1px solid #e2e4e7;
			margin: 1rem 0;
        }
	}

    .components-base-control__help {
        margin-top: .5rem;
    }

    .components-panel__row {
        > div {
            flex-grow: 1;
            margin-right: 1rem;

            &:last-of-type {
                margin-right: 0;
            }
        }
    }

    .wholesome-plugin__notices {
        .components-snackbar {
            bottom: .5rem;
            position: fixed;
        }
    }
}
```

Viewing the Settings Page
------------------------------

Here is the final result:

![The Settings Page](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989726783/VlZXz2YD2.gif)The Settings Page

Extra: Adding a Settings Link to the Plugin Page
--------------------------------------------------

Some plugins have a ‘settings’ link in the plugins page like so:

![Settings Link on Plugin Settings Page Panel](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989731143/amCY15u50.png)Settings Link on Plugin Settings Page Panel

To achieve this add the following block of code to the root of your plugin file (in this case `wholesome-plugin.php`):

```
function wholesomecode_wholesome_plugin_settings_link( $links ) : array {
	$label = esc_html__( 'Settings', 'wholesome-plugin' );
	$slug  = 'wholesome_plugin_settings';

	array_unshift( $links, "<a href='options-general.php?page=$slug'>$label</a>" );

	return $links;
}
add_action( 'plugin_action_links_' . plugin_basename( __FILE__ ), 'wholesomecode_wholesome_plugin_settings_link', 10 );
```

Extra: Taking it Further
--------------------------

*   Have a look at creating [nested child blocks with the `InnerBlocks` component](https://wholesomecode.ltd/guides/template-innerblocks-wordpress-gutenberg/)
*   Have a look at [using post meta fields in Gutenberg blocks](https://wholesomecode.ltd/guides/post-meta-fields-store-attributes-wordpress-gutenberg/)
*   Have a look at [creating a customizer panel with Gutenberg components](https://wholesomecode.ltd/guides/create-customizer-panel-wordpress-gutenberg-components/)