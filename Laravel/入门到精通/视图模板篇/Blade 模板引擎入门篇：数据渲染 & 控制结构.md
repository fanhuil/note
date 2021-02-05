# Blade 模板引擎入门篇：数据渲染 & 控制结构

### Blade 概述

与[视图文件](https://laravelacademy.org/post/9620.html)紧密关联的就是模板代码，我们在视图文件中通过模板代码和 HTML 代码结合实现视图的渲染。和很多其他后端语言不同，PHP 本身就可以当做模板语言来使用，但是这种方式有很多缺点，比如安全上的隐患、容易产生业务逻辑与视图模板的耦合，而且在视图文件中到处使用 `<?php` 内联代码一点都不优雅，甚至是 ugly code，所以你会看到绝大多数现代框架都会提供一套模板引擎，比如 [Smarty](https://www.smarty.net/)，[Twig](https://twig.symfony.com/)，以及 Laravel 使用的 [Blade](https://laravelacademy.org/post/9550.html)。

> 注：不同于其他基于 Symfony 的 PHP 框架，Laravel 没有使用 Twig 模板引擎，不过你想要使用的话，可以借助 [TwigBridge](https://github.com/rcrowe/TwigBridge) 扩展包来实现。

Blade 模板引擎是由 Laravel 框架提供的自有实现，借鉴了 .NET 的 [Razor](http://www.w3school.com.cn/aspnet/razor_intro.asp) 引擎语法，其语法简洁，易于上手，同时提供了强大而直观的继承模型，而且方便扩展。下面是一个简单的 Blade 模板代码示例：

1

```
<h1>{{ $group->title }}</h1> 
```

2

```
{!! $group->imageHtml() !!} 
```

3

```
@forelse ($users as $user) 
```

4

```
    {{ $user->username }} {{ $user->nickname }}<br> 
```

5

```
@empty 
```

6

```
    该组中没有任何用户 
```

7

```
@endforelse
```



正如你所看到的，Blade 模板引擎有三种常见的语法：

- 通过 `{{ }}` 渲染 PHP 变量（最常用）
- 通过 `{!! !!}` 渲染原生 HTML 代码（用于富文本数据渲染）
- 通过以 `@` 作为前缀的 Blade 指令执行一些控制结构和继承、引入之类的操作

下面我们就来逐一介绍这些语法。

> 注：Blade 模板代码存放在以 `.blade.php` 后缀结尾的视图文件中，最终会被编译为原生 PHP 代码，并缓存起来，直到视图模板有修改才会再次编译，所以拥有与原生 PHP 几乎一致的性能，这些编译后的代码位于 `storage/framework/views` 目录下。你当然可以在 Blade 模板中使用原生 PHP 代码，但是不建议这么做，如果你非要这么做的话，可以通过 [@php](https://laravelacademy.org/post/8773.html#toc-13) 指令引入。

### 渲染数据

首先我们来看一下 `{{}}` 语法，我们通过通过该语法包裹需要渲染的 PHP 变量，如 `{{ $variable }}`，你可以将其类比为 `<?php echo $variable; ?>`，但是 Blade 模板代码的功能要更强大，通过 `{{}}` 语法包裹渲染的 PHP 变量会通过 `htmlentities()` 方法进行 HTML 字符转义，从而避免类似 XSS 这种攻击，提高了代码的安全性，所以 `{{ $variable }}` 编译后的最终代码是：

1

```
<?php echo htmlentities($variable); ?>
```



但是某些情况下不能对变量中 HTML 字符进行转义，比如我们在表单通过富文本编辑器编辑后提交的表单数据，这种场景就需要通过 `{!! !!}` 来包裹待渲染数据了：

1

```
{!! $variable !!}
```



这样编译后的代码就是 `<?php echo $variable; ?>` 了。

> 注：对于富文本数据 XSS 攻击防护，可以参考[这篇教程](https://laravelacademy.org/post/9476.html)。

最后，关于数据变量渲染，我们还要注意的是，很多前端框架也是通过 `{{}}` 来输出 JavaScript 变量数据的，比如 Laravel 的好基友 Vue.js 就是，对于这种情况，我们需要在渲染前端 JavaScript 变量的 `{{}}` 前面加上 `@` 前缀，这样，Blade 模板引擎在编译模板代码的时候会跳过带 `@` 前缀的 `{{}}` 数据渲染，并将 `@` 移除从而可以后续执行对应的 JavaScript 框架渲染逻辑：

1

```
// Blade 引擎会将其编译为对应的 PHP 代码
```

2

```
{{ $phpData }}
```

3

```
// Blade 引擎编译时会移除 @，保留 {{ $vueData }} 结构
```

4

```
@{{ $vueData }}
```



如果要注释一段 PHP 代码，可以通过 `{{-- 注释内容 --}}` 实现。

### 控制结构

Blade 中的控制结构语法和 PHP 大同小异，学习成本几乎为零，不过 Blade 为我们额外提供了一些有用的辅助变量和方法，方便我们进行条件判断。

#### 条件语句

**@if、@else、@elseif**

Blade 模板中的 `@if` 等价于 PHP 的 `<?php if ($condition):`，`@else` 和 `@elseif` 依次类推，最后以一个 `@endif` 收尾：

1

```
@if (count($students) === 1) 
```

2

```
    操场上只有一个同学
```

3

```
@elseif (count($students) === 0)
```

4

```
    操场上一个同学也没有
```

5

```
@else
```

6

```
    操场上有 {{ count($students) }} 个同学
```

7

```
@endif
```



和原生 PHP 中的用法如出一辙。

**@unless**

`@unless` 是 Blade 提供的一个 PHP 中没有的语法，用于表示和 `@if` 条件相反的条件，`@unless($condition)` 可以理解为 `<?php if (!$condition):`，然后以 `@endunless` 收尾：

1

```
@unless ($user->hasPaid()) 
```

2

```
    用户没有支付
```

3

```
@endunless
```



**@isset、@empty**

这两个指令和 PHP 中的 `isset()` 和 `empty()` 方法等价：

1

```
@isset($records)
```

2

```
    // 记录被设置
```

3

```
@endisset
```

4

```

```

5

```
@empty($records)
```

6

```
    // 记录为空
```

7

```
@endempty
```



后面两个都是语法糖，如果你不想记太多东西，不防都用 `@if` 来实现对应的逻辑了。

**@switch**

顾名思义，Blade 中的 `@switch` 指令和 PHP 中的 `switch` 语句等价，我们可以通过 `@switch`、`@case`、`@break`、`@default` 和 `@endswitch` 指令构建对应逻辑：

1

```
@switch($i)
```

2

```
    @case(1)
```

3

```
        // $i = 1 做什么
```

4

```
        @break
```

5

```

```

6

```
    @case(2)
```

7

```
        // $i = 2 做什么
```

8

```
        @break
```

9

```

```

10

```
    @default
```

11

```
        // 默认情况下做什么
```

12

```
@endswitch
```



#### 循环结构

**@for、@foreach 和 @while**

和 PHP 一样，在 Laravel 中，我们可以通过与之等价的 `@for`、`@foreach` 和 `@while` 实现循环控制结构，使用语法和 PHP 代码相仿：

1

```
// for 循环
```

2

```
@for ($i = 0; $i < $talk->slotsCount(); $i++) 
```

3

```
    The number is {{ $i }}<br> 
```

4

```
@endfor
```

5

```

```

6

```
// foreach 循环
```

7

```
@foreach ($talks as $talk)
```

8

```
    {{ $talk->title }} ({{ $talk->length }} 分钟)<br> 
```

9

```
@endforeach
```

10

```

```

11

```
// while 循环  
```

12

```
@while ($item = array_pop($items)) 
```

13

```
    {{ $item->orSomething() }}<br> 
```

14

```
@endwhile
```



**@forelse**

这个指令是 PHP 中具备的，可以理解为处理以下 PHP 代码逻辑：

1

```
<?php 
```

2

```
if ($students) {
```

3

```
    foreach ($students as $student) {
```

4

```
       // do something ...
```

5

```
    }
```

6

```
} else {
```

7

```
    // do something else ...
```

8

```
}
```



在 Blade 模板中我们可以使用 `@forelse` 指令通过以下代码实现上述逻辑：

1

```
@forelse ($students as $student)
```

2

```
    // do something ...
```

3

```
@empty
```

4

```
    // do something else ...
```

5

```
@endforelse
```



**@foreach 和 @forelse 中的 $loop 变量**

在循环控制结构中，我们要重磅介绍的就是 Blade 模板为 `@foreach` 和 `@forelse` 循环结构提供的 `$loop` 变量了，通过该变量，我们可以在循环体中轻松访问该循环体的很多信息，而不用自己编写那些恼人的面条式代码，比如当前迭代索引、嵌套层级、元素总量、当前索引在循环中的位置等，`$loop` 实例上有以下属性可以直接访问：

![img](Blade%20%E6%A8%A1%E6%9D%BF%E5%BC%95%E6%93%8E%E5%85%A5%E9%97%A8%E7%AF%87%EF%BC%9A%E6%95%B0%E6%8D%AE%E6%B8%B2%E6%9F%93%20&%20%E6%8E%A7%E5%88%B6%E7%BB%93%E6%9E%84/e9711e424babeddc72d297d3da2c5f31.jpg)

下面是一个简单的使用示例：

1

```
<ul> 
```

2

```
@foreach ($pages as $page)
```

3

```
    @if ($loop->first)
```

4

```
        // 第一个循环迭代
```

5

```
    @endif 
```

6

```
    <li>{{ $loop->iteration }}: {{ $page->title }} 
```

7

```
        @if ($page->hasChildren()) 
```

8

```
        <ul> @foreach ($page->children() as $child) 
```

9

```
            <li>{{ $loop->parent->iteration }}. {{ $loop->iteration }}: {{ $child->title }}</li> 
```

10

```
            @endforeach 
```

11

```
        </ul> 
```

12

```
        @endif 
```

13

```
    </li> 
```

14

```
    @if ($loop->last)
```

15

```
        // 最后一个循环迭代
```

16

```
    @endif
```

17

```
@endforeach 
```

18

```
</ul>
```



有了这个 `$loop` 变量，确实能够帮我们节省很多重复的逻辑判断和编码工作，推荐使用。