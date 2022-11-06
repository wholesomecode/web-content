---

title: get_post_meta WordPress Block Editor (Gutenberg) Equivalent is getEditedPostAttribute(‘meta’)
post_date: 2021-02-07T19:39:08.000Z
post_status: publish

---

If you are new to writing plugins for the WordPress Block Editor (Gutenberg), you may be searching around for how to get the meta of the current post with `get_post_meta`.

Never fear, you can still `get_post_meta`, just use `getEditedPostAttribute('meta')` on the current post to get all the meta, like so:

```
const meta = wp.data.select('core/editor').getEditedPostAttribute('meta');
```

As long as you have [made your meta key available in the rest API](https://wholesomecode.ltd/#register-your-meta-key) then you can now grab it from the `meta` variable.

```
const meta = wp.data.select('core/editor').getEditedPostAttribute('meta');

const metaExample = meta['wholesomecode_meta_key_example'];
```

In the above code I grab the value of my post meta (`wholesomecode_meta_key_example`) and pass it into the variable `metaExample`.

Register your Meta Key
----------------------

To ensure your post meta is available in Gutenberg you need to register it and ensure that `show_in_rest` is set to `true`.

```
add_action( 'init', 'register_meta');

function register_meta() {
    register_post_meta(
        'post',
        'wholesomecode_meta_key_example',
        [
            'show_in_rest' => true,
            'single'       => true,
            'type'         => 'string',
        ]
    );
}
```

Get the Meta from a different Post ID
-------------------------------------

If you want to get the post meta of another post, you can use `getEntityRecord`, and pass it the Post ID of the post you want to get:

```
const {meta} = wp.data.select('core').getEntityRecord('postType', 'post', 42);
```

In the example above all the meta for the post ([provided it has been registered](https://wholesomecode.ltd/#register-your-meta-key)) is now located in the `meta` variable.

Using `getEntityRecord` is the equivalent of doing a get\_post in Gutenberg.

Try it in the Console
---------------------

Log into your site, and use the `wp` object in the console whilst editing a post to try it for yourself. You should get an easy to work with JSON object of the post meta:

```
wp.data.select('core/editor').getEditedPostAttribute('meta');
{
   wholesomecode_meta_key_example: 'Example',
}
```

Updating Post Meta
------------------

If you want to [update post meta from within WordPress Gutenberg, you can do this with `editPost` and `editEntityRecord`](https://wholesomecode.ltd/blog/update_post_meta-gutenberg-equivalent-editpost/).