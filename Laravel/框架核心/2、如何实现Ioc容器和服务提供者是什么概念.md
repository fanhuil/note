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

> 补充

Laravel中容器共有15个方法，简单分类了下:

![img](2%E3%80%81%E5%A6%82%E4%BD%95%E5%AE%9E%E7%8E%B0Ioc%E5%AE%B9%E5%99%A8%E5%92%8C%E6%9C%8D%E5%8A%A1%E6%8F%90%E4%BE%9B%E8%80%85%E6%98%AF%E4%BB%80%E4%B9%88%E6%A6%82%E5%BF%B5/1797787-d65f75f86bd0131b.png)

## 注册

### bind

先来看下注册，Laravel的容器支持好多种注册方式，先看最常用的`bind`，其函数签名是：

```php
public function bind($abstract, $concrete = null, $shared = false);
```

看到签名中有3个参数，在函数内部经过各种操作后，最终落地到存储上，形式是：

```php
$bindings = [
  'abstract' => [
    'concrete' => $concrete,
    'shared' => $shared;
   ],   
];
```

`bind`在注册上，像之前提到过的，可以注册文本、数值，甚至是对象、接口、回调函数，下面就每种形式给出测试，

先看闭包形式：

```php
public function testClosureResolution()
    {
        $container = new Container;
        $container->bind('name', function () {
            return 'Taylor';
        });
		// dd($container);
        $this->assertEquals('Taylor', $container->make('name'));
    }
```

上面为了测试，通过`dd`可以打印出好container来，我们看到

```shell
Illuminate\Container\Container {#20
  #resolved: []
  #bindings: array:1 [
    "name" => array:2 [
      "concrete" => Closure {#21
        class: "LaravelContainerTest"
      }
      "shared" => false
    ]
  ]
  #instances: []
  #aliases: []
  #extenders: []
  #tags: []
  #buildStack: []
  +contextual: []
  #reboundCallbacks: []
  #globalResolvingCallbacks: []
  #globalAfterResolvingCallbacks: []
  #resolvingCallbacks: []
  #afterResolvingCallbacks: []
}
```

上面是container的内部，经过bind后，里面的`bindings`多了我们注册过的`name`，下一步注册过了，就应该要调用`make`实例化出来，调用`make`后，container中resolved多个key

```shell
 #resolved: array:1 [
    "name" => true
  ]
```

在实现make的时候，通过判断是否是闭包来判断，如果是闭包，则直接调用，否则通过反射机制实例化出来

```php
if ($concrete instanceof Closure) {
   return $concrete($this, $parameters);
}

$reflector = new ReflectionClass($concrete);
```

### instance

instance是将我们已经实例化出来的对象、文本等注册进入容器，使用方法如下

```php
    public function testSimpleInstance()
    {
        $c = new Container();
        $name = 'zhuanxu';
        $c->instance('name',$name);
        $this->assertEquals($name,$c->make('name'));
    }
```

instance方法将其写入到`instances: []`中

### singleton

```php
$container = new Container;
$container->singleton('ContainerConcreteStub');

$var1 = $container->make('ContainerConcreteStub');
$var2 = $container->make('ContainerConcreteStub');
$this->assertSame($var1, $var2);
```

singleton是对bind的简单封装

```php
public function singleton($abstract, $concrete = null)
    {
        $this->bind($abstract, $concrete, true);
    }
```

### alias

```php
public function testAliases()
    {
        $container = new Container;
        $container['foo'] = 'bar';
        $container->alias('foo', 'baz');
        $container->alias('baz', 'bat');
        $this->assertEquals('bar', $container->make('foo'));
        $this->assertEquals('bar', $container->make('baz'));
        $this->assertEquals('bar', $container->make('bat'));
        $container->bind(['bam' => 'boom'], function () {
            return 'pow';
        });
        $this->assertEquals('pow', $container->make('bam'));
        $this->assertEquals('pow', $container->make('boom'));
        $container->instance(['zoom' => 'zing'], 'wow');
        $this->assertEquals('wow', $container->make('zoom'));
        $this->assertEquals('wow', $container->make('zing'));
    }
```

alias函数是通过起别名的方式来让容器`make`

### share

`share`是通过闭包的形式，加上关键字`static`实现的

```php
public function share(Closure $closure)
    {
        return function ($container) use ($closure) {
            static $object;

            if (is_null($object)) {
                $object = $closure($container);
            }

            return $object;
        };
    }
```

### extend

extend是在当原来的容器实例化出来后，可以对其进行扩展

```php
public function testExtendInstancesArePreserved()
    {
        $container = new Container;
        $container->bind('foo', function () {
            $obj = new StdClass;
            $obj->foo = 'bar';

            return $obj;
        });
        $obj = new StdClass;
        $obj->foo = 'foo';
        $container->instance('foo', $obj);
        $container->extend('foo', function ($obj, $container) {
            $obj->bar = 'baz';

            return $obj;
        });
        $container->extend('foo', function ($obj, $container) {
            $obj->baz = 'foo';

            return $obj;
        });
        
        $this->assertEquals('foo', $container->make('foo')->foo);
        $this->assertEquals('baz', $container->make('foo')->bar);
        $this->assertEquals('foo', $container->make('foo')->baz);
    }
```

## 实例化

### call

call直接调用函数，自动注入依赖

```php
public function testCallWithDependencies()
    {
        $container = new Container;
        $result = $container->call(function (StdClass $foo, $bar = []) {
            return func_get_args();
        });

        $this->assertInstanceOf('stdClass', $result[0]);
        $this->assertEquals([], $result[1]);

        $result = $container->call(function (StdClass $foo, $bar = []) {
            return func_get_args();
        }, ['bar' => 'taylor']);

        $this->assertInstanceOf('stdClass', $result[0]);
        $this->assertEquals('taylor', $result[1]);

        /*
         * Wrap a function...
         */
        $result = $container->wrap(function (StdClass $foo, $bar = []) {
            return func_get_args();
        }, ['bar' => 'taylor']);

        $this->assertInstanceOf('Closure', $result);
        $result = $result();

        $this->assertInstanceOf('stdClass', $result[0]);
        $this->assertEquals('taylor', $result[1]);
    }
```



























