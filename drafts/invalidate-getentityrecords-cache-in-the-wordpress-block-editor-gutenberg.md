---

title: Invalidate getEntityRecords Cache in the WordPress Block Editor (Gutenberg)
post_date: 2022-07-28T08:52:55.682Z
post_status: publish

---

If you have used the Gutenberg API to include post data in your blocks, say with getEntityRecords, and you have some level of interactivity that changes the block, you may find that the block will not update do to the cache.

To clear the cache use this technique:

```
wp.data.dispatch( 'core' ).receiveEntityRecords( 'postType', 'post', [], {}, true );
```

The fifth parameter will ensure the cache is invalidated, and the calls `getEntityRecords`  within your block will be reloaded. 