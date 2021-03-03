## Laravel Mix

laravel 在与前端相结合这块做的很不错，提供了 Laravel Mix

Laravel Mix 提供了简洁且可读性高的 API ，用于使用几个常见的 CSS 和 JavaScript 预处理器为应用定义 Webpack 构建步骤。可以通过简单链式调用来定义资源的编译。

我们可以自己使用 sass，npm，es6 语法，压缩，生成随机文件名，通过 webpack 打包生成 注入到我们的 html 中

具体使用例子

webpack.mix.js 中

```php
mix.js('resources/assets/js/app.js', 'public/js')
    .js('resources/assets/js/geetest.js','public/js')
    .sass('resources/assets/sass/app.scss', 'public/css')
    .sass('resources/assets/sass/index.scss', 'public/css')
    .sass('resources/assets/sass/post.scss', 'public/css')
    .sass('resources/assets/sass/search.scss', 'public/css')
    .version();
```

blade.php 中就可以使用

```php
<link rel="stylesheet" href="{{ mix('/css/index.css') }}">
```

## Laravel 5.5 preset 命令

Laravel 5.5 发布，新增加了一个 preset 的命令。

```bash
php artisan preset
```

这行代码主要就是方便我们切换前端框架，比如 react,vue,bootstarp 时。如果想使用 vue 就可以

```bash
php artisan preset vue
```

## vue 在 laravel 中具体实例

下面做个返回头部的 vue 的组件

1.我们在 resources\js\components 定义 GoTop.vue

```vue
<template>
  <transition name="fade">
    <div class="goTop" @click="goTop" v-if="isShow">
        <span class="glyphicon glyphicon-menu-up"></span>
    </div>
  </transition>
</template>

<script>
    export default {
        data() {
            return {
                isShow : false,
            }
        },
        mounted() {
            let that = this;
            $(window).scroll(function(){
                if( $(this).scrollTop() > 50 ){
                    that.isShow = true;
                } else {
                    that.isShow = false;
                }
            });
        },
        methods: {
            goTop() {
                $('html,body').animate({scrollTop:0});
            }
        }
    }
</script>
<style scoped lang="scss">
  .fade-enter-active, .fade-leave-active {
    transition: opacity .5s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
  .goTop {
    position: fixed;
    right: 36px;
    bottom: 50px;
    background: #FFF;
    width: 50px;
    height: 50px;
    line-height: 60px;
    text-align: center;
    border-radius: 2px;
    box-shadow: 0 4px 12px 0 rgba(7,17,27,.1);
    cursor: pointer;
    z-index: 1000;
    span {
      font-size: 20px;
    }
  }
</style>
```

2.在 resources\js\app.js 注册

```js
Vue.component('go-top', require('./components/GoTop.vue'));
```

3.在blade.php 模版中使用

```php
<go-top></go-top>
```

注意: vue 做全局组件不易太多，因为打包 app.js 会被所有页面都会加载到

## vue 彩蛋

如果一个组件里引入另组件可能你的控制台就会报一个错误，[Vue warn]: Failed to mount component: template or render function not defined. (found in root instance)

[原因的话，具体参考这篇文章](https://blog.csdn.net/csdn_yudong/article/details/59109902)

那如果我们在 laravel 中遇到这种问题该怎么解决呢？，就是把所有组件都在 app.js 注册。

这个章节的代码，请参考最后一章基于 laravel 做的一个简单版的项目。

















