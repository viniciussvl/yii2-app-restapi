<p align="center">
    <a href="https://github.com/yiisoft" target="_blank">
        <img src="https://avatars0.githubusercontent.com/u/993323" height="100px">
    </a>
    <h1 align="center">Yii 2 API Rest Project Template</h1>
    <br>
</p>

Yii 2 API Rest Project Template is a skeleton [Yii 2](http://www.yiiframework.com/) application best for
rapidly creating API Rest projects.

DIRECTORY STRUCTURE
-------------------

The directory structure is similar to the Basic template added in the Advanced feature, which is the generation of the `*-local.php`, `web/index.php` files, and the `yii` script according to your environment

      + assets/             contains assets definition
      + components/         containes the application components
      + commands/           contains console commands (controllers)
      - config/             contains application configurations
        * routes/           contains the routes configurations files
      + environments        contains environments templates files (see the advanced template)
      + mail/               contains view files for e-mails
      + migrations/         contains the migrations scripts
      + models/             contains model classes
      + modules/            contains de application modules
      + runtime/            contains files generated during runtime
      + tests/              contains various tests for the basic application
      + vendor/             contains dependent 3rd-party packages
      + web/                contains the entry script and Web resources


REQUIREMENTS
------------

The minimum requirement by this project template that your Web server supports PHP 5.4.0.


INSTALLATION
------------

### Install via Composer

If you do not have [Composer](http://getcomposer.org/), you may install it by following the instructions
at [getcomposer.org](http://getcomposer.org/doc/00-intro.md#installation-nix).

You can then install this project template using the following command:

~~~
php composer create-project --stability=dev dersonsena/yii2-app-restapi
~~~

Now you should be able to access the application through the following URL, assuming `basic` is the directory
directly under the Web root.

~~~
http://localhost/basic/web/
~~~

### Install with Docker

Update your vendor packages

    docker-compose run --rm php composer update --prefer-dist
    
Run the installation triggers (creating cookie validation code)

    docker-compose run --rm php composer install    
    
Start the container

    docker-compose up -d
    
You can then access the application through the following URL:

    http://127.0.0.1:8000

**NOTES:** 
- Minimum required Docker engine version `17.04` for development (see [Performance tuning for volume mounts](https://docs.docker.com/docker-for-mac/osxfs-caching/))
- The default configuration uses a host-volume in your home directory `.docker-composer` for composer caches


CONFIGURATION
-------------

### Environment Files

Just like in the Advanced template, to generate the files according to your work environment you need to execute the `init` script and then choose your environment: `Development` or `Production`

This script will create the files:

      /yii
      /web/index.php
      /config/console-local.php
      /config/web-local.php
      
**NOTES:**

See more details for use in the [Advanced template documentation](https://github.com/yiisoft/yii2-app-advanced/blob/master/docs/guide/start-installation.md).

### Database

Edit the file `config/web-local.php` with real data, for example:

```php
'components' => [
    'db' => [
        'class' => 'yii\db\Connection',
        'dsn' => 'mysql:host=127.0.0.1;dbname=your_database_name',
        'username' => 'root',
        'password' => '',
        'charset' => 'utf8',

        // Schema cache options (for production environment)
        //'enableSchemaCache' => true,
        //'schemaCacheDuration' => 60,
        //'schemaCache' => 'cache',
    ],
],
```

**NOTES:**
- Yii won't create the database for you, this has to be done manually before you can access it.
- You also need to configure the database guidelines in `config/console-local.php` for CLI scripts.

### Migration

Run the migration script to create the users table with the following command:

```
php ./yii migrate
```

The `User.php` model has already been generated by the GII, but a few things have been added. To access it, simply open the `models/User.php` file

**NOTES:**

All models **MUST** extend the `app\components\ModelBase` class. See it for more implementation details.

### API Routes

All your API routes must be within `config/routes`. The following code is the already configured route of users:

```php
return [
    [
        'class' => 'yii\rest\UrlRule',
        'controller' => 'v1/system/users',
        'tokens' => ['{id}' => '<id:[\\w-]+>'],
        'pluralize' => false,
    ]
];
```

### Accessing Users Web Service

To access the list of users through your REST API, simply access the address below in your browser or in external applications such as [Postman](https://www.getpostman.com/):

```
GET http://<YOUR_BASE_URL>/v1/system/users
```

When accessing this address you should see a list with only one user similar to JSON below:

```json
[
    {
        "id": 1,
        "username": "admin",
        "password_reset_token": null,
        "email": "admin@admin.com.br",
        "status": 1,
        "created_at": "09/04/2018 04:18:52",
        "updated_at": "09/04/2018 04:18:52"
    }
]
```

See more details in [RESTful Web Services Guide](https://www.yiiframework.com/doc/guide/2.0/en/rest-quick-start#trying-it-out).

### Authentication

The authentication used is [HttpBearerAuth](https://www.yiiframework.com/doc/api/2.0/yii-filters-auth-httpbearerauth), where a Token with [JWT](https://jwt.io/) is passed for user authentication in its API. Initially it is inactive. To activate it is quite simple, just go in the `app\components\BaseController` class and uncomment the lines:

```php
$behaviors['authenticator'] = [
    'class' => HttpBearerAuth::className(),
        'except' => $excepts
    ];

    $behaviors['jsxValidator'] = [
        'class' => JsxValidator::className(),
        'except' => $excepts
    ];
]
```

When uncommenting these lines and trying to rerun the list of users, you should return a message as below:

```json
{
    "name": "Unauthorized",
    "message": "Your request was made with invalid credentials.",
    "code": 0,
    "status": 401,
    "type": "yii\\web\\UnauthorizedHttpException"
}
```

**NOTES:** If you want some action to be open, that is, do not authenticate to the API, just add inside the `$excepts` array.

For more details on REST authentication with Yii, see the [Official Framework Documentation](https://www.yiiframework.com/doc/guide/2.0/en/rest-authentication).