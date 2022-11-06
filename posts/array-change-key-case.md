---

title: Array Change Key Case
post_date: 2021-12-20T12:15:20.404Z
post_status: publish

---

Array Change Key Case (`array_change_key_case`) is a simple function THAT allows you to make all the keys of your array uppercase or lowercase.

*   [Basic Example](#basic_example)
*   [Composition](#composition)
*   [Features and Specific Examples](#features_and_specific_examples)
*   [Further Reading](#further_reading)

<a id="basic_example"/>Basic Example
-------------

If we setup an array with uppercase keys (line 1), we can use the `array_change_key_case` function to make all of these keys lowercase (line 5).

This gives us the same array back, but with all the keys lowercase.

```
$array = [
    'P' => 42,
	'POST_STATUS' => 'publish',
];
$lower_case_array = array_change_key_case( $array, CASE_LOWER );
print_r( $lower_case_array ); // Output the new array.

/**
 * Output:
 *
 * Array
 * (
 *    [p] => 42
 *    [post_status] => publish
 * )
 **/

```

<a id="composition"/>Composition
-----------

```
array_change_key_case( $array, $case );
```

The function accepts two parameters. The `$array` you want to change, and the `$case` (optional, `CASE_LOWER` by default) that you want the keys to be transformed to. These are as follows:

1.  The `$array` parameter accepts an array, and is the initial array that we we wish to transform.
2.  The `$case` parameter accepts an integer, which is inputted using one of the two PHP predefined constants:
    1.  `CASE_UPPER`
    2.  `CASE_LOWER` (default)

<a id="features_and_specific_examples"/>Features and Specific Examples
------------------------------

### Changing the Case of Object Keys

In PHP you can easily cast Objects as Arrays and vice-versa, so with that logic you can transform the keys of an Object with `array_change_key_case` simply by casting the object to an array and back again.

```
$object              = new StdClass();
$object->ID          = 42;
$object->POST_STATUS = 'publish';
$lower_case_object   = (object) array_change_key_case( (array) $object, CASE_LOWER );
print_r( $lower_case_object ); // Output the new Object.

/**
 * Output:
 * stdClass Object
 * (
 *    [id] => 42
 *    [post_status] => publish
 * )
 **/
```

In the above example we create an object with uppercase keys, and then transform it to use lowercase keys.

<a id="further_reading"/>Further Reading
---------------

For more reading you can checkout the [PHP manual](https://www.php.net/manual/en/function.array-change-key-case.php).