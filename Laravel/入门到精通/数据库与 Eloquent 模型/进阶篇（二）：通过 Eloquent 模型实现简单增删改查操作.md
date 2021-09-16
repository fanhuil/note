# 通过 Eloquent 模型实现简单增删改查操作

### 概述

Eloquent 是一个 ActiveRecord ORM 框架，ORM 全称是 Object Relational Mapping，意为对象关系映射，用于实现面向对象编程语言里不同类型系统的数据之间的转换，简单来说，它会构建类与数据表之间的映射关系，从而建立起一个可在编程语言里使用的「虚拟对象数据库」。「ActiveRecord」是 ORM 的一种实现模式，Eloquent 则是 Laravel 版的「ActiveRecord」。

一个 Eloquent 模型类映射一张数据表，通过模型类提供的方法，你可以获取其映射的数据表的所有记录，也可以获取单条记录，还可以创建、更新和删除对应数据表记录，而这一切都不需要你编写任何 SQL 语句、或者构建查询构建器即可完成。

Eloquent 专注于简单，并且和其他框架一样遵循「约定优于配置」，从而允许你通过最少的代码构建功能强大的模型类。

你可能对「约定优于配置」这个理念不太了解，看几个例子就明白了，比如模型类映射的表名、主键、关联关系、日期格式系统都约定好了，一般而言，遵循这个约定，你就可以不用做任何配置、编写任何额外代码即可完成功能，结合我们平时怎么创建使用模型类的，就可以更好的理解这一理念了。下面我们将模型类定义的时候还会强调这一点。

### 模型类定义

使用模型类之前，需要在数据库有对应的数据表，因为模型类就是数据表在面向对象编程语言中的映射。比如我们前面几篇教程中用到的 `User` 模型和 `Post` 模型都是这样，要创建一个模型类，需要使用 `make:model` 命令：

```bash
php artisan make:model Post
```

> 注：如果对应的数据表尚未创建，你还可以在创建模型类的同时创建对应的数据库迁移文件，通过 `php artisan make:model Post -m` 即可。如果你想将模型类创建到 `app/Models` 目录下，可以这么运行上述命令 `php artisan make:model Models/Post`。

接下来我们就是 `posts` 表映射的 `Post` 模型为例，来看看默认都有哪些约定。新生成的 `Post` 模型类如下：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    //
}
```

里面什么东西都没有，但是我们就可以通过它完成数据表记录的增删改查操作了，怎么做到的？这就是「约定优于配置」的功劳了。下面我们就来看看这些默认的约定。

#### 表名

Eloquent 约定模型类映射表名是将类名由驼峰格式转化为小写+下划线（含多个单词的话），最后将其转化为复数形式，比如 `Post` 对应表名是 `posts`、`PostTag` 对应表名是 `post_tags` 等等。当然，如果你不想遵循这个系统约定的规则，也可以通过手动设置模型类属性的方式进行自定义，例如：

```php
protected $table = 'articles';
```

#### 主键

Eloquent 默认假设每张数据表都有一个整型的自增主键，其字段名为 `id`，如果你的数据表主键名不是 `id`，可以通过 `$primaryKey` 属性来指定：

```php
protected $primaryKey = 'post_id';
```

如果主键不是自增的，还可以设置 `$incrementing` 属性为 `false`：

```php
public $incrementing = false;
```

如果主键不是整型，还可以设置 `$keyType` 属性为 `string`：

```php
protected $keyType = 'string';
```

#### 时间戳

Eloquent 默认约定每张表都有 `created_at` 和 `updated_at` 字段（迁移类中 `$table->timestamps()` 会生成这两个字段），并且在保存模型类时会自动维护这两个字段。如果你的数据表里面不包含这两个字段，或者只包含一个，都需要设置 `$timestamps` 属性为 `false`：

```
public $timestamps = false;
```

或者通过 `CREATED_AT` 和 `UPDATED_AT` 常量来设置自定义的创建和更新时间字段：

```php
public const CREATED_AT = 'create_time';
public const UPDATED_AT = 'update_time';
```

此外，默认时间的存储格式是 `Y-m-d H:i:s`，你还可以通过 `$dateFormat` 属性来自定义时间戳的格式，该属性值通过 PHP 的 `date()` 函数进行解析，所以原则上支持 [date](http://php.net/manual/en/function.date.php) 函数支持的所有语法格式，比如将时间设置为 Unix 时间戳：

```php
protected $dateFormat = 'U';
```

这样，保存到数据库的时间格式就是 Unix 时间戳了，前提是你的 `created_at` 和 `updated_at` 字段是整型，否则会报格式错误。

#### 数据库连接

```php
protected $connection = 'connection_name';
```

### 查询数据

日常开发中，大部分操作都是数据库中查询数据，Eloquent 模型了为我们提供了很多方法帮助我们从数据库中获取数据。

#### 获取所有记录

我们可以通过模型类提供的 `all` 方法获取一张表的所有记录：

```php
$posts = Post::all();
```

和[查询构建器](https://laravelacademy.org/post/9698.html)一样，该方法返回的也是集合，只不过是模型类集合：

![img](https://laravel.gstatics.cn/wp-content/uploads/2018/11/c41d1fc0002d441dd3ba9afedd497e82.jpg)

要获取指定模型类的字段属性，遍历该集合即可：

```php
foreach ($posts as $post) {
    dump($post->title);
}
```

和查询构建器一样，如果结果集很大的话，模型类也支持通过 `chunk` 方法分块获取查询结果：

```php
Post::chunk(10, function ($posts) {
   foreach ($posts as $post) {
       if ($post->views == 0) {
           continue;
       } else {
           dump($post->title . ':' . $post->views);
       }
   }
});
```

除此之外，在 Eloquent 模型中还可以通过 `cursor` 方法每次只获取一条查询结果，从而最大限度减少内存消耗：

```php
foreach (Post::cursor() as $post) {
    dump($post->title . ':' . $post->content);
}
```

#### 获取指定查询结果

如果想要指定查询条件和查询字段，可以通过 `where` 方法和 `select` 方法来实现：

```php
$posts = Post::where('views', '>', 0)->select('id', 'title', 'content')->get();
```

对应查询结果如下：

![img](https://laravel.gstatics.cn/wp-content/uploads/2018/11/343926aa88b8ba8ece2cbfaae4677e5a.jpg)

实际上，Eloquent 模型类底层的查询也是基于[查询构建器](https://laravelacademy.org/post/9698.html)来实现的，你可以在模型类上调用所有查询构建器的 [Where 查询方法](https://laravelacademy.org/post/9698.html#toc-2)，同样是以流接口的模式构建方法链调用即可。前面提到的 `chunk` 和 `cursor` 方法也适用于这种指定查询条件的查询操作。

因为是查询构建器，所以我们还可以在模型查询操作中对查询结果进行排序和分页：

```php
$posts = Post::where('views', '>', 0)->orderBy('id', 'desc')->offset(10)->limit(5)->get();
```

对应的返回结果如下：

![img](https://laravel.gstatics.cn/wp-content/uploads/2018/11/28dfefe6bcdf0a7d4125e9110d9d50d2.jpg)

#### 获取单条记录

当然，你也可以通过查询构建器的方式在模型类查询中获取单条记录：

```php
$user = User::where('name', '学院君')->first();
```

返回的结果是一个模型类实例：

![img](https://laravel.gstatics.cn/wp-content/uploads/2018/11/26df2f47d52ebbd094003c8f8de3359c.jpg)

你可以直接通过 `$user->name` 这样的方式访问模型类实例的属性。

此外，如果查询的条件是主键 ID 的话，还可以将上述调用简化为通过 `find` 方法来实现：

```php
$user = User::find(1);
```

返回结果与上面完全一致。

模型类查询结果为空会返回 `null`。如果你想要在单条记录返回结果为空时返回 404 响应（在控制器方法中可能需要用到类似操作），可以通过 `firstOrFail` 或者 `findOrFail` 方法在找不到对应记录时抛出 404 异常，从而简化代码编写：

```php
$user = User::findOrFail(111);
```

如果 `id=111` 的记录在 `users` 数据表中不存在，就会返回 404 响应：

#### 获取聚合结果

Eloquent 模型类同样支持 `count`、`sum`、`avg`、`max`、`min` 等聚合函数查询：

```php
$num = User::whereNotNull('email_verified_at')->count();       # 计数     
$sum = User::whereNotNull('email_verified_at')->sum('id');     # 求和    
$avg = User::whereNotNull('email_verified_at')->avg('id');     # 平均值   
$min = User::whereNotNull('email_verified_at')->min('id');     # 最小值   
$max = User::whereNotNull('email_verified_at')->max('id');     # 最大值
```

你会发现，如果你掌握了查询构建器，就等同于掌握了 Laravel 中的所有数据库查询操作。只不过将 `DB::table` 换成对应的模型类而已。

> 注：除获取单条记录之外，ELoquent 模型类查询返回的结果都是集合类，因此你可以在查询结果上调用集合类的所有方法，还可以自定义模型对应集合类，详情请查看对应[官方文档](https://laravelacademy.org/post/9585.html)。

### 插入数据

通过 Eloquent 模型类插入记录到数据库也比较简单：

```php
$post = new App\Post;
$post->title = '测试文章标题';
$post->content = '测试文章内容';
$post->user_id = 1;
$post->save();
```

创建时间和更新时间字段由 Eloquent 底层自动帮我们维护（遵循默认约定的话）。执行上面的代码就会在数据库新增一条记录（我们在 [Tinker](https://laravelacademy.org/post/9689.html) 中执行上述代码）：

![img](https://laravel.gstatics.cn/wp-content/uploads/2018/11/0e4357431bf35d7d72bc8702959911bd.jpg)

我们先要创建一个新的 `Post` 模型实例，然后依次设置需要设置的字段，最后调用 `save` 方法保存即可。

此外，Eloquent 还为我们提供了一些快捷的插入方法，比如 `firstOrCreate` 和 `firstOrNew`，这两个方法都会先尝试通过指定查询条件在数据库中查找对应记录，如果没有找到的话，会创建对应模型类的实例，并将查询条件作为对应字段值设置到模型属性上。两者的区别是 `firstOrCreate` 方法在设置完模型属性后会将该模型记录保存到数据库中，而 `firstOrNew` 不会：

```php
$post_1 = Post::firstOrCreate([
    'title' => '测试文章标题1',
    'user_id' => 1,
]);

$post_2 = Post::firstOrNew([
    'title' => '测试文章标题1',
    'user_id' => 1,
]);
```

不过学院君倒不建议这么做，感觉还是分开写代码可读性更好一些，也方便自己去处理一些异常情况。

### 更新数据

通过模型类更新数据表记录也很简单：

```php
$post = Post::find(31);
$post->title = '测试文章标题更新';
$post->save();
```

更新时间 Eloquent 底层会自动帮我们维护，执行上面的代码即可完成该 `$post` 模型对应数据表记录的更新：

![img](https://laravel.gstatics.cn/wp-content/uploads/2018/11/e2b4ce092608d48b5f1ed37720ced424.jpg)

同样，Eloquent 也为我们提供了快捷的更新方法 `updateOrCreate`，该方法首先会根据传入参数对模型对应记录进行更新，如果发现对应记录不存在，则会将更新数据作为初始数据插入数据库，并保存（同样也不建议这么做，除非你的场景特别适合）：

```php
$user = user::updateOrCreate(
    ['name' => '学院君'],
    ['email' => 'admin@laravelacademy.org']
);
```

有的时候我们可能需要批量更新模型对应数据表的多条记录，这可以借助查询构建器来实现：

```php
Post::where('views', '>', 0)->update(['views' => 100]);
```

### 删除数据

通过模型类删除对应数据表记录和更新记录类似，都要先获取对应操作模型实例，删除对应记录更简单，获取到模型实例后，直接调用其删除方法即可：

```php
$post = Post::find(31);
$post->delete();
```

这样，就完成了 `id = 31` 对应数据表记录的删除，你还可以通过 Eloquent 提供的 `destroy` 方法一次删除多条记录，通过数组传递多个主键 ID 即可：

```php
Post::destroy([1,2,3]);
```

当然，你也可以通过查询构建器的方式删除指定记录：

```php
$user = User::where('name', '学院君')->fisrt();
$user->delete();
```















