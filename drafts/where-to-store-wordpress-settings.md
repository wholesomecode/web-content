---

title: Where to Store WordPress Settings?
post_date: 2020-06-15T09:43:00.000Z
post_status: publish

---

Saving some data in WordPress, but not entirely sure where it should go?

It is always good to have consistency when creating plugins, so I’ve made a handy little guide.

![Block Settings Diagram](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990069010/QN3ru7Zn-.png)Block Settings Diagram

Network Setting (Multisite wide settings)
-----------------------------------------

For settings that impact several of your sites (and provided they are in a Multisite network), you can create a network settings page.

Site Settings
-------------

If you only have a single site, or the settings only impact a single site of a Multisite instance, you can a settings page.

Site Appearance
---------------

If the changes impact the appearance of your entire site (not just a specific page), you should add a panel to the Customizer.

Post Settings
-------------

If your settings are on a per-post level, you should create a custom sidebar in the block editor (Gutenberg), or add a panel to the ‘Document’ sidebar.

You could also create a classic meta box.

Block Settings or Appearance
----------------------------

For individual block settings, or appearance you can use block Edit controls for block content, or use Block Controls (toolbar) and Inspector Controls for block specific layout and appearance.