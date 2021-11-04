# 第四天 typescript 的 Object 进阶

我们在用 JavaScript 或者 typescript 的时候，很多地方都会用到 Object 类型的操作，或者是字面量的写法

有在函数传递参数时用匿名的，也有通过`interface`定义的，又或者是通过`type`别名定义的

```typescript
function greet(perseon: { name: string; age: number }) {}
```

```typescript
interface Person {
  name: string;
  age: number;
}

function greet(perseon: Person) {}
```

```typescript
type Person = {
  name: string;
  age: number;
};

function greet(perseon: Person) {}
```

## Property Modifiers 属性修饰符

### Optional Properties 可选属性

像上面的`Person`定义了两个属性那就一定得传一个属性全能找到的要不然就会报错，那这时候我想在加一个`gender`性别属性呢，我可能不用但是我就想有，万一以后用了呢

那这时候就可以选择用可选属性来标记起来

```typescript
interface Person {
  name: string;
  age: number;
  gender?: string;
}
```

> 可选属性也有需要注意的点，就是你的操作逻辑中如果涉及到了可选项的话，你要注意他可能没有传过来而变成`undefined`

### Index Signatures 索引签名

可选属性意思是我提前知道可能有这么个参数，但是我不用就放着

但是假如我们有好多未知的属性可咋整，typescript 也提供了方法

```typescript
interface StringArray {
  [index: number]: string;
}

const myArray: StringArray = ["123", "456"];
const secondItem = myArray[1];
```

上面这个定义也很简单，我们都知道取数组中的一项应该是`arr[0]`的写法

对应在上面定义的`StringArray`翻译一下就是，`[]`中传一个值，这个值得类型是`number`，之后在调用`[index]`后返回一个值的类型是`string`。

索引的签名(index)只能是`string`或者`number`，可以在想一下数组(Array)和对象(Object)的取值方法

> 要注意用了`index signature`的话，会对剩余的属性也产生影响

```typescript
interface NumberDictionary {
  [index: string]: number;
  length: number; // ok
  name: string; // Property 'name' of type 'string' is not assignable to 'string' index type 'number'.ts(2411)
}
```

可以想象一下以下操作

1. 有个对象，就是每一项的 key 都是`string`类型

   ```typescript
   const a = { d: 1, length: 2 };
   ```

2. 这样子的话`index`和`length`都是可以正常取值的，取出来的值也是`number`类型

   ```typescript
   console.log(a["d"]);
   console.log(a.length);
   ```

3. 这时候假如加入一个`name`，值是`string`的话，就会和`[index: string]: number`发生冲突。

   你明明说每次`key`返回的值都是`number`类型，结果现在来了个`string`的？

## Extending Types 扩展类型

我们很多时候一开始创建的类型很可能是我们没想到以后在扩展的情况，typescript 也给了我们处理的方案

可以用`extends`的方式，就像`class`的`extends`一样，从一个基础类型，做扩展一个新的类型出来

```typescript
interface Colorful {
  color: string;
}

interface Circle {
  radius: number;
}

interface ColorfulCircle extends Colorful, Circle {}

const cc: ColorfulCircle = {
  color: "red",
  radius: 42,
};
```

## Generic Object Types 泛型对象类型

在不确定参数的类型时候，**尽量不要用 any**，可以尝试使用泛型，这样子可以尽量保留更多的信息

```typescript
// x 不好的方法
interface NumberBox {
  contents: number;
}

interface StringBox {
  contents: string;
}

interface BooleanBox {
  contents: boolean;
}

// √ 比较好的方法
interface Box<Type> {
  contents: Type;
}
```

我们像在函数传参那样，给我们的类型一个泛型，这样子就可以不用`any`也可以达到不确定类型的实现了

同样的，因为这里的`Type`会变成任何传进来的样子，所以我们可以更自由大胆一点

```typescript
interface Box<Type> {
  contents: Type;
}

interface Apple {
  // ....
}

// Same as '{ contents: Apple }'.
type AppleBox = Box<Apple>;
```

刚才说的都是`interface`形式的泛型，`type`别名也是可以用泛型来表示的

```typescript
type Box<Type> = {
  contents: Type;
};
```

同时它还可以把每次的类型传递给别的类型

```typescript
type OrNull<Type> = Type | null;

type OneOrMany<Type> = Type | Type[];

type OneOrManyOrNull<Type> = OrNull<OneOrMany<Type>>;

type OneOrManyOrNullStrings = OneOrManyOrNull<string>;
```

这里可以借用一下`Array`来辅助理解

平时像`number[]`或者是`string[]`的写法其实都是简写，他们完整的写法应该是`Array<number>`这个样子的

与上面的`Box`一样，`Array`本身也是泛型

```typescript
interface Array<Type> {
  // ···
}
```

像`Array`的简写写法，还有和他关系密切的`ReadonlyArray`

```typescript
let x: readonly string[] = [];
let y: ReadonlyArray<string> = [];
```

### Tuple Types 元组类型

这一个与数组相似的语法，和数组不同的是，元组的数量和每个元素的位置等信息都是精准固定的

这块可以阅读一下 👉[官方文档](https://www.typescriptlang.org/docs/handbook/2/objects.html#generic-object-types)
