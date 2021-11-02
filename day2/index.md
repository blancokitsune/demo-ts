# 第二天 typescript 小进阶

> 了解更多略显复杂的基础

常用的数据结构自然不用多说，但是正常的开发中不止会用到几种最基础的类型，就像玩积木一样，除了最基础的模块还可以组合出各种牛逼的形态。

## Type Aliases 类型别名

没别的意思，就是自定义一种类型，我想让我之后的东西都拥有这些特性

比如我这有个方法，想接受坐标值，那我就提前定义好，到时候不符合我标准的我一样就看到了

```typescript
type Point = {
  x: number;
  y: number;
};

function 接受坐标点(t: Point) {}
```

## Type Assertions 类型断言

不知道有没有人遇到过那种情景，一个大教室上课，老师点名了一个人小明，结果同时站起来了一男一女，这时候就很尴尬。

又或者这节课来了男小明，但是老师本来想要叫的是女小明，这就算是一种"**运行时才报错**"了。

typescript提供了方法让我们更精准的提前捕获到这些错误，也就是类型断言

我们尝试进行总结，小明都算人，只不过是一男一女，可以看下面的伪代码

```typescript

function 老师点名(name: HumanName):Human{
    return 被点名的人
}

const a1 = 老师点名(小明) as 男
```

我感觉还是比较明确的，就是这个方法返回肯定都是人，但是我就想要的是那个男的

也可以用另一种语法来表示

```typescript
const a2 = <女>老师点名(小明)
```

<b style="border-left: 5px solid #d9534f;padding:10px 0 5px 10px">警告</b> 不要瞎强制给类型，比如把一个`string`的给弄成`number`



## Literal Types 

这也是个比较特殊的用法，和JavaScript中的`var`、`let`、`const`还有点关系

比如分别用这三个创建一个字符串的变量

```typescript
var vs = "var string";
let lsg = "let string";
const cs = "const string";
```

如果已经知道了自动推导的话，第一眼过去就知道他们都是`xx: string`的样子了，但是用`const`创建的那个值也没得变了，所以这时候你用vscode去看的话，`cs`上面显示的会是他的值，也就是`"const string"`

如果把这些放在函数的参数类型定义上，也会有很不一样的表现，就比如一个函数的某个参数类型是`const`定义的`cs`，那他其实能接受的不是所有`string`类型，而是只有`const string`这一个值

这块的东西不是很麻烦，官网看一眼就ok了，[👉官网链接](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#literal-types)

## Narrowing 缩小范围

有时候我们的方法中的一个参数会可以接受不同类型的值，比如`string`或者`number`两种，如果只是单纯的做一个`console.log()`的操作那没有任何的问题。但是假如我们会进行一些`number`或者`string`才能做到的单独事情怎么办

看下面的例子

```typescript
function padLeft(padding: number | string, input: string) {
  return new Array(padding + 1).join(" ") + input;
}
```

很明显的例子，`padding`可能是多种类型，`padding + 1`应该不会想传入一个`"1"`然后变成`"11"吧`

解决的方法，简单又暴力

```typescript
function padLeft(padding: number | string, input: string) {
  if (typeof padding === "number") {
    return new Array(padding + 1).join(" ") + input;
  }
  return padding + input;
}
```

很朴实的方法，就是利用`typeof`这个关键字来帮助我们进行条件拆分

如果你已经是个熟练的bug maker的话就知道他处理不了`null`这一类东西，但幸好我们还有[Truthy](https://developer.mozilla.org/zh-CN/docs/Glossary/Truthy)这种好帮手，最少可以让我们摆脱掉`null`



> typescript他最起码是JavaScript的超集，他的很多操作都是为了弥补JavaScript的一些东西，不是说就让JavaScript滚一边去了

很多时候我们会在JavaScript里进行大量的数据判断，比如用`swtich`、`if`、`instanceof`、`in`等等你能想到的判断方式都可以让我们在typescript里和类型判断生活的更加融洽

比如一个我们定义一个武器盲核抽奖系统，只有手枪和手榴弹的区别，抽到其中一个就学会武器的操作

```typescript
type Gun = { papapa: () => void };
type Shoulei = { fireInTheHole: () => void };

function move(weapon: Gun | Shoulei) {
  if ("papapa" in weapon) {
    return weapon.papapa();
  }

  return weapon.fireInTheHole();
}

```

我们用正常的思维来想这件事，**如果**我抽到了**手枪**，那就学会**啪啪啪**，那**反之**，肯定就是**手榴弹**了，就学会了**扔手榴弹**

<div style="background: #d9534f; padding: 10px; color: #fff">
  <b>总之这些事情就都是为了缩小类型范围，试着用typescript的类型+你所习惯JavaScript中的流程控制就可以达到</b>
</div>

