# async await

使用`async`修饰符可将方法、lambda 表达式或匿名方法指定为异步

await 运算符暂停对其所属的 async 方法的求值，直到其操作数表示的异步操作完成。 异步操作完成后，await 运算符将返回操作的结果（如果有）。 当 await 运算符应用到表示已完成操作的操作数时，它将立即返回操作的结果，而不会暂停其所属的方法。 await 运算符不会阻止计算异步方法的线程。 当 await 运算符暂停其所属的异步方法时，控件将返回到方法的调用方。

```C#
public async Task<int> GetUrlContentLengthAsync()
{
    var client = new HttpClient();

    Task<string> getStringTask =
        client.GetStringAsync("https://learn.microsoft.com/dotnet");

    DoIndependentWork();

    string contents = await getStringTask;

    return contents.Length;
}

void DoIndependentWork()
{
    Console.WriteLine("Working...");
}
```

## 异步方法的运行机制

![异步方法运行机制](/image/%E5%BC%82%E6%AD%A5%E6%96%B9%E6%B3%95.jpg)

1. 调用并等待`GetUrlContentLengthAsync`方法

2. 创建 HttpClient 实例调用`GetStringAsync`方法

3. `GetStringAsync`要等待网站下载，避免阻止资源,`GetStringAsync`会将控制权让出给其调用方`GetUrlContentLengthAsync`，会返回未完成的 `Task<string>`

4. 由于`GetStringAsync`未完成,`GetUrlContentLengthAsync`会执行不依赖`GetStringAsync`结果的其他工作

5. `DoIndependentWork`方法执行，并返回其调用方

6. `GetUrlContentLengthAsync`已运行完毕，可以不受`getStringTask`的结果影响。接下来，`GetUrlContentLengthAsync`需要计算并返回已下载的字符串的长度，但该方法只有在获得字符串的情况下才能计算该值。

因此，`GetUrlContentLengthAsync`使用一个`await`运算符来挂起其进度，并把控制权交给调用`GetUrlContentLengthAsync`的方法。`GetUrlContentLengthAsync`将 Task<int> 返回给调用方。 该任务表示对产生下载字符串长度的整数结果的一个承诺。

调用方可以执行其他不依赖`GetUrlContentLengthAsync`的其他操作，否则就等待`GetUrlContentLengthAsync`,`GetUrlContentLengthAsync`又等待 `GetStringAsync`

7. `getStringTask`任务完成时，`await`会将结果从`getStringTask`任务中提取出来，赋值给`contents`

# 返回类型和参数

异步方法通常返回 Task 或 Task<TResult>。 在异步方法中，await 运算符应用于通过调用另一个异步方法返回的任务

如果方法包含指定 TResult 类型操作数的 return 语句，将 Task<TResult> 指定为返回类型。

如果方法不含任何 return 语句或包含不返回操作数的 return 语句，则将 Task 用作返回类型。

还可以指定任何其他返回类型，前提是类型包含 GetAwaiter 方法。

```C#
async Task<int> GetTaskOfTResultAsync()
{
    int hours = 0;
    await Task.Delay(0);

    return hours;
}

Task<int> returnedTaskTResult = GetTaskOfTResultAsync();
int intResult = await returnedTaskTResult;
// 或者
int intResult = await GetTaskOfTResultAsync();


async Task GetTaskAsync()
{
    await Task.Delay(0);
}

Task returnedTask = GetTaskAsync();
await returnedTask;
// 或者
await GetTaskAsync();
```
