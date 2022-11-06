---

title: Extend the WordPress Customizer Using JavaScript
post_date: 2021-03-18T21:52:01.000Z
post_status: publish

---

Did you know that you can create Customizer Sections and Panels in JavaScript? Did you know that you can also register Customizer Settings and Controls in JavaScript too?

You can do all that and more, with code similar to the following:

```
const { customize } = wp;

customize.bind('ready', function () {
    const panelKey = 'wholesomecode-wholesome-plugin-customizer-panel';
    const sectionKey = 'wholesomecode-wholesome-plugin-customizer-section';

    customize.panel.add(
        new customize.Panel( panelKey, {
          	description: __( 'Wholesome Plugin Example Panel', 'wholesome-plugin' ),
            priority: 1000,
            title: __( 'Wholesome Plugin Panel', 'wholesome-plugin' ),
        })
    );
    customize.section.add(
        new customize.Section( sectionKey, {
            customizeAction: __( 'Wholesome Plugin ▸ Section', 'wholesome-plugin' ),
            panel: panelKey,
         	title: __( 'Wholesome Plugin Section', 'wholesome-plugin' ),
        })
    );

    customize.control.add(
        new customize.Control( 'wholesomecode-wholesome-plugin-customizer-gutenberg-control', {
            description: __( 'Example Description', 'wholesome-plugin' ),
            label: __( 'Example Text', 'wholesome-plugin' ),
            section: sectionKey,
            setting: 'wholesomecode_wholesome_plugin_example_text',
            type: 'text',
        })
    );
});
```

Note that in our text control we have used the setting `wholesomecode_wholesome_plugin_example_text`, unfortunately we will still need PHP to register this setting.  
  
Add the following PHP code block to your project:

```
function wholesomecode_wholesome_plugin_register_customizer_settings( $wp_customize ) {
	$wp_customize->add_setting( 'wholesomecode_wholesome_plugin_example_text' );
}
add_action( 'customize_register', 'wholesomecode_wholesome_plugin_register_customizer_settings', 10 );
```

And thats it, we now have a Panel, a Section and a Field in Customizer, created mostly in JavaScript:

![Customizer Panel, Section and Field registered in JavaScript](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989674687/fGEpH102Q.png)Customizer Panel, Section and Field registered in JavaScript

To see this code in action, check out how I use it in my guide about how you can [use WordPress Gutenberg Controls in Customizer](https://wholesomecode.ltd/guides/create-customizer-panel-wordpress-gutenberg-components/).