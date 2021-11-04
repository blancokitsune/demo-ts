# 第四天 typescript 的 Object 进阶

我们在用JavaScript或者typescript的时候，很多地方都会用到Object类型的操作，或者是字面量的写法

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

## Property Modifiers属性修饰符

### Optional Properties可选属性

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

### Index Signatures索引签名

可选属性意思是我提前知道可能有这么个参数，但是我不用就放着

但是假如我们有好多未知的属性可咋整，typescript也提供了方法

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
  [index: number]: number;
  length: number; // ok
  name: string; // Property 'name' of type 'string' is not assignable to 'string' index type 'number'.ts(2411)
}
```

可以想象一下以下操作