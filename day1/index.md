# 第一天 typescript基础

> 认识、了解typescript的作用

> 认识一些typescript的基础数据类型和作用



### 尝试在运行前进行错误提示

传统的JavaScript编码过程中是没有类型等报错信息的，直有当你运行的那一刻你才知道哪里有错误，这是很痛苦的一件事，万一你写了个超长的代码，每次还得运行到最后才报错也太耽误时间了。

那typescript给了我们在运行前就可以找出这些基础错误的能力，也就是很重要的一个基础能力，静态类型检查。

一般人大家要是纯手写的话可能一不小心就会写出下面这种代码，`toLocaleLowercase`是个不小心写错的函数。如果使用JavaScript的话，你得运行到那一行才会知道错了，如果用了typescript就可以在编写的时候提醒你写错了。

```typescript
const announcement = "Hello World!";
announcement.toLocaleLowercase(); 
//Property 'toLocaleLowercase' does not exist on type '"Hello World!"'. Did you mean 'toLocaleLowerCase'?ts(2551)
```

### 显示类型

> 总有些事情我们想在一开始就知道他是什么成分👍

> 如果我一开始就想要冰糖山药葫芦，你就别给我推荐你那个冰糖大腰子葫芦行不行👊

我们会希望在一开始的时候就声明他是什么**类型**，这样可以一眼就看出来想要的是什么

```typescript
// 假设有个数据类型，就叫山药
function 想吃冰糖葫芦(name: 山药):冰糖葫芦{
    return 冰糖 + name + 葫芦
}

想吃冰糖葫芦(山药) // 🔆
想吃冰糖葫芦(大腰子)//🤜
```

当然了，我们也可以不说，typescript会帮我们做这个**推断**的过程

就像下面这个，技术我们不说最后想要的是冰糖葫芦，但是我们和typescript都知道最后一定是冰糖葫芦，哪怕他是用大腰子或者别的什么东西做的

```typescript
// 假设有个数据类型，就叫山药
function 想吃冰糖葫芦(name: 山药){
    return 冰糖 + name + 葫芦
}

想吃冰糖葫芦(山药) // 🔆
想吃冰糖葫芦(大腰子)//🤜
```

但是也可以看得出来，这个功能非常的直接，没有那么多绕来绕去的过程，即使是我们也可以一眼看出来最后返回的是什么。

这种大家都能做出简单判断的事情就可以不用写出那个**声明**了，但是但是，如果中间的步骤稍一复杂，最后的返回值也没有这么单一的话，就不要想着偷懒了！

### typescript的基础类型

> 就像JavaScript有基础的数据类型，typescript这个JavaScript的超集也是肯定会有的，而且肯定是息息相关的！

#### The primitives：`string`,`number` and `boolean`

感觉这是什么意思已经不用多说了，直接看demo就行了

很直白的字符串、数字、布尔类型。

就是告诉你，我这个变量就要用这个类型了，就算改成`let`我也不会变成其他形状的！

```typescript
const s: string = "123";
const n: number = 123;
const b: boolean = true;
```

#### Arrays

数组的声明有两种写法，每种的样子都比较容易懂，第二种形似`T<U>`的写法叫做**泛型**

```typescript
const sarr: string[] = ["1", "2", "3"];
const narr: Array<number> = [1, 2, 3];
```

#### any

这个东西可以帮你避免各种错误，什么叫各种错误？

正常的`Array`上面是没有`string`身上的一些特性的，如果你不小心使用了，他会提前给你报错的

```typescript
const sarr: string[] = ["1", "2", "3"];
sarr.substr(0)
// Property 'substr' does not exist on type 'string[]'.ts(2339)
```

但是，如果你用的是`any`的话，那就不会报错啦~

当你运行到这里的时候你就会看到控制台弹出红红的报错了！

```typescript
const sarr: any = ["1", "2", "3"];
sarr.substr(0)
```

还有一些像**`Functions`**、**`Object`**、**`Union Types`**等等的类型，看一看官网就行了，都是简单易懂的样子

### Interface & Type

其中比较重点说明的是这两个，这是玩家在玩耍过程中经常要自定义的两个东西

这两个东西都是可以实现自定义一个类型集合的，看下面的例子

```typescript
interface Animal {
  name: string
}

type Animal = {
  name: string
}
```

这两个东西现在除了关键字不一样，其他剩下的效果基本上是一模一样的，如果只是现在这样子不想修改的话

那意思很明确了，如果想修改的话，就会有不同的操作了，这个不同之处就在于他俩的**目的性**和**扩展性**了

先看一下`interface`的扩展方式

```typescript
interface Animal {
  name: string;
}

interface Animal {
  sex: string;
}

let a: Animal
```

很明显的就是我可以不停的去添加新的属性，这是`interface`带来的便捷

如果是`type`的话，那这样是不行的，因为他不让你在原来的`type`上动手动脚，你要是想扩展就在新建一个`type`

这俩在实际应用中好像也是比较突出这个性质，如果你想这个特性向外暴露并且可以尝试扩展的话就用`interface`；如果就像在内部写死了，外面的人只是负责调用就用`type`

