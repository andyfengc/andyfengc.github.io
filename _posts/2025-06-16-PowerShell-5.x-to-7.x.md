---
layout: post
title: PowerShell Tutorial
author: Andy Feng
---
# Install v7 first
https://learn.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-windows
# How to set v7 as default in windows
让整个 **Windows 系统默认使用 PowerShell 7（pwsh）** 代替 PowerShell 5（powershell.exe），可以通过以下方式实现。这里分为 **用户级别** 和 **系统级别** 的设置：
## 在 Windows Terminal 中设置默认使用 PowerShell 7
安装完 PowerShell 7 后，Windows Terminal 会自动识别它。

**设置方法：**

1. 打开 Windows Terminal，选Powershell 7，不要选windows poweshell
    
2. 点击右上角下拉菜单 → `Settings`
![](images/posts/Pasted%20image%2020250616154308.png)
    
3. 在 “Startup” → `Default Profile` 选择 `PowerShell `
    ![](images/posts/Pasted%20image%2020250616154143.png)
4. 保存即可

检查当前powershell版本
```
$PSVersionTable.PSVersion
```
## 给 `powershell` 命令设置别名（仅限你自己）
修改快捷方式、别名 `powershell` → `pwsh`
**设置方式：**

### 如果你用的是 WSL 或其他 bash/zsh：
`alias powershell=pwsh`

### 在PowerShell 里设置别名（仅当前会话）
`Set-Alias powershell pwsh`
但这只对当前会话有效，关闭窗口就失效。

### 永久设置别名（修改 PowerShell 配置文件），需要在 PowerShell 配置文件 `$PROFILE` 中加入：
具体方法：
打开powershell

```
notepad $PROFILE
```

在打开的文件中加入：
```
Set-Alias powershell pwsh
```
保存后关闭。下次启动 PowerShell 就自动生效。
这样你在终端中输入 `powershell` 就会自动变成运行 PowerShell 7 的 `pwsh`

💡 如果 $PROFILE 文件不存在，上面的命令会自动创建。
## 修改 PATH 环境变量顺序
把 PowerShell 7 路径排到前面
1. 打开：`控制面板 → 系统 → 高级系统设置 → 环境变量 (environement)`
    
2. 找到系统的 `Path` 变量
    
3. 将以下路径移动到前面：
        
    `C:\Program Files\PowerShell\7\`
    
4. 确保它在如下路径前面：
        
    `C:\Windows\System32\WindowsPowerShell\v1.0\`
    
5. 重启命令提示符、VSCode、终端等生效
    ![](images/posts/Pasted%20image%2020250616154530.png)

📌 **注意**：这并不能改变 `powershell.exe`，但会让你输入 `pwsh` 时优先使用 PowerShell 7。

## 设置 VS Code 中默认使用 PowerShell 7

重启visual studio code
打开命令面板：`Ctrl + Shift + P`
输入并选择：`Terminal: Select Default Profile`
![](images/posts/Pasted%20image%2020250616154957.png)
选择带有 PowerShell 7 的选项（通常路径是 `C:\Program Files\PowerShell\7\pwsh.exe`）
![](images/posts/Pasted%20image%2020250616154948.png)
安装扩展：PowerShell Extension
    
2. 打开命令面板（Ctrl + Shift + P）输入：
     
    `PowerShell: Select Interpreter`
    
3. 选择路径为：
    
    `C:\Program Files\PowerShell\7\pwsh.exe`
可以在vs code 的terminal 里检查powershell版本进一步确认
![](images/posts/Pasted%20image%2020250616160532.png)
## Visual studio 2022 设置v7为默认
### 将项目从.net 8.0 升级到9.0
安装nuget library  `Microsoft.PowerShell.SDK`
这样就能使用powershell的sdk了
![](images/posts/Pasted%20image%2020250616163411.png)
还有一个 `System.Management.Automation`，非官方，不推荐
### 设置terminal
- 打开 Visual Studio 2022    
- 点击菜单栏 → `工具 (Tools)` → `选项 (Options)`    
- 在弹出的窗口中，导航到 终端 (Terminal)`
- 点击 “添加” 添加一个新的配置：
    
- **名称 (Name)**: PowerShell 7
	
- **命令行路径 (Command line path)**: `C:\Program Files\PowerShell\7\pwsh.exe`
	![](images/posts/Pasted%20image%2020250616160051.png)
- 工作目录保持默认或自定义 
- 确认保存并关闭

以后在 VS 中打开“终端窗口”时（`视图 → 终端` 或 `Ctrl + ~`），将使用 PowerShell 7
![](images/posts/Pasted%20image%2020250616160322.png)
### 修改代码
如你代码中写的是：

`Process.Start("powershell.exe", ...);`

想改为 PowerShell 7，则只需要将文件名替换为完整路径：

`Process.Start(@"C:\Program Files\PowerShell\7\pwsh.exe", ...);`

如果你不想写死路径，也可以读取环境变量或在 `PATH` 中优先设置 PowerShell 7 路径（见之前回答）
# Visual studio c#项目调用powershell script
## v7 使用powershell sdk
using System;
using System.Management.Automation;

class Program
{
    static void Main()
    {
        bool isDebug = true;

        using (PowerShell ps = PowerShell.Create())
        {
            // 加载脚本
            ps.AddCommand(@"C:\Path\To\test.ps1");
            ps.AddParameter("isDebug", isDebug);

            // 执行脚本
            var results = ps.Invoke();

            // 输出返回结果
            foreach (var result in results)
            {
                Console.WriteLine(result.ToString());
            }
        }
    }
}
## v5, v7 使用Process.Start
bool isDebug = true;
string boolArg = isDebug ? "$true" : "$false";

var psi = new ProcessStartInfo()
{
    FileName = "powershell",
    Arguments = $@"-ExecutionPolicy Bypass -File ""C:\Path\To\test.ps1"" -isDebug {boolArg}",
    RedirectStandardOutput = true,
    UseShellExecute = false,
    CreateNoWindow = true
};

using (var process = Process.Start(psi))
{
	string output = process.StandardOutput.ReadToEnd();
	process.WaitForExit();
	Console.WriteLine(output);
}
## powershelll sdk vs ProcessStartInfo
**PowerShell SDK API** 调用脚本，这是比 `ProcessStartInfo` 更强大、结构化的方式，优点非常明显。
vs project -> powershell.create() > 启动 powershell sdk > .net core 9.0 > powershell v7，开发方便，我选这种

vs project > processtartinfo > .启动 `powershell.exe` > net core 9.0 or .net framework 4.x > powershell 5.x/7.x，兼容性好但开发略麻烦
![](images/posts/Pasted%20image%2020250616164604.png)
### 用 `PowerShell.Create()`（你的代码）：

- 你已经引用了 `Microsoft.PowerShell.SDK`，在 C# 项目中内嵌调用 PowerShell。
    
- 参数多，需传复杂对象或结构化数据。
    
- 希望精确控制命令执行、结果解析、错误处理。
    
### ✅ 用 `ProcessStartInfo`：

- 快速调用 `.ps1` 脚本，无需添加 SDK。
    
- 脚本是独立运行（非嵌入），适合部署时与系统 PowerShell 配合使用。
    
- 不关心结构化返回，只要拿到输出结果。
# FAQ
## vs 项目升级到了.net 9.0，提示错误：Unhandled exception. System.Management.Automation.Runspaces.PSSnapInException: Cannot load PowerShell snap-in Microsoft.PowerShell.Diagnostics because of the following error: Could not find file '...\bin\Release\net9.0\runtimes\win\lib\net9.0\Microsoft.PowerShell.Commands.Diagnostics.dll
安装 Microsoft.PowerShell.SDK
确认所有关联项目都升级到了9.0
## vs项目 运行出错，Unhandled exception. System.Management.Automation.PSSecurityException: File ...\test-boolean.ps1 cannot be loaded because running scripts is disabled on this system
说明你当前系统的 **PowerShell 执行策略 (ExecutionPolicy)** 禁止运行 `.ps1` 脚本。这是 Windows 系统的一个默认安全设置。
### 推荐方案：**在调用时指定临时“Bypass”策略**

如果你是通过 C# 代码调用脚本，**最安全也最简单的做法是添加这一行参数：**

`Arguments = $"-ExecutionPolicy Bypass -File \"{scriptPath}\""`

或者，你也可以改当前用户的策略（不会影响其他人）：

`Set-ExecutionPolicy RemoteSigned -Scope CurrentUser`

