# 视图入门：Laravel 支持的视图格式以及在路由中的基本使用

### Laravel 视图概述

我们在之前几篇教程中定义的路由大多数返回的都是纯字符串文本或者字符串拼接的 HTML，这主要是为了测试方便，在实际开发中，除了 API 路由返回指定格式数据对象外，大部分 Web 路由返回的都是视图，以便实现更加复杂的页面交互，我们在前面已经看到过了视图的定义方式：

1

```
return view('以.分隔的视图模板路径');
```



我们将在这篇教程中具体讨论视图实现技术。

视图是 [MVC 模式](https://laravelacademy.org/post/9614.html#toc-0)中的 View 部分，大部分视图都应该是 HTML 格式文本，在 Laravel 中，支持三种格式的视图文件解析：CSS 文件，原生 PHP 和 Blade 模板（下一篇教程将会讨论）。从文件名扩展就可以区分它们，CSS 文件后缀是 `.css`，通过文件引擎解析，原生 PHP 文件后缀是 `.php`，通过 PHP 引擎解析，Blade 模板文件后缀是 `.blade.php`，通过 Blade 引擎解析（底层实现逻辑可参考 `vendor/laravel/framework/src/Illuminate/View/ViewServiceProvider.php` 中的 `registerEngineResolver` 方法）。

Laravel 在解析视图时是通过实时解析文件后缀名再调用相应的引擎进行处理的，视图文件位于 `resources/views` 目录下，对于多级子目录以「.」号分隔，并且引用时不带文件后缀名。我们可以定义三个路由进行简单的测试：

1

```
// php 
```

2

```
Route::get('user/{id?}', function ($id = 1) {
```

3

```
    return view('user.profile', ['id' => $id]);
```

4

```
})->name('user.profile');
```

5

```

```

6

```
// blade
```

7

```
Route::get('page/{id}', function ($id) {
```

8

```
    return view('page.show', ['id' => $id]);
```

9

```
})->where('id', '[0-9]+');
```

10

```

```

11

```
// css
```

12

```
Route::get('page/css', function () {
```

13

```
    return view('page.style');
```

14

```
});
```



然后在 `resources/views` 目录下创建 `user/profile.php` 文件：

1

```
用户ID: <?php echo $id;?>
```



在 `resources/views` 目录下创建 `page/show.blade.php` 文件：

1

```
页面ID: {{ $id }}
```



最后在 `resources/views` 目录下创建 `page/style.css` 文件：

1

```
body {
```

2

```
    background: gray;
```

3

```
}
```



当我们按照路由在浏览器中查看返回视图时，都可以正常解析。在实际开发过程中，我们大多会使用 Blade 模板，因为其提供了丰富的语法从而简化了视图文件的编写。

### 视图返回与参数传递

Laravel 提供了多个语法糖在路由中返回视图，如辅助函数 `view` 或 `View::make` 方法，还可以注入 `Illuminate\View\View Factory` 类（最底层实现），通常我们使用辅助函数 `view`，因为最简洁：

1

```
// 使用 view 辅助函数
```

2

```
Route::get('/', function () { 
```

3

```
    // 该函数会在 resources/views 目录下查找 home.blade.php 或 home.php 视图文件，
```

4

```
    // 加载文件内容并解析 PHP 变量或语句，然后传递给响应，最终呈现给用户
```

5

```
    return view('home'); 
```

6

```
});
```



如果要传递数据给视图，可以这么做（多个数据以数组方式传递），这样就可以将 `tasks` 数据变量传递到视图以便在视图中进行引用：

1

```
return view('home')->with('tasks', Task::all());
```



还可以这么做：

1

```
return view('home', ['tasks' => Task::all()]);
```



推荐使用后者，因为简单。

### 在视图间共享变量

有时候在不同视图间传递同一个数据变量很麻烦，是否可以做到一次定义，多处使用呢？答案是可以，通过视图对象提供的 `share` 方法即可实现，我们可以在某个服务提供者如 `AppServiceProvider` 的 `boot` 方法中定义共享的视图变量：

1

```
view()->share('siteName', 'Laravel学院');
```

2

```
view()->share('siteUrl', 'https://xueyuanjun.com');
```



然后就可以在各个视图中使用 `$siteName` 和 `$siteUrl` 这两个变量了（其它变量定义方式类似），而无需每次传递这个数据变量，比如我们在不修改路由定义的前提下修改上述 `resources/views/page/show.blade.php` 文件：

1

```
页面ID: {{ $id }}
```

2

```
<hr>
```

3

```
By <a href="{{ $siteUrl }}">{{ $siteName }}</a>
```



这样，在浏览器中访问 `http://blog.test/page/111`，就能看到如下输出了：

![img](%E8%A7%86%E5%9B%BE%E5%85%A5%E9%97%A8%EF%BC%9ALaravel%20%E6%94%AF%E6%8C%81%E7%9A%84%E8%A7%86%E5%9B%BE%E6%A0%BC%E5%BC%8F%E4%BB%A5%E5%8F%8A%E5%9C%A8%E8%B7%AF%E7%94%B1%E4%B8%AD%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8/fb19326ace6ef7bfda68644af5f6fec4.jpg)

对于那些位于页头、页尾、面包屑或导航条中的数据变量，使用共享变量的方式定义将会很方便。