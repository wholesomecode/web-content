---

title: Converting React.createClass to React.Component
post_date: 2019-01-14T18:18:34.000Z
post_status: publish

---

In my [last article I talked about patching older versions of React to work in a modern environment](https://wholesomecode.ltd/blog/broken-react-createclass-component-lets-fix-it/). In this article I go one further and give a step by step guide to upgrading a classic React component to a modern one, by switching out the `React.createClass` way of doing things to the modern and preferred `React.Component` method.

The files we need will be in the ‘Original’ and the ‘Final’ folders of the components directory in the accompanying GitHub solution.

[Get Tutorial Files from GitHub](https://github.com/mattwatsoncodes/Tutorial-Convert-React-createClass-to-React-Component)

Again working with the component `[react-checkbox-list](https://github.com/sonyan/react-checkbox-list)` [by Sony An](https://github.com/sonyan/react-checkbox-list) (available as [`react-checkbox-list` on npm](https://www.npmjs.com/package/react-checkbox-list)). This guide shows you step by step how to replace the structure and methods of a classic `React.createClass` component to a modern `React.Component`.

Starting with the `.jsx` file in the `react-checkbox-list` solution, we will firstly delete the `.js` file and rename this to `.js` as `.jsx`files no longer need to be named differently.

That gives us the following starting code:

```
/** @jsx React.DOM */
'use strict';
var React = require('react');

module.exports = React.createClass({
	displayName: 'CheckBoxList',

	propTypes: {
		defaultData: React.PropTypes.array,
		onChange: React.PropTypes.func
	},

	getInitialState: function() {
		return {
			data: this.props.defaultData || []
		};
	},

	handleItemChange: function(e) {
		var selectedValues = [],
			newData = [];

		this.state.data.forEach(function(item) {
			if(item.value === e.target.value) {
				item.checked = e.target.checked;
			}
			if(item.checked) {
				selectedValues.push(item.value);
			}
			newData.push(item);
		});

		this.setState({data: newData});

		if(this.props.onChange) {
			this.props.onChange(selectedValues);
		}
	},

	// uncheck all items in the list
	reset: function() {
		var newData = [];
		this.state.data.forEach(function(item) {
			item.checked = false;
			newData.push(item);
		});

		this.setState({data: newData});
	},
	
	checkAll: function() {
		var newData = [];
		this.state.data.forEach(function(item) {
			item.checked = true;
			newData.push(item);
		});

		this.setState({data: newData});
	},
	
	render: function() {
		var options;

		options = this.state.data.map(function(item, index) {
			return (
				<div key={'chk-' + index} className="checkbox">
					<label>
						<input
							type="checkbox"
							value={item.value}
							onChange={this.handleItemChange}
							checked={item.checked ? true : false} /> {item.label}
					</label>
				</div>
			);
		}.bind(this));

		return (
			<div>
				{options}
			</div>
		);
	}
});
```

Converting the Component
------------------------

When trying to load this code, the first error we get is `Uncaught Error: Module build failed: SyntaxError: The @jsx React.DOM pragma has been deprecated as of React 0.12`

![Uncaught Error: Module build failed: SyntaxError: The @jsx React.DOM pragma has been deprecated as of React 0.12](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990262858/TAywXCq1_.png)Uncaught Error: Module build failed: SyntaxError: The @jsx React.DOM pragma has been deprecated as of React 0.12

It is simple enough to correct this. Just remove the line `/** @jsx React.DOM */` from the top of the document.

We now get the error `Uncaught TypeError: Cannot read property 'array' of undefined` the same as in the [patching](https://wholesomecode.ltd/blog/broken-react-createclass-component-lets-fix-it/) `[React.createClass](https://wholesomecode.ltd/blog/broken-react-createclass-component-lets-fix-it/)`[tutorial](https://wholesomecode.ltd/blog/broken-react-createclass-component-lets-fix-it/). This is because React.propTypes was deprecated in React version 15.50, so as per that tutorial, go ahead and install the PropTypes package with the command via npm. We will import this into our package later.

```
npm install --save prop-types
```

What follows now is a complete overhaul of the codebase, so we cannot refresh and fix an error as we may have done previously, so we won’t know if it has worked until the very end. Buckle up!

Lets start by replacing the `React.createClass` function by declaring a new `React.Component`. Alter our code so it looks like the following:

```
'use strict';
import React from 'react';

class CheckBoxList extends React.Component {
...
}
```

Note that we have done a few things here:

*   Removed the opening JSX comment
*   Altered the declaration type from `var React =...` to `import React from 'react';` this is the modern way of making declarations in React.
*   We now declare a new class of `CheckBoxList`, instead of exporting a function.
*   Due to the syntax of the object now being enclosed in parenthesis `{...}`we need to drop the closing `);`

However this new class is now missing a way for it to be exported so other components can use it, so let’s add an export declaration at the bottom of that code.

```
'use strict';
import React from 'react';

class CheckBoxList extends React.Component {
...
}

export default CheckBoxList;
```

In our original code, the first line of code in the function was `displayName: 'CheckBoxList',`our export now handles this, so we can remove that code completely.

The next line down declares our `propTypes` these now sit outside of the class, and need the `PropTypes` dependancy we added via npm. Lets add that into our imports, and write the PropTypes like so:

```
'use strict';
import React from 'react';
import PropTypes from 'prop-types';

class CheckBoxList extends React.Component {
...
}

CheckBoxList.propTypes = {
	defaultData: PropTypes.array,
	onChange: PropTypes.func,
};

export default CheckBoxList;
```

Next up the state is declared via the method `getInitialState` with `React.Component` we set our initial state with a constructor. Add the following code into our build:

```
...
class CheckBoxList extends React.Component {
	constructor( props ) {
		super( props );
		this.state = {
			data: [],
		}
	}
...
}
...

```

There we go, that sets up the state of the component, but hang on, we havent actually told it where to get its state from. Thats where `componentWillMount` comes in handy.

```
...
class CheckBoxList extends React.Component {
	constructor( props ) {
		super( props );
		this.state = {
			data: [],
		}
	}

	componentWillMount() {
		this.setState({
			data: this.props.defaultData,
		});
	}
...
}
...
```

A key thing to note is that the inner methods of `React.Component` do not end in commas (`,`), so make sure that any methods you add to the component do not end in a comma!

Next up, let’s add the render function back in. This is probably the easiest part, it is almost the same, with the key difference that we change `render: function() {` to just `render() {`.

```
...
class CheckBoxList extends React.Component {
...
	render() {
		var options;

		options = this.state.data.map(function(item, index) {
			return (
				<div key={'chk-' + index} className="checkbox">
					<label>
						<input
							type="checkbox"
							value={item.value}
							onChange={this.handleItemChange}
							checked={item.checked ? true : false} /> {item.label}
					</label>
				</div>
			);
		}.bind(this));

		return (
			<div>
				{options}
			</div>
		);
	}
...
}
...

```

For the purposes of keeping this tutorial to the point, I will not convert the two methods `reset` and `checkAll` however the concepts used for the port of the method `handleItemChange` apple to both, so feel free to give them a shot yourself.

To migrate the `handleItemChange` method, we firstly need to remove the closing comma (`,`) and alter the function declaration type from `handleItemChange: function(e) {`to `handleItemChange( e ) {` making sure we still pass `e` in as the event parameter.

Let’s add that above our render method.

```
...
class CheckBoxList extends React.Component {
...
	handleItemChange( e ) {
		var selectedValues = [],
			newData = [];

		this.state.data.forEach(function(item) {
			if(item.value == e.target.value) {
				item.checked = e.target.checked;
			}
			if(item.checked) {
				selectedValues.push(item.value);
			}
			newData.push(item);
		});

		this.setState( {data: newData} );

		if(this.props.onChange) {
			this.props.onChange(selectedValues);
		}
	}
...
}
...

```

Our component will now render, however it will not be functional. When you try to click a on a checkbox you will get the error `Uncaught TypeError: Cannot read property 'state' of undefined`.

![Uncaught TypeError: Cannot read property 'state' of undefined](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990269629/B4ixwD1Us.gif)Uncaught TypeError: Cannot read property ‘state’ of undefined

This is because in our `handleItemChange` method, where we try to access the state, `this` is undefined. To fix this we need to bind our function to `this` by adding the following line into our constructor: `this.handleItemChange = this.handleItemChange.bind( this );`.

```
...
class CheckBoxList extends React.Component {
	constructor( props ) {
		super( props );
		this.state = {
			data: [],
		}
		this.handleItemChange = this.handleItemChange.bind( this );
	}
...
}
...
```

And there we have it, our newly converted React component in action.

![React.createClass to React.Component](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990278843/_wEr2hmTc.gif)React.createClass to React.Component

Tutorial Source Code
--------------------

You can [download the source code for the original and final version of the component on GitHub](https://github.com/wholesomecode/Tutorial-Convert-React-createClass-to-React-Component). The plugin contains a WordPress Gutenberg block that you can use to play around with the code, with three files that you can delete as applicable:

![The GitHub Tutorial Files for the React.createClass patch](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990284534/XWN_yphHm.png)The GitHub Tutorial Files for the React.createClass to React.Component Conversion