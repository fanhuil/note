## trait 是什么？

Trait 是为类似 PHP 的单继承语言而准备的一种代码复用机制。Trait 为了减少单继承语言的限制，使开发人员能够自由地在不同层次结构内独立的类中复用 method。Trait 和 Class 组合的语义定义了一种减少复杂性的方式，避免传统多继承和 Mixin 类相关典型问题。

Trait 和 Class 相似，但仅仅旨在用细粒度和一致的方式来组合功能。 无法通过 trait 自身来实例化。它为传统继承增加了水平特性的组合；也就是说，应用的几个 Class 之间不需要继承。

简单来说 trait 就是解决 php 不能实现多继承的。

## 具体的案例

如我们定义类的时候，很多都要做成单例模式，因此我们只需要定一个 trait 在需要单例的时候 use trait 就可以了。下面看下代码

```php

trait Singleton {
    protected static $_instance;

    final public static function getInstance() {
        if(!isset(self::$_instance)) {
            self::$_instance = new static();
        }

        return self::$_instance;
    }

    private function __construct() {
        $this->init();
    }

    protected function init() {}

}

class Db {
    use Singleton;
    protected function init() {

    }
}
```

## trait 在 Laravel 中的应用

laravel 中大量的使用到了 trait, 下面我拿比较常用的 Laravel Auth 来看，首先执行

```bash
php artisan make:auth
```

然后到 app/Http/Controllers/Auth/ 目录下找到 LoginController，你会发现 LoginController 中的代码比较少，只定义了一个 use AuthenticatesUsers; 处理数据，登录之类的核心功能都不在这个控制器中。我们然后打开这个 AuthenticatesUsers，你会发现所有有关 login 的功能都在这个文件里面。

那有同学就会感到疑问了，为什么不直接写到 LoginController 呢？下面我来给大家说下

AuthenticatesUsers 这个里面实现了根 login 的业务逻辑，但是呢？所有的可配置的参数，可重写改的方法你都可以在 LoginController 中就实现重写，这样代码不就简洁了嘛。我们随意找一个举例，我们重写 login 的验证加入极验验证。

```php

namespace App\Http\Controllers\Auth;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;
use Illuminate\Validation\ValidationException;
use Illuminate\Foundation\Auth\AuthenticatesUsers;

class LoginController extends Controller
{

    use AuthenticatesUsers;

    protected function redirectTo()
    {
        return '/';
    }

    protected $gee;

    public function __construct(GeeCaptchaController $gee)
    {
        $this->gee = $gee;
        $this->middleware('guest')->except('logout');
    }

    /**
     * 重写login验证逻辑，增加极验验证
     *
     * @param  \Illuminate\Http\Request  $request
     * @return void
     */
    protected function validateLogin(Request $request)
    {
        $this->validate($request, [
            $this->username() => 'required|string',
            'password' => 'required|string',
        ]);

        if (!$this->gee->verify()) {
            throw ValidationException::withMessages([
                'captcha' => '无效的验证码失效!',
            ]);
        }
    }
}
```

