---

title: How to make Responsive Web Design (RWD) Background Images
post_date: 2017-07-28T19:50:41.000Z
post_status: publish

---

Responsive Web Design (RWD) is a service that comes as standard with all the products that I develop, and the method created by Dave Green is an excellent way to make sure that your background images adhere to the same responsive rules as your embedded images. 

Responsive Images on any Website
--------------------------------

The following method focuses on implementing responsive images within WordPress, but the example Code Pen Dave has created can work with any website. Here is a fork:

<iframe id="cp_embed_oJrEmJ" src="//codepen.io/anon/embed/oJrEmJ?height=250&amp;theme-id=1&amp;slug-hash=oJrEmJ&amp;default-tab=result" height="250" class="cp_embed_iframe" style="width:100%;overflow:hidden">CodePen Embed Fallback</iframe>

This is a JavaScript based solution, that has dependencies on Modernizr and jQuery, however it could easily be reworked to be a native solution.

Because of the usage of JavaScript this means that your background images wont show if you do not have JavaScript enabled. However from an accessibility perspective, background images are for decoration only, so just ensure that you have a suitable fallback in place in your CSS (such as a solid colour or gradient). Avoid using an image as a fallback, as the image will load before the responsive script, which kind of defeats the point.

Responsive Images with WordPress
--------------------------------

In Dave’s (now removed) article he provided some great Gists that show how you can create functions for WordPress that will do all of the heavy lifting for you. I have made a [fork of the RWD Background Image Gists here](https://gist.github.com/mattwatsoncodes/21e660e028f2ad022a9b266a3f228123) (because I fully intend tinkering with the code).