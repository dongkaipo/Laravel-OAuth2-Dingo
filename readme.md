Please visit [https://github.com/dongkaipo/laravel-restful-api](https://github.com/dongkaipo/laravel-restful-api) to view the latest version.

## Laravel
### Install 
```
composer create-project laravel/laravel --prefer-dist Laravel-OAuth2-Dingo

cd Laravel-OAuth2-Dingo
chmod -R 777 storage
chmod -R 777 bootstrap/cache/

```

### Configure

```
valet link api.laravel
```

## Test
visit http://api.laravel.dev

return laravel hello world page

## OAuth - Passport

### Install
```
composer require laravel/passport
```
```
file: config/app.php

providers:
Laravel\Passport\PassportServiceProvider::class,

```

And Run Command

```
php artisan migrate
php artisan passport:install
```

### Configure
```
file: app/User.php

<?php 
namespace App;

use Laravel\Passport\HasApiTokens;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use HasApiTokens, Notifiable;
}
```
```
file: app/Providers/AuthServiceProvider.php

<?php

namespace App\Providers;

use Laravel\Passport\Passport;
use Illuminate\Support\Facades\Gate;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

class AuthServiceProvider extends ServiceProvider
{
    /**
     * The policy mappings for the application.
     *
     * @var array
     */
    protected $policies = [
        'App\Model' => 'App\Policies\ModelPolicy',
    ];

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Passport::routes();
    }
}

```

```
file: config/auth.php

'guards' => [
    'web' => [
        'driver' => 'session',
        'provider' => 'users',
    ],

    'api' => [
        'driver' => 'passport',
        'provider' => 'users',
    ],
],

```

### Test

Add user data to table `users`

```
name: Jack
email: admin@jackd.io
password: jackPassword (Hash::make('jackPassword'))
```

```
curl --request POST \
  --url http://api.laravel.dev/oauth/token \
  --header 'cache-control: no-cache' \
  --header 'content-type: application/x-www-form-urlencoded' \
  --data 'client_id=2&client_secret=sakK5TlDQa1TnSSwdB96Z00cCs77X7rY6c5Zw7N4&scope=*&username=admin%40jackd.io&password=jackPassword&grant_type=password'

```

The `client_id` and `client_secret` can be found in table `oauth_clients` which is `password_client` = `'1'`

### Success

```
{
    "token_type": "Bearer",
    "expires_in": 31536000,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImp0aSI6IjU3ZGQ5NTE2YzhjY2MwZWViMjQ3ODM4Y2QxYzU4NzZmMzIyNTg2YjVkY2EwYjQwNTUzMmFmZmU1OWQzN2I5YTljYWRlNDVlZDM2N2QzNmEzIn0.eyJhdWQiOiIyIiwianRpIjoiNTdkZDk1MTZjOGNjYzBlZWIyNDc4MzhjZDFjNTg3NmYzMjI1ODZiNWRjYTBiNDA1NTMyYWZmZTU5ZDM3YjlhOWNhZGU0NWVkMzY3ZDM2YTMiLCJpYXQiOjE1MTQ4MTc1OTIsIm5iZiI6MTUxNDgxNzU5MiwiZXhwIjoxNTQ2MzUzNTkyLCJzdWIiOiIxIiwic2NvcGVzIjpbIioiXX0.BoYKtQn9yFEs2Y5M2myZS8-R4G-MnT78JI4ygRYIZLEA9c6f1-JWI88Ihc0FB_u_obuz-UVu2IQV4gk-VEphc9MTnq3TdiZJhvoHCzNL3Sj3yiIat0k75npGnXzHbsTXHXPM7xJnxTbP9K4THibEIaTYe0klNuVojvpNEnNlqLZQwTjvUGuUWIWDczlQrelLQjLdjkfPn00jehYPHNg98MSQ6f3Yes-dxnbwD44l3PJCE755Q6xycUY5L0GXmeof1WlsF65WA4_UswqbnIW1mrB_w2wGdvao6IKjScqEuQHZFDDDpjOo5TiB80gyLZmBFTeMhS4x11-SBKprYogDlgMA-bVNgYzPvazdpFJhkbRJOchLDqOLiWeBuKppFVyFXOAC7FioB9sjsji2jKy1QvZ3cKK_vYohnivwxuq4NvVqDXCn6nv8BEQeSWGUyXq7tuSZBQv6ajHLfrxN6HKk7JcmB4QP7K1jXpFrjVOpITPeoudKwmr3YRuM3BuPig3_R-yeMxh7xghPyE9fDDk-iF4hVmPqS5HEB0ESgRcq8KeJYQ7-un5i0SrDZxMx82SX231S8sNpyuJT8DK_6DONRiEhwQdBSfOwtzHpl9g-Fc2x8Uz5tEXWy2ehiWgGZl-5RSt1VobMJLDDFCT1sN1Hvhev2j1jdJ5T8QyIrL7ALG0",
    "refresh_token": "def50200a2d6cabb67e0ebf8c66513c51171478595484e37b14db1be12b21702dad6517546c807d31e59c1a7b17f7041645029889a2b8f188a8e0b20450d347452ff8e9d034643a0b0275492ae58f61148c83214a7e395ed5e4fd830d1426c179e08a530a159ec871377f6513dc4e0c0a371bc6499fba65c440ca1a4b43499dcf0f74c8427bab05ca3a0f2dd804822cd7fea20e96511a12757164b66d757afdb9af341a1ebfa3419e9535d441ad9063498fe4835dcea808d9040707c237b7daec82620d91a4ddd6c3eb3d4e90d7e4199bc497605099962384f3ab04373cb797d1376f3c5a011b2738ed9f60bef5d09ca961d207d62e930d9091b606cbcb1f86e3c709a02ac60d65b8921fc82f6c18cd9ac84b324a82aaa2287a9e13c6bd0c237ac2483f19ca04ca087dfe36c54d8d1e68cedd6493fb3cf1feabdabec1b036a6d233aef8bff2e696c3d972072a9a15c8e13a8020c6de46457ae473382fbd8c81a6d50ce9e"
}
```

## Dingo-Api

### Install
```
file: composer.json

"require": {
    "dingo/api": "2.0.0-alpha1"
}
```

And run command

```
composer update
```

```
php artisan vendor:publish --provider="Dingo\Api\Provider\LaravelServiceProvider"
```

### Configure

```
file:.env

API_STANDARDS_TREE=vnd
API_SUBTYPE=Laravel
API_DOMAIN=api.laravel.dev
API_NAME="Laravel API"
API_CONDITIONAL_REQUEST=false
API_STRICT=true
API_DEFAULT_FORMAT=json
API_DEBUG=true

```
```
file: routes/api.php 


$api = app('Dingo\Api\Routing\Router');
$api->version('v1', function ($api) {

    $api->get('/dingo',function(){
        return "hello world";
    });


    $api->get('/no_access', function () {
        return "no_access";
    });

    $api->group(['middleware' => 'auth:api', 'bindings'], function ($api) {
        $api->get('my', function () {
            return 'oauth my';
        });
    });
});
```
```
file: app/Http/Controllers/Controller.php

use Dingo\Api\Routing\Helpers;

use Helpers;

```
### Test

```
curl --request GET \
  --url http://api.laravel.dev/dingo \
  --header 'accept: application/vnd.Laravel.v1+json'
```

It will return 'hello world'


## Dingo Link OAuth2

### Configure
Create `Authenticate.php` in `/app/Http/Middleware/`
 
```
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Contracts\Auth\Factory as Auth;
use Symfony\Component\HttpKernel\Exception\UnauthorizedHttpException;

class Authenticate
{
    /**
     * The authentication factory instance.
     *
     * @var \Illuminate\Contracts\Auth\Factory
     */
    protected $auth;

    /**
     * Create a new middleware instance.
     *
     * @param  \Illuminate\Contracts\Auth\Factory $auth
     * @return void
     */
    public function __construct(Auth $auth)
    {
        $this->auth = $auth;
    }

    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request $request
     * @param  \Closure $next
     * @param  string[] ...$guards
     * @return mixed
     *
     * @throws \Illuminate\Auth\AuthenticationException
     */
    public function handle($request, Closure $next, ...$guards)
    {
        $this->authenticate($guards);
        return $next($request);
    }

    /**
     * Determine if the user is logged in to any of the given guards.
     *
     * @param  array $guards
     * @return void
     *
     * @throws \Illuminate\Auth\AuthenticationException
     */
    protected function authenticate(array $guards)
    {
        if (empty($guards)) {
            return $this->auth->authenticate();
        }
        foreach ($guards as $guard) {
            if ($this->auth->guard($guard)->check()) {
                return $this->auth->shouldUse($guard);
            }
        }
        throw new UnauthorizedHttpException('UnAuthorized User');
    }
}

```
And change `$routeMiddleware` `auth` to `\App\Http\Middleware\Authenticate::class,`
```
file: app/Http/Kernel.php

protected $routeMiddleware = [
    'auth' => \App\Http\Middleware\Authenticate::class,
    'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,        'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
    'can' => \Illuminate\Auth\Middleware\Authorize::class,
    'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
];

```


### Test 

```
curl --request GET \
  --url http://api.laravel.dev/my \
  --header 'accept: application/vnd.Laravel.v1+json'
```
It will return `401` 
```
{"message":"Failed to authenticate because of bad credentials or an invalid authorization header.","status_code":401}
```

Get the token

```
curl --request POST \
  --url http://api.laravel.dev/oauth/token \
  --header 'accept: application/vnd.Laravel.v1+json' \
  --header 'content-type: application/x-www-form-urlencoded' \
  --data 'client_id=2&client_secret=sakK5TlDQa1TnSSwdB96Z00cCs77X7rY6c5Zw7N4&scope=*&username=admin%40jackd.io&password=jackPassword&grant_type=password' 
```

It will return the `access_token` like 

```
{"token_type":"Bearer","expires_in":31536000,"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImp0aSI6ImZiZjRlMTYyNTJlZTE0Y2FiMzhjY2YyNzliYmZlMjBlMTBhNWViODAyMGE0YzJkMjJkOWIyNjBiZTc0YjVmYzdjOWQ3MmIzMDkzNTY4NGFmIn0.eyJhdWQiOiIyIiwianRpIjoiZmJmNGUxNjI1MmVlMTRjYWIzOGNjZjI3OWJiZmUyMGUxMGE1ZWI4MDIwYTRjMmQyMmQ5YjI2MGJlNzRiNWZjN2M5ZDcyYjMwOTM1Njg0YWYiLCJpYXQiOjE1MTQ4MTkzMTMsIm5iZiI6MTUxNDgxOTMxMywiZXhwIjoxNTQ2MzU1MzEzLCJzdWIiOiIxIiwic2NvcGVzIjpbIioiXX0.2rlZMyTLt1QSgKJecElUSe8coz5nMGcCvvPZ5sJdE4Rmo57wf9xwZJ1-9yTZa9gCvc3ylMX0cX0vjWnmcGgHRBTwPJuWEodS3uupdUMjXD-0Vtsi3PVQOeh9gqxlDaZAl_o0lq4cRPzIfX903k-LdGobHTepu4RGmJTG_DVFiR0Kd8vuGOOy4gq8C4uBT8GJnV4wfw-famm43MvKYiQx3_nJIih7iWntXijJSGCUxo8ZjIBhuSHmly1K9Vyyc5GQIqHCuHqwpPrzGEXFLfJcj3DduJSwbiw1P3nVtnFY92WHOI1nYJ1po23URBZmELC-7yeRJPBLVtbvZQkdI8kR0yNXK1ZQn3607u5LL4aoU5Se3KphU6rZGampJlC2AWkzLmeBUU6rSnw995XxtTILSOToFPk1lO3lkhCsm3K7vB3I7a77TKJ0UX6qVeYpUhVTOOgTiOmWZg8UbDXPtZQEJahveSgjFjzDAi524-Y-lNK-gTnsPUSAoHYMYNW2zSMT7-H4Xd05luogsfA90gp1gVbwt76yb1jKIP0aP10wdCSWSjvfJtDF9ClQcMUZUFt1RFeiYYjbndLZ-pANYMvEfPPD5u4NHbyKt5WBg2Ossjo9QYrSQ4wiBO9W5c--OAMyEg2HbvSqy-isqHTfC-ilGtIrfGMgXE0sFFMJdb185Sc","refresh_token":"def5020020942595916a4298e1bb58aa4aea965baf37a527dd8df806657b9acaee12ea953cb05a43e94f66795001521751b5c10f9208d49dfad89028a40b86546fbdaf98b948225bca276f19cd97bf8e77565f4b33740a71feedaa23ab70bb4671330728e4d602100b4409b4c3235a9cb4136d12cad4634cbbe328fdfc459a596635da2fe108e3d980d4154ba00216aea9c703bab047a9c6fa4e5c929b529dc430375225b27b81f251b4f5eb54e61566ceb8b5fe8e31dde36287ea359a18108abd0d0ed162080781709f1a72a4239c9ad2d6705c67a43fae0323e11b8eec7ad259a9a72f8dfcece610b7f599b609a66d6e7a1757e286370bcf6f4d16436de339cde9a5e3c6cfcd32e6f8a86711dbbf9db4e9870c865b7fcda24cb07c5ba1c0b27c6e685e096cda10f21966c73688080a43e2bbe03f65ee272e6c51b8e7d9d01e2b64b58971cb63022225ad3bbd38c1eb9b4a09859ae22f9f99226d825fe2128e1b78a862"}
```

Last use this `access_token` to visit api `/my`

```
curl --request GET \
  --url http://api.laravel.dev/my \
  --header 'accept: application/vnd.Laravel.v1+json' \
  --header 'authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImp0aSI6IjU2OWM3Y2UwM2IxYTM1N2FjZGQ4MjY1OGRhNDY1MTQwOTY0NzM1ZWUxYTIxMDE1YjYyMzhiOTcwYTE1Mzk0ZTgxY2I2YzQzMTUyZGY0YjQ5In0.eyJhdWQiOiIyIiwianRpIjoiNTY5YzdjZTAzYjFhMzU3YWNkZDgyNjU4ZGE0NjUxNDA5NjQ3MzVlZTFhMjEwMTViNjIzOGI5NzBhMTUzOTRlODFjYjZjNDMxNTJkZjRiNDkiLCJpYXQiOjE1MTQ4MTk0NDYsIm5iZiI6MTUxNDgxOTQ0NiwiZXhwIjoxNTQ2MzU1NDQ2LCJzdWIiOiIxIiwic2NvcGVzIjpbIioiXX0.W-YOm8odtl89TI1oxqT5HEPRQ21L3k2dNHmTsDTP3u4kKLeHUG_awiu761z6vlOvtyNZodjaNFT3Sj4nmy9f007KL1RUCyBDdq5MW6OB_v0-8tocyn1MrIK-3F4hxRRcehUIeTDExPxY7G4oXw96uGQavZ_Urcx1CI_j4IyqsFkd-JIkWRBptwEy2LQ9wU5hxqhF2svR6ACz7_8DyMXMdPpdgJSlHxZXjD1HsiO_xa7Y4M8tz17PPxoFTs6mSvPfR_dIkrihp6xZOOnHO8kEiCbuopfNwxEhEQAd0ECPGxxW2nUvR4Alas8RIIFiw9_aYkUbdBOm8WqqJyJXrz4kjzYr4Kp_5tcUG53UzNcC9qK-Cv-mbC4AOvUcbT-pOK9o1cklzEJJwzwLcWRo-Y70rpi8S_N7Xypy9evnmC6LiU3Gh_BJfq5KMa1xHQ2m8QC0OseK8jBFuhTvAYIC50l-_fCODO_pN_Bu8PRtbClaMs7jQlW05MuPuACX8sQLAWaqauQ12eaqWIHqm4D0Eeo-XgbPi-Hn4EvVeT5JDZmWzqUWKdmD7BwHNvTZjCb_nmNINsgHTnoPPC5J4Hu4ON0yse6rYpnaejeWEMhTu1UpFF0Qi2GA3_qYvE1T6QYKtRBwWfjoLWBwy2OGgAkfiwj9sdTtUjRqTSiZUtyMiCLUoj0'
```

> `header authorization` value is `Bearer` + space + `access_token`

If success , you will get 'oauth my'

## Info

1. Laravel is based on [Laravel 5.5](https://laravel.com/docs/5.5)
2. Oauth is based on [Laravel Passport](https://laravel.com/docs/5.5/passport)
3. Api is based on [dingo/api](https://github.com/dingo/api)

> You can find the `.env` file content in `.env.example` file.

