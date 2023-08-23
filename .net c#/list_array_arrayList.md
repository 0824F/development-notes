# List Array ArrayList

## 三者的区别

Array 长度不可变，强类型

ArrayList 长度可变，弱类型(Object)

List 长度可变，强类型

# 创建方法

Array

```C#
int[] num = new int[]{1,2,3,4,5};
int[] num = new int[5];
int[] num = {1,2,3,4,5};
```

ArrayList

```C#
ArrayList num = new ArrayList();
num.Add(1);
num.Add({id: 1, name: "张三"});
```

List

```C#
List<int> num = new List<int>();
num.Add(1);
num.Add(2);
num.Add({id: 1, name: "张三"}); // error
```

Dictionary 泛型字典

```C#
Dictionary<string, int> nums = new Dictionary<string, int>();
nums.add([key], [value]);
```

## 遍历

```C#
while (条件)
{

}

do
{

} while (条件);

for (初始化; 条件; 更新)
{

}

ForEach(item => Console.WriteLine(item))
```
