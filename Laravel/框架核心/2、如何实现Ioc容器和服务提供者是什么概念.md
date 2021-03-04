## 什么是 Ioc 容器和服务提供者？

上一节我们的代码还是没有完全达到解偶，假如我们项目里有很多功能用到了这个 login 功能，我们则在这几个页面反复写。但是突然我们有一天换需求了，觉得数据库记录日志不太好，想要改成文件的，那我们不是每个页面用到 login 功能的都去替换成 new FileLog () 呢？，那改如何修改呢？

我们可以借助一个容器，提前把 log,user 都绑定到 Ioc 容器中。User 的创建交给这个容器去做。比如下面这样的，你再任何地方使用 login。都不需要关心是用什么记录日志了，哪怕后期需要修改只需要在 ioc 容器修改绑定其他记录方式日志就行了。

## 具体代码实现的思路

1. Ioc 容器维护 binding 数组记录 bind 方法传入的键值对如:log=>FileLog, user=>User
2. 在 ioc->make ('user') 的时候，通过反射拿到 User 的构造函数，拿到构造函数的参数，发现参数是 User 的构造函数参数 log, 然后根据 log 得到 FileLog。
3. 这时候我们只需要通过反射机制创建 $filelog = new FileLog ();
4. 通过 newInstanceArgs 然后再去创建 new User ($filelog);

```php
//实例化ioc容器
$ioc = new Ioc();
$ioc->bind('log','FileLog');
$ioc->bind('user','User');
$user = $ioc->make('user');
$user->login();
```

这里的容器就是指 Ioc 容器，服务提供者就是 User。

对啦，上一节遗留一个问题如果参数是接口该怎么处理，其实就是通过 Ioc 容器提前绑定好。

```php

interface Log
{
    public function write();
}

// 文件记录日志
class FileLog implements Log
{
    public function write(){
        echo 'file log write...';
    }
}

// 数据库记录日志
class DatabaseLog implements Log
{
    public function write(){
        echo 'database log write...';
    }
}

class User
{
    protected $log;
    public function __construct(Log $log)
    {
        $this->log = $log;
    }
    public function login()
    {
        // 登录成功，记录登录日志
        echo 'login success...';
        $this->log->write();
    }
}

class Ioc
{
    public $binding = [];

    public function bind($abstract, $concrete)
    {
        //这里为什么要返回一个closure呢？因为bind的时候还不需要创建User对象，所以采用closure等make的时候再创建FileLog;
        $this->binding[$abstract]['concrete'] = function ($ioc) use ($concrete) {
            return $ioc->build($concrete);
        };
		
    }

    public function make($abstract)
    {
        // 根据key获取binding的值
        $concrete = $this->binding[$abstract]['concrete'];
        return $concrete($this);
    }

    // 创建对象
    public function build($concrete) {
        $reflector = new ReflectionClass($concrete);
        $constructor = $reflector->getConstructor();
        if(is_null($constructor)) {
            return $reflector->newInstance();
        }else {
            $dependencies = $constructor->getParameters();
            $instances = $this->getDependencies($dependencies);
            return $reflector->newInstanceArgs($instances);
        }
    }

    // 获取参数的依赖
    protected function getDependencies($paramters) {
        $dependencies = [];
        foreach ($paramters as $paramter) {
            $dependencies[] = $this->make($paramter->getClass()->name);
        }
        return $dependencies;
    }

}

//实例化IoC容器
$ioc = new Ioc();
$ioc->bind('Log','FileLog');
$ioc->bind('user','User');
$user = $ioc->make('user');
$user->login();
```

至此，我们的 Ioc 就已经实现了。

## laravel 中的服务容器和服务提供者是什么样子呢？

可以在 config 目录找到 app.php 中 providers, 这个数组定义的都是已经写好的服务提供者

```php
$providers = [
    Illuminate\Auth\AuthServiceProvider::class,
    Illuminate\Broadcasting\BroadcastServiceProvider::class,
    Illuminate\Bus\BusServiceProvider::class,
    Illuminate\Cache\CacheServiceProvider::class,
    ...
]
...

// 随便打开一个类比如CacheServiceProvider，这个服务提供者都是通过调用register方法注册到ioc容器中，其中的app就是Ioc容器。singleton可以理解成我们的上面例子中的bind方法。只不过这里singleton指的是单例模式。

class CacheServiceProvider{
    public function register()
    {
        $this->app->singleton('cache', function ($app) {
            return new CacheManager($app);
        });

        $this->app->singleton('cache.store', function ($app) {
            return $app['cache']->driver();
        });

        $this->app->singleton('memcached.connector', function () {
            return new MemcachedConnector;
        });
    }
}
```

具体服务提供者 register 方法是什么时候执行的，我们到讲 laravel 生命周期的时候再详细说。

好了我们这个章节的内容就到这里。