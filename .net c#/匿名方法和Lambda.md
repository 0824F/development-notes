# 匿名方法

(){}

```C#
Func<int,int> getNum = delegate(int x)
{
    return x * x
}
```

# Lambda 表达式

() => {}

```C#
Func<int,int> getNum = (x) =>
{
    return x * x
}
```
