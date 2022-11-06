---

title: Compiling Sass for Multiple Blocks with the webpack Config
post_date: 2020-02-17T20:47:44.000Z
post_status: publish

---

This article shows an example of how to use the Sass files we setup in the article [Extending the WordPress Create Block Script webpack Config](https://wholesomecode.ltd/articles/extending-the-wordpress-create-block-script-webpack-config/) with multiple blocks within the same plugin. If you have not read that article, please read it first.

**This article is for an older version of the Create Block Script**

*Although the theory can still be applied, some of the information in this article may no longer be compatible with the latest create block script. You can [view our updated Create Block Script Guide here](https://wholesomecode.ltd/guides/creating-plugin-wordpress-gutenberg/).  *
  
We also have an updated guide about [adding additional entry points to webpack](https://wholesomecode.ltd/guides/entry-points-wordpress-create-block-script/) so you can have multiple CSS files generated.

Setting up Multiple Blocks
--------------------------

As I mentioned in the previous article, adding the Sass imports to the index of the plugin allows us to define multiple blocks within the plugin, each with their own `editor.scss` and `style.scss` files.

The webpack config we have setup will take the `editor.scss` and `style.scss` files for each block, and compile it into the `/editor.css` and `/style.css` files at the root of the plugin.

To try this out, lets move the `/src/index.js` and `/src/scss` files and folders into `/src/blocks/block-one`, then copy this block into `/src/blocks/block-two`. Finally create a new file at `/src/index.js`.  
  
You should now have the following file structure:

*   `/src`
    *   `/blocks`
        *   `/block-one`
            
            *   `/scss`
                *   `/editor.scss`
                *   `/style.scss`
            
            *   `/index.js`
        *   `/block-two/`
            *   `/scss`
                *   `/editor.scss`
                *   `/style.scss`
            *   `/index.js`
        *   `/index.js`

Then lets make some minor changes to `/block-two` so that it is registered as a unique block. Simply alter the following line in the `/src/blocks/block-two/index.js` file from:

```
registerBlockType( 'wcltd/wholesome-notes', {
```

to:

```
registerBlockType( 'wcltd/wholesome-notes-2', {
```

and change the name of the file from `Wholesome Notes` to `Wholesome Notes 2`:

```
	title: __(
		'Wholesome Notes 2',
		'wcltd'
	),
```

Altering the Sass
-----------------

In `/src/blocks/block-two/scss/editor.scss` and `/src/blocks/block-two/scss/style.scss` make some minor amendments, so that we will recognise the changes in the compiled files.

Also, let’s not forget to alter the classes in those files to use the new namespace `.wp-block-wcltd-wholesome-notes-2`.

Here are the amendments I made:

`/src/blocks/block-two/scss/editor.scss`

```
.wp-block-wcltd-wholesome-notes-2 {
	border: 1px dotted #0f0;
}
```

`/src/blocks/block-two/scss/style.scss`

```
.wp-block-wcltd-wholesome-notes-2 {
	background-color: #fff;
	color: #000;
	padding: 2px;
}
```

Nothing grand, I just changed a few changed colours.

Setting up the Index File
-------------------------

Now lets edit the file at `/src/blocks/index.js`. We just need to import the two blocks we have created:

```
import './blocks/block-one/index';
import './blocks/block-two/index';
```

Viewing the Results
-------------------

Now let’s run `npm start` and view the results of the outputted files.

The `/editor.css` file looks like this:

```
/**
 * The following styles get applied inside the editor only.
 *
 * Replace them with your own styles or remove the file completely.
 */
.wp-block-wcltd-wholesome-notes {
  border: 1px dotted #f00; }

/**
 * The following styles get applied inside the editor only.
 *
 * Replace them with your own styles or remove the file completely.
 */
.wp-block-wcltd-wholesome-notes-2 {
  border: 1px dotted #0f0; }
```

See how the Sass from the `editor.scss` files of each block within the plugin have been compiled into this single `/editor.css` file.

Now let’s look at the `/style.css` file:

```
/**
 * The following styles get applied both on the front of your site
 * and in the editor.
 *
 * Replace them with your own styles or remove the file completely.
 */
.wp-block-wcltd-wholesome-notes {
  background-color: #000;
  color: #fff;
  padding: 2px; }

/**
 * The following styles get applied both on the front of your site
 * and in the editor.
 *
 * Replace them with your own styles or remove the file completely.
 */
.wp-block-wcltd-wholesome-notes-2 {
  background-color: #fff;
  color: #000;
  padding: 2px; }

```

You can see that the `style.scss` files of each block within the plugin have been compiled into this single `/style.css` file.