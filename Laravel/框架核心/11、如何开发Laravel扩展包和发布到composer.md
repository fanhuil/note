## 如何自己开发一个 laravel 的扩展包，供大家使用呢？

我们来做一个根据第一个字符或者汉字生成头像的 laravel 扩展包。其实原理就是我们自己去写一个服务提供者，把服务提供者配置到 app/providers 数组中。

1. 第一步现在自己项目中根目录创建 packages/cxp/avatar/src
2. 修改 composer.json

```json
"psr-4": {
    "App\\": "app/",
    "Cxp\\Avatar\\": "packages/cxp/avatar/src/"
}
```

​	3.执行 composer dumpautoload

​	4.src 目录创建 Avatar.php 具体代码

```php

/**
 * Created by PhpStorm.
 * User: mac
 * Date: 2019-01-10
 * Time: 14:06
 */
namespace Cxp\Avatar;
use Illuminate\Config\Repository;
class Avatar {
    protected $config;
    /**
     * 构造方法
     */
    public function __construct(Repository $config)
    {
        $this->config = $config->get('avatar');
    }
    /**
     * 生成图像
     * @return resource 图片资源
     */
    private function generate($name)
    {
        // 创建图片资源
        $img_res = imagecreate($this->config['width'], $this->config['height']);
        // 背景颜色
        $bg_color = imagecolorallocate($img_res, mt_rand(120, 190), mt_rand(120, 190), mt_rand(120, 190));
        // 文字颜色
        $font_color = imagecolorallocate($img_res, mt_rand(190, 255), mt_rand(190, 255), mt_rand(190, 255));
        // 填充背景色
        imagefill($img_res, 1, 1, $bg_color);
        // 计算文字的宽高
        $pos = imagettfbbox($this->config['size'], 0, $this->config['font_file'], mb_substr($name, 0, 1));
        $font_width = $pos[2] - $pos[0] + 0.32 * $this->config['size'];
        $font_height = $pos[1] - $pos[5] + -0.16 * $this->config['size'];
        // 写入文字
        imagettftext($img_res, $this->config['size'], 0, ($this->config['width'] - $font_width) / 2, ($this->config['height'] - $font_height) / 2 + $font_height, $font_color, $this->config['font_file'], mb_substr($name, 0, 1));
        return $img_res;
    }
    /**
     * 输出图片（默认输出到浏览器，给定输出文件位置则输出到文件）
     * @param string|false $path 保存路径
     */
    public function output($name, $path = false)
    {
        $img_res = $this->generate($name);
        // 确定输出类型和生成用的方法名
        $content_type = 'image/' . $this->config['type'];
        $generateMethodName = 'image' . $this->config['type'];
        // 确定是否输出到浏览器
        if (!$path) {
            header("Content-type: " . $content_type);
            $generateMethodName($img_res);
        } else {
            $generateMethodName($img_res, $path);
        }
        // 释放图片内存
        imagedestroy($img_res);
    }
}
```

​	5.在 src 下创建 config 目录来存取我们的配置参数文件如 config/avatar.php

```php

/**
 * Created by PhpStorm.
 * User: mac
 * Date: 2019-01-10
 * Time: 14:28
 */
return   [
    'type' => 'png', // jpeg|png|gif|bmp
    'width' => '100',
    'height' => '100',
    'size' => '26',
    'font_file' => public_path() . '/fonts/WawaSC-Regular.otf',
];
```

6.在 src 创建 AvatarProvider.php 即服务提供者。供 ioc 容器注册

```php
namespace Cxp\Avatar;
use Illuminate\Support\ServiceProvider;
class AvatarProvider extends ServiceProvider
{
    /**
     * Bootstrap the application services.
     *
     * @return void
     */
    public function boot()
    {
        // 发布配置文件
        $this->publishes([
            __DIR__.'/config/avatar.php' => config_path('avatar.php'),
        ]);
    }
    /**
     * Register the application services.
     *
     * @return void
     */
    public function register()
    {
        $this->app->singleton('avatar', function ($app) {
            return new Avatar($app['config']);
        });
    }
}
```

7.如果想使用门脸，可以在 src 目录下创建 Facades 目录，提供门脸

```php
namespace Cxp\Avatar\Facades;
use Illuminate\Support\Facades\Facade;
class Avatar extends Facade
{
    protected static function getFacadeAccessor()
    {
        return 'avatar';
    }
}
```

到此我们开发就完成了，那该怎么使用了

1.先发布配置文件在 config 目录下面

```bash
php artisan vendor:publish
```

2.在 app/config 目录注册我们的服务提供者和门脸类

```php
'providers' => [
    Cxp\Avatar\AvatarProvider::class,
]

'aliases' => [
    'Avatar' => Cxp\Avatar\Facades\Avatar::class,
]
```

3.程序中使用

Avatar::output (' 赵 ','zhao.png')

```json

## 如果发布到composer 供大家使用呢

1.在avatar目录执行composer init,生成composer.json

​```php
{
    "name": "cxp/laravel-avatar",
    "description": "laravel avatar",
    "license": "MIT",
    "authors": [
        {
            "name": "cxp1539",
            "email": "457714145@qq.com"
        }
    ],
    "autoload": {
      "psr-4": {
        "Cxp\\Avatar\\": "src"
      }
    },
    "require": {}
}    
```



2. 在 github 创建个项目，将 avatar 目录的代码推送到 github 上。

3. 打开 https://packagist.org/ 注册个账号，提交 git 的地址就可以了。



```php
"extra": {
    "laravel": {
        "providers": [
            "Barryvdh\\Debugbar\\ServiceProvider"
        ],
        "aliases": {
            "Debugbar": "Barryvdh\\Debugbar\\Facade"
        }
    }
}
```

把你所有的 provider 写入到 providers 中，Facade 写入到 aliases 中，是不是很简单？为啥可以这么做呢？因为在 laravel 框架的引导中，Laravel 会读取 vendor/composer/installed.json，这个文件记录了所有安装的第三包的 composer.json 文件的内容，installed.json 中的每一个元素对应一个 composer 包，Laravel 会逐一解析，这就是上面的方式为什么可以生效的原因，下面这是我项目中的 installed.json 截图：

![img](11%E3%80%81%E5%A6%82%E4%BD%95%E5%BC%80%E5%8F%91Laravel%E6%89%A9%E5%B1%95%E5%8C%85%E5%92%8C%E5%8F%91%E5%B8%83%E5%88%B0composer/6yQ9xxQLWn.png!large)





