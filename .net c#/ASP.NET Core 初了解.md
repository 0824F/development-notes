# ASP.NET Core

ASP.NET Core 是一个跨平台的高性能开源框架，用于生成启用云且连接 Internet 的新式应用。

使用 ASP.NET Core，可以

1. 生成 Web 应用和服务、物联网 (IoT) 应用和移动后端。

2. 在 Windows、macOS 和 Linux 上使用喜爱的开发工具。

3. 部署到云或本地。

4. 在 .NET Core 上运行。

# 创建 Web 应用项目

```C#
dotnet new webapp -o aspnetcoreapp

dotnet dev-certs https --trust

cd aspnetcoreapp

dotnet watch run
```

# 启动过程

ASP .NET Core 5

```C#
using ...;

namespace WebAppRPv5
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            services.AddRazorPages();
        }

        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Error");
                app.UseHsts();
            }

            app.UseHttpsRedirection();
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapRazorPages();
            });
        }
    }
}

using ...;

namespace WebAppRPv5
{
    public class Program
    {
        public static void Main(string[] args)
        {
            CreateHostBuilder(args).Build().Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
    }
}
```

`Startup.cs`包含了应用程序的配置和初始化，`ConfigureServices` 方法用于配置应用程序服务容器，`Configure` 方法用于配置请求处理管道。

`Program.cs`包含了应用程序的主入口点，`CreateHostBuilder` 方法用于创建主机，包括了应用程序的默认设置和指定了使用的启动配置类。`Main` 方法创建并启动主机从而启动应用程序。

ASP .NET Core 6

```C#
var builder = WebApplication.CreateBuilder(args); // 创建宿主构建器

builder.Services.AddRazorPages(); // 添加服务到依赖注入容器

var app = builder.Build(); // 构建应用程序

// 中间件设置
if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Error");
    app.UseHsts();
}

app.UseHttpsRedirection();
app.UseStaticFiles();

app.UseRouting();

app.UseAuthorization();

app.MapRazorPages();

// 启动应用程序
app.Run();
```

与 ASP .NET Core 5 相比

1. 将 `Startup.cs` 和 `Program.cs` 统一到单个 `Program.cs` 文件中

2. 使用顶级语句最大程度减少应用所需的代码

3. 使用全局 using 指令消除或最大程度减少所需的 using 语句行数

可以看出以`var builder = WebApplication.CreateBuilder(args);`-`var app = builder.Build();`-`app.Run();` 为界，`var app = builder.Build();`往上为 相当于.NET 5 的`ConfigureServices`方法，往后为 `Configure` 方法。

# 生命周期

ASP.NET Core 的依赖注入（DI）容器提供了三种生命周期:

- Transient：每次请求服务时，都会创建一个新的实例

```C#
services.AddTransient<IMyService, MyService>();
```

- Scoped：在同一个作用域内会共享同一个服务实例

```C#
services.AddScoped<IMyScopedService, MyScopedService>();
```

- Singleton：在应用程序的整个生命周期内只会创建一个实例，并且该实例会在应用程序的生命周期内一直存在

```C#
services.AddSingleton<IMySingletonService, MySingletonService>();
```

## 选择生命周期的建议

1. 如果服务是无状态的，且不需要在不同请求之间共享状态，应该选择瞬时生命周期

2. 如果服务需要在同一请求内共享状态，应该选择作用域生命周期

3. 如果服务需要在整个应用程序中共享状态，应该选择单例生命周期

4. 如果不确定服务的状态和使用场景，可以选择作用域生命周期作为默认生命周期
