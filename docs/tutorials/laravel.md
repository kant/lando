Working with Laravel
====================

Lando offers a [configurable recipe](./../recipes/laravel.md) for spinning up [Laravel](https://laravel.com/) apps. Let's go over some basic usage.

<!-- toc -->

Getting Started
---------------

Before you can use all the awesome Laravel Lando magic you need a codebase with a `.lando.yml` file in its root directory. There are a few ways you can do this...

### Option 1. Start with an existing codebase

```bash
# Clone codebase from git, un-tar codebase, receive as gift from the gods, etc.
git clone https://private-repository.com/laravel-project.git mysite

# Or install laravel with composer
# See: https://laravel.com/docs/5.4/installation
composer create-project --prefer-dist laravel/laravel mysite

# Go into the cloned site
cd mysite

# Initialize a .lando.yml for this site
lando init --recipe laravel
```

### Option 2. Get your site from GitHub

```bash
# Create a folder to clone your site to
mkdir mysite

# Initialize a laravel .lando.yml after getting code from GitHub
# This require a GitHub Personal Access Token
# See: https://docs.devwithlando.io/cli/init.html#github
lando init github --recipe laravel
```

Starting Your Site
------------------

Once you've completed the above you should be able to start your Drupal 8 site.

```bash
# Start up app
lando start

# Optionally run composer install if needed
lando composer install
```

If you visit any of the green-listed URLs that show up afterwards you should be welcomed with your application's `index.php`.

You may need to configure a local Laravel database connection. Run `lando info` and use the `internal_connection` information.

Importing Your Database
-----------------------

Once you've started up your Laravel site you will likely want to pull in a database before you can really start to dev all the dev. Importing a database can be done using our helpful `lando db-import` command.

```bash
# Go into my app
cd /path/to/my/app

# Grab your database dump
curl -fsSL -o database.sql.gz "https://url.to.my.db/database.sql.gz"

# Import the database
# NOTE: db-import can handle uncompressed, gzipped or zipped files
lando db-import database.sql.gz
```

You can learn more about the `db-import` command [over here](./db-import.md).

Tooling
-------

Each Lando Laravel recipe will also ship with helpful dev utilities. This means you can use things like `artisan`, `laravel`, `composer` and `php-cli` via Lando and avoid mucking up your actual computer trying to manage `php` versions and tooling.

```bash
lando artisan                  Run artisan commands
lando composer                 Run composer commands
lando db-import <file>         Import <file> into database. File is relative to approot.
lando db-export                Export a database. Resulting file: {DB_NAME}.TIMESTAMP.gz
lando laravel                  Run laravel commands
lando mysql                    Drop into a MySQL shell
lando php                      Run php commands
```

```bash
# Do a basic laravel gut check with artisan
lando artisan env

# Run composer install
lando composer install

# List laravel commands
lando laravel list

# Drop into a mysql shell
lando mysql

# Check the app's php version
lando php -v
```

You can also run `lando` from inside your app directory for a complete list of commands.

Configuration
-------------

### Recipe

You can also manually configure the `.lando.yml` file to switch `php` versions, toggle between `apache` and `nginx`, activate `xdebug` or an optional `caching` backend, choose a database type and version, set a custom webroot locaton and use your own configuration files.

{% codesnippet "./../examples/laravel/.lando.yml" %}{% endcodesnippet %}

You will need to rebuild your app with `lando rebuild` to apply the changes to this file. You can check out the full code for this example [over here](https://github.com/lando/lando/tree/master/examples/laravel).

### Environment Variables

Lando will add some helpful environment variables into your `appserver` so you can get database credential information. These are in addition to the [default variables](./../config/services.md#environment) that we inject into every container. These are accessible via `php`'s [`getenv()`](http://php.net/manual/en/function.getenv.php) function.

```bash
# Cache
CACHE_HOST: index
CACHE_PORT: 6379

# Database
DB_HOST=database
DB_USER=laravel
DB_PASSWORD=laravel
DB_NAME=laravel
DB_PORT=3306
```

These are in addition to the [default variables](./../config/services.md#environment) that we inject into every container. Note that these can vary based on the choices you make in your recipe config.

### Automation

You can take advantage of Lando's [events framework](./../config/events.md) to automate common tasks. Here are some useful examples you can drop in your `.lando.yml` to make your Laravel app super slick.

```yml
events:

  # Composer install + custom script + artisan migrate
  post-start:
    - appserver: cd $LANDO_MOUNT && composer install
    - appserver: cd $LANDO_WEBROOT && php script.php
    - appserver: cd $LANDO_WEBROOT && artisan migrate

```

Advanced Service Usage
----------------------

You can get more in-depth information about the services this recipe provides by running `lando info`.

Next Steps
----------

*   [Adding additional services](http://docs.devwithlando.io/tutorials/setup-additional-services.html)
*   [Adding additional tooling](http://docs.devwithlando.io/tutorials/setup-additional-tooling.html)
*   [Adding additional routes](http://docs.devwithlando.io/config/proxy.html)
*   [Adding additional events](http://docs.devwithlando.io/config/events.html)
*   [Setting up front end tooling](http://docs.devwithlando.io/tutorials/frontend.html)
*   [Accessing services (eg your database) from the host](http://docs.devwithlando.io/tutorials/frontend.html)
*   [Importing SQL databases](http://docs.devwithlando.io/tutorials/db-import.html)
*   [Exporting SQL databases](http://docs.devwithlando.io/tutorials/db-export.html)
*   [Using Composer to Manage a Project](http://docs.devwithlando.io/tutorials/composer-tutorial.html)
*   [Lando and CI](http://docs.devwithlando.io/tutorials/lando-and-ci.html)
*   [Lando, Pantheon, CI, and Behat (BDD)](http://docs.devwithlando.io/tutorials/lando-pantheon-workflow.html)
