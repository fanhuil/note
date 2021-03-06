## Laravel 中间件是什么

Laravel 中间件提供了一种方便的机制来过滤进入应用的 HTTP 请求。例如，Laravel 内置了一个中间件来验证用户的身份认证。如果用户没有通过身份认证，中间件会将用户重定向到登录界面。但是，如果用户被认证，中间件将允许该请求进一步进入该应用。

当然，除了身份认证以外，还可以编写另外的中间件来执行各种任务。例如：CORS 中间件可以负责为所有离开应用的响应添加合适的头部信息；日志中间件可以记录所有传入应用的请求。

Laravel 自带了一些中间件，包括身份验证、CSRF 保护等。所有这些中间件都位于 app/Http/Middleware 目录。

简单来说就是请求在不去修改自身的逻辑，通过中间件扩展或者处理一些功能。

## 那和面向切面编程有什么关系呢？

在我理解 Laravel 中间件和切面编程就是一个意思，就是都需要经过一些步骤，不去修改自己的代码，以此来扩展或者处理一些功能。举个生活中的例子电影院验票。验票员就是所谓的中间件，每个买票的同学都需要经过他的验证才能进入影院看电影。

## 一个程序该如何实现中间件呢？

我们也采用面向接口编程的形式，来定义我们的中间件

```php

interface Milldeware {
    public static function handle(Closure $next);
}

class VerfiyCsrfToekn implements Milldeware {

    public static function handle(Closure $next)
    {
        echo '验证csrf Token <br>';
        $next();
    }
}

class VerfiyAuth implements Milldeware {

    public static function handle(Closure $next)
    {
        echo '验证是否登录 <br>';
        $next();
    }
}

class SetCookie implements Milldeware {
    public static function handle(Closure $next)
    {
        $next();
        echo '设置cookie信息！';
    }
}

function call_middware() {
    SetCookie::handle(function (){
        VerfiyAuth::handle(function() {
            $handle = function() {
                echo '当前要执行的程序!';
            };
            VerfiyCsrfToekn::handle($handle);
        });
    });
}

call_middware();
```

下面我们来说一下 call_middware 都做了什么事

1. 第一步执行 call_middware 函数 会执行 SetCookie::handle。

2. 当执行 SetCookie::handle 的时候会发现要先执行 $next (); 再 echo ' 设置 cookie 信息！';

3. 所以就先执行了 VerfiyAuth::handle，这时候会先执行 echo ' 验证是否登录
   '; 然后执行 $next ();

4. 执行 VerfiyCsrfToekn::handle 这时候会先执行 echo ' 验证 csrf Token
   '; 然后执行 $next ();

5. 执行 $handle ();

6. 最后 在 echo ' 设置 cookie 信息！'。

   明白了其中原理，有些同学想这样代码肯定不会很好维护和扩展啊。那我们应该怎么去修改我们的代码呢？

首先，希望同学能先知道两个函数的使用

```php
1.call_user_func();
2.array_reduce();
```

具体两个函数的作用请先查看 php 文档

```php

    interface Milldeware {
        public static function handle(Closure $next);
    }

    class VerfiyCsrfToekn implements Milldeware {

        public static function handle(Closure $next)
        {
            echo '验证csrf Token <br>';
            $next();
        }
    }

    class VerfiyAuth implements Milldeware {

        public static function handle(Closure $next)
        {
            echo '验证是否登录 <br>';
            $next();
        }
    }

    class SetCookie implements Milldeware {
        public static function handle(Closure $next)
        {
            $next();
            echo '设置cookie信息！';
        }
    }

    $handle = function() {
        echo '当前要执行的程序!';
    };

    $pipe_arr = [
        'VerfiyCsrfToekn',
        'VerfiyAuth',
        'SetCookie',
    ];

    $callback = array_reduce($pipe_arr,function($stack,$pipe) {
        return function() use($stack,$pipe){
            return $pipe::handle($stack);
        };
    },$handle);

    call_user_func($callback);
```

最后的实现就是这样子的。当然 laravel 中实现 middware 也是主要通过 call_user_func 和 array_reduce () 实现的