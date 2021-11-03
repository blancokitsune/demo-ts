# 第三天 typescript 更多技巧学习

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

我们都知道函数其实也是对象，可以在上面挂载属性，如果我们想在typescript里实现这个效果，可以这样写

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