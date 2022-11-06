---

title: wp_get_attachment_image WordPress Block Editor (Gutenberg) Equivalent is getMedia
post_date: 2021-02-09T21:56:47.000Z
post_status: publish

---

Looking for how to get the post thumbnail, or an attachment URL by its Id in WordPress gutenberg?

You may have a [returned post object by using the WordPress Gutenberg equivalent of WP\_Query](https://wholesomecode.ltd/blog/wp_query-gutenberg-equivalent-getentityrecords/#the-javascript-post-object) (or the [WordPress Gutenberg equivalent of get\_post](https://wholesomecode.ltd/blog/get_post-gutenberg-equivalent-getentityrecord/)).

Note that in the post object you can get the featured media ID, which is stored in the `featured_media` property.

You can then use the `getMedia` to get the properties for this attachment by it Id, like so:

```
wp.data.select( 'core' ).getMedia( 42 );
```

Here it is in the console:

![Using getMedia](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989959824/9SrmJIIKF.png)Using getMedia in the console.

We can explore the image object that is returned in the console, and see that we can grab all the data, alt text, meta and image sizes that we need to display the image.

Note that the image sizes in the example data below have been abbreviated, otherwise it goes on for quite a bit.

```
id: 101
date: "2020-03-21T14:02:00"
date_gmt: "2020-03-21T14:02:00"
guid: {
	rendered: "https://wholesomecode.ltd/wp-content/uploads/2020/03/Code-Example.png"
	raw: "https://wholesomecode.ltd/wp-content/uploads/2020/03/Code-Example.png"
}
modified: "2020-03-21T14:02:00"
modified_gmt: "2020-03-21T14:02:00"
slug: "code-example"
status: "inherit"
type: "attachment"
link: "https://wholesomecode.ltd/code-example/"
title: {
	raw: "Code Example"
	rendered: "Code Example"
}
author: 1
comment_status: "closed"
ping_status: "closed"
template: ""
meta: {
	_wcltd_meta_example_1: "1" 		
	_wcltd_meta_example_2: "Hello"
}
permalink_template: "https://wholesomecode.ltd/wp-content/uploads/2020/03/Code-Example.png/?attachment_id=101"
generated_slug: "code-example"
description: {
	raw: "Code Example"
	rendered: "..."
}
caption: {
	raw: "Code Example"
	rendered: "<p>Code Example</p>↵"
}
alt_text: "Code Example"
media_type: "image"
mime_type: "image/png"
media_details: {
	width: 2310
	height: 1400
	file: "2020/03/Code-Example.png"
	sizes: {
		...
        large: {
			file: "Code-Example-1024x621.png"
			width: 1024
			height: 621
			mime_type: "image/png"
			source_url: "https://wholesomecode.ltd/wp-content/uploads/2020/03/Code-Example-1024x621.png"
        }
	}
	image_meta: {
        aperture: "0"
		credit: ""
		camera: ""
		caption: ""
		created_timestamp: "0"
		copyright: ""
		focal_length: "0"
		iso: "0"
		shutter_speed: "0"
		title: ""
		orientation: "0"
    }
}
post: null
source_url: "https://wholesomecode.ltdwp-content/uploads/2020/03/Code-Example.png"
```

See this in action in the [WordPress Gutenberg post loop example](https://wholesomecode.ltd/blog/wp_query-gutenberg-equivalent-getentityrecords/#gutenberg-post-loop).