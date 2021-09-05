# COOKIE

COOKIE原理

> 1、第一次请求时，PHP通过setcookie函数通过http协议响应头传输给浏览器
>
> 2、浏览器在第一次响应的时候将Cookie数据保存到浏览器
>
> 3、浏览器后续请求同一个网站的时候，会自动检测是否存在Cookie数据，如果存在将在请求同中将数据携带到服务器
>
> 4、PHP执行的时候会自动判断浏览器请求中是否携带Cookie，如果携带，自动保存到$_COOKIE中
>
> 5、利用$_COOKIE方位Cookie数据



# SESSION

## SESSION原理

session与浏览器无关，但是与Cookie有关。

> 1、PHP碰到session_start()时开启session会话，会自动检测sessionID
>
> ​	a)如果Cookie中存在，则使用现成的
>
> ​    b)如果Cookie中不存在，创建一个sessionID，并通过响应头以Cookie形式保存到浏览器上
>
> 2、初始化超全局变量$_SESSION为一个空数组
>
> 3、PHP通过sessionID去指定位置(session文件存储位置)匹配对应文件
>
> ​    a)不存在该文件：创建一个sessionID命名文件
>
> ​    b)存在该文件：读取文件内容（反序列化），将数据存储到$_SESSION中
>
> 4、脚本执行结束，将$_SESSION中保存的所有数据序列化存储到sessionID对应文件中

![image-20210905203703209](Cookie%E5%92%8CSession/image-20210905203703209.png)



## SESSION基本使用

> 启用session，任何时候都需要开启session(脚本使用到$_SESSION就开启一次)
>
> $_SESSION是通过session_start()函数的调用才会定义，没有直接定义

## 设置SESSION信息

> 如果想存储数据到session中，那么只要不断地给$_SESSION数组添加元素即可

## 读取SESSION信息

> $_SESSION就是一个数组，存储什么数据，什么方式存的，就是可以通过什么方式访问数据

会话技术的本质是为了实现跨脚本数据共享：在一个脚本中定义数据，在另一个脚本中保存数据

## 删除一个SESSION信息

> unset($_SESSION[下标])，删除指定session数据

## 删除全部SESSION信息

> 删除全部数据就是让$_SESSION变成一个空数组

## SESSION相关配置

### SESSION基础配置

> 1）session.name：session名字，保存到COOKIE中sessionID对应名字
>
> 2)  session.auto_start：是否自动开启session(无需手动session_start())，默认是关闭的
>
> 3)  session.save_handler:session：数据的保存方式，默认是文件形式
>
> 4)  session.save_path：session文件默认存储的位置

### SESSION常用配置

> 1) session.cookie_lifetime：PHPsessionID在浏览器端对应COOKIE的生命周期，默认是会话结束
>
> 2) session.cookie_path：sessionID在浏览器存储之后允许服务器访问路径（COOKIE有作用范围）
>
> 3) session.cookie_domain：COOKIE允许访问的子域（COOKIE可以跨子域）

## 配置的两种形式

> 1) php.ini中配置：全局配置，修改php.ini中的配置项
>
> 2) 脚本配置：PHP可以通过ini_set()函数来在运行中设定某些匹配项(只会对当前运行的脚本有效)

## 销毁SESSION

> Session删除是指删除session数据，$_SESSION中看不到而已；销毁是指删除session对应的session文件
>
> 系统提供一个函数：session_destory()，会自动根据session_start()得到的sessionID去找到指定的session文件，并把其删除

## SESSION垃圾回收机制

> session会话结束后，session文件并不会主动清除，如果每天有大量session文件产生但是又都是失效的，会增加服务器的压力和影响session效率。
>
> 垃圾回收，是指session机制提供了一种解决垃圾session文件的方式：给session文件指定周期，通过session文件最后更改时间与生命周期进行结合判定
>
> ，如果已经过期规则删除对应的session文件，如果没有过期则保留。这样就可以及时清理无效的僵尸文件，从而提升空间利用率和session工作效率。
>
> 1、如何一次session开启，session都会尝试去读取session文件
>
> 2、读取session文件后，有可能触发垃圾回收机制
>
> 3、垃圾回收机制会自动读取所有session文件的最后编辑时间，然后加上生命周期（配置文件）与当前时间进行比较

## 实现无COOKIE使用SESSION

> 在PHP中，想要解决没有COOKIE也实现session技术的方式有两种：
>
> 方案一：可以利用PHP提供的session函数：session_id和session_name来获得和设置sessionID或者name，从而解决session_start产生新的sessionID情况
>
> 方案二：可以利用session中已经提供的解决方案自动操作