在 C# 中调用 Python 有几种常用方法，取决于你想执行的是 独立 Python 脚本，还是需要 在 C# 中直接调用 Python 函数。
# 通过命令行调用 Python 脚本
这是最简单的方法。C# 启动一个进程去运行 Python 脚本，并获取输出。
```csharp
using System;
using System.Diagnostics;

class Program
{
    static void Main()
    {
        // Python 可执行文件路径
        string pythonExe = @"C:\Python310\python.exe";
        // Python 脚本路径
        string scriptPath = @"C:\path\to\script.py";
        // 传入的参数
        string args = "arg1 arg2";

        ProcessStartInfo psi = new ProcessStartInfo();
        psi.FileName = pythonExe;
        psi.Arguments = $"\"{scriptPath}\" {args}";
        psi.UseShellExecute = false;
        psi.RedirectStandardOutput = true;
        psi.RedirectStandardError = true;
        psi.CreateNoWindow = true;

        Process process = Process.Start(psi);

        string output = process.StandardOutput.ReadToEnd();
        string error = process.StandardError.ReadToEnd();
        process.WaitForExit();

        Console.WriteLine("Output:");
        Console.WriteLine(output);

        if (!string.IsNullOrEmpty(error))
            Console.WriteLine("Error:");
        Console.WriteLine(error);
    }
}
```
✅ **优点**：简单，几乎不需要额外库。  
❌ **缺点**：效率稍低，数据传输只能通过标准输入输出或文件。

e.g. 调用 rembg 或 backgroundremover
```csharp
using System.Diagnostics;

var psi = new ProcessStartInfo();
psi.FileName = "cmd.exe";
psi.Arguments = $"/c rembg i \"c:\\Delete\\1\\inputs\\00.jpg\" \"c:\\Delete\\1\\outputs\\00-output.png\"";
psi.RedirectStandardOutput = true;
psi.UseShellExecute = false;
psi.CreateNoWindow = true;

var process = Process.Start(psi);
process.WaitForExit();
```
# 使用 Python.NET (pythonnet) 直接调用 Python
[Python.NET](https://github.com/pythonnet/pythonnet) 可以让你在 C# 中直接引用 Python 模块和函数。
nuget 安装
![](images/posts/20250824-python-tutorial-(4).jpeg)
安装 Python.NET：（可选，如果想在python里面调用.net)
```
pip install pythonnet
```
c#
```csharp
using System;
using Python.Runtime;

class Program
{
    static void Main()
    {
        // 初始化 Python 引擎
        PythonEngine.Initialize();

        using (Py.GIL()) // 获取全局锁
        {
            dynamic np = Py.Import("numpy");
            dynamic result = np.arange(0, 10);
            Console.WriteLine(result);
        }

        // 关闭 Python 引擎
        PythonEngine.Shutdown();
    }
}
```
✅ **优点**：可以直接调用 Python 函数、模块，效率高。  
❌ **缺点**：需要管理 Python 环境和 Python.NET 版本兼容性。
# **通过 IronPython（仅限 Python 2.7/3.4 兼容）**
IronPython 是 .NET 平台上的 Python 实现，可以直接在 C# 中运行 Python 代码。
```csharp
using System;
using IronPython.Hosting;

class Program
{
    static void Main()
    {
        var py = Python.CreateEngine();
        py.Execute("print('Hello from Python!')");
    }
}
```
✅ **优点**：完全嵌入，直接运行 Python 代码。  
❌ **缺点**：对现代 Python 版本支持有限，尤其是 Python 3.10+。
# **通过 Web API 或 Socket**
如果 Python 脚本比较复杂，或者涉及大量计算，可以把 Python 封装成一个 **HTTP 服务** 或 **Socket 服务**，C# 通过网络请求调用：
- Python 使用 Flask/FastAPI 创建 API    
- C# 使用 `HttpClient` 调用
```csharp
using System.Net.Http;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        using HttpClient client = new HttpClient();
        string response = await client.GetStringAsync("http://127.0.0.1:5000/api");
        Console.WriteLine(response);
    }
}
```
✅ **优点**：跨语言、跨平台，可分布式。  
❌ **缺点**：需要额外启动 Python 服务。
e.g.
```csharp
HttpClient client = new HttpClient();
var content = new MultipartFormDataContent();
content.Add(new StreamContent(File.OpenRead("input.png")), "file", "input.png");
var response = await client.PostAsync("http://localhost:5000/removebg", content);
var result = await response.Content.ReadAsByteArrayAsync();
File.WriteAllBytes("output.png", result);
```
# **命令行调用 Python 脚本 vs Python.NET**
| 对比项        | 方法 1：命令行调用 Python 脚本                                       | 方法 2：Python.NET (pythonnet)                       |
| ---------- | ---------------------------------------------------------- | ------------------------------------------------- |
| **原理**     | C# 启动一个独立进程运行 `python.exe script.py`，通过标准输入输出获取结果          | 直接在 C# 进程中嵌入 Python 解释器，调用 Python 函数、模块、变量        |
| **性能**     | 较慢，每次调用都要启动新进程；适合少量调用或一次性任务                                | 快，Python 与 C# 在同一进程内运行，可多次调用 Python 函数而不启动新进程     |
| **使用难度**   | 简单；只需 `ProcessStartInfo` 调用即可                              | 中等；需安装 Python.NET、管理 Python 环境、处理 GIL（全局锁）        |
| **传参方式**   | 只能通过命令行参数、标准输入、文件或 JSON                                    | 可以直接传入 C# 对象到 Python 函数，支持复杂数据结构（数组、字典、numpy 数组等） |
| **获取返回值**  | 通过标准输出或文件，返回值需要解析                                          | 直接返回 Python 对象，可直接转成 C# 对象（int、string、list 等）     |
| **多线程/异步** | 可以启动多个进程实现并行，但管理较麻烦                                        | 支持多线程，但必须使用 `Py.GIL()` 管理 Python 全局锁              |
| **调用第三方库** | 完全支持 Python 所有库（numpy、pandas、tensorflow 等），只要 Python 环境安装了 | 同样支持所有库，但要注意兼容性和 Python.NET 版本限制                  |
| **调试与维护**  | 脚本独立，调试 Python 简单；C# 只管启动进程                                | Python 脚本嵌入 C#，调试稍复杂，需要管理 Python 环境               |
| **适用场景**   | 简单、一次性任务；跨语言松耦合；Python 脚本独立开发                              | 高性能调用；需要频繁调用 Python 函数；需要在 C# 内直接使用 Python 库      |
| **优点**     | 简单、易部署；Python 脚本可独立更新                                      | 高性能；直接操作 Python 对象；数据传输方便                         |
| **缺点**     | 启动慢；参数和返回值传递麻烦；跨进程                                         | 依赖 Python.NET；需管理 Python 环境；多线程需注意 GIL            |
|            |                                                            |                                                   |
命令行适合：**
    
    - 脚本调用少，或者一次性运行的批处理任务        
    - Python 脚本独立开发，需要 C# 只是触发脚本        
    - 不追求高性能
        
- **Python.NET适合：**    
    - 高频调用 Python 函数、复杂逻辑嵌入 C#        
    - 需要直接使用 Python 第三方库（numpy、pandas、scikit-learn）        
    - 对性能有要求，或者希望直接在 C# 中操作 Python 对象

如果你只是偶尔调用 Python 脚本并获取输出，用 **方法 1** 足够简单可靠。
如果你需要频繁调用 Python、共享数据结构、或者在 C# 中直接操作 Python 对象，用 **方法 2（Python.NET）** 更高效。