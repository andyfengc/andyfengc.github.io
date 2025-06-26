---
layout: post
title: PowerShell Tutorial
author: Andy Feng
---
# Introduction
Windows PowerShell is a **command-line shell** and **scripting language** designed especially for system administration. It's analogue in Linux is called as Bash Scripting. Built on the .NET Framework, Windows PowerShell helps IT professionals to control and automate the administration of the Windows operating system and applications that run on Windows Server environment.
## v5 vs v7
v5 based on .net 4.5
v6 based on .net core 2.0. already expired
v7 based on .net core 8.0
# Install
Install v7
## IDE
1. PowerShell ISE, build-in at windows system, support until v7
![](images/posts/Pasted%20image%2020250603174811.png)
2. Install visual studio code extension, recommended
![](images/posts/Pasted%20image%2020250603180733.png) 
# Cmdlets
A _cmdlet_ (pronounced "command-let") is a compiled command. A cmdlet can be developed in .NET or .NET Core and invoked as a command within PowerShell. Thousands of cmdlets are available in your PowerShell installation. You can create and invoke them programmatically through Windows PowerShell APIs.

Cmdlets are named according to a verb-noun naming standard. This pattern can help you to understand what they do and how to search for them. It also helps cmdlet developers create consistent names. You can see the list of approved verbs by using the `Get-Verb` cmdlet. Verbs are organized according to activity type and function.

`$PSVersionTable`

`$PSVersionTable.PSVersion`

`Get-Variable`

`Get-Verb

Get-Command

Get-Help

Get-Command -Noun alias*

Get-Command -Verb Get -Noun alias*

Get-Command -Noun File*

Get-Command -Verb Get -Noun File*

Location
Get-Service "vm*" | Get-Member`

# Scripting
variables 
> Variable name should start with $ and can contain alphanumeric characters and underscore
> $location = Get-Location

special variables
`$$`
`$?`
`$_`
[https://www.tutorialspoint.com/powershell/powershell_special_variables.htm](https://www.tutorialspoint.com/powershell/powershell_special_variables.htm)

# Error & Exceptions
## `Write-Output` 是标准输出
```
Write-Output "FFmpeg finished successfully"
```
	Write-Output 用于向调用者返回正常结果。
	它输出的是你期望正常处理的数据。
	在 PowerShell SDK 中，它的内容会进入 PowerShell.Invoke() 的返回结果中。
C# powershell sdk中接收
```
var results = ps.Invoke();
foreach (var item in results)
	Console.WriteLine(item); // 输出 Write-Output 的结果
```
## `Write-Error` 是错误流
```
Write-Error "FFmpeg failed to process input"
```
	Write-Error 用于报告发生了错误或异常。
	它不会中断脚本（除非用 -ErrorAction Stop），但会被 PowerShell SDK 捕获为错误流。
在 C# 中，它会出现在 
```
ps.Streams.Error
``` 
C# powershell sdk中接收方式：
```
if (ps.Streams.Error.Count > 0)
{
    foreach (var err in ps.Streams.Error)
        Console.WriteLine("错误: " + err); // 输出 Write-Error 的内容
}
```
## 标准写法
ProcessStartInfo
```
var psi = new ProcessStartInfo
        {
            FileName = "powershell.exe",
            Arguments = $"-NoProfile -ExecutionPolicy Bypass -File \"{scriptPath}\" {arguments}",
            RedirectStandardOutput = true,
            RedirectStandardError = true,
            UseShellExecute = false,
            CreateNoWindow = true,
        };
using (var process = Process.Start(psi))
{
	string stdOutput = process.StandardOutput.ReadToEnd();
	string stdError = process.StandardError.ReadToEnd();

	process.WaitForExit();

	// 输出日志
	Console.WriteLine("=== FFmpeg 标准输出 ===");
	Console.WriteLine(stdOutput);

	if (!string.IsNullOrWhiteSpace(stdError))
	{
		Console.WriteLine("=== FFmpeg 错误输出 ===");
		Console.WriteLine(stdError);

		// 你也可以在这里抛出异常
		throw new Exception("FFmpeg 执行失败: \n" + stdError);
	}

	if (process.ExitCode != 0)
	{
		throw new Exception($"FFmpeg 执行失败，退出代码: {process.ExitCode}");
	}
}
```
SDK
```
using (PowerShell ps = PowerShell.Create())
{
	ps.AddCommand("Set-ExecutionPolicy").AddParameter("Scope", "Process").AddParameter("ExecutionPolicy", "Bypass").Invoke();
	ps.Commands.Clear();

	ps.AddScript($"& '{scriptPath}'");

	var results = ps.Invoke();

	foreach (var outputItem in results)
	{
		Console.WriteLine(outputItem.ToString());
	}

	if (ps.Streams.Error.Count > 0 || ps.HadErrors)
	{
		foreach (var error in ps.Streams.Error)
		{
			Console.WriteLine("PowerShell Error: " + error.ToString());
		}

		throw new Exception("FFmpeg 脚本执行失败。");
	}
}
```
## Exception
PowerShell 支持抛出异常（`throw`），而且 **C# 使用 PowerShell SDK 调用时是可以捕获这些异常的**，不过捕获的位置和方式与普通 .NET 异常略有不同。
```
throw "Something went wrong"
or
throw [System.Exception]::new("Explicit exception from PowerShell")
```
PowerShell 的 `throw` 在 **PowerShell SDK 中不会直接变成 C# 的 try/catch 异常**，而是会被捕获进：
```
ps.Streams.Error
```
强制抛出错误
```
if ($process.ExitCode -ne 0) {
    $errorMessage = Get-Content "stderr.txt" -Raw
    throw "ffmpeg failed with exit code $($process.ExitCode): `n$errorMessage"
}
```
## ffmpeg
注意powershell**不会**捕捉 `ffmpeg` 之类 CLI 工具输出的错误信息。如果powershell调用ffmpeg，**PowerShell SDK** 中不会自动将 `ffmpeg.exe` 的错误写入 `ps.Streams.Error`，因为它是子进程行为（非 PowerShell 命令）。
	FFmpeg 是外部可执行程序（非 PowerShell cmdlet），它的错误信息**默认写入标准错误流（stderr）**，而非 PowerShell 的 `$Error` 或 `ps.Streams.Error`。
`ffmpeg`（和大多数 CLI 工具）**总是把普通日志写到 stderr（标准错误流）**，比如：
```
`ffmpeg version ...   
Input #0, ...  
Stream mapping:   ...
```

即使执行成功，这些“正常日志”也会进 `stderr`，而 PowerShell 会把 stderr 内容封装进 `ErrorRecord` 放到 `ps.Streams.Error` 里。所以下面方式是无法捕获到ffmpeg异常
```
ps.Streams.Error
or
s.HadErrors == true
```
if ($process.ExitCode -ne 0) {
    $errorMessage = Get-Content "stderr.txt" -Raw
    throw "ffmpeg failed with exit code $($process.ExitCode): `n$errorMessage"
}
## Best practice
Powershell完全忽略 `ps.HadErrors`，只用 `$LASTEXITCODE` 让脚本决定是否真的失败。
- 不要依赖 FFmpeg 的 stderr 内容
- 使用 `$LASTEXITCODE` 判断成功
- 使用`Write-Error`将错误写入 `PowerShell.Streams.Error`（C# 能读取）
- 使用 `throw` 抛错：让 PowerShell 脚本中止执行，也便于调试    
- `$_` 或 `$_.Exception.Message`：确保抛出完整错误信息（FFmpeg 或逻辑错误

c#端
- 调用 `ps.Invoke()`
- 完全信任 `$LASTEXITCODE` + 自定义错误
- `ps.Invoke()` 本身**不会返回 `$LASTEXITCODE`**，因为 `$LASTEXITCODE` 是 PowerShell 的内部变量，`System.Management.Automation.PowerShell` API 并不会自动将它暴露给 C#。  我们需要在 **PowerShell 脚本中将 `$LASTEXITCODE` 显式输出**，C# 才能获取并检查它。
- 不信任ps.HadErrors
- 使用`ps.Streams.Error`获取错误信息（当然对于ffmpeg这个也输出正常信息）

基于这些分析。

powershell：
```
try{
...
    # 检查输出是否成功完成
    if ($LASTEXITCODE -ne 0) {
        $errorMsg = "FFmpeg failed with exit code $LASTEXITCODE"
        Write-Error $errorMsg
        throw $errorMsg
    }
    else {
        Write-Output "FFmpeg succeed: $OutputPath"
    }
    # 正常结尾在脚本尾部加一个标识格式输出：
    Write-Output "##SCRIPT_EXIT_CODE:$LASTEXITCODE"
    exit 0
}
catch {
    Write-Error "Script error: $($_.Exception.Message)"
    # 加标识
    Write-Output "\n##SCRIPT_EXIT_CODE:$LASTEXITCODE"
    throw $_
}
```

C# SDK
如果 FFmpeg 报错并在 PowerShell 脚本中写出 `exit 1`，在 C# 中通过解析 `##SCRIPT_EXIT_CODE:1` 获取真正的失败。
```
            // 捕捉错误
            // 提取 PowerShell 输出中的 exit code（如果你在脚本中输出了它）
            int? exitCode = null;
            foreach (var item in results)
            {
                string? line = item?.ToString();
                if (line != null && line.StartsWith("##SCRIPT_EXIT_CODE:"))
                {
                    var codePart = line.Substring("##SCRIPT_EXIT_CODE:".Length);
                    if (int.TryParse(codePart.Trim(), out var parsedCode))
                    {
                        exitCode = parsedCode;
                    }
                }
            }

            // 检查 exit code 是否非0
            if (exitCode.HasValue && exitCode.Value != 0)
            {
                //throw new Exception($"Script exited with code {exitCode.Value}.");
                // 处理 PowerShell Streams 错误
                if (ps.HadErrors)
                {
                    var realErrors = ps.Streams.Error
                        .Where(e =>
                            !string.IsNullOrWhiteSpace(e?.ToString()) &&
                            !e.ToString().Contains("ffmpeg version", StringComparison.OrdinalIgnoreCase) &&
                            !e.ToString().Contains("Input #0", StringComparison.OrdinalIgnoreCase)
                        )
                        .ToList();

                    if (realErrors.Count > 0)
                    {
                        var message = string.Join(Environment.NewLine, realErrors.Select(e => e.ToString()));
                        throw new Exception("PowerShell script error:\n" + message);
                    }
                }

            }
```

| 特性       | `Write-Output`     | `Write-Error`               |
| -------- | ------------------ | --------------------------- |
| 用途       | 返回正常数据 / 结果        | 报告错误 / 异常                   |
| 是否终止脚本   | 否                  | 否（除非指定 `-ErrorAction Stop`） |
| SDK 结果位置 | `ps.Invoke()` 的返回值 | `ps.Streams.Error`          |
| 输出到控制台   | 打印到标准输出（stdout）    | 打印到错误输出（stderr）             |
| 建议场景     | 正常结果，如文件路径、状态、文本   | 报错信息，如找不到文件、无权限等            |


# FAQ
## How to call powershell script from c# code?
[https://learn.microsoft.com/en-us/powershell/scripting/developer/hosting/adding-and-invoking-commands?view=powershell-7.4](https://learn.microsoft.com/en-us/powershell/scripting/developer/hosting/adding-and-invoking-commands?view=powershell-7.4)

[https://learn.microsoft.com/en-us/answers/questions/1334905/c-process-how-to-execute-powershell-command-with-a](https://learn.microsoft.com/en-us/answers/questions/1334905/c-process-how-to-execute-powershell-command-with-a)

[https://stackoverflow.com/questions/26184932/get-powershell-errors-from-c-sharp](https://stackoverflow.com/questions/26184932/get-powershell-errors-from-c-sharp)

https://stackoverflow.com/questions/74016517/declare-a-variable-to-ffmpeg
https://stackoverflow.com/questions/8213865/ffmpeg-drawtext-over-multiple-lines
## How to escape special character?
Use `` or \`
for example:
![](images/posts/Pasted%20image%2020250605215239.png)

 ```
`$ for $
`# for #
`" for "
`( for (
\`: for :
\\% for %
 ``' for '
```
 
 # How to define functions in Powershell?
  way1
``` 
 function splitStringBySizeByWord([string]$string, [int]$size){
 ....
 }
```
way2
```
 function splitStringBySizeByWord{

    Param([string]$string, [int]$size)
    ....
    }
```

how to call:
```
# import
. "C:\Workspace\...\string.ps1"

....
$multiLineString = splitStringBySizeByWord -string $string -size $size;
```
# token '&&' is not a valid statement separator in this version?
这种写法：
```
ffmpeg -i input.mp4 output.mp4 && echo "done"
```
主要问题是：Windows PowerShell 5.x 没有内置 `&&` 操作符（直到 PowerShell 7+ 才支持 `&&` 和 `||`）
改为使用 `if ($?)` 或 `;`
```
ffmpeg -i input.mp4 output.mp4; Write-Host "转换完成"
```
或
```
ffmpeg -i input.mp4 output.mp4
if ($?) {
    Write-Host "Done"
} else {
    Write-Host "Error occurred"
}
```
或
```
ffmpeg -i input.mp4 output.mp4; Write-Host "转换完成"
```
![](images/posts/Pasted%20image%2020250614194938.png)
[Introduction to PowerShell](https://learn.microsoft.com/en-us/training/modules/introduction-to-powershell/)
[Powershell Tutorial](https://www.tutorialspoint.com/powershell/index.htm)
[PoweShell 101](https://learn.microsoft.com/en-us/powershell/scripting/learn/ps101/00-introduction)
[PowerShell Docs](https://learn.microsoft.com/en-us/powershell/)
[Migrating from Windows PowerShell 5.1 to PowerShell 7](https://learn.microsoft.com/en-us/powershell/scripting/whats-new/migrating-from-windows-powershell-51-to-powershell-7)
[Differences between Windows PowerShell 5.1 and PowerShell 7.x](https://learn.microsoft.com/en-us/powershell/scripting/whats-new/differences-from-windows-powershell)
[PowerShell Support Lifecycle](https://learn.microsoft.com/en-us/powershell/scripting/install/powershell-support-lifecycle)