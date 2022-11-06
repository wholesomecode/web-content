---

title: Add a Custom Settings Link to Your WordPress Plugin
post_date: 2021-03-19T11:41:46.000Z
post_status: publish

---

Have you ever noticed how some plugins have one or more custom links on the WordPress Plugin screen? To be honest if I cannot find the settings for the plugin this is the first place I look:

![Settings Link on Plugin Settings Page Panel](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989666870/Yxz84Pj2P.png)Settings Link on Plugin Settings Page Panel

If you want to add your own custom link to the Plugin screen, just use this block of code to the root of your plugin (or adjust the code so it works in a sub file, your call).

```
function wholesomecode_wholesome_plugin_settings_link( $links ) : array {
	$label = esc_html__( 'Settings', 'wholesome-plugin' );
	$slug  = 'wholesome_plugin_settings';

	array_unshift( $links, "<a href='options-general.php?page=$slug'>$label</a>" );

	return $links;
}
add_action( 'plugin_action_links_' . plugin_basename( __FILE__ ), 'wholesomecode_wholesome_plugin_settings_link', 10 );
```

There we go, we now have a ‘Settings’ link on the plugin screen. Why not try adding some other links here? Or even use AJAX to make the button functional (IE, a ‘reset settings’ link).

If you are interested in this tidbit, you may be interested to see how I built a [settings page using WordPress Gutenberg components](https://wholesomecode.ltd/guides/create-settings-page-wordpress-gutenberg-components/).