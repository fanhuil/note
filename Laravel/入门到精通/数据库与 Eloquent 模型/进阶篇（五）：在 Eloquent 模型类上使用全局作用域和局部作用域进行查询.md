# 进阶篇（五）：在 Eloquent 模型类上使用全局作用域和局部作用域进行查询

### 问题引出

在[通过 Eloquent 模型实现增删改查](https://laravelacademy.org/post/9699.html)这篇教程中，我们已经学习了如何在 Eloquent 模型类中进行各种查询，但是这些查询大多需要手动调用查询构建器提供的各种方法来实现。如果有一些查询需要在多个地方调用，那么在每个地方都要编写同样的代码，有没有什么办法对这种场景下的查询代码进行优化呢？

Eloquent 模型类提供的「Scope」功能就可以帮我们实现这种优化。「Scope」字面意义上翻译为「作用域」，有点不那么好理解，从功能上来说，把它看作预置的「过滤器」更合适。我们将那些需要在多处调用的查询条件编写过滤器，然后将调用查询代码的地方改为调用过滤器，调用过滤器比编写那些冗长而重复的查询方法更加便捷，可读性也更好。

从调用方式或者过滤器的作用范围来说，可以把「作用域」分为「全局作用域」和「局部作用域」。「作用域」都是围绕模型类展开的，不管是全局作用域还是局部作用域，都是作用到某个模型类上。接下来，我们就来演示如何在 Eloquent 模型类上使用「作用域」进行查询。

### 全局作用域

所谓「全局作用域」，指的是预置过滤器在注册该「全局作用域」的模型类的所有查询中生效，不需要指定任何额外条件。

以 `User` 模型类为例，我们在系统中可能只想针对已经验证过邮箱的用户进行操作，在没有介绍「作用域」之前，可能你会在应用中到处编写这样的代码：

```php
$users = User::whereNotNull('email_verified_at')->...
```

接下来，我们通过「全局作用域」来优化上面的代码。

#### 通过全局作用域类实现

要实现「全局作用域」，首先需要编写一个实现 `Illuminate\Database\Eloquent\Scope` 接口的全局作用域类，这里我们将其命名为 `EmailVerifiedAtScope`，并将其放到 `app/Scopes` 目录

```php
<?php
namespace App\Scopes;

use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Scope;

class EmailVerifiedAtScope implements Scope
{
    public function apply(Builder $builder, Model $model)
    {
        return $builder->whereNotNull('email_verified_at');
    }
}
```

在这个全局作用域类中，只需要实现 `apply` 方法即可，在该方法中，在查询构建器上应用过滤器方法并将其返回。

然后，我们需要将这个全局作用域类注册到 `User` 模型类上，这样，在 `User` 模型类上进行查询的时候才可以应用相应的过滤条件。这个工作可以通过在 `User` 模型类中重写父类的 `boot` 方法来完成：

```php
protected static function boot()
{
    parent::boot();

    static::addGlobalScope(new EmailVerifiedAtScope());
}
```

> 注：`boot` 方法会在模型类实例化的时候调用。你可以在这里进行一些模型类的初始化操作。

这样，就可以将刚刚编写的全局过滤器应用到 `User` 模型上。如果有多个全局作用域类，可以多次调用 `static::addGlobalScope` 方法来注册。

这样，当我们通过 `User` 模型类进行查询的时候，就会自动应用全局作用域指定的查询条件了，以 `User::all()` 为例，我们通过 [Telescope](https://laravelacademy.org/post/9687.html) 的 「Queries」 页面就能看到对应的 SQL：

```php
select * from `users` where `email_verified_at` is not null
```

包含了全局作用域的过滤条件。

这样，我们就可以把之前查询代码 `User::whereNotNull('email_verified_at')->...` 中对 `email_verified_at` 的过滤条件去掉了。

#### 通过匿名函数实现

如果你觉得编写一个「全局作用域」类很麻烦，过滤逻辑又很简单，还可以在模型类的 `boot` 方法中通过匿名函数实现全局作用域：

```php
protected static function boot()
{
    parent::boot();

    //static::addGlobalScope(new EmailVerifiedAtScope());
    static::addGlobalScope('email_verified_at_scope', function (Builder $builder) {
        return $builder->whereNotNull('email_verified_at');
    });
}
```

实现效果和上面通过全局作用域类完全一样。

#### 移除全局作用域

在某些特定场景下，我们可能需要移全局作用域，比如在后台用户管理页，我们需要将未验证邮箱的用户页显示出来，这个时候我们可以借助模型类的 `withoutGlobalScope` 方法来实现，该方法支持多种传参格式，移除多种全局作用域及其组合：

```php
User::withoutGlobalScope(EmailVerifiedAtScope::class)->get(); # 指定类
User::withoutGlobalScope('email_verified_at_scope')->get();   # 匿名函数
User::withoutGlobalScopes()->get();  # 移除所有全局作用域
User::withoutGlobalScopes([FirstScope::class, SecondScope::class])->get();   # 移除多个类/匿名函数
```

### 局部作用域

「全局作用域」虽然强大，但不够灵活，有的时候我们的预置过滤器可能因不同场景而已，不同场景需要不同的预置过滤器，这个时候就不能使用「全局作用域」了，要改用「局部作用域」，在不同场景应用不同的局部作用域来完成查询功能。

所谓「局部作用域」，指的是预置过滤器在对应模型类的指定查询中生效，与「全局作用域」不同，「局部作用域」需要额外指定才能生效，但是相应的，也更加灵活，可以适用于不同场景。

「局部作用域」的实现也比较简单，在需要应用它的模型类中定义一个过滤器方法即可。该方法需要以 `scope` 开头，然后附加该过滤器的名称，以文章列表页显示最流行文章为例（按照浏览数逆序），可以在 `Post` 模型类中编写一个 `scopePopular` 方法：

```php
public function scopePopular(Builder $query)
{
    return $query->where('views', '>', '0')->orderBy('views', 'desc');
}
```

而在文章详情页，我们希望展示的是已发布的文章详情，如果文章没有发布，返回 404，因此我们再定义一个「局部作用域」方法 `scopeActive`（没有 `status` 字段的话新增一个）：

```php
public function scopeActive(Builder $query)
{
    return $query->where('status', Post::ACTIVED);
}
```

同时这个作用域也要应用到列表页，否则影响用户体验。

在模型类上调用「局部作用域」过滤器方法只需调用 `scope` 之后的过滤器名称即可，Eloquent 底层会通过魔术方法自动调用对应完整方法：

```php
$post = Post::active()->find(100);
```

对应的 SQL 语句如下：

```php
select * from `posts` where `status` = ? and `posts`.`id` = ? and `posts`.`deleted_at` is null limit 1
```

说明局部作用域已经生效了，通过这个例子你可能不觉得「局部作用域」的优势，我们来看列表页的查询。我们可以在模型类上通过方法链的方式应用多个「局部作用域」，所以对于按照浏览数逆序查询，可以通过下面这种方式实现：

```php
$post = Post::active()->popular()->get();
```

对应的 SQL 语句如下：

```php
select
  *
from
  `posts`
where
  `status` = 1
  and `views` > "0"
  and `posts`.`deleted_at` is null
order by
  `views` desc
```

如果我们要把这个 SQL 语句转化为查询构建器的话，显然需要编写多个查询方法，而且如果要在多个地方进行这种查询，一方面代码可读性很差，另一方面而且容易出错，可维护性不好，每次修改一个地方的参数，其它地方要同步修改，换成局部作用域来实现，既清晰又简洁。推荐使用这种方式来构建需要在多个场景调用的复杂 Eloquent 查询。

移除局部作用域很简单，不要在查询中指定对应的过滤器方法即可。

### 动态作用域

此外，Eloquent 模型类还支持「动态作用域」，所谓动态作用域指的是在查询过程中动态设置预置过滤器的查询条件，动态作用域和局部作用域类似，过滤器方法名同样以 `scope` 开头，只不过可以通过额外参数指定查询条件，比如我要在文章中查询指定类型的文章，可以通过在 `Post` 模型类中定义如下方法：

```php
public function scopeOfType(Builder $query, $type)
{
    return $query->where('type', $type);
}
```

这样，在查询指定类型的文章时，就可以这么实现：

```php
$posts = Post::active()->ofType(Post::Article)->get();
```

对应的 SQL 语句如下：

```php
select * from `posts` where `status` = ? and `type` = ? and `posts`.`deleted_at` is null
```

动态作用域的调用和移除方式和局部作用域一样。



























