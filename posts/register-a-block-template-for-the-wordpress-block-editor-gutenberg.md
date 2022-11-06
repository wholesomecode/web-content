---

title: Register a Block Template for the WordPress Block Editor (Gutenberg)
post_date: 2021-03-03T13:10:12.000Z
post_status: publish

---

In my previous guide I explored [the power of block patterns](https://wholesomecode.ltd/guides/register-block-pattern-wordpress-gutenberg/). Block templates are similar, but instead of relying on your client to insert block patterns into the editor, it gives them a full layout as soon as they insert a new post.

Prerequisites
------------------

*   WordPress installation
*   Code Editor

Create your Block Template
-----------------------------------

In this example I am going to insert the same [block pattern I created in my previous guide](https://wholesomecode.ltd/guides/register-block-pattern-wordpress-gutenberg/), but as a block template.

In that guide we copied the HTML of the editor output. In this guide, we are going to do something slightly different, and build up an array of block namespaces, properties and content.

In its simplest form a block template looks a little like this:

```
$block_template = [
  [ 'core/heading', [ 'level'   => 2, 'content' => 'Example Block Template' ] ],
  [ 'core/paragraph', [ 'content' => 'Lorem ipsum dolor sit amet labore cras venenatis.' ] ],
];
```

As you can see, it is an array, that contains blocks in the following pattern:

*   Block Namespace
*   Block Attributes

We want to use nested blocks however, so we are going to pass in a third parameter to this block array, which is the innerBlocks.

**Just want the block placement?**

*Note you can leave the attributes empty, to just provide the blocks with no set content.*

The array to create the same block we created in the block pattern guide, we need a more complex array, like so:

```
$block_template = [
  [
    'core/group',
    [],
    [
      [
        'core/heading',
        [
          'level'   => 2,
          'content' => 'Example Block Template',
        ]
      ],
      [
        'core/paragraph',
        [
          'content' => 'Lorem ipsum dolor sit amet labore cras venenatis.',
        ]
      ],
      [
        'core/columns',
        [],
        [
          [
            'core/column',
            [],
            [
              [
                'core/heading',
                [
                  'level'   => 3,
                  'content' => 'Sub Heading 1',
                ]
              ],
              [
                'core/paragraph',
                [
                  'content' => 'Lorem ipsum dolor sit amet id erat aliquet diam ullamcorper tempus massa eleifend vivamus.',
                ]
              ],
            ]
          ],
          [
            'core/column',
            [],
            [
              [
                'core/heading',
                [
                  'level'   => 3,
                  'content' => 'Sub Heading 2',
                ]
              ],
              [
                'core/paragraph',
                [
                  'content' => 'Morbi augue cursus quam pulvinar eget volutpat suspendisse dictumst mattis id.',
                ]
              ],
            ]
          ],
        ],
      ],
    ]
  ]
];
```

Register the Block Template
--------------------------------

### Registering a Block Template for an Existing Post Type

To register a block template for an Existing Post Type (such as the `post` Post Type) you can use the code below to override the post type object.

```
function register_block_template() {
	// ... Define $block_template using an array of blocks.

	$post_type_object                = get_post_type_object( 'post' );
	$post_type_object->template      = $block_template;
    $post_type_object->template_lock = 'all';
}
add_action( 'init', 'register_block_template' );
```

This code simply sets the template and (optionally) the `[template_lock](https://wholesomecode.ltd/#template-lock)` type.

The _full code_ would look like this:

```
function register_block_template() {
	$block_template = [
      [
        'core/group',
        [],
        [
          [
            'core/heading',
            [
              'level'   => 2,
              'content' => 'Example Block Template',
            ]
          ],
          [
            'core/paragraph',
            [
              'content' => 'Lorem ipsum dolor sit amet labore cras venenatis.',
            ]
          ],
          [
            'core/columns',
            [],
            [
              [
                'core/column',
                [],
                [
                  [
                    'core/heading',
                    [
                      'level'   => 3,
                      'content' => 'Sub Heading 1',
                    ]
                  ],
                  [
                    'core/paragraph',
                    [
                      'content' => 'Lorem ipsum dolor sit amet id erat aliquet diam ullamcorper tempus massa eleifend vivamus.',
                    ]
                  ],
                ]
              ],
              [
                'core/column',
                [],
                [
                  [
                    'core/heading',
                    [
                      'level'   => 3,
                      'content' => 'Sub Heading 2',
                    ]
                  ],
                  [
                    'core/paragraph',
                    [
                      'content' => 'Morbi augue cursus quam pulvinar eget volutpat suspendisse dictumst mattis id.',
                    ]
                  ],
                ]
              ],
            ],
          ],
        ]
      ]
    ];

	$post_type_object                = get_post_type_object( 'post' );
	$post_type_object->template      = $block_template;
    $post_type_object->template_lock = 'all';
}
add_action( 'init', 'register_block_template' );
```

### Registering a Block Template for a New Post Type

If you are registering a post type, you can pass the template into the `template` argument of `register_post_type`.

You can also pass in the `[template_lock](https://wholesomecode.ltd/#template-lock)` argument into `register_post_type`.

### Template Lock

The `template_lock` argument can do several things depending on the argument that is passed to it. These are listed in the [Gutenberg development guide](https://developer.wordpress.org/block-editor/developers/block-api/block-templates/) as:

*   `all` — prevents all operations. It is not possible to insert new blocks, move existing blocks, or delete blocks.
*   `insert` — prevents inserting or removing blocks, but allows moving existing blocks.

**Template lock does not work on nested blocks**

*The template lock only works on top level blocks. Use this to your advantage by creating a layout that has fixed headings, images a paragraphs, and a group area for your client to insert whatever content they like.  *
  
You can also [create a custom nested block template with InnerBlocks](https://wholesomecode.ltd/guides/template-innerblocks-wordpress-gutenberg/).

You can also [limit the blocks that you are allowed to insert into certain post types](https://wholesomecode.ltd/#allowed-block-types).

Use the Block Template
---------------------------

To use the block template just create a new post (or custom post type depending how you configured things). The template will be inserted, and you will now have fixed content areas on your page.

![Inserting a Post shows the block template](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989824705/kKmxbbDfB.png)Inserting a Post shows the block template

Extra: Allowed Block Types
----------------------------

You can limit the blocks that are available for various post types using the following code.

```
function filter_allowed_block_types( $allowed_block_types, $post ) {

  if ( $post->post_type !== 'post' ) {
    return $allowed_block_types;
  }
  
  $allowed_block_types = [
    'core/column',
    'core/columns',
    'core/group',
    'core/heading',
    'core/paragraph',
  ];

  return $allowed_block_types;
}
add_filter( 'allowed_block_types', 'filter_allowed_block_types', 10, 2 );
```

The code above limits the blocks allowed in the `post` post type to the following:

*   Column (`core/column`)
*   Columns (`core/column`s)
*   Group (`core/group`)
*   Heading (`core/heading`)
*   Paragraph (`core/paragraph`)

Extra: Taking it Further
--------------------------

*   Need something more flexible? Try [Registering a Block Pattern](https://wholesomecode.ltd/guides/register-block-pattern-wordpress-gutenberg/) instead
*   Need something that applies to child blocks? Try [registering an InnerBlocks Template](https://wholesomecode.ltd/guides/template-innerblocks-wordpress-gutenberg/)