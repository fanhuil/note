## 外观模式 Facade 理解

上一节我们讲到需要 $ioc->make ('user') 才能拿到 User 的实例，再去使用 $user->login (); 那能不能更方便点，比如下面的用法，是不是很方便。

```php
UserFacade::login(); 
```

## Facade 工作原理

1. Facade 核心实现原理就是在 UserFacade 提前注入 Ioc 容器。
2. 定义一个服务提供者的外观类，在该类定义一个类的变量，跟 ioc 容器绑定的 key 一样，
3. 通过静态魔术方法__callStatic 可以得到当前想要调用的 login
4. 使用 static::$ioc->make ('user');

## 具体实现 Facade

1. 我们定义一个 User 的外观类 UserFacade

```php

class UserFacade
{
    // 维护Ioc容器
    protected static $ioc;

    public static function setFacadeIoc($ioc)
    {
        static::$ioc = $ioc;
    }

    // 返回User在Ioc中的bind的key
    protected static function getFacadeAccessor()
    {
        return 'user';
    }

    // php 魔术方法，当静态方法被调用时会被触发
    public static function __callStatic($method, $args)
    {
        $instance = static::$ioc->make(static::getFacadeAccessor());
        return $instance->$method(...$args);
    }

}

//实例化IoC容器

$ioc = new Ioc();
$ioc->bind('log','FileLog');
$ioc->bind('user','User');

UserFacade::setFacadeIoc($ioc);

UserFacade::login();
```

如果有对__callStatic 魔术方法不太明白的，可以查看 php 文档

## Laravel 为什么要定义 Facades，它有什么好处？

有的同学说，使用 Facade 那不是更加麻烦了啊，还需要注入 ioc 容器，还得需要用魔术方法。

其实 laravel 在框架运行的时候这些步骤都帮我们自动加好了，我们只需要使用 UserFacade::login (); 就可以了。

使用 Facades 其实最主要的就是它提供了简单，易记的语法，从而无需手动注入或配置长长的类名。此外，由于他们对 PHP 静态方法的独特调用，使得测试起来非常容易。

好了我们这个章节的内容就到这里。
