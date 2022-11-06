---

title: Dynamic Variable Names in PHP
post_date: 2018-08-03T10:40:08.000Z
post_status: publish

---

A question I sometimes get asked is can you dynamically set the name of a variable in PHP.

For example someone may have a loop, and they want the variables in that loop to have unique names.

The answer is, yes this can be done! I’ll tell you how, but I’ll also tell you that there is a much better way to do this.

You can of course use a double dollar: `$$custom_variable` or you can use a good old curly brace: `${$custom_variable}`

To implement this, you would use a loop like so:

```
$custom_variable_array = array( 'name_1', 'name_2', 'name_3' );

foreach ( $custom_variable_array as $custom_variable ) {

    // Method 1
    $$custom_variable = 'Some Value';
    
    // Method 2
    ${$custom_variable} = 'Some Other Value';

    // Of course as both of these have the same dynamic variable name,
    // the output will be 'Some Other Value' each time
    // echo $$custom_variable;
}

// I can also now access the variable with the dynamically assigned name 
// by its full name. So the variable $name_1 would have been set by my 
// loop above.
echo $name_1;
```

Pretty cool right? PHP is fun!

A Better Way
------------

However a more practical way to do this would be to use an array to store your variable names like so:

```
$custom_variable_array = array( 'name_1', 'name_2', 'name_3' );
$custom_storage_array  = array();

foreach ( $custom_variable_array as $custom_variable ) {

    // Use the variable to create a named index
    $custom_storage_array[ $custom_variable ] = 'Some Value';

    // This will output 'Some value';
    // echo $custom_storage_array[ $custom_variable ];
}

// I can also now access the array and all of its indexes, including the ones we set above.
echo $custom_storage_array[ 'name_1 ];
```

Isn’t that better?