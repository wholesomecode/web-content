---

title: Hook into the Save Action for the WordPress Block Editor (Gutenberg)
post_date: 2021-03-18T21:21:15.000Z
post_status: publish

---

Have you ever wondered how you can perform an action on save when using Gutenberg? Are you looking for a hook or a filter so you can perform an action when saving a post?

Good news, you can easily hook into the save with Gutenberg with the following code. Just add the following to your `componentDidMount` function in your Gutenberg Component.

```
subscribe( () => {
  const isSavingPost = select('core/editor').isSavingPost();
  const isAutosavingPost = select('core/editor').isAutosavingPost();

  if ( isAutosavingPost && ! isSavingPost ) {
    return;
  }

  // Do action.
});
```

Here you could insert some JavaScript code, or even send some data via AJAX or the REST API.

For this to work, don’t forget to import the `subscribe` and `select` from `@wordpress/data`:

```
import { select, subscribe } from '@wordpress/data';
```

To see an example of this code in action take a look at my guide on [using options to store data when creating a Gutenberg block](https://wholesomecode.ltd/guides/options-settings-data-wordpress-gutenberg/). Here we use the function above to save data to the Settings API on save.