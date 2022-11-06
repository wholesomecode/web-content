---

title: Broken React.createClass Component? Let’s Fix It!
post_date: 2019-01-14T17:20:47.000Z
post_status: publish

---

If you are building WordPress Gutenberg Blocks (or even if you are not) you may be trying to [get a third party React Component working in your build](https://wholesomecode.ltd/blog/add-select2-as-an-inspectorcontrol-in-wordpress-gutenberg/). But if it’s an older element, specifically one that uses the deprecated `React.createClass` method, you may get stuck.

The files that accompany this tutorial are in the original and patched folders in the components folder of the GitHub solution.

[Get Tutorial Files from GitHub](https://github.com/mattwatsoncodes/Tutorial-Convert-React-createClass-to-React-Component)

I was looking for a checkbox list that allowed me to pass an array of options (similar to how you might pass multiple options to the `<SelectControl/>` in WordPress Gutenberg). The built in `<CheckboxControl/>`didn’t have an option, but after some searching the web I found `[react-checkbox-list](https://github.com/sonyan/react-checkbox-list)` [by Sony An](https://github.com/sonyan/react-checkbox-list) (available as [`react-checkbox-list` on npm](https://www.npmjs.com/package/react-checkbox-list)).

This did everything I wanted it to do, but after installing it via npm (`npm install react-checkbox-list --save`) it didn’t work. I knew I would have to fork and patch the plugin.

Note that this tutorial covers patching the existing `React.createClass` and `React.propTypes` methods. [If you are interested in learning how to patch them with the preferred method of using](https://mattwatson.codes/converting-react-createclass-to-react-component/) `[React.Component](https://mattwatson.codes/converting-react-createclass-to-react-component/)` [please view the post on that topic](https://mattwatson.codes/converting-react-createclass-to-react-component/).

Patching the React Component
----------------------------

The first issue you will come across is that the component will not even load, and you will be presented with a rather nasty `Uncaught TypeError: Cannot read property 'array' of undefined` error.

![Uncaught TypeError, Cannot read property 'array' of undefined](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990293175/74Lyj1xNJ.png)Uncaught TypeError, Cannot read property ‘array’ of undefined

This is because the Component contains the following line `defaultData: React.PropTypes.array,` and it is because `React.PropTypes` that is undefined.

![React.PropTypes is undefined](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990297188/fIc65muks.png)React.PropTypes is undefined  

A bit of investigation into the [React documentation shows that](https://reactjs.org/docs/typechecking-with-proptypes.html) `[React.PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html)` [moved into a different package in React version 15.5](https://reactjs.org/docs/typechecking-with-proptypes.html). It advises us to use the prop-types library instead.

It even gives you a link to the [npm repository for the prop-types library](https://www.npmjs.com/package/prop-types). So lets go ahead and install the prop-types library.

```
npm install --save prop-types
```

At the top of our document, let’s import the `prop-types` library. [The modern way of this would have us use the](https://mattwatson.codes/converting-react-createclass-to-react-component/) `[import](https://mattwatson.codes/converting-react-createclass-to-react-component/)` [syntax](https://mattwatson.codes/converting-react-createclass-to-react-component/), but for now, let’s keep to the same syntax add `var PropTypes = require('prop-types');` to the document below where we declare `React`. The opening declaration should now look a little something like this:

```
'use strict';
var React     = require('react');
var PropTypes = require('prop-types');

module.exports = React.createClass({
	displayName: 'CheckBoxList',

	propTypes: {
		defaultData: React.PropTypes.array,
		onChange: React.PropTypes.func
	},
...
```

Great stuff! However we still get the `'array' of undefined` error message. Well thats because the syntax being used is `React.PropTypes.array` and we just declared our PropTypes to use the variable `PropTypes`. So the simple fix is to find and replace all instances of `React.PropTypes` with `PropTypes`.

Our opening block of code should now look like this:

```
'use strict';
var React     = require('react');
var PropTypes = require('prop-types');

module.exports = React.createClass({
	displayName: 'CheckBoxList',

	propTypes: {
		defaultData: PropTypes.array,
		onChange: PropTypes.func
	},
...
```

There we go, our PropTypes issue is now resolved! Lets refresh our component and see if it works.

Nope, we now get the error `Uncaught TypeError: React.createClass is not a function.`

![Uncaught TypeError: React.createClass is not a function.﻿](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990298979/vn22fnAlz.svg+xml)![Uncaught TypeError: React.createClass is not a function.﻿](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990301130/l6Exfr8Tb.png)Uncaught TypeError: React.createClass is not a function.﻿

Again, a little search around the [React documentation and release notes for v15.5.0](https://reactjs.org/blog/2017/04/07/react-v15.5.0.html#new-deprecation-warnings) shows us that `React.createClass` was depreciated in version 15.5.0.

Along with `React.PropTypes`,`React.createClass` was refactored into its own package, as [the preferred method of creating React Components uses](https://mattwatson.codes/converting-react-createclass-to-react-component/) `[React.Component](https://mattwatson.codes/converting-react-createclass-to-react-component/)`.

The replacement for React.createClass is now in its own [npm repository as create-react-class](https://www.npmjs.com/package/create-react-class). So let’s go ahead and install this into our build.  

```
npm install --save create-react-class
```

We need to import the package into our build, again keeping the syntax in the classic format add the line `var CreateReactClass = require('create-react-class');` to the top of the document, like so:

```
'use strict';
var React            = require('react');
var PropTypes        = require('prop-types');
var CreateReactClass = require('create-react-class');

module.exports = React.createClass({
	displayName: 'CheckBoxList',

	propTypes: {
		defaultData: PropTypes.array,
		onChange: PropTypes.func
	},
...
```

Again, our error won’t go away, because we need to replace all instances of `React.createClass` with our new imported object `CreateReactClass`. That should look a little bit like this:

```
'use strict';
var React            = require('react');
var PropTypes        = require('prop-types');
var CreateReactClass = require('create-react-class');

module.exports = CreateReactClass({
	displayName: 'CheckBoxList',

	propTypes: {
		defaultData: PropTypes.array,
		onChange: PropTypes.func
	},
...
```

And thats it! The component no longer errors, and our JavaScript console is nice and clean.

The finished solution looks a little like this:

![The Patched React Multiple Checklist Component In WordPress Gutenberg](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990308181/yVoWx8Cuq.gif)The Patched React Multiple Checklist Component In WordPress Gutenberg

Tutorial Source Code
--------------------

You can [download the source code for the original and patched version of the component on GitHub](https://github.com/wholesomecode/Tutorial-Convert-React-createClass-to-React-Component). The plugin contains a WordPress Gutenberg block that you can use to play around with the code, with three files that you can delete as applicable:

![The GitHub Tutorial Files for the React.createClass patch](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990313994/5JX5uB0Iz.png)The GitHub Tutorial Files for the React.createClass patch

Submitting a Pull Request to the Original Repository
----------------------------------------------------

The great thing about the open source community is that we are free to contribute and improve code. Here is the [Pull Request with the patch that I have just created](https://github.com/sonyan/react-checkbox-list/pull/7) that I have submitted to Sony An.