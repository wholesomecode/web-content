---

title: update_post_meta WordPress Block Editor (Gutenberg) Equivalent is editPost
post_date: 2021-02-10T20:54:42.000Z
post_status: publish

---

If you are wondering how to `update_post_meta` on the current post with Gutenberg, this is the article for you. You can just use `editPost` like so:

```
wp.data.dispatch( 'core/editor' ).editPost( {
	meta: {
		wholesomecode_meta_key_example: 'Example',
	},
} );
```

The code above will update the post meta `wholesomecode_meta_key_example` with the value `'Example'`.

Remember that you will need to [register your meta key using the `show_in_rest` argument, like I showed you in the `get_post_meta` example](https://wholesomecode.ltd/blog/get_post_meta-gutenberg-equivalent-geteditedpostattributemeta/#register-your-meta-key).

Update the Meta from a different Post ID
----------------------------------------

If you want to update the post meta of a post by its ID, you can use `editEntityRecord` like in the following example:

```
wp.data.dispatch( 'core' ).editEntityRecord( 'postType', 'post', 42, {
	meta: {
		wholesomecode_meta_key_example: 'Example',
	},
} );
```

It works in pretty much the same way, but you have to pass in the post type and the ID.