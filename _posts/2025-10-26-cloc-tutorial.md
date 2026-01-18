# Introduction
cloc（Count Lines of Code） 是一个开源命令行工具，用于统计：
- 代码行数（Code lines）
- 注释行数（Comment lines）
- 空行数（Blank lines）
- 按编程语言分类的行数
适用于 Windows / macOS / Linux，C#、Python、JavaScript、HTML、PowerShell 等几乎所有语言。
# Installation
直接下载可执行文件
[here](https://github.com/AlDanial/cloc/releases)
把它放在任意目录，将该目录添加到系统环境变量 PATH，这样全局可用。
# How to use
统计当前目录所有文件：
```bash
cloc .
```
会输出类似：
```markdown
     150 text files.
     130 unique files.
     45 files ignored.

--------------------------------------------------------------------
Language          files          blank        comment        code
--------------------------------------------------------------------
C#                32            985            726           4532
JSON              8              0              0            812
XML               4             10             50            340
--------------------------------------------------------------------
SUM:              44            995            776           5684
--------------------------------------------------------------------
```
输出结果解读

| 列名      | 含义                     |
| ------- | ---------------------- |
| blank   | 空行数（纯空白）               |
| comment | 注释行数（含 `//` 或 `/* */`） |
| code    | 实际有效代码行数               |
| files   | 文件数（按语言分）              |
```
cloc --version
cloc --help
```
# 常用参数
## `--include-lang=<语言>`
只统计指定语言的文件。  
例如只统计 C#：
```bash
cloc --include-lang="C#" .
```
（⚠️ 注意大小写）
更精准一点（仅统计 `.cs` 文件）：
```
cloc --match-f='\.cs$' .
```
也可以多个文件夹：
```
cloc "src" "tests"
```
## 统计指定文件夹
```
cloc "D:\Projects\MyApp"
```
## --exclude-dir=<目录>
排除不想统计的文件夹：
```bash
cloc --exclude-dir=obj,bin,packages .
```
避免统计 NuGet 包、编译输出目录等。
## --by-file
显示每个文件的代码行数：
```bash
cloc --by-file --include-lang="C#" .
```
输出：
```
-------------------------------------------------------------- 
File                      blank        comment           code
-------------------------------------------------------------- 
Program.cs                  15             12            130
MainWindow.xaml.cs          20             25            220
VideoConcatTemplate.cs      30             10            530
--------------------------------------------------------------
SUM:                        65             47            880
--------------------------------------------------------------
```
## 只想看总行数
只关心 `.cs` 文件的总行数，可以用：
```
cloc --match-f='\.cs$' . --quiet --csv | Select-String "SUM"
```
或者更简单：
```
cloc --match-f='\.cs$' . | find "SUM"
```
## --exclude-ext=<扩展名>
排除特定文件类型，例如排除 `.Designer.cs`：
```bash
cloc --exclude-ext=Designer.cs --include-lang="C#" .
```
## 导出成文件
导出格式化统计结果，用于自动化报告：
```bash
cloc . --out=cloc_report.txt
```
导出成json等
`--csv` / `--json` / `--xml`
```
cloc --include-lang="C#" --json . > report.json
```
## --counted=<输出文件>
将已统计的文件列表写入文件，方便查看哪些文件被包含：
```bash
cloc --counted=files.txt .
```
# Demos
## 典型C#项目统计命令（推荐）
```
cloc . --exclude-dir="bin,obj,packages,.vs,node_modules,.git,SourceGenerators"

可选添加
--include-lang="C#"
--by-file
--out=cloc_report.txt
```
## 统计多个项目并比较：
```
cloc ProjectA ProjectB
```
自动生成对比报告，方便版本演进分析。
## 统计 Git 仓库中的变更行数：
```
cloc --git --include-lang="C#" .
```
或指定提交区间：
```
cloc --git --include-lang="C#" --git-diff-filter=ACM HEAD~10 HEAD
```
## 统计某个文件类型的平均行数
```
cloc --include-lang="C#" --by-file . | sort -n -k3
```
可用于找出哪些类代码量太大。
# FAQ
## Can't cd to ...
Can't cd to ../../../../../../../../.. from ./erver/87495a4286184a9a236b890c30fe5b9e14076b1397cd4e8304ff3751e1b1def9/WinWpf/obj/Debug/net9.0-windows/win-x64/CommunityToolkit.Mvvm.SourceGenerators/CommunityToolkit.Mvvm.SourceGenerators.ObservablePropertyGenerator: No such file or directory at `<embedded>`/File/Find.pm line 477.
📖 原因：`cloc` 默认会递归扫描整个目录，包括 `obj`、`bin` 等临时文件夹，路径太深可能出错。
✅ 解决：
```
cloc --include-lang="C#" --exclude-dir=obj,bin,packages,.vs .
```
## -B on closed filehandle $fh at script/cloc line 6642
📖 原因：旧版本在某些压缩目录或符号链接下会出现。  
✅ 解决：升级到最新版（≥2.0）或加上：
```
cloc --no3 --include-lang="C#" .
```