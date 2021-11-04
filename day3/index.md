# 第三天 typescript 的 Function 进阶

> function 相关的东西有很多细碎的内容，比如像 this 绑定、可选参数、Rest Parameters 之类的有很多是没有写的

## Function Type Expressions 函数类型表达式

有时候我们在传参的时候会经常传入一个函数类型的参数，这也是可以做出约束的

```typescript
function greeter(fn: (a: string) => void) {
  fn("Hello, World");
}

function printToConsole(s: number) {
  console.log(s);
}

greeter(printToConsole);
```

这里的`printToConsole`的参数我定义的类型是`number`，上面这段代码就会提示相应的错误

## Call Signatures 呼叫签名

我们都知道函数其实也是对象，可以在上面挂载属性，如果我们想在 typescript 里实现这个效果，可以这样写

```typescript
type DescribableFunction = {
  description: string;
  (someArg: number): boolean;
};

function doSomething(fn: DescribableFunction) {
  console.log(fn.description + " returned " + fn(6));
}

function d(p: number) {
  return !!p;
}
d.description = "d.description";

doSomething(d);
```

这里和上面的**function type expression**略有一点不同，就是`DescribableFunction`中的函数定义我们用的是`:`而不是`=>`

## Construct Signatures 构造签名

如果我们传入的参数是一个构造函数呢，也就是类

`type`和`interface`都可以弄成一个函数的样子，下面这个先用`SomeConstructor`作为`type`，它的值是在被`new`后的类型是`SomeObject`

这些流程很明显的就是`class`的声明实例的过程，所以我们只需要把对应的类扔进去就好了，最后的`fn`的效果就会像**工厂函数**一样了

```typescript
interface SomeObject {}
class Obj implements SomeObject {
  constructor() {}
}

type SomeConstructor = {
  new (s: string): SomeObject;
};

function fn(ctor: SomeConstructor) {
  return new ctor("hello");
}

const a = Obj;

fn(a);
```

## Generic Functions 通用函数

```typescript
function id(arg: any): any {
  return arg;
}
```

如果我们写出了这样的一个函数，看似结果是一定的，但是如果这时候对`arg`进行一些类型转换等等的操作的话，最后返回的值和我们一开始传入的类型是没关系的，再用`any`的时候我们丢失了太多的信息

所以 typescript 这里给我们提供了一个方案解决，就是用泛型

```typescript
function id<T>(arg: T): T {
  return arg;
}
```

我们假设`id`这个函数传入了类型为`T`的参数，最后返回值也是`T`。也就是你传入了什么类型的，最后我要你的返回值还用这个类型的。

在调用的时候我们可以又不同的使用方法，可以指定类型，也可以自动推断

```typescript
const a1 = id(1);
const a2 = id<string>("1");
```

### Constraints 约束

泛型很好，既保留了想要的类型信息，又显得更加通用，但有时候我们还是想做出点约束的

```typescript
function longest<Type extends { length: number }>(a: Type, b: Type) {
  if (a.length >= b.length) {
    return a;
  } else {
    return b;
  }
}
```

很明显，我们就是想让传进来的这俩能有个`length`属性，这就做到了泛型的约束效果

### Specifying Type Arguments 指定类型参数

有时候太过奔放的使用泛型也是可能犯错的，要时刻记得如果**返回类型**也是进入的类型的话，要仔细检查

```typescript
function combine<Type>(arr1: Type[], arr2: Type[]): Type[] {
  return arr1.concat(arr2);
}
```

上面代码实现的效果理应是传入的如果是数字数组那最后返回的还应该是数字数组，但是假如像下面这样写呢

```typescript
const arr = combine([1, 2, 3], ["hello"]); //Type 'string' is not assignable to type 'number'.ts(2322)
```

他会报错，你一开始传进来的是`number[]`，那我推断出你最好返回的理应也是`number[]`不会有错吧，那这样一来后面的`string[]`就尴尬了，因为函数的逻辑最后会把他们拼在一起，这样子就是混合的，解决的办法也很简单

那就是指定好类型，在实际的 coding 中也是比较推荐这样做的

```typescript
const arr = combine<string | number>([1, 2, 3], ["hello"]);
```

## Function Overloads 函数重载

函数的重载没啥可说的，就是重写函数签名，以达到不同的逻辑，但是这里还有个小坑是需要注意的

比如我们对一个函数做了两次重载操作，分别可以接受 1 个参数和 3 个参数，最后我们通过两个可选参数实现了他们，这并不意味我们可以传递两个参数

```typescript
function makeDate(timestamp: number): Date;
function makeDate(m: number, d: number, y: number): Date;
function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp, d);
  } else {
    return new Date(mOrTimestamp);
  }
}
const d1 = makeDate(12345678);
const d2 = makeDate(5, 5, 5);
const d3 = makeDate(1, 3); // No overload expects 2 arguments, but overloads do exist that expect either 1 or 3 arguments.ts(2575)
```
