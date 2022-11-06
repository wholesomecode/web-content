---

title: Secure Blocks for WordPress Gutenberg
post_date: 2019-01-01T23:00:00.000Z
post_status: publish

---

**Plugin deprecated**

Please note that this plugin has now been deprecated. The guide is left here to show the features that I developed during my early exploration into the WordPress block editor (Gutenberg) development.  
  
There are many plugins in the WordPress repository that recreate the functionality of this plugin, most do it better.

---

Secure your content in the WordPress Gutenberg editor via login or by user role, with Secure Blocks for Gutenberg.

Using Secure Blocks you can add any Gutenberg block inside a secure block, and have it render only to logged in users, or you can lock it down to a specific user role (or roles) of your choosing.

Secure Blocks also provides an additional area that displays blocks to users that do not have permission to view the content.

Overview of Secure Blocks for Gutenberg
---------------------------------------

Here is a quick video walkthrough of how Secure Blocks for Gutenberg works:

<iframe width="1100" height="619" src="https://www.youtube.com/embed/fXGPeWo_nzg?feature=oembed"></iframe>

A quick video introduction to to Secure Blocks on YouTube

There are a planned number of additional blocks that will be added to the Secure Blocks plugin, but for now the main functionality is as follows:

*   Display content only to logged-users
*   Display alternative content to logged-out users
*   Display content to users within certain user roles
*   Display alternative content to users not in those user roles

Making the Content Secure in the WordPress Gutenberg Editor with Secure Blocks
------------------------------------------------------------------------------

To add a secure block simply choose the ‘Secure Block’ block type when clicking the ‘insert block’ button.

![Adding a secure block in Gutenberg](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990331938/r4H0J73XK.png)Adding a secure block in Gutenberg

Once you insert a secure block you will be presented with the default UI. In the top area you can add multiple sub-blocks that will be made secure (e.g. by default, content in this area is shown if a user is logged in)

In the bottom area you can add multiple sub-blocks that will be shown if the conditions you have set for restricting the top area are not met (e.g. by default, content in this area is shown if a user is logged out) .

![The default Secure Blocks for Gutenberg UI](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990338231/l4hJEy54_.png)The default Secure Blocks for Gutenberg UI

There is no limit to the block types you can add in these areas. The next example simply shows a heading and a paragraph.

![Adding Secure and Non-Secure Gutenberg content](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990343458/bTJvqs1Uo.png)Adding Secure and Non-Secure Gutenberg content

Then depending on the restrictions you have set for the secure area, and how you are viewing the site, you are presented with the appropriate content.

![Secure Content is presented to logged-in users](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990349341/6jJT2iMI8.png)Secure Content is presented to logged-in users

![Non secure content is presented to logged out users](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990354397/fsxzj7_kw.png)Non secure content is presented to logged out users

Restricting Content Further by User Role
----------------------------------------

By using the ‘Select Roles’ block inspector you can restrict content further to a user role. 

![Secure Blocks for Gutenberg UI Updated with Restricted Users](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990360032/Ku0CPNCgR.png)Secure Blocks for Gutenberg UI Updated with Restricted Users

You can select multiple roles that the content will be restricted to, and the content will only be shown if a user is in **any** of those roles.

![Restricting content by user role](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990364181/aJRbckjSv.png)Restricting content by user role

You can clearly see how the content has been restricted as the blocks UI is updated with the selected options.

![The Secure Blocks for Gutenberg UI is automatically updated with the selected user roles](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990369498/JvQRnSDnE.png)The Secure Blocks for Gutenberg UI is automatically updated with the selected user roles

No Block Restrictions
---------------------

You can add any type of Gutenberg block into the secure area, and you can choose any of the display options.

![Any Gutenberg Block can be made secure](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990375476/VaqPkuuyx.png)Any Gutenberg Block can be made secure

As you can see, the bounds of the secure block do not limit the rendering of the sub blocks.

![Full screen content is rendered securely and not restricted by Secure Block container](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990384113/WKvN06yeO.png)Full screen content is rendered securely and not restricted by Secure Block container