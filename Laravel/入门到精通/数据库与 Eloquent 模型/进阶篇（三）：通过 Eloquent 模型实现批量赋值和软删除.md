# 通过 Eloquent 模型实现批量赋值和软删除

在[上一篇教程](https://laravelacademy.org/post/9699.html)中，我们基于 Eloquent 模型实现了对数据表记录的增删改查操作，今天我们在此基础上介绍两个 Eloquent 模型提供的高级功能 —— 批量赋值和软删除。

### 批量赋值

批量赋值主要用于快速设置模型属性。

在介绍批量赋值之前，我们先看一个例子，之前我们新增或者修改 Eloquent 模型时都是通过依次设置每个属性来实现的：

```php
$post = new App\Post;
$post->title = '测试文章标题';
$post->content = '测试文章内容';
$post->user_id = 1;
$post->save();
```

如果模型类就那么三五个属性还好，如果是十几个甚至几十个呢？每次这么做得崩溃掉，到时候我们的控制器类里面可能会遍布这种设置代码，Laravel 号称的优雅就是打脸了。所以这个时候，批量赋值就粉墨登场了，批量赋值就是为我们解决这个问题的。

#### 创建模型

以创建模型实例为例，批量赋值允许我们以数组的方式将待设置属性以关联数组的方式传递构造函数：

```php
$post = new Post([
    'title' => '测试文章标题', 
    'content' => '测试文章内容'
]);
```

仅这么看的话，好像跟之前的写法没有什么大的优势，还是需要指定每个属性，但是这为我们提供了一个很好的基础，如果和用户请求数据结合起来使用，就能焕发它的光彩了。比如，如果我们的请求数据是一个文章发布表单提交过来的数据，包含 `title`、`content` 等字段信息，就可以通过下面这种方式进行批量赋值了：

```php
$post = new Post($request->all());
```

这样一来，不管多少字段，一条语句就搞定了全部属性的赋值。但是，细心的同学可能会发现，这里有一个安全隐患，如果用户发布的时候，包含了用户字段 `user_id`，并且设置的不是自己的用户 ID，而是其它用户的 ID，发布出来的文章就变成其他人发布的了；又或者文章需要审核后才能发布，但用户在表单中传递了状态字段将文章状态设置为审核通过，这样文章保存后就直接是已发布状态了。诸如此类的问题还有很多，总而言之，批量赋值给我们带来便利的同时，也给我们带来了烦恼。

作为一个成熟的 ORM 框架，Eloquent 在设计之初肯定不会没有考虑到这样的问题，实际上，我们可以借助模型类中的白名单属性或黑名单属性来解决这个困扰。

所谓白名单属性就是该属性中指定的字段才能应用批量赋值，不在白名单中的属性会被忽略；与之相对的，黑名单属性指定的字段不会应用批量赋值，不在黑名单中的属性则会应用批量赋值。可以看到，这两个属性是互斥的，只要设置一个属性就可以解决所有问题了，不要同时设置两个属性。

Eloquent 模型类默认白名单属性为空，黑名单属性为 `*`，即所有字段都不会应用批量赋值：

```php
/**
 * 使用批量赋值的属性（白名单）
 *
 * @var array
 */
protected $fillable = [];

/**
 * 不使用批量赋值的字段（黑名单）
 *
 * @var array
 */
protected $guarded = ['*'];
```

我们在实际开发中，对于频繁变动的数据表，建议使用白名单，这样安全性更好，因为哪些字段应用批量赋值始终是可控的，黑名单则会在后续新增字段的时候容易遗漏。而对于相对稳定或者字段很多的数据表，建议使用黑名单，免去设置字段之苦，但是对于这样的模型类，每次修改数据表结构的时候都要记得维护这个黑名单，看看是否需要变动。

所以，以 `Post` 模型为例，我们需要为其设置一个黑名单字段：

```php
protected $guarded = ['user_id'];
```

白名单和黑名单都是以数组属性，支持设置多个字段。这样设置就代表除了 `user_id` 字段之外，所有其它字段都支持批量赋值。

那排除在批量赋值之外的字段怎么设置呢？只能通过模型属性来设置了：

```php
$post = new Post($request->all());
$post->user_id = 0;
$post->save();
```

#### 更新模型

如果是更新模型类，也可以通过批量赋值的方式实现，只需在获取模型类后使用 `fill` 方法批量填充属性即可：

```php
$post = Post::findOrFail(11);
$post->fill($request->all());
$post->save();
```

### 软删除

我们在日常开发过程中，删除数据库记录在所难免，但是我们多数时候并不想从数据库中物理删除记录，而只是想从业务角度逻辑删除。

> 注：所谓物理删除就是彻底删除该记录，逻辑删除只是给这条记录打上一个「已删除」的标记，不再出现在查询结果中，但是并没有真正删除这条记录。

#### 实现原理

Eloquent 模型类为我们提供了「软删除」功能的支持。这就意味着，在 Laravel 中，我们不需要编写任何额外代码就可以实现对数据库记录的「软删除」。其底层实现原理是在支持软删除的数据表中添加一个 `deleted_at` 字段，这可以通过数据库迁移来实现。比如我们想要让 `posts` 表支持软删除，需要为其创建一个数据库迁移：

```php
php artisan make:migration alter_posts_add_deleted_at --table=posts
```

然后在新生成的迁移文件中编写代码如下：

```php
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class AlterPostsAddDeletedAt extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('posts', function (Blueprint $table) {
            $table->softDeletes();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('posts', function (Blueprint $table) {
            $table->dropColumn('deleted_at');
        });
    }
}
```

这样，运行 `php artisan migrate` 命令即可在 `posts` 表中新增一个 `deleted_at` 字段。该字段默认值为 `NULL`，表示没有被软删除。如果要在模型类中支持软删除，需要在对应模型类（在本例中是 `Post` 模型）中添加支持软删除的 Trait

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class Post extends Model
{
    use SoftDeletes;

    protected $guarded = ['user_id'];
}
```

`SoftDeletes` Trait 提供了一系列与软删除相关的方法，下面我们会介绍到。

这样我们在模型类上做所有常规查询操作的时候就会过滤掉被软删除的记录（这些常规查询在[上一篇教程](https://laravelacademy.org/post/9699.html#toc-6)中已经给出）。

> 注：你也可以修改这个默认约定的 `deleted_at` 字段，但何必费这个劲呢，除非你是从其它系统迁移过来的，原来的表结构已经存在了，这时候可以通过再模型类中设置静态属性 `DELETED_AT` 来自定义软删除字段。

要软删除一条记录，在对应模型类实例上调用 `delete` 方法即可，底层会自动将数据表的 `deleted_at` 字段设置为当前时间，表示该记录已经被「删除」。

#### 相关方法

要判断一条记录是否被软删除，可以通过 `trashed` 方法：

```php
$post = Post::findOrFail(32);
$post->delete();
if ($post->trashed()) {
    dump('该记录已删除');
}
```

此时再查询 `id=32` 的记录，已经不存在了，报 404 异常。如果想要在查询结果中出现软删除记录，可以通过在查询的时候调用 `withTrashed` 方法实现：

```php
$post = Post::withTrashed()->find(32);
```

返回结果和正常查询结果一样：

![img](%E8%BF%9B%E9%98%B6%E7%AF%87%EF%BC%88%E4%B8%89%EF%BC%89%EF%BC%9A%E9%80%9A%E8%BF%87%20Eloquent%20%E6%A8%A1%E5%9E%8B%E5%AE%9E%E7%8E%B0%E6%89%B9%E9%87%8F%E8%B5%8B%E5%80%BC%E5%92%8C%E8%BD%AF%E5%88%A0%E9%99%A4/b738d0d52d6ba9bd6dc393d0a99ded8e.jpg)

在某些场景下，你可能只需要获取被软删除的记录，这可以通过 `onlyTrashed` 方法来实现：

```php
$post = Post::onlyTrashed()->where('views', 0)->get();
```

如果是误删除的话，你可以 `restore` 方法来恢复软删除记录：

```php
$post->restore();   // 恢复单条记录
Post::onlyTrashed()->where('views', 0)->restore(); // 恢复多条记录
```

最后，如果你确实是想物理删除数据表记录，通过 `forceDelete` 方法删除即可：

```php
$post->forceDelete();
```















