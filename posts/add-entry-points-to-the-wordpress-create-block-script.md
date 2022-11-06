---

title: Add Entry Points to the WordPress Create Block Script
post_date: 2021-03-09T07:32:55.000Z
post_status: publish

---

When you first create a block with the WordPress create block script using `npm init @wordpress/block` it provides you with an entry point to `/src/index.js`, that you can use to compile all of your block based assets and styles.

Sometimes you want to add other entry points, for example, you may want to use WordPress Block Editor (Gutenberg) blocks elsewhere in the editor, for example [on a settings page](https://wholesomecode.ltd/guides/create-settings-page-wordpress-gutenberg-components/), or in the customizer.

This guide helps you extend the WordPress Create Block Script `webpack.config` so you can add those additional entry points.

Prerequisites
------------------

*   Be familiar with [creating plugins for WordPress Gutenberg with the create block script](https://wholesomecode.ltd/guides/creating-plugin-wordpress-gutenberg/)

Create Your Files
--------------------------

Following on from the [create block script guide](https://wholesomecode.ltd/guides/creating-plugin-wordpress-gutenberg/), we are going to create a few files in our solution:

### Create `/src/admin.js`

This file will be the main entry point for any React we wish to add to the WordPress admin screen (for example, if we wanted to [build an options page to control our settings with Gutenberg Components](https://wholesomecode.ltd/guides/create-settings-page-wordpress-gutenberg-components/).  
  
Create the file `/src/admin.js` and paste in the following code:

```
import { __ } from '@wordpress/i18n';
import api from '@wordpress/api';

import './admin.scss';
```

We are not really doing anything in this file just yet, that will be the subject of later guides. For now we are just putting in some code so we can test the output.

It does however import the `/src/admin.scss` file, which will compile to contain all of our custom CSS for the WordPress admin.

### Create `/src/admin.scss`

Create the file `/src/admin.scss` and add some test SCSS:

```
body {
  background: pink;
}
```

Again, this code serves no use, other than to test if the SCSS gets compiled.

### Create `/webpack.config.js`

Create a file in the root of your plugin called `webpack.config.js` we will cover what to put in this file shortly.

Edit the Root PHP File of the Plugin
-----------------------------------------

If you have been following along with the previous guide, that plugin will be called `wholesome-plugin.php`.

In this file we want to register the scripts for the admin area, which will be output by our webpack configuration. Add the following function to the bottom of that file:

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
	wp_register_script(
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

We have pretty much copied the code from the previous enqueue block, but made it relevant to our expected outputted files of `/build/admin.js` and `/build/admin.css`. Note that we are also loading the specially generated `/build/admin.asset.php` file, that will automatically add all of the WordPress block editor (Gutenberg) dependancies for our admin script.

Edit the `webPack.config`
------------------------------

In step 2 we created the file `webpack.config.js`, in this file add the following code:

```
const defaultConfig = require( '@wordpress/scripts/config/webpack.config' );
const path = require( 'path' );

module.exports = {
    ...defaultConfig,
    entry: {
        ...defaultConfig.entry,
        admin: path.resolve( process.cwd(), 'src', 'admin.js' ),
    }
};
```

This code imports the `webpack.config` from `@wordpress/scripts`. This is the code that runs every-time you run `npm start` in your solution.

All we are doing is taking this config, and adding in another entry point, with the line `admin: path.resolve( process.cwd(), 'src', 'admin.js' ),`.

The rest of the code is just telling webpack to use the settings that are already in place (eg `...defautConfig`, `...defaultConfig.entry`).

Compile the Code
---------------------

Compile you’re plugin with the `npm start` command, and let’s look at what happens:

![Compiling the Code with the New Entry Point](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989746872/cBKwNEwr3.gif)Compiling the Code with the New Entry Point

The script now generates some new files for us:

* `/build/admin.assets.php` — This PHP file is autogenerated when the `/src` folder is compiled. It contains an array of all the WordPress Editor (Gutenberg) JavaScript decencies used by your new entry point. It is enqueued via the main `wholesome-plugin.php` loader file via the additional function we put in place.

* `/build/admin.css` — This is the admin CSS file, and is enqueued via the main `wholesome-plugin.php` loader file via the additional function we put in place.

* `/build/admin.js` — This is the main compiled JavaScript file, and is enqueued via the main `wholesome-plugin.php` loader file via the additional function we put in place.

* `/build/admin.css.map` — This file is only generated when you are compiling the assets for development mode (IE you run `npm start` and not `npm build` when you are compiling your assets. It is a helpful file for debuggers to help identify file names and line numbers in the CSS if errors occur.

* `/build/admin.js.map` — This file is only generated when you are compiling the assets for development mode (IE you run `npm start` and not `npm build` when you are compiling your assets. It is a helpful file for JavaScript debuggers to help identify file names and line numbers if errors occur.

Extra: Taking it Further
--------------------------

*   Look at using [post meta fields in Gutenberg blocks](https://wholesomecode.ltd/guides/post-meta-fields-store-attributes-wordpress-gutenberg/)
*   Look at using [options and settings in Gutenberg blocks](https://wholesomecode.ltd/guides/options-settings-data-wordpress-gutenberg/)
*   Look at creating [custom meta boxes in Gutenberg](https://wholesomecode.ltd/guides/custom-meta-boxes-wordpress-gutenberg/)
*   Look at building an [options and settings page using Gutenberg components](https://wholesomecode.ltd/guides/create-settings-page-wordpress-gutenberg-components/)