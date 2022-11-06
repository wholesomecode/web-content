---

title: Install WordPress Repository Plugins and Themes with Composer
post_date: 2021-02-21T23:19:43.000Z
post_status: publish

---

In my last guide I told you how to [install your GitHub maintained WordPress themes and plugins with composer](https://wholesomecode.ltd/guides/install-wordpress-plugins-themes-github-composer/). Well what if I told you you could also use composer to install any Theme or Plugin from the WordPress repository?

[WordPress Packagist](https://wpackagist.org/) is a fantastic and free to use service that takes plugins and themes from the WordPress repository, and creates handy composer packages.

Prerequisites
------------------

*   [Install composer](https://getcomposer.org/doc/00-intro.md)
*   Ensure the theme or plugin you want to use is in the WordPress repository, and know the slug

Configure Your WordPress Project
-----------------------------------------

We want to configure your WordPress project so it will pull in themes and plugins via Composer.

Create a `composer.json` in the root of your WordPress project (or amend an existing one if it already exists).

Ensure it contains the following lines of code:

```
{
    "name": "wholesomecode/composer-example",
    "description": "Demonstration of how to use composer within WordPress",
    "require": {
        "wpackagist-plugin/wholesome-publishing":">=1.3.0"
    },
    "repositories": [
        {
            "type":"composer",
            "url":"https://wpackagist.org"
        }
    ]
}
```

In the `require` area, we have linked to the plugin that we want to download, prefixed with `wpackagist-plugin` (for themes use `wpackagist-theme`). It also states the version we want to download. In this case greater than or equal to version 1.3.0.

### Repositories

In the `repositories` we have added an entry of the type `composer` with the url pointing to WordPress Packagist.

### GitIgnore

If you are using GitHub to manage your project, be sure to add the path to plugin (or theme) to your `.gitignore` file:

```
/vendor
wp-content/plugins/wholesome-publishing
```

Remember to also add `/vendor` to the file to ensure that we do not commit additional files installed by composer.

Running Composer
---------------------

If your WordPress project has the `composer.json` file is in the same folder as your `wp-content` folder, we shouldn’t have to configure anything else (If not see the [custom install path](https://wholesomecode.ltd/guides/install-wordpress-plugins-themes-github-composer/#custom-install-path) guide in my previous composer article).

In your terminal, ensure you are in the same folder as your `composer.json` file and enter the following command:

```
composer install
```

If you have ran composer before and have a `composer.lock` file, you can run `composer update`.

The plugin will be installed into `wp-content/plugins/wholesome-publishing:`

![Running Composer Install to install a theme from the WordPress repository](https://cdn.hashnode.com/res/hashnode/image/upload/v1639989928294/aHiX3_CZZ.gif)Running Composer Install to install a theme from the WordPress repository

### What Happened?

*   The plugin has been installed into `wp-content/plugins/wholesome-publishing`
*   A `vendor` has been created that contains the installer files used to put the theme in the correct location
*   A `composer.lock` file has been created. This makes a note of the file versions you are using. If you commit this then other people that run the `composer install` command will get the same versions.

Extra: Taking it Further
--------------------------

*   [Installing your own plugins and themes from GitHub with composer](https://wholesomecode.ltd/guides/install-wordpress-plugins-themes-github-composer/)
*   [Configuring a custom installation path](https://wholesomecode.ltd/guides/install-wordpress-plugins-themes-github-composer/#custom-install-path)