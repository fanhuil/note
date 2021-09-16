# spl_autoload_register 

## 参数

- `autoload_function`

  欲注册的自动装载函数。如果没有提供任何参数，则自动注册 autoload 的默认实现函数[spl_autoload()](mk:@MSITStore:F:\FHL\Data\php_manual_zh.chm::/res/function.spl-autoload.html)。       

- `throw`

  此参数设置了 `autoload_function` 无法成功注册时, **spl_autoload_register()**是否抛出异常。       

- `prepend`

  如果是 true，**spl_autoload_register()** 会添加函数到队列之首，而不是队列尾部。 

> 函数的功能就是把传入的函数（参数可以为回调函数或函数名称形式）注册到 SPL __autoload 函数队列中，并移除系统默认的 __autoload() 函数。
>
> 一旦调用 spl_autoload_register() 函数，当调用未定义类时，系统就会按顺序调用注册到 spl_autoload_register() 函数的所有函数，而不是自动调用 __autoload() 函数。

