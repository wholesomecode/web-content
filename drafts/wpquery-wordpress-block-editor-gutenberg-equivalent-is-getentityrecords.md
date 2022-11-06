---

title: WP_Query WordPress Block Editor (Gutenberg) Equivalent is getEntityRecords
post_date: 2021-02-05T23:54:00.000Z
post_status: publish

---

If you are new to writing plugins for the WordPress Block Editor (Gutenberg), you may be a little confused how to do all the things you did regularly, like grabbing posts with WP\_Query.

You will be very pleased to know that you can pretty much still do everything you used to do with WP\_Query by using `getEntityRecords`.

For example, we can grab the latest posts by using the following bit of code:

wp.data.select( 'core' ).getEntityRecords( 'postType', 'posts' );

You can also query Custom Post Types (CPT) in the same way, as long as you have [made your CPT accessible via the REST API](https://developer.wordpress.org/rest-api/extending-the-rest-api/adding-rest-api-support-for-custom-content-types/#registering-a-custom-post-type-with-rest-api-support).

wp.data.select( 'core' ).getEntityRecords( 'postType', 'my\_custom\_post\_type' );

We can even pass in some arguments that may look _sort of_ familiar:

```
wp.data.select( 'core' ).getEntityRecords(
	'postType', 
	'post', 
	{
        'status': 'draft', 
		'per_page': 2, 
	} 
);
````

You can find the definitive list of the arguments you can pass into the function here in the [Gutenberg developer guide](https://developer.wordpress.org/rest-api/reference/posts/#arguments).

Try it in the Console
---------------------

If you are logged into a site, and you are viewing the front end, you can test out pretty much any Gutenberg command. WordPress stores them all in the handy `wp` object.

Typing the above command into the console on one of my development sites I get the following:

![Using getEntityRecords](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989972984/W4DmEwrR2.png)Using getEntityRecords to grab posts in the Console

The screenshot above shows the console returning two WordPress post objects in the JavaScript, which are draft posts (as per the parameters I passed into `getEntityRecords`).

The JavaScript Post Object
--------------------------

Exploring the post object further in the console you can see there are plenty of properties returned for you to access.

```
    id: 42
    date: "2020-03-21T14:02:00"
    date_gmt: "2020-03-21T14:02:00"
    guid: {
    	rendered: "https://wholesomecode.ltd/?p=42"
    	raw: "https://wholesomecode.ltd/?p=42"
    }
    modified: "2020-03-21T14:02:00"
    modified_gmt: "2020-03-21T14:02:00"
    password: ""
    slug: "wp-query-and-the-wordpress-block-editor-gutenberg"
    status: "draft"
    type: "post"
    link: "https://wholesomecode.ltd/?p=42"
    title: {
    	raw: "WP_Query and the WordPress Block Editor (Gutenberg)"
    	rendered: "WP_Query and the WordPress Block Editor (Gutenberg)"
    }
    content: {
    	raw: "Content goes here."	
    	rendered: "<p>Content goes here.</p>↵"
    	protected: false
    	block_version: 0
    }
    excerpt: {
    	raw: ""
    	rendered: "<p>Content goes here.</p>↵"
    	protected: false
    }
    author: 1
    featured_media: 101
    comment_status: "open"
    ping_status: "open"
    sticky: false
    template: ""
    format: "standard"
    meta: {
    	_wcltd_meta_example_1: "1" 		
    	_wcltd_meta_example_2: "Hello"
    }
    categories: [1]
    tags: []
    country: []
    region: []
    city: []
    permalink_template: "https://wholesomecode.ltd/%postname%/"
    generated_slug: "wp-query-and-the-wordpress-block-editor-gutenberg"
```

Post Meta in Gutenberg
----------------------

One thing you will notice from the post object we explored above, is that all of the post meta is contained within the object in the `meta` property. This saves us doing the equivalent of `get_post_meta()` for this object in Gutenberg.

However if you do want to get the post meta for a single post object [see the `get_post_meta` equivalent in Gutenberg article](https://wholesomecode.ltd/blog/get_post_meta-wordpress-block-editor-gutenberg-equivalent-is-geteditedpostattributemeta/?preview_id=3880&preview_nonce=46ba9ffabf&preview=true).

Getting the Image Details in Gutemberg
--------------------------------------

The post object you can get the featured media ID, which is stored in the `featured_media` property.

You can use `getMedia` (the [WordPress Gutenberg equivalent of `wp_get_attachment_image`](https://wholesomecode.ltd/blog/wp_get_attachment_image-gutenberg-equivalent-getmeta/)) to get the properties for this attachment.

The Gutenberg Post Loop
-----------------------

Lets expand the [block created by the `@wordpress/create-block` script, that I wrote about previously](https://wholesomecode.ltd/articles/an-overview-of-the-wordpress-create-block-script/) to include an example of how to make the equivalent of a WP\_Query loop in Gutenberg.

First we need to import the `select()` function from wp-data. We can do this by either referencing the NPM module:

`import { select } from '@wordpress/data;`

Or by using the `wp` object:

`const { select } = wp.data;`

We are going to stick with the practices used in the created block and use the NPM module, and use the `@wordpress/data` method.

Here is a basic implementation of the equivalent of a WP\_Query loop in Gutenberg.

**Featured Images**

*Note that featured images must be set for the latest two posts for this example to work correctly.*

```
import { registerBlockType } from '@wordpress/blocks';
import { select, withSelect } from '@wordpress/data';
import { __ } from '@wordpress/i18n';

registerBlockType( 'wholesomecode/wholesome-plugin', {
	title: __( 'Wholesome Plugin', 'wholesomecode' ),
	description: __( 'Just an Example Plugin', 'wholesomecode' ),
	category: 'common',
	icon: 'smiley',

	edit: withSelect( ( select ) => {
		const posts = select( 'core' ).getEntityRecords( 'postType', 'post', { 'per_page': 2 } );
		let media = {};
		posts.forEach( post => {
			media[ post.id ] = select('core').getMedia( post.featured_media );
		});
		return {
			posts,
			media
		};
	} )( props => {

		const { media, posts } = props;

		if ( ! posts || ! media ) {
			return (
				<p>{ __( 'Loading...', 'wholesomecode' ) }</p>
			);
		}

		return (
			<ul>
			{ posts.map(
				( post ) => {
					if ( media[ post.id ] ) {
						const imageThumbnailSrc = media[ post.id ].media_details.sizes.thumbnail.source_url;
					return (
						<li>
							<img src={ imageThumbnailSrc } />
							<a href={ post.link }>
								{ post.title.raw }
							</a>
						</li>
					)
					}
				}
			) }
			</ul>
		);
	} ),

	save() {
		return (
			<p>
				{ __( 'Wholesome Plugin – Nothing to see here.', 'wholesomecode' ) }
			</p>
		);
	},
} );

```

Note the use of `withSelect` to pre-load the data before the block loads. You can [find out more about withSelect by reading the Gutenberg documentation](https://developer.wordpress.org/block-editor/packages/packages-data/#withSelect).

The above code outputs the following (very basic) example of an article list and their associated thumbnails in the editor:

![Example Post Loop in Gutenberg](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989977832/GcYsy9lSg.png)Example Post Loop in Gutenberg.

Using the REST API
------------------

If you would like to get custom data via the REST API check out my post on [using the REST API with the WordPress Block Editor (Gutenberg)](https://wholesomecode.ltd/articles/using-the-wordpress-block-editor-gutenberg-with-the-rest-api/).