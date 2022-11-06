---

title: Prevent XSS (Cross-Site Scripting) by escaping values in your WordPress templates
post_date: 2021-12-20T10:12:09.413Z
post_status: publish

---

If you are developing a WordPress theme or plugin then it is important to make it as secure and robust as possible. One way to do this is to properly escape values that you use in your project so that XSS (Cross-Site-Scripting) is prevented.

An example of a XSS attack may be that you have created a form which saves a value, such as their name. Let’s say that you output this value back to the form.

A malicious customer could enter their name as: `Malicious User" onload="alert( 'Attack!' );"`

When the data is loaded on your unescaped form, the first `"` would end the `value=""` attribute, and the `onload=""` would be inserted into the form, resulting in the following code being executed when the page is loaded:

![A JS alert with the message 'Attack!'](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990507419/O9Nvis-xP.png)XSS at work. A JS alert with the message ‘Attack!’

This would be a pretty mild piece of malicious code, but as you can imagine it could be exchanged for some very nasty code indeed.

Escape all the things
---------------------

This horrible and malicious attack could have just been avoided if we had escaped the attribute using `esc_attr()` like so:

```
<input type="text" value="<php echo esc_attr( $my_value ); ?>"/>
```

There are a load of different escapes that you can use for a whole variety of scenarios (e.g. escaping URLs, escaping Email Addresses), and there is a fantastic section in the [WordPress handbook](https://developer.wordpress.org/themes/theme-security/data-sanitization-escaping/#escaping-securing-output) all about Escaping.

Sanitising Data
---------------

Something that is equally important is sanitising your data, which you should do whenever you receive data from the client.

By doing this you make sure that data that is passed into your website or application does not have any malicious code within it, and can be processed appropriately.

Remember to sanitise when you receive data, and escape when you output data.

You can learn more about sanitising in the [WordPress handbook](https://developer.wordpress.org/themes/theme-security/data-sanitization-escaping/).