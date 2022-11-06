---

title: get_post WordPress Block Editor (Gutenberg) Equivalent is getEntityRecord
post_date: 2021-01-04T19:49:00.000Z
post_status: publish

---

New to writing plugins for the WordPress Block Editor (Gutenberg)? you may be searching around for how to do a `get_post` to get a post by ID.

You can still get a post with `get_post`, just use `getEntityRecord` and pass in the post type and ID.

const post = wp.data.select('core').getEntityRecord('postType','post', 42);

`getEntityRecord` will return a single post object of the post you are querying. [Read more about the JavaScript Post Object in the WP\_Query equivalent article](https://wholesomecode.ltd/wordpress/wp_query-gutenberg-equivalent-getentityrecords/#the-javascript-post-object).

The post object you just returned also contains all of the meta for the post object, so you can also use this method as the [equivalent of `get_post_meta` in Gutenberg](https://wholesomecode.ltd/blog/get_post_meta-gutenberg-equivalent-geteditedpostattributemeta/#get-the-meta-from-a-different-post-id).

Try it in the Console
---------------------

Log into your site, and use the `wp` object in the console whilst editing a post to try it for yourself.

You will get a JSON object of the post to work with.

```
wp.data.select('core').getEntityRecord('postType','post', 42);
{
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
}
```