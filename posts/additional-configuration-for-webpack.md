---

title: Additional Configuration for  webpack
post_date: 2020-02-23T14:41:37.000Z
post_status: publish

---

This article builds on top of the `webpack.config.js` file we created in the article [Extending the WordPress Create Block Script webpack Config](https://wholesomecode.ltd/articles/extending-the-wordpress-create-block-script-webpack-config/). If you have not read that article, you may want to give it a quick read.

**This article is for an older version of the Create Block Script**

*Although the theory can still be applied, some of the information in this article may no longer be compatible with the latest create block script. You can [view our updated Create Block Script Guide here](https://wholesomecode.ltd/guides/creating-plugin-wordpress-gutenberg/).  *
  
We also have an updated guide about [adding additional entry points to webpack](https://wholesomecode.ltd/guides/entry-points-wordpress-create-block-script/).

Supporting CSS Cross Browser with Polyfills and Prefixes
--------------------------------------------------------

Before we jump in, I want to give credit to [Jeffrey Carandang](https://jeffreycarandang.com/) on his awesome [article on using PostCSS with webpack](https://jeffreycarandang.com/create-gutenberg-block-plugin-wp-scripts-postcss-build/), for which I took inspiration for this section of the article.

To support polyfills and Cross-Browser prefixes we are going to use the `postcss-loader` npm package to load the plugin `[postcss-preset-env](https://www.npmjs.com/package/postcss-preset-env)`.

Ensuring we are `cd`‘ed into the root of our plugin run the following to install these two packages.

```
npm install postcss-loader postcss-preset-env --save-dev
```

Next we need to extend our `/webpack.config.js` to include the configuration for these two packages:

```
const defaultConfig = require( '@wordpress/scripts/config/webpack.config' );
const IgnoreEmitWebPackPlugin = require( 'ignore-emit-webpack-plugin' );
const MiniCssExtractPlugin = require( 'mini-css-extract-plugin' );
const postcssPresetEnv = require( 'postcss-preset-env' );
const production = 'development' !== process.env.NODE_ENV;

module.exports = {
    ...defaultConfig,
    module: {
        ...defaultConfig.module,
        rules: [
            ...defaultConfig.module.rules,
            {
                test: /\.s[ac]ss$/i,
                use: [
                    { loader: MiniCssExtractPlugin.loader },
                    { loader: 'css-loader' },
                    {
						loader: 'postcss-loader',
						options: {
							plugins: () => [ postcssPresetEnv( { stage: 3 } ) ],
						},
					},
                    { loader: 'sass-loader' },
                ],
            },
        ],
    },
    optimization: {
		...defaultConfig.optimization,
		splitChunks: {
			cacheGroups: {
				editor: {
                    chunks: 'all',
                    enforce: true,
					name: 'editor',
					test: /editor\.s[ac]ss$/i,
				},
				style: {
                    chunks: 'all',
                    enforce: true,
					name: 'style',
					test: /style\.s[ac]ss$/i,
				},
				default: false,
			},
		},
	},
    plugins: [
        ...defaultConfig.plugins,
        new MiniCssExtractPlugin( {
            filename: '../[name].css',
        } ),
        new IgnoreEmitWebPackPlugin( [ 'editor.js', 'style.js' ] ),
    ],
};

if ( production ) {
    module.exports.devtool = false;
}
```

Note the modifications on lines 4 and 18.

### Testing the Autoprefixer

Let’s add some code to our `/src/sass/style.scss` file to test the autoprefixer function of the PostCSS plugin. I will insert some rules for `::placeholder` into the file, which does not yet have full browser support.

```
::placeholder {
	color: #f00;
}
```

In our compiled `/style.css` file the following is now output:

```
::-webkit-input-placeholder {
  color: #f00; }

::-moz-placeholder {
  color: #f00; }

:-ms-input-placeholder {
  color: #f00; }

::-ms-input-placeholder {
  color: #f00; }

::placeholder {
  color: #f00; }
```

### Testing the Polyfills

Let’s add some code to our `/src/sass/style.scss` file to test the polyfill function of the PostCSS plugin. I will insert some rules for the `all` property (which resets an elements properties) into the file, which does not yet have full browser support.

```
a {
	all: initial;
}
```

In our compiled `/style.css` file the following is now output:

```
a {
  -webkit-animation: none 0s ease 0s 1 normal none running;
          animation: none 0s ease 0s 1 normal none running;
  -webkit-backface-visibility: visible;
          backface-visibility: visible;
  background: transparent none repeat 0 0 / auto auto padding-box border-box scroll;
  border: medium none currentColor;
  border-collapse: separate;
  -o-border-image: none;
     border-image: none;
  border-radius: 0;
  border-spacing: 0;
  bottom: auto;
  box-shadow: none;
  box-sizing: content-box;
  caption-side: top;
  clear: none;
  clip: auto;
  color: #000;
  -webkit-columns: auto;
     -moz-columns: auto;
          columns: auto;
  -webkit-column-count: auto;
     -moz-column-count: auto;
          column-count: auto;
  -webkit-column-fill: balance;
     -moz-column-fill: balance;
          column-fill: balance;
  grid-column-gap: normal;
  -webkit-column-gap: normal;
     -moz-column-gap: normal;
          column-gap: normal;
  -webkit-column-rule: medium none currentColor;
     -moz-column-rule: medium none currentColor;
          column-rule: medium none currentColor;
  -webkit-column-span: 1;
     -moz-column-span: 1;
          column-span: 1;
  -webkit-column-width: auto;
     -moz-column-width: auto;
          column-width: auto;
  content: normal;
  counter-increment: none;
  counter-reset: none;
  cursor: auto;
  direction: ltr;
  display: inline;
  empty-cells: show;
  float: none;
  font-family: serif;
  font-size: medium;
  font-style: normal;
  font-feature-settings: normal;
  font-variant: normal;
  font-weight: normal;
  font-stretch: normal;
  line-height: normal;
  height: auto;
  -webkit-hyphens: none;
      -ms-hyphens: none;
          hyphens: none;
  left: auto;
  letter-spacing: normal;
  list-style: disc outside none;
  margin: 0;
  max-height: none;
  max-width: none;
  min-height: 0;
  min-width: 0;
  opacity: 1;
  orphans: 2;
  outline: medium none invert;
  overflow: visible;
  overflow-x: visible;
  overflow-y: visible;
  padding: 0;
  page-break-after: auto;
  page-break-before: auto;
  page-break-inside: auto;
  perspective: none;
  perspective-origin: 50% 50%;
  position: static;
  right: auto;
  -moz-tab-size: 8;
    -o-tab-size: 8;
       tab-size: 8;
  table-layout: auto;
  text-align: left;
  -moz-text-align-last: auto;
       text-align-last: auto;
  text-decoration: none;
  text-indent: 0;
  text-shadow: none;
  text-transform: none;
  top: auto;
  transform: none;
  transform-origin: 50% 50% 0;
  transform-style: flat;
  transition: none 0s ease 0s;
  unicode-bidi: normal;
  vertical-align: baseline;
  visibility: visible;
  white-space: normal;
  widows: 2;
  width: auto;
  word-spacing: normal;
  z-index: auto;
  all: initial; }
```

Now that is a lot of CSS!

Minifying the Output
--------------------

We can load our JavaScript and CSS assets quicker by making sure that they are minified. To do this we can add in a couple of extra plugins into our build.

### Minifying CSS with webpack

To minify CSS we are going to use the [`optimize-css-assets-webpack-plugin` npm package](https://www.npmjs.com/package/optimize-css-assets-webpack-plugin).

Ensure that you are `cd`‘ed into the root of your plugin and run the following command in your terminal:

```
npm install optimize-css-assets-webpack-plugin --save-dev
```

Now make the following alterations to your `/webpack.config.js` file:

```
const defaultConfig = require( '@wordpress/scripts/config/webpack.config' );
const IgnoreEmitWebPackPlugin = require( 'ignore-emit-webpack-plugin' );
const MiniCssExtractPlugin = require( 'mini-css-extract-plugin' );
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
const postcssPresetEnv = require( 'postcss-preset-env' );
const production = 'development' !== process.env.NODE_ENV;

module.exports = {
    ...defaultConfig,
    module: {
        ...defaultConfig.module,
        rules: [
            ...defaultConfig.module.rules,
            {
                test: /\.s[ac]ss$/i,
                use: [
                    { loader: MiniCssExtractPlugin.loader },
                    { loader: 'css-loader' },
                    {
						loader: 'postcss-loader',
						options: {
							plugins: () => [ postcssPresetEnv( { stage: 3 } ) ],
						},
					},
                    { loader: 'sass-loader' },
                ],
            },
        ],
    },
    optimization: {
        ...defaultConfig.optimization,
        minimize: true,
        minimizer: [ new OptimizeCssAssetsWebpackPlugin() ],
		splitChunks: {
			cacheGroups: {
              	default: false,
				editor: {
                    chunks: 'all',
					name: 'editor',
					test: /editor\.s[ac]ss$/i,
				},
				style: {
                    chunks: 'all',                   
					name: 'style',
					test: /style\.s[ac]ss$/i,
				},
			},
		},
	},
    plugins: [
        ...defaultConfig.plugins,
        new IgnoreEmitWebPackPlugin( [ 'editor.js', 'style.js' ] ),
        new MiniCssExtractPlugin( {
            filename: '../[name].css',
        } ),
    ],
};

if ( production ) {
    module.exports.devtool = false;
}
```

Note the import on line 4, us telling webpack to minimize on line 32, and the plugin being loaded on line 33.

Now when we run `npm start` we can examine one of our CSS files in the root of the plugin to see the output. Here is what the `/style.css` now looks like:

```
.wp-block-wcltd-wholesome-notes{background-color:#000;color:#fff;padding:2px}
```

It has removed all of the comments, spaces and put everything on one line, which has reduced the size of the file.

### Minifying JavaScript with webpack

If we take a look at the `/build/index.js` file, it is quite lengthy. This file could also do with some minification.

To do this we are going to use the [`terser-webpack-plugin` npm package](https://www.npmjs.com/package/terser-webpack-plugin).

Ensure that you are `cd`‘ed into the root of your plugin and run the following command in your terminal:

```
npm install terser-webpack-plugin  --save-dev
```

Now make the following alterations to your `/webpack.config.js` file:

```
const defaultConfig = require( '@wordpress/scripts/config/webpack.config' );
const IgnoreEmitWebPackPlugin = require( 'ignore-emit-webpack-plugin' );
const MiniCssExtractPlugin = require( 'mini-css-extract-plugin' );
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
const postcssPresetEnv = require( 'postcss-preset-env' );
const production = 'development' !== process.env.NODE_ENV;
const TerserWebPackPlugin = require( 'terser-webpack-plugin' );

module.exports = {
    ...defaultConfig,
    module: {
        ...defaultConfig.module,
        rules: [
            ...defaultConfig.module.rules,
            {
                test: /\.s[ac]ss$/i,
                use: [
                    { loader: MiniCssExtractPlugin.loader },
                    { loader: 'css-loader' },
                    {
						loader: 'postcss-loader',
						options: {
							plugins: () => [ postcssPresetEnv( { stage: 3 } ) ],
						},
					},
                    { loader: 'sass-loader' },
                ],
            },
        ],
    },
    optimization: {
        ...defaultConfig.optimization,
        minimize: true,
        minimizer: [ 
            new OptimizeCssAssetsWebpackPlugin(),
            new TerserWebPackPlugin(),
        ],
		splitChunks: {
			cacheGroups: {
                default: false,
				editor: {
                    chunks: 'all',
					name: 'editor',
					test: /editor\.s[ac]ss$/i,
				},
				style: {
                    chunks: 'all',
					name: 'style',
					test: /style\.s[ac]ss$/i,
				},
			},
		},
	},
    plugins: [
        ...defaultConfig.plugins,
        new IgnoreEmitWebPackPlugin( [ 'editor.js', 'style.js' ] ),
        new MiniCssExtractPlugin( {
            filename: '../[name].css',
        } ),
    ],
};

if ( production ) {
    module.exports.devtool = false;
}
```

Note the import on line 7, and the addition to the minimizer on line 36.

Now if we inspect our `/build/index.js` file, the JavaScript has been minified.

![Minifying the JavaScript](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990108460/3dgZC0gXA4.png)Minifying the JavaScript

### Get the Prefix and Minification Gist

You can [download the polyfill and minification `/webpack.config.js` file and the accompanying `/package.json` files from the GitHub gist](https://gist.github.com/mattwatsoncodes/b7cbae62ce645a9c6fa18ff136ea7775).

Bonus Extras
------------

The finished configuration also contains a few bonus extras. Here is a quick overview of each.

### Automatically Lint and Tidy your CSS

You can format, autofix and tidy up your Sass as you go, to do this install the following packages:

```
npm install stylelint stylelint-config-standard stylelint-order stylelint-webpack-plugin --save-dev
```

Also save the following file into your config with the name `.stylelintrc`. This inherits the defaults provided by `stylelint-config-standard`, and adds a few bonus extras, including alphabetical ordering.

You can configure this file to your preferences by using the guidance in the [Styleline documentation](https://stylelint.io/).

```
{
	"extends": "stylelint-config-standard",
	"plugins": [
		"stylelint-order"
	],
	"rules": {
		"color-hex-length": "long",
		"font-family-name-quotes": null,
		"function-url-quotes": "always",
		"indentation": "tab",
		"max-empty-lines": 2,
		"number-leading-zero": "never",
		"order/order": [
			"custom-properties",
			"declarations"
		],
		"order/properties-alphabetical-order": true,
		"string-quotes": "single",
	}
}
```

Now in your `/src/editor.scss` file make your Sass a little messy, and add in a couple of test rules, like so:

```
.wp-block-wcltd-wholesome-notes{
  border: 1px dotted #f00; background: url("https://via.placeholder.com/150");}
```

Now when you are running `npm start` hit save, it will be automatically tidied and alphabetised like so:

```
.wp-block-wcltd-wholesome-notes {
	background: url('https://via.placeholder.com/150');
	border: 1px dotted #ff0000;
}
```

Note that some of the rules I added in my `.stylelintrc` file have been implemented, such as:

*   Using full hex values for colours.
*   Alphabetising of properties.
*   Single quotes for Strings.
*   Enforcing quotes for URLs

### Get Friendly Errors from webpack

You can make your webpack errors much easier to read by installing the npm package `friendly-errors-webpack-plugin`.

Install the plugin in the usual way with your terminal:

```
npm install friendly-errors-webpack-plugin --save-dev
```

Now, if you have an error in your JavaScript, the output will be much more friendlier than before.

Before the plugin:

![Before friendly-errors-webpack-plugin](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990112964/vW48H0Vm0.png)Before `friendly-errors-webpack-plugin`

After the plugin:

![After friendly-errors-webpack-plugin](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990116790/Tn36r4Vna.png)After `friendly-errors-webpack-plugin`