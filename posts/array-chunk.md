---

title: Array Chunk
post_date: 2021-12-20T12:14:27.961Z
post_status: publish

---

Using array chunk (`array_chunk`) we can easily split an array into a multidimensional array (an array of arrays).

The array chunk function could be used to break an array into smaller parts, perhaps to make it easier to process.

*   [Basic Example](#basic_example)
*   [Composition](#composition)
*   [Features and Specific Examples](#features_and_specific_examples)
*   [Further Reading](#further_reading)

<a id="basic_example"/>Basic Example
-------------

Assuming we setup an array as per the below code example (`$array` on line 1), we can chunk this array into a new multidimensional array using the `array_chunk` function on line 9.

Running this code would result in the multidimensional array on line 15.

```
$array = [
    'Post 0', 
    'Post 1', 
    'Post 2', 
    'Post 3', 
    'Post 4', 
    'Post 5',
];
$chunked_array = array_chunk( $array, 2 );
print_r( $chunked_array ); // Output the new array.

/**
 * Output:
 *
 * Array
 * (
 *   [0] => Array
 *       (
 *          [0] => Post 0
 *          [1] => Post 1
 *       )
 *   [1] => Array
 *       (
 *          [0] => Post 2
 *          [1] => Post 3
 *       )
 *   [2] => Array
 *       (
 *           [0] => Post 4
 *           [1] => Post 5
 *       )
 *)
 **/
```

<a id="composition"/>Composition
-----------

```
$chunked_array = array_chunk( $array, $chunk, $preserve_keys );
```

The `array_chunk` function accepts three parameters, `$array`, `$chunk` and `$preserve_keys` (optional, false by default). These are as follows:

1.  The `$array` parameter accepts an array, and is the initial array that we we wish to transform.
2.  The `$chunk` accepts an integer, and is the number that we use to split the `$array` into parts of this value.
3.  The optional `$preserve_keys` parameter accepts a boolean, and will preserve the keys of the array if `true`.

<a id="features_and_specific_examples"/>Features and Specific Examples
------------------------------

### Preserve Keys

Exploring the `$preserve_keys` parameter, our previous example starts the index of each nested array at 0. However if we were to pass `true` into this parameter, the original index of initial array would be kept intact:

```
$array = [
    'Post 0', 
    'Post 1', 
    'Post 2', 
    'Post 3', 
    'Post 4', 
    'Post 5',
];
$chunked_array = array_chunk( $array, 2 , true );
print_r( $chunked_array ); // Output the new array.

/**
 * Output:
 *
 * Array
 * (
 *   [0] => Array
 *       (
 *          [0] => Post 0
 *          [1] => Post 1
 *       )
 *   [1] => Array
 *       (
 *          [2] => Post 2
 *          [3] => Post 3
 *       )
 *   [2] => Array
 *       (
 *           [4] => Post 4
 *           [5] => Post 5
 *       )
 *)
 **/

```

In the output the keys are now sequential, as they would be in the original array (_0, 1, 2, 3…_).

### Preserve Keys when the Original Array has Named Keys

If our original array has named keys, then we can preserve the keys to maintain these in our chunked array.

The following example has a bit of fun with this. Let’s say we have somehow got an array with some Post ID’s and Post Names mixed together.

We could use `array_chunk` with the `$preserve_keys` parameter set to `true` to split these into the individual posts.

```
$array = [
    'id_0'   => '10', 
    'name_0' => 'Post 1', 
    'id_1'   => '20', 
    'name_1' => 'Post 2', 
    'id_2'   => '30', 
    'name_2' => 'Post 3',
];
$chunked_array = array_chunk( $array, 2 , true);
print_r( $chunked_array ); // Output the new array.

/**
 * Output:
 *
 * Array
 * (
 *   [0] => Array
 *       (
 *          [id_0] => 10
 *          [name_0] => Post 1
 *       )
 *   [1] => Array
 *       (
 *          [id_1] => 20
 *          [name_1] => Post 2
 *       )
 *   [2] => Array
 *       (
 *           [id_2] => 30
 *           [name_2] => Post 3
 *       )
 *)
 **/

```

Still having fun with this array, let’s imagine how we could loop through the `$chunked_array`, and output only the names.

```
foreach( $chunked_array as $key => $nested_array ) {
    echo $nested_array[ 'name_' . $key ] . PHP_EOL;
}

/**
 * Output: 
 *
 * Post 1
 * Post 2
 * Post 3
 **/

```

### Chunking a Large Array for Processing.

If we have a very large array, we can use `array_chunk` to break it into smaller chunks that we could then store separately. Let’s see how we might do this in WordPress:

```
$original_array = [...]; // Let's imagine this is a very large array.
$post_id        = get_the_ID();
$chunked_array  = array_chunk( $original_array, 10, true );

// Let's store it as smaller parts.
foreach( $chunked_array as $key => $array ) {
    update_post_meta( 
        $post_id,
        '_wc_chunked_array_partial_' . $post_id,
        $array
    );
}

```

<a id="further_reading"/>Further Reading
---------------

For more reading you can checkout the [PHP manual](https://www.php.net/manual/en/function.array-chunk.php).