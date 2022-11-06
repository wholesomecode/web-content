---

title: Debugging WordPress
post_date: 2020-06-18T10:07:00.000Z
post_status: publish

---

You have just pushed out an update to your WordPress site, but somethings gone wrong. Perhaps you have got the dreaded White Screen of Death, or some error is being outputted on the screen.

Here are the steps you need to put things right again.

![Debugging WordPress Diagram](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990061232/HGS3saOCJ.png)Debugging WordPress Diagram

Check the Error Log
-------------------

The WordPress server error log is the first place you should go. It will list out all the errors that WordPress encounters, including the file and line number of the function that caused.

Check the Browser Console
-------------------------

If the bug is of a JavaScript nature, you will want to check your browsers JavaScript console. Similar to the error log, it should tell you the file, line number and function of the issue.

Turn Off All Plugins
--------------------

If you are not getting any errors in the error log or console, let’s do an old favourite and turn off all the plugins on the site.

*   If the issue goes away, turn them on one at a time
*   This will help you find the broken plugin

Switch your theme
-----------------

If a plugin wasn’t the issue, it’s time to check the theme. You can do this by switching to a default theme that comes with WordPress (eg TwentyTwenty).

If this issue goes away, you now know you need to fix your theme

If you are still having trouble, or **if you don’t like getting your hands dirty in the code**, the WordPress community is always full of people that can help. I’m one of them, [why not get in touch to see how I can help](https://wholesomecode.ltd/contact/).