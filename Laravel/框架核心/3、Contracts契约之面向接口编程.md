这一节的内容很简单。其实在我们讲依赖注入的时候已经说过了。我们再来具体解释一下。

## 什么是契约

1. 契约就是所谓的面向接口编程。

### 我们拿之前的例子说如果不使用接口会有什么问题？

```php

// 文件记录日志
class FileLog
{
    public function write(){
        echo 'file log write...';
    }
}

// 数据库记录日志
class DatabaseLog
{
    public function write(){
        echo 'database log write...';
    }
}

class User
{
    protected $log;
    public function __construct(FileLog $log)
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

$user = new User(new FileLog());
$user->login();
```

就说上面的，看似没有什么问题，那如果随着我们日后需求的变更，想更换数据库作为记录日志的方式呢？那就得去改 User 类，没有解偶。

所以才有了面向接口编程，也就是 laravel 中的契约。代码修改如下。

```php

// 定义日志的接口规范
interface log
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

$user = new User(new DatabaseLog());
$user->login();
```

## 在 Laravel 中契约是什么样子的？

比如 Cache，定义的契约规范在 Illuminate\Contracts\Cache\Repository 文件中。

我们可以写多种缓存方式如 file,redis,memcached 实现这个契约，编写契约中的 set,get,remove 之类的方法

在使用上，跟上面的例子一样，构造函数或者方法只需要传入对应的契约接口，使用的时候可以随意更换 file,redis,memcached
