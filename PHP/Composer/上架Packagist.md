# 项目

```shell
composer.json文件中Package Type改为project
```

## 其余同插件



# 插件

## 创建文件夹

```shell
mkdir first-composer
cd first-composer
```

## 创建composer.json

```shell
$ composer init
                                            
  Welcome to the Composer config generator                                            

This command will guide you through creating your composer.json config.

# 包的名字 vendor代表生产商，也是识别包的标识
# 包的名字， 当报名相同时，通过vendor来区分
# 系统会默认获取当前登录的用户来做vendor,所在文件夹做包的名字
Package name (<vendor>/<name>) [zxc/frame]: zxc/frame
# 这个是对包的描述
Description []: 这个是包的描述
# 这个是作者的个人信息， 使用git时设置的user.name 和user.email
# 如果没有设置的话，会使用系统给默认的用户名和邮箱
Author [周小川 <zxc_4567@123.com>, n to skip]: 张三 <zxc_1234@123.com>
# 版本 dev, alpha, rc, stable, beta
Minimum Stability []: dev
# 类型
Package Type (e.g. library, project, metapackage, composer-plugin) []: library
# 许可协议
License []:

Define your dependencies.

# 这个是在需要使用到的依赖
Would you like to define your dependencies (require) interactively [yes]? yes
# 这里会根据你输入的关键字对包进行搜索
Search for a package:  dump
#这里会有其他的操作， 自己实验一下哦

# 安装开发扩展， 这个在项目部署的时候不安装不会影响项目的运行
Would you like to define your dev dependencies (require-dev) interactively [yes]?
Search for a package:

{
    "name": "zxc/frame",
    "type": "library",
    "authors": [
        {
            "name": "张三",
            "email": "zxc_1234@123.com"
        }
    ],
    "minimum-stability": "dev",
    "require": {}
}
# 是否生成composer.json
Do you confirm generation [yes]?
# 是否现在安装依赖
Would you like the vendor directory added to your .gitignore [yes]
```

## 创建代码仓库

> github创建仓库

> 提交代码

> 推送到github

## 上传到Packagist

![20210831121926559](G:\Knowledge Base\note\PHP\Composer\上架Packagist\20210831121926559.png)

> 填写github地址直接提交

