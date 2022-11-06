---

title: Using the WordPress Block Editor (Gutenberg) with the REST API
post_date: 2020-05-06T11:54:28.000Z
post_status: publish

---

In my previous article I talked about [how to loop through posts and custom post types within Gutenberg](https://wholesomecode.ltd/articles/wp_query-and-the-wordpress-block-editor-gutenberg/). In this post I talk about receiving and using custom data within Gutenberg from WordPress REST API endpoints.

In this example we are going to:

*   [Create a REST API endpoint](#creating-a-rest-api-endpoint)
*   [Register a Redux like data store using the Gutenberg API](#registereing-a-data-store)
*   [Consume the custom data within a Gutenberg Block](#using-the-data-with-a-block)

**REST API is not always the right path.**

*We use `get_option` in the REST API as an example only. If you are only require access to an option, and you do not intend to change its state, instead you can use `wp_localize_script` to pass the options into JavaScript.  
  
If you want to access an option AND change its state, I would recommend reading my [guide on storing settings and data in the option table with Gutenberg using the Settings API](https://wholesomecode.ltd/guides/options-settings-data-wordpress-gutenberg/).*

<a id="creating-a-rest-api-endpoint"/>Creating a REST API Endpoint
----------------------------

We are going to create a simple REST API endpoint that returns the value of a WordPress option (using `get_option`).

```
function wcltd_get_option() {
	register_rest_route(
		'wcltd/wholesome-plugin/v1',
		'/get/option/(?P<option>([A-Za-z0-9\_])+)/',
		array(
			'callback'            => function ( $request ) {
				$option = isset( $request['option'] ) ? esc_attr( $request['option'] ) : null;
				$value  = get_option( $option, '' );
				return $value;
			},
			'methods'             => 'GET',
			// 'permission_callback' => function () {
			// 	return current_user_can( 'edit_posts' );
			// },
		)
	);
}
add_action( 'rest_api_init', 'wcltd_get_option' );
```

After you have created this endpoint, be sure to **resave your permalinks** so that it kicks in. Note that there are better ways to do this (such as using `flush_rewrite_rules()` on the `register_activation_hook`), but for this example a quick resave will suffice.

Now when we pass the URL `/wp-json/wcltd/wholesome-plugin/v1/get/option/test/` (after your own domain obviously) into our browser it will return a value, which either be an empty string `''` or the value of the option if set.

[Our endpoint, returning an empty string](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990082853/S6umfXuzNF.png)Our endpoint, returning an empty string

Note that I have purposefully commented out lines 12-14, which would ensure that only someone that has `edit_post` rights can run the code if left in. However with this commented out we cannot test the code in our browser.

Try adding some code to give your option a value, and see what it returns. For example if I were to run the following code before my function:

```
update_option( 'wcltd_example_option', 'Hello World' );
```

Now if I hit the endpoint `/wp-json/wcltd/wholesome-plugin/v1/get/option/wcltd_example_option/` I will get the following output:

![Our endpoint, returning some data.](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990086793/hUK2gA77O.png)Our endpoint, returning some data.

<a id="registereing-a-data-store"/>Registering a Data Store
------------------------

Now that we have a REST API endpoint, we can create a datastore with `registerStore`.

`registerStore` provides a Redux-like datastore, that enables us to manage _state_ across our application. It also supports resolvers, which enable us to populate the state from an external source (such as our REST API).

You can [read more about REACT state in the official documentation](https://reactjs.org/docs/state-and-lifecycle.html).

Here is how we can create a store (as `store.js` in our project that will allow us to pull data from our REST API endpoint.

### Dependancies

We will need to extract `apiFetch` and `registerStore` from our WordPress dependancies.

```
import apiFetch from '@wordpress/api-fetch';
import { registerStore } from '@wordpress/data';
```

### Selectors

The first thing we want to do is create a selector function, in an object called ‘selectors’.

All this does is extract the ‘option’ from the state and return it.

```
const selectors = {
    getOption( state, optionKey ) {
        const { option } = state;
        return option;
    },
};
```

### Resolvers

The resolver is a side-effect of the selector, and does a little more.

Here we create our function that takes our optionKey and passes a REST API endpoint to our actions (which we will define next).

This then sets the option, with another call to our actions.

```
const resolvers = {
    *getOption( optionKey ) {
        const option = yield actions.getOption(
            '/wcltd/wholesome-plugin/v1/get/option/' + optionKey + '/',
        );
        return actions.setOption( option );
    },
};
```

### Actions

The two functions in an object named `actions` that we called earlier are defined here.

They indicate which control or reducer we are going to use to set or get the value.

```
const actions = {
	setOption( option ) {
		return {
			type: 'SET_OPTION',
			option,
		};
	},
	getOption( path ) {
		return {
			type: 'GET_OPTION',
			path,
		};
	},
};
```

### Controls

In our controls we have `GET_OPTION` which makes the API call using `apiFetch` and the path that we defined earlier.

```
const controls = {
    GET_OPTION( action ) {
        return apiFetch( { path: action.path } );
    },
};
```

### Reducer

In our reducer function we have `SET_OPTION` which takes our value (in this case `option`, and sets its state.

```
function reducer( state = { option: '' }, action ) {
    switch ( action.type ) {
        case 'SET_OPTION':
            return {
                ...state,
                option: action.option,
            };
    }
    return state;
};
```

### Registering the Store

Finally, we register a store, give it a namespace so we can access it, and pass in an object of all the above objects and functions.

```
const store = registerStore(
    'wcltd/wholesome-plugin/data',
    {
        actions,
        controls,
        reducer,
        resolvers,
        selectors,
    }
);

export default store;
```

export default store;

<a id="using-the-data-with-a-block"/>Using the Data with a Block
---------------------------

Lets expand the [block created by the `@wordpress/create-block` script, that I wrote about previously](https://wholesomecode.ltd/articles/an-overview-of-the-wordpress-create-block-script/) to enable us to connect to a REST API endpoint.

Create a file in the `/src` folder called `store.js` and copy all of the above code into it:

```
import apiFetch from '@wordpress/api-fetch';
import { registerStore } from '@wordpress/data';

/**
 * Selectors
 */
const selectors = {
    getOption( state, optionKey ) {
        const { option } = state;
        return option;
    },
};

/**
 * Resolvers
 */
const resolvers = {
    *getOption( optionKey ) {
        const option = yield actions.getOption(
            '/wcltd/wholesome-plugin/v1/get/option/' + optionKey + '/',
        );
        return actions.setOption( option );
    },
};

/**
 * Actions
 */
const actions = {
	setOption( option ) {
		return {
			type: 'SET_OPTION',
			option,
		};
	},
	getOption( path ) {
		return {
			type: 'GET_OPTION',
			path,
		};
	},
};

/**
 * Controls
 */
const controls = {
    GET_OPTION( action ) {
        return apiFetch( { path: action.path } );
    },
};

/**
 * Reducer
 *
 * @param {object} state
 * @param {string} action
 */
function reducer( state = { option: '' }, action ) {
    switch ( action.type ) {
        case 'SET_OPTION':
            return {
                ...state,
                option: action.option,
            };
    }
    return state;
};

/**
 * Register Store
 */
const store = registerStore(
    'wcltd/wholesome-plugin/data',
    {
        actions,
        controls,
        reducer,
        resolvers,
        selectors,
    }
);

export default store;

```

Now edit our `/src/index.js` file to include the `store.js` file:

```
/**
 * Internal dependencies
 */
import store from './store';
import Edit from './edit';
import save from './save';
```

We also need to ensure that we have included \`withSelect\` at the top of our file:

```
import { withSelect } from '@wordpress/data';
```

Then let’s wrap our Edit module with `withSelect` to make the REST API call to our option, like so:

```
/**
 * @see ./edit.js
 */
edit: withSelect( ( select ) => {
	const option = select( 'wcltd/wholesome-plugin/data' ).getOption( 'wcltd_example_option' );
	return {
		option,
	};
} )( Edit ),
```

Now we can access `option` in our props within `edit.js`, like so:

```
export default function Edit( { className, option } ) {
	return (
		<p className={ className }>
			{ option }
		</p>
	);
}
```

Finally, let’s view our block in the editor and see what we get:

[The output in the block editor.](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990090843/2uT6sdoTo.png)The output in the block editor.