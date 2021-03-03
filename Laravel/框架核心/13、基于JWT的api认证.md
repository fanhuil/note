## 什么是 jwt

jwt 单词代表的意思就是 JSON Web Token。

我们在做 api 请求的时候，通常要使用 token，来验证是否这个请求能不能访问。

一旦用户登录，后续每个请求都将包含 JWT，允许用户访问该令牌允许的路由、服务和资源。单点登录是现在广泛使用的 JWT 的一个特性，因为它的开销很小，并且可以轻松地跨域使用。

## 为什么不使用 session，而使用 jwt 呢？

Session 是在服务器端的，而 JWT 是在客户端的。

Session 方式存储用户信息的最大问题在于要占用大量服务器内存，增加服务器的开销，而 JWT 方式将用户状态分散到了客户端中，可以明显减轻服务端的内存压力。

## laravel 中如何使用我们的 jwt 呢？

1. composer 安装 jwt-auth

```
composer require tymon/jwt-auth 1.0.0-rc.1
```

​	2.注册我们的服务提供者

```php
'providers' => [

    Tymon\JWTAuth\Providers\LaravelServiceProvider::class,
]
```

​	3.发布配置文件

```php
php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\LaravelServiceProvider"
```

​	4.生成密钥

```bash
php artisan jwt:secret
```

​	5.配置 Auth guard，让 api 的 driver 使用 jwt

```php
'guards' => [
    'api' => [
        'driver' => 'jwt',
        'provider' => 'users',
    ],
],
```

​	6.更改 User Model, 让 User 支持 jwt-auth

```php

use Tymon\JWTAuth\Contracts\JWTSubject;
class User extends Authenticatable implements JWTSubject
{
    /**
     * Get the identifier that will be stored in the subject claim of the JWT.
     *
     * @return mixed
     */
    public function getJWTIdentifier()
    {
        return $this->getKey();
    }

    /**
     * Return a key value array, containing any custom claims to be added to the JWT.
     *
     * @return array
     */
    public function getJWTCustomClaims()
    {
        return [];
    }
}
```

下面就可以使用了，跟 laravel csrf-token 一样我们在每个模版 meta 中注册 api-token。

```
<meta name="csrf-token" content="{{ csrf_token() }}">
<meta name="api-token" content="{{ Auth::check() ? 'Bearer '.JWTAuth::fromUser(Auth::user()) : '' }}">
```

每次 ajax 请求的时候获取 api-token 放置到 header 中就可以了。

```js
let api_token = document.head.querySelector('meta[name="api-token"]');

if(api_token) {
    window.axios.defaults.headers.common['Authorization'] = api_token.content;
}
```

哪个 api 路由需要使用 jwt, 只需要加入 'auth:api' 中间件就可以了。

```php
Route::middleware('auth:api')->group(function($router) {
    // 点赞
    $router->get('/post/is-zan/{zan_post}','\App\Api\Controllers\PostController@isZan');
});
```

































