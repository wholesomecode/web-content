---

title: Creating a Custom CMB2 Link Picker Control for WordPress
post_date: 2019-01-01T23:00:00.000Z
post_status: publish

---

In this tutorial I will look at how you can create a custom control to extend the functionality of [CMB2](https://wordpress.org/plugins/cmb2/) (Custom Meta Boxes 2) by [WebDevStudios](https://webdevstudios.com/).

I develop websites (and web applications) with the [WordPress](https://wordpress.org/) CMS (Content Management System), and when a new project lands you can guarantee that there will be a requirement for me to develop ‘Custom Meta Boxes’ to allow the user to have fine control over the sites content and layout.

I will detail how I built the CMB2 control [Link Picker for CMB2](https://en-gb.wordpress.org/plugins/link-picker-for-cmb2/) (available from all good WordPress plugin repositories). A screenshot of which can be seen below.

![Link Picker CMB2 Control](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990402575/tMuVEbaSX.png)The ‘Link Picker’ CMB2 control in action

The Link Picker fires the built in WordPress ‘Insert/edit link’ dialog when you click the ‘Choose’ button. This can be seen in the screenshot below:

![Link Picker Pop-Up](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990407330/dKPvS3OEq.png)Hitting the button lets you choose from a link (or add your own)

I am sure you will agree that having a control like this is incredibly handy if you want to give your site editors the ability add a link and also search WordPress for its internal links, rather then them having to cut and paste the links into a link field.

Introduction / History
----------------------

For those not in the know, a meta box lives on the editor screen of a WordPress post, and will likely contain various form controls (text boxes, dropdown lists, checkboxes etc…). These controls let your website users easily be able to change a custom piece of text or functionality on the site.

![An example of a meta box with various form controls](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990409319/dM_OnvpHM.svg+xml)![An example of a meta box with various form controls](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990411878/iBwrfg3Ou.png)An example of a meta box with various form controls

WordPress lets you create meta boxes using functions (such as `[add_meta_box](https://developer.wordpress.org/reference/functions/add_meta_box/)`), but creating meta boxes in this way can be a lengthy process, with lots of code repetition (especially if you want to use the same form controls in multiple projects).

Why CMB2?
---------

Some of you may have heard of [Advanced Custom Fields](https://www.advancedcustomfields.com/) (ACF) which provides a GUI (Graphical User Interface) that lets you create meta boxes directly with WordPress.

ACF in my opinion is **not a great tool** for any web solution that scales. The plugin has too much reliance on data being stored within the database. This causes pain when deploying changes to a site, as you cannot just push up your code and see the changes instantly. Instead you have to do the work again on the various deployment environments (staging, live, et al). So we needed a solution that let us create meta boxes programatically. Enter CMB2.

Before we adopted CMB2, we previously used [HM Custom Meta Boxes](https://github.com/humanmade/Custom-Meta-Boxes) from those lovely humans at [Human Made](https://hmn.md/) (which began as a fork of WebDevStudio’s forerunner to CMB2, ‘Custom Meta Boxes’).

We loved HM Custom Meta Boxes, and with the simplest snippets of code, we could quickly create custom Meta Boxes to do pretty much anything!

![HM Custom Meta Boxes Markup Example](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990416802/EKRMFpyyx.png)HM Custom Meta Boxes Markup Example (this is the markup for the Instagram Meta Box in the first Screenshot)

So why the move to CMB2? Well, HM Custom Meta Boxes unfortunately wasn’t getting a whole lot of love (I spoke to its lead developer and he is a very very busy man), whereas CMB2 was moving ahead with new features, new controls, and it had gained traction in the WordPress community with many people adopting it and releasing plugins to extend it (including several of our partner agencies).

Finally, as you might have gathered, working with CMB2 is as incredibly simple as we had become accustomed to, as both platforms share a common ancestor.

Tutorial
--------

Before we begin, everyone has their own set of ideals on how to create a WordPress plugin, and I’ve tried a fair few, however the tutorial on ‘[Root Composition in WordPress](https://tomjn.com/2015/06/24/root-composition-in-wordpress-plugins/)‘ by Tom J Nowell, completely changed the way I work. I find its approach clean, simple, and it makes future maintenance of any plugin a cinch. If you grab the [source of the Link Picker for CMB2](https://wordpress.org/plugins/link-picker-for-cmb2/) plugin, you can see the methods he teaches in practice.

### Building the Form

To build the form that renders the Link Picker, the first thing we need to do is hook into the `cmb2_render_[control_name]` action. As I have called this control ‘link\_picker’ we can complete the hook like so:

```
<?php
add_action( 'cmb2_render_link_picker', array( $this, 'cmb2_render_link_picker' ), 10, 5 );
````

For those of you who don’t really understand the `add_action` hook, it works as follows:

1.  The first argument `cmb2_render_link_picker` is the name of the hook we want to hook into.
2.  The second argument `array( $this, 'cmb2_render_link_picker' )` is the function we want to call when this hook runs. Note that I am wrapping this in an array, with `$this` as the first parameter, because I am calling the function inside a class. If you are not working with classes you can just use the function name `cmb2_render_link_picker`.
3.  The `10`, is the order that we want the function to fire (the lower the number, the sooner it fires when the action is called).
4.  The `5` is the amount of parameters that will be passed to the function that I am calling (this will become clear shortly).

Next up we create the function that will render the form:

```
<?php
/**
* Render 'link_picker' custom field type
*
* @param array $field The passed in `CMB2_Field` object
* @param mixed $value The value of this field escaped.
* It defaults to `sanitize_text_field`.
* If you need the unescaped value, you can access it
* via `$field->value()`
* @param int $object_id The ID of the current object
* @param string $object_type The type of object you are working with.
* Most commonly, `post` (this applies to all post-types),
* but could also be `comment`, `user` or `options-page`.
* @param object $field_type_object The `CMB2_Types` object
*/
public function cmb2_render_link_picker( $field, $value, $object_id, $object_type, $field_type_object ) {
…
}
````

I have left the ‘DocBlock’ in the code above that describes what each of parameters passed into the `cmb2_render_link_picker()` function does.

Note that my function begins with the `public` declaration. This again is because I am working within a class. If you are not working with classes you can omit this.

The value of this field is passed into the function via the `$value` parameter. In the case of this field, we will be passing through an array, as our control has three separate elements to it:

*   The text
*   The URL
*   If the link opens in a new window (or not)

Because the `$value` is not always set (for instance the first time the control is rendered) we need to initialise it with some default values. We do this with the following bit of code:

```
<?php
$value = wp_parse_args( 
	$value, 
	array(
		'text'  => '',
		'url'   => '',
		'blank' => 'false',
	) 
);
```

We can then get to work rendering out the form. Here is an example of the first text input control:

```
<p>
	<label for="<?php echo $field_type_object->_id( '_text' ); ?>'">
		<?php echo esc_html( $field_type_object->_text( 'link_picker_text', 'Text' ) ); ?>
	</label>
</p>
<?php 
	echo $field_type_object->input( 
    		array(
			'class' => 'cmb_text',
			'name'  => $field_type_object->_name( '[text]' ),
			'id'    => $field_type_object->_id( '_text' ),
			'value' => $value['text'],
		) 
  	); 
?>
```

Phew! That looks a bit messy doesn’t it? Lets break it down, line-by-line:

1.  The opening paragraph tag.
2.  The opening label tag for the control, but with the `for` attribute automatically set by the `$field_type_object` `_id` parameter. This will automatically generate an ID for the control when it is rendered.
3.  The text of our label, built using text from the controls options array (or falls back to the word ‘Text’).
4.  The closing label tag
5.  The closing paragraph tag.
6.  Start PHP declaration
7.  Use an input control (part of the `$field_type_object` to create a form input (default type will be text).
8.  Start the array of parameters
9.  Set the class of the input.
10.  Set the name of the input, again using the `$field_type_object` helper.
11.  Set the ID of the input, to the same ID that was set on the label tag.
12.  Get the value from the `$value`, as this is an array, we want the ‘text’ key for this control.
13.  Close the array.
14.  Close the input function.
15.  Close the PHP declaration.

The URL form field markup is much the same, only to use HTML5 input types we can set an additional parameter of ‘type’ to ‘url’:

```
<?php 
…
'type'  => 'url',
…
```

Finally we want to implement a dropdown. The markup is very familiar:

```
<?php
echo $field_type_object->select( 
	array(
		'class'   => 'cmb_dropdown',
		'name'    => $field_type_object->_name( '[blank]' ),
		'id'      => $field_type_object->_id( '_blank' ),
		'options' => $blank_options,
	) 
);
```

Note that the `$field_type_object` function we are using is `select` to generate a dropdown. Also note that on line 6 we have a new attribute of `options`. Into this we are passing a string of ‘options’. This is generated prior to this control like so:

```
<?php 
$blank_options = '';
$blank_options .= '<option value="false" '. selected( $value['blank'], 'false', false ) .'>Opens in same</option>';
$blank_options .= '<option value="true" '. selected( $value['blank'], 'true', false ) .'>Opens in new</option>';
```

Then all we need to do is wrap it in some `<div>`‘s and we have our fully rendered control:

```
<?php
public function cmb2_render_link_picker( $field, $value, $object_id, $object_type, $field_type_object ) {
	$value = wp_parse_args( $value, array(
		'text'  => '',
		'url'   => '',
		'blank' => 'false',
	) );
	$blank_options = '';
	$blank_options .= '<option value="false" '. selected( $value['blank'], 'false', false ) .'>Opens in same</option>';
	$blank_options .= '<option value="true" '. selected( $value['blank'], 'true', false ) .'>Opens in new</option>';
	?>
	<div class="link-picker">
		<div class="text">
			<p>
				<label for="<?php echo $field_type_object->_id( '_text' ); ?>'">
					<?php echo esc_html( $field_type_object->_text( 'link_picker_text', 'Text' ) ); ?>
				</label>
			</p>
			<?php 
				echo $field_type_object->input( 
					array(
						'class' => 'cmb_text',
						'name'  => $field_type_object->_name( '[text]' ),
						'id'    => $field_type_object->_id( '_text' ),
						'value' => $value['text'],
						'desc'  => '',
					) 
				); 
			?>
		</div>
		<div class="url">
			<p>
				<label for="<?php echo $field_type_object->_id( '_url' ); ?>'">
					<?php echo esc_html( $field_type_object->_text( 'link_picker_url', 'URL' ) ); ?>
				</label>
			</p>
			<?php 
				echo $field_type_object->input( 
					array(
						'class' => 'cmb_text_url',
						'name'  => $field_type_object->_name( '[url]' ),
						'id'    => $field_type_object->_id( '_url' ),
						'value' => $value['url'],
						'type'  => 'url',
						'desc'  => '',
					) 
				); 
			?>
		</div>
		<div class="blank">
			<p>
				<label for="<?php echo $field_type_object->_id( '_blank' ); ?>'">
					<?php echo esc_html( $field_type_object->_text( 'link_picker_blank', 'Window' ) ); ?>
				</label>
			</p>
			<?php 
				echo $field_type_object->select( 
					array(
						'class'   => 'cmb_checkbox',
						'name'    => $field_type_object->_name( '[blank]' ),
						'id'      => $field_type_object->_id( '_blank' ),
						'options' => $blank_options,
						'desc'    => '',
					)
				); 
			?>
		</div>
		<div class="choose">
			<p>
				<label>Choose</label>
			</p>
			<button class="dashicons dashicons-admin-links js-insert-link button button-primary" title="<?php esc_html_e( 'Insert Link', 'cmb' ); ?>">
	 			<span class="screen-reader-text"><?php esc_html_e( 'Choose Link', 'cmb' ); ?></span>
	 		</button>
		</div>
	</div>
	<p class="clear">
		<?php echo $field_type_object->_desc();?>
	</p>
<?php
}
```

And thats it! We have made our control! CMB2 automatically handles all the data we want to save, so nothing to do there.

### Styles

The screenshot of the control we are creating (near the top of this post) has a few custom styles applied to it so it renders inline. I will not go into how to style the form today, but if you are curious you can [download the plugin and view the source](https://en-gb.wordpress.org/plugins/link-picker-for-cmb2/developers/).

### Making the control repeatable

For those of you who want to get a little more advanced, you can make the control work with CMB2’s repeatable regions. To do that you need to do a little bit of array mapping. To do that use the code below:

```
<?php
/**
 * The following snippets are required for allowing the link_picker field
 * to work as a repeatable field, or in a repeatable group
 */
public function cmb2_sanitize_link_picker( $check, $meta_value, $object_id, $field_args, $sanitize_object ) {
	// if not repeatable, bail out.
	if ( ! is_array( $meta_value ) || ! $field_args['repeatable'] ) {
		return $check;
	}
	foreach ( $meta_value as $key => $val ) {
		$meta_value[ $key ] =  null;
		if( ! empty( $val['url'] ) ) {
			$meta_value[ $key ] = array_map( 'sanitize_text_field', $val );
		}
	}
	return $meta_value;
}
public function cmb2_types_esc_link_picker( $check, $meta_value, $field_args, $field_object ) {
	// if not repeatable, bail out.
	if ( ! is_array( $meta_value ) || ! $field_args['repeatable'] ) {
		return $check;
	}
	foreach ( $meta_value as $key => $val ) {
		$meta_value[ $key ] =  null;
		if( ! empty( $val['url'] ) ) {
			$meta_value[ $key ] = array_map( 'esc_attr', $val );
		}
	}
	return $meta_value;
}
```

Choosing a Link
---------------

Of course the whole point of the link picker is to integrate into WordPress’s own link choosing functionality, making the ‘Insert/edit link’ dialog screen pop-up when the ‘Choose’ button is clicked.

To make this happen we rely heavily on JavaScript. In particular I am using [jQuery](https://jquery.com/) to make things happen.

Before I show you the JavaScript that launches the dialog, we must first enqueue WordPress’s own internal JavaScript first, that will pre-load the modal’s and libraries that our code depends on. That looks a little something like this:

```
<?php
global $post_id;
/* Media */
if ( isset( $post_id ) ) {
	wp_enqueue_media( array( 'post' => $post_id ) );
}
/* JS */
$plugin_js_url  = plugins_url( 'js/plugin.js', ROOT );
wp_enqueue_script( 'wholesomecode', $plugin_js_url, array( 'jquery', 'jquery-ui-core', 'jquery-ui-draggable', 'jquery-ui-droppable', 'thickbox', 'wpdialogs' ), '1.0.0', true );

```

As you can see, a lot of the internal WordPress libraries rely on jQuery to load the popup, so it makes sense for our pop-up trigger to do the same. This is done via the `/js/plugin.js` which is loaded on line 10 of the above example.

```
jQuery(document).ready(function($) {

	/* Set Defaults */
	var url   = $('body');
	var text = $('body');
	var blank = $('body');
	
	/* Open the Link Window on button click */
	$('body').on('click', '.js-insert-link', function(event) {
		
		/* Stop the defalut event from happening */
		event.preventDefault ? event.preventDefault() : event.returnValue = false;
		event.stopPropagation();
		
		/* Set the variables to related to the chosen control */
		url            = $(this).closest('.link-picker').find('input.cmb_text_url ');
		text           = $(this).closest('.link-picker').find('input.cmb_text ');
		blank          = $(this).closest('.link-picker').find('input.cmb_checkbox ');
		
		/* Open the link picker */
		wpActiveEditor = true;
		wpLink.open();
		wpLink.textarea = url;

		return false;
	});

	/* If the action is canceled, close the link picker pop-up */
	$('body').on('click', '#wp-link-cancel, #wp-link-backdrop, #wp-link-close', function(event) {

		wpLink.textarea = url;
		wpLink.close();
		event.preventDefault ? event.preventDefault() : event.returnValue = false;
		event.stopPropagation();
		return false;
	});

	/* Once the link is chosen, populate the control */
	$('body').on('click', '#wp-link-submit', function(event) {
		console.log(text)
		var linkAtts = wpLink.getAttrs();

		linkAtts.text = $('#wp-link-text').val();

		url.val(linkAtts.href);

		if( linkAtts.text != '' ) {
			text.val(linkAtts.text);
		}

		if (linkAtts.target == '_blank') {
			blank.prop('checked', true);
		} else {
			blank.prop('checked', false);
		}

		wpLink.textarea = url;
		wpLink.close();
		event.preventDefault ? event.preventDefault() : event.returnValue = false;
		event.stopPropagation();
		return false;
	});
});
```

Using the classes that we wrapped around our form controls, the JavaScript targets the controls, and pushes the selected result from the link picker pop-up into the relevant control fields.

Using the Control
-----------------

So, after looking through the tutorial above, and possibly after [reviewing the source code](https://en-gb.wordpress.org/plugins/link-picker-for-cmb2/developers/) of the [Link Picker for CMB2](https://en-gb.wordpress.org/plugins/link-picker-for-cmb2/) plugin, or just downloading my version, you may now be wondering how to use the thing with CMB2. Well, it couldn’t be easier:

```
<?php
function wholesomecode_create_meta_boxes() {
  $prefix = '_profile_';
	/**
 	 * Initiate the metabox
	 */
	$cmb = new_cmb2_box(
		array(
			'id'            => 'cta',
			'title'         => __( 'Call to Action', 'cmb2' ),
			'object_types'  => array( 'profile' ),
			'context'       => 'normal',
			'priority'      => 'low',
			'show_names'    => true,
		)
	);
	/**
 	 * Add the control
	 */
	$field1 = $cmb->add_field( 
		array(
			'name' => __( 'Link Picker', 'cmb2' ),
			'id'   => $prefix . 'cta_link',
			'type' => 'link_picker', // Here is where we add our control
		) 
	);
}
add_action( 'cmb2_admin_init', 'wholesomecode_create_meta_boxes' );
```