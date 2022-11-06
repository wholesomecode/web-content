---

title: Compile SCSS and JavaScript with Grunt
post_date: 2013-12-30T14:03:27.000Z
post_status: publish

---

According to its ‘read me’ file, [Grunt](http://gruntjs.com/) is a task based command line build tool for JavaScript projects. However it is much much more than this.

At the time of writing this, Grunt is a fixed staple in my front end workflow. I use it for compressing images, creating documentation, auto-compiling [Modernizer](http://modernizr.com/), and a whole host of other purposes, but of course its pride of place is compiling [SCSS](http://sass-lang.com/) and JavaScript.

This tutorial was written when I first discovered Grunt, and it is written from the perspective of a complete beginner.

Before we jump in, it is worth noting that this guide is aimed at Mac OSX (MacOS) users. Grunt relies on you using the command line. If you are not comfortable with this then don’t worry, I’ll be **very** gentle.

Let’s begin
-----------

In the grunt build I create in this tutorial I will do the following:

*   Use [Compass](http://compass-style.org/) to compile and compress my SCSS files into CSS
*   Compile and compress my JavaScript files
*   Use [JSHint](http://jshint.com/) to warn me of JavaScript errors
*   Do all of the above automatically whenever a file is changed

Installing grunt
----------------

First things first, grunt is built upon [node.js](http://nodejs.org/) so, if you haven’t already, go ahead and install that. Don’t worry terminal newbies, there is a nice [installer file](http://nodejs.org/download/) available.

Now that you have node.js installed you can run the Node Package Manager (npm) terminal command to install grunt. Open your terminal window and type the following:

```
sudo npm install -g grunt-cli
``` 

The command `sudo` lets you **do** something as a **s**uper **u**ser. Typing this in means that you will be prompted for your password to continue (**s**uper **u**ser **do**).

The next part is the `npm` command which runs node package manager.

The part `install -g` will install the package globally, and finally `grunt-cli` is the package name for grunt (Grunt Command Line Interface).

Once you have run this command you should see something similar to the following happen in your terminal window (**note:** I’m using [iTerm 2](http://www.iterm2.com/) as my terminal program).

![Grunt being installed](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990840035/Tc8UQp5ZJ.png)Grunt being installed

So now Grunt is now all is installed and all is well with the world right? Well, not quite. Unless you want the dreaded error: ‘**Fatal error: Unable to find local grunt**‘, you will need to use the terminal to navigate to the root of your project (for example: `cd path/to/your/project`), and enter the following command:

```
sudo npm install grunt
```

This will put the relevant files that are needed to run grunt commands into your project, creating a new ‘node\_modules’ folder in the root of your project.

Working with Grunt
------------------

To get started we need to add two files to our project in order for grunt to do it’s business. These are:

*   `package.json`
*   `Gruntfile.js`

Grunt uses plugins to perform certain tasks, such as the ones I mentioned at the start of this tutorial.

The first file; `package.json` is a file that tells grunt what plugins to install, and what version of grunt we are using. `gruntfile.js` contains the configuration for each plugin.

### `package.json`

Create a file in the root of your solution called `package.json`. In it, to get you started, add the following code:

```
{
    "name": "",
    "version": "0.0.1",
    "devDependencies": {
       "grunt": "~0.4.2"
    }
}
```

The **`name`** will be the name of the project that we wish to create. For my example I am building a WordPress theme for my site, so I will call it `matt-watson-theme`.

The **`version`** is the version of the project. As this is my first draft I have set it to 0.0.1.

The **`devDependencies`** object is essentially a list of dependencies. The first one being the version of grunt we are using. This tutorial was written using Grunt v0.4.2 so that is the version I have defined in this file.

In this **`devDependencies`** object we will also add the following plugin references:

*   [`grunt-contrib-compass`](https://github.com/gruntjs/grunt-contrib-compass) – to allow us to use SCSS (including concatenation and compression)
*   [`grunt-contrib-jshint`](https://github.com/gruntjs/grunt-contrib-jshint) – to check the syntax of our JavaScript
*   [`grunt-contrib-concat`](https://github.com/gruntjs/grunt-contrib-concat) – to allow us to concatenate JavaScript
*   [`grunt-contrib-uglify`](https://github.com/gruntjs/grunt-contrib-uglify) – to compress our JavaScript
*   [`grunt-contrib-watch`](https://github.com/gruntjs/grunt-contrib-watch) – to allow grunt to run tasks on certain actions.

The resulting `package.json` will look like the following (**note:** all version numbers written in the file below are the latest at time that this post was published):

```
{
    "name": "matt-watson-theme",
    "version": "0.0.2",
    "devDependencies": {
       "grunt": "~0.4.2",
       "grunt-contrib-compass": "~0.7.0",
       "grunt-contrib-jshint":  "~0.8.0",
       "grunt-contrib-concat":  "~0.3.0",
       "grunt-contrib-uglify":  "~0.2.7",
       "grunt-contrib-watch":   "~0.5.3"
    }
}
```

Before we move on, and before we can create the Gruntfile itself, we can run the following command in our terminal:

```
sudo npm install
```

This will automatically install the dependencies we have defined in our project and put them in the ‘node\_modules’ folder at the root of our project.

### `gruntfile.js`

On its own, `package.json` file isn’t going too do much. We need to define our Gruntfile. This file references `package.json`, and goes on to do the hard work.

In the root of your project create a file called `gruntfile.js`. As a starter add the following code:

```
'use strict';
module.exports = function (grunt) {
    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json')
    });
};
```

This simply tells Grunt where the `package.json` file resides.

#### `grunt-contrib-concat`

Now lets add some configuration to enable the concatenation of JavaScript files into a `main.js` file.

The following code will configure the `grunt-contrib-concat` plugin so that the two JavaScript files ‘`assets/js/modules/module1.js`‘ and ‘`assets/js/modules/modules.js`‘ compile into ‘`assets/js/main.js`‘. When they are concatenated the separator will be a new line (`'rn'`):

```
concat: {
    options: {
        separator: 'rn'
    },
    dist: {
        src: \['assets/js/modules/module1.js', 'assets/js/modules/module2.js'\],
        dest: 'assets/js/main.js'
    }
}
```

We are almost there, but before we can put it all together we need to tell our Gruntfile to load the relevant plugin, and register a task to run by default. We can do that with this code

```
grunt.loadNpmTasks( 'grunt-contrib-concat' );
grunt.registerTask( 'default', 'concat' );
```

Now let’s put our three blocks of code together:

```
'use strict';
module.exports = function (grunt) {
    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),

        concat: {
            options: {
                separator: 'rn'
            },
            dist: {
                src: \['assets/js/modules/module1.js', 'assets/js/modules/module2.js'\],
                dest: 'assets/js/main.js'
            }
        }

    });

    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.registerTask('default', 'concat');
};
```

As you can see the configuration object ‘`concat`‘ is placed into the `grunt.initConfig` object, and the task registration code goes at the bottom before the `module.exports` function ends.

To run the code we have added so far, open your terminal window, navigate to your project folder and type:

```
grunt
```

If all is well, you should get the following output in your terminal window:

![Using Grunt to Concatinate JavaScript](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990843698/gFQSBKZ44.png)Using Grunt to Concatenate JavaScript

The code that was in ‘`assets/js/modules/module1.js`‘ and ‘`assets/js/modules/modules.js`‘ can now be found in ‘`assets/js/main.js`‘, separated with a new line.

#### `grunt-contrib-uglify`

So we have concatenated our code, lets compress (uglify) it too to save on precious bandwidth.

To do this, we need to add the following code into our grunt file:

```
uglify: {
    options: {
        banner: '/\*! <%= pkg.name %> <%= grunt.template.today("dd-mm-yyyy") %> \*/n'
    },
    dist: {
         files: {
              'assets/js/main.min.js': \['<%= concat.dist.dest %>'\]
     }
    }
}
```

The code above outputs a banner at the top of your destination file using the package name (taken from the `package.json` file) and todays date. It then uses the output of the concat object (`concat.dist.dest`), minifies it and places it in the destination file which here has been defined as: `assets/js/main.min.js`.

We also need to change the part of the script that loads our plugins and sets the tasks. Change this to the following:

```
grunt.loadNpmTasks('grunt-contrib-concat');
grunt.loadNpmTasks('grunt-contrib-uglify');
grunt.registerTask('default', \['concat', 'uglify'\]);
```

You will note that the `registerTask` element now contains an array as its second argument, instead of a string. This now contains the names of the tasks ‘`concat`‘ and ‘`uglyfy`‘.

To test this, you can now add the ‘`uglyfy`‘ object into the `grunt.initConfig` object, and modify the tasks at the bottom of the `Gruntfile.js`, as mentioned previously.

Run the script with the `grunt` command and your previous two concatenated files should now be both concatenated and compressed within the file you defined (in this case it should be `assets/js/main.min.js`).

#### `grunt-contrib-jshint`

Now lets check our JavaScript files for errors. We can do this with JSHint which can be found in the handy plugin `grunt-contrib-jshint`.

We can do this by adding in the following object into our code:

```
jshint: {
     files: \['gruntfile.js', 'assets/js/\*.js', 'assets/js/modules/\*.js'\],
     options: {
         globals: {
              jQuery: true,
              console: true,
              module: true
         }
     }
}
```

The code above will run a JS linter (JSHint) against the files `gruntfile.js`, any file ending with `.js` within the folder `assets/js/` and any JavaScript file within the folder `assets/js/modules/`.

Along with that add the following lines to the tasks area of the script:

```
grunt.loadNpmTasks('grunt-contrib-concat');
grunt.loadNpmTasks('grunt-contrib-uglify');
grunt.loadNpmTasks('grunt-contrib-jshint');
grunt.registerTask('default', \['concat', 'uglify', 'jshint'\]);
```

To test the script run the `grunt` command. If you have followed everything so far you should get at least one error in your terminal, which will be: **Use the function form of “use strict”**.

Because of the error that we have found, the grunt script will abort (no more tasks will be run after that point).

![JSHint Error](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990847553/CJ6GFm2Ru.png)JSHint Error

Lets fix the error we found by replacing the `'use strict';` with the following piece of code:

```
(function () {
    'use strict';
}());
```

Replacing the ‘`use strict`‘ directive with this function variant will prevent it being repeated multiple times in our concatenated files.

Once you have fixed any errors that are in your code, you should end up with an output similar to the following:

![JSHint Pass](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990851319/mGdTe3t0Q.png)JSHint Pass

#### `grunt-contrib-compass`

As you can see, Grunt is pretty powerful when it comes to working with JavaScript, but how about we use it to concatenate and compress our SCSS files into CSS.

To do that, we need to configure our Grunt compass plugin by adding the following object into our `grunt.initConfig`:

```
compass: {
    dist: {
        options: {
            sassDir: 'assets/scss',
            cssDir: 'assets/css',
            environment: 'development',
            outputStyle: 'compressed'
        }
    }
}
```

The code takes anything in the `assets/scss/` folder (that doesn’t begin with an underscore), converts it to css, compresses it, and puts it in the `assets/css/` folder.

At the bottom of the script file, add the following along with the other tasks:

```
grunt.loadNpmTasks('grunt-contrib-concat');
grunt.loadNpmTasks('grunt-contrib-uglify');
grunt.loadNpmTasks('grunt-contrib-jshint');
grunt.loadNpmTasks('grunt-contrib-compass');
grunt.registerTask('default', \['concat', 'uglify', 'jshint', 'compass'\]);
```

Test the file by running the `grunt` command. If all is well, you should get the following output:

![Compass compiled successfully](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990854842/Qv5Z-dwU4.png)Compass compiled successfully

#### `grunt-contrib-watch`

The final piece of the puzzle is to automate it all. We can do this with the `grunt-contrib-watch` plugin.

Add the following object to the script:

```
watch: {
    files: \['<%= jshint.files %>', 'assets/scss/\*\*/\*.scss'\],
    tasks: \['concat', 'uglify', 'jshint', 'compass'\]
}
```

This tells the script that whenever any of the files configured in the `jshint` object are changed, or any of the files within `assets/scss/` (ending in `.scss`) are changed, the tasks ‘`concat`‘, ‘`uglify`‘, ‘`jshint`‘ and ‘`compass`‘ are run.

Finally at the bottom of the script file, add the following along with the other tasks:

```
grunt.loadNpmTasks('grunt-contrib-concat');
grunt.loadNpmTasks('grunt-contrib-uglify');
grunt.loadNpmTasks('grunt-contrib-jshint');
grunt.loadNpmTasks('grunt-contrib-compass');
grunt.loadNpmTasks('grunt-contrib-watch');
grunt.registerTask('default', \['concat', 'uglify', 'jshint', 'compass', 'watch'\]);
```

Run the `grunt` command to start grunt listening for changes. This should look something like the following:

![Grunt file watching files](https://cdn.hashnode.com/res/hashnode/image/upload/v1639990858360/sAG3DYivl.png)Watching files

Putting it all together
-----------------------

The final `Gruntfile.js` file, should look a little something like this:

```
(function () {
   'use strict';
}());
module.exports = function(grunt) {
    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),

        concat: {
            options: {
                separator: 'rnrn'
            },
            dist: {
                src: \['assets/js/modules/module1.js', 'assets/js/modules/module2.js'\],
                dest: 'assets/js/main.js'
            }
        },

        uglify: {
            options: {
                banner: '/\*! <%= pkg.name %> <%= grunt.template.today("dd-mm-yyyy") %> \*/n'
            },
            dist: {
                files: {
                    'assets/js/main.min.js': \['<%= concat.dist.dest %>'\]
                }
            }
        },

        jshint: {
            files: \['gruntfile.js', 'assets/js/\*.js', 'assets/js/modules/\*.js'\],
            options: {
                globals: {
                    jQuery: true,
                    console: true,
                    module: true
                }
            }
        },

        compass: {
            dist: {
                options: {
                    sassDir: 'assets/scss',
                    cssDir: 'assets/css',
                    environment: 'development',
                    outputStyle: 'compressed'
                }
            }
        },

        watch: {
            files: \['<%= jshint.files %>', 'assets/scss/\*\*/\*.scss'\],
            tasks: \['concat', 'uglify', 'jshint', 'compass'\]
        }

    });

    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-compass');
    grunt.loadNpmTasks('grunt-contrib-watch');
    grunt.registerTask('default', \['concat', 'uglify', 'jshint', 'compass', 'watch'\]);
};
```