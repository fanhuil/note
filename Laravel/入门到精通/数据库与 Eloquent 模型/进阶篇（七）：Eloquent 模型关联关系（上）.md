# 进阶篇（七）：Eloquent 模型关联关系（上）

我们所熟知的 MySQL、SQL Server、Oracle 都是关系型数据库，何谓关系型数据库？简单来说就是数据表之间存在关联关系。到目前为止，我们介绍的所有 Eloquent 模型操作都是针对单表的，接下来我们将花三篇左右的篇幅来给大家介绍如何在 Eloquent 模型类中建立模型之间的各种关联关系，以及如何实现关联查询和更新。

Eloquent 模型支持的关联关系包括以下七种：

- 一对一
- 一对多
- 多对多
- 远层一对多
- 多态关联（一对一）
- 多态关联（一对多）
- 多态关联（多对多）

下面我们将以设计一个简单的博客系统数据库为例一一介绍上述关联关系。

### 一对一

#### 建立关联关系

一对一是最简单的关联关系，一般可用于某张数据表的扩展表与主表之间的关联关系。比如在大型系统中，我们的用户表通常用于最基本信息的存储，如邮箱、用户名、密码等，然后像用户爱好、标签、个性签名、所在地等信息都存到另一张扩展表中，需要的时候才会去扩展表取数据，从而提高查询性能。针对这样的场景，我们就可以在两张表对应模型之间建立一对一关联。

在开始之前，我们先通过[数据库迁移](https://laravelacademy.org/post/9693.html)创建一张 `user_profiles` 数据表，并创建对应模型 `UserProfile`，这可以通过以下 Artisan 命令一次完成：

```php
php artisan make:model UserProfile -m
```

在生成的 `create_user_profiles` 迁移文件中编写迁移类的 `up` 方法如下：

```php
public function up()
{
    Schema::create('user_profiles', function (Blueprint $table) {
        $table->increments('id');
        $table->integer('user_id')->unsigned()->default(0)->unique();
        $table->string('bio')->nullable()->comment('个性签名');
        $table->string('city')->nullable()->comment('所在城市');
        $table->json('hobby')->nullable()->comment('个人爱好');
        $table->timestamps();
    });
}
```

注意，我们在 `user_profiles` 表中添加了一个 `user_id` 字段用于指向所属用户，从而建立于 `users` 表的关联。运行 `php artisan migrate` 在数据库创建这张数据表。

准备好数据表之后，接下来，我们来通过模型类建立 `users` 表和 `user_profiles` 表之间的关联，Eloquent 模型类底层提供了相应的 API 方法帮助我们建立模型之间的关联。首先，我们在 `User` 模型类中通过 `hasOne` 方法定义其与 `UserProfile` 的一对一关联：

```php
public function profile()
{
    return $this->hasOne(UserProfile::class);
}
```

