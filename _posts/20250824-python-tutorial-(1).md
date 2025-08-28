# Introduction
**Python** was created by Guido van Rossum during 1985- 1990.

characteristics 
- It supports functional and structured programming methods as well as [OOP](https://www.tutorialspoint.com/python/python_oops_concepts.htm).
- It can be used as a scripting language or can be compiled to byte-code for building large applications.
- It provides very high-level dynamic data types and supports dynamic type checking.
- It supports automatic garbage collection.
- It can be easily integrated with C, C++, COM, ActiveX, CORBA, and Java.

Python is a general purpose programming language known for its readability. It is widely applied in various fields.
- In [**Data Science**](https://www.tutorialspoint.com/data_science/index.htm), Python libraries like [**Numpy**](https://www.tutorialspoint.com/numpy/index.htm), [**Pandas**](https://www.tutorialspoint.com/python_pandas/index.htm), and [**Matplotlib**](https://www.tutorialspoint.com/matplotlib/index.htm) are used for data analysis and visualization.
- Python frameworks like [**Django**](https://www.tutorialspoint.com/django/index.htm), and [**Pyramid**](https://www.tutorialspoint.com/python_pyramid/index.htm), Flask, FasAPI, make the development and deployment of Web Applications easy.
- This programming language also extends its applications to **computer vision** and image processing.
- 在科研和量化金融领域，Python 代替了 Matlab 和 R
- It is also favored in many tasks like **Automation**, Job Scheduling, [GUI development](https://www.tutorialspoint.com/python/python_gui_programming.htm), etc.

There are many other good reasons that make Python the top choice of any programmer:

- **Python is Interpreted** − Python is processed at runtime by the interpreter. You do not need to compile your program before executing it. This is similar to PERL and PHP.
- **Python is Interactive** − You can actually sit at a Python prompt and interact with the interpreter directly to write your programs.
- **Python is Object-Oriented** − Python supports Object-Oriented style or technique of programming that encapsulates code within objects.
- **Python is a Beginner's Language** − Python is a great language for the beginner-level programmers and supports the development of a wide range of applications from simple text processing to WWW browsers to games.
# 特点
![](images/posts/20250824-install-python-2.jpeg)

简单易学：
这是 Python 广受欢迎的最重要原因之一。Python 的关键字数量有限。它的语法简单，使用缩进避免了大括号的杂乱，动态类型无需事先声明变量，这些特点都有助于初学者快速轻松地学习 Python。

动态类型：
Python 是一种动态类型编程语言。在 Python 中，您不需要在变量声明时指定变量的时间。由于 Python 的动态类型特性，变量类型是在运行时根据赋值指定的。

Python 解释器有交互模式和脚本模式。
标准 Python 发行版自带一个交互式 shell，它的工作原理是 REPL（Read Evaluate Print Loop，读取评估打印循环）。shell 显示一个 Python 提示符 >>>。您可以键入任何有效的 Python 表达式，然后按 Enter。Python 解释器会立即返回响应，并返回提示以读取下一个表达式。
![](/images/posts/20250824-install-python.jpeg)
quit(), exit() 来退出
交互模式对于熟悉程序库和测试其功能特别有用。在编写程序之前，你可以在交互模式下试用一些小代码片段。
脚本模式就是写.py文件

多范式：
Python 是一种完全面向对象的语言。Python 程序中的一切都是对象。然而，Python 可以很方便地封装其面向对象的特性，以用作命令式或过程式语言（如 C 语言）。此外，还开发了一些第三方工具来支持其他编程范式，如面向方面编程和逻辑编程。

标准库：
 Python 的关键字非常少（只有 35 个），但它的软件发布时包含了一个由大量模块和包组成的标准库。因此，Python 完全支持序列化、数据压缩、互联网数据处理等编程需求。

Python 的一些流行模块包括
NumPy
Pandas
Matplotlib
Tkinter
Math

图形库开发：
Python 的标准发行版有一个名为 TKinter 的优秀图形库。它是大受欢迎的图形用户界面工具包 TCL/Tk 的 Python 移植。您可以用 Python 构建极具吸引力的用户友好 GUI 应用程序。GUI 工具包通常用 C/C++ 编写。其中许多已被移植到 Python。例如 PyQt、WxWidgets、PySimpleGUI 等。

数据库连接
几乎任何类型的数据库都可以用作 Python 应用程序的后端。DB-API 是一套数据库驱动软件规范，用于让 Python 与关系数据库通信。通过许多第三方库，Python 还能与 MongoDB 等 NoSQL 数据库协同工作。

可扩展性
所谓可扩展性，是指添加新功能或修改现有功能的能力。如前所述，CPython（Python 的参考实现）是用 C 语言编写的，因此可以轻松地用 C 语言编写模块/库，并将其纳入标准库中。Python 还有其他实现，如 Jython（用 Java 编写）和 IPython（用 C# 编写）。因此，用 Java 和 C# 分别编写和合并这些实现中的新功能是可能的。

它支持函数式和结构式编程方法以及 OOP。

它可以作为脚本语言使用，也可以编译成字节码，用于构建大型应用程序。

它提供非常高级的动态数据类型，并支持动态类型检查。

它支持自动垃圾回收。

它可以与 C、C++、COM、ActiveX、CORBA 和 Java 轻松集成。

## python vs java/c#
- Python **严格意义上是解释型语言**，代码每次运行都靠解释器逐行执行。
![](images/posts/20250824-install-python-3.jpeg)
 - Java / C# 是 **虚拟机语言 + JIT**，代码先编译成中间码，运行时再编译成本地机器码，所以效率比 Python 高很多。    
- 所以很多资料会把 Java / C# 称作 **半解释型** 或 **编译型虚拟机语言**。

| 语言       | 执行流程                                                                                       | 类型           |
| -------- | ------------------------------------------------------------------------------------------ | ------------ |
| **Java** | 1. 编译成 **字节码 (.class 文件)**2. JVM（Java 虚拟机）加载字节码3. **JIT（即时编译）**将字节码编译成本地机器码执行              | 半编译型 / 虚拟机语言 |
| **C#**   | 1. 编译成 **中间语言 IL（Intermediate Language）**2. .NET CLR（公共语言运行时）加载 IL3. **JIT**在运行时编译成本地机器码执行 | 半编译型 / 虚拟机语言 |
任何编程语言的指令都必须翻译成机器代码，处理器才能执行。编程语言要么基于编译器，要么基于解释器。
如果是编译器，则会生成整个源程序的机器语言版本。即使有一条错误的语句，转换也会失败。因此，对于初学者来说，开发过程是乏味的。C 系列语言（包括 C、C++、Java、C# 等）都是基于编译器的。

在任何基于编译器的语言（如 Java）中，除非整个代码没有错误，否则源代码不会转换成字节码。而在 Python 中，语句会一直执行到遇到第一个错误为止。

## python vs c++
### Python 是一种通用的高级编程语言。
Python 用于网络开发、机器学习和其他尖端软件开发。Python 既适合新手，也适合经验丰富的 C++ 和 Java 程序员。Guido Van Rossam 于 1989 年在荷兰国家研究院创建了 Python。Python 于 1991 年发布。

Python 是一种高级解释型编程语言。与其他语言相比，Python 的学习曲线要低得多，使用起来也相当简单。

Python 是人工智能、机器学习 (ML)、数据科学、物联网 (IoT) 等领域专业人员的首选编程语言，因为它既擅长脚本应用程序，也擅长独立程序。

除此之外，Python 还是首选语言，因为它简单易学。由于其出色的语法和可读性，减少了维护费用。程序的模块化和代码的可重用性都有助于其支持各种软件包和模块。

使用 Python，我们可以进行
网络开发
数据分析和机器学习
自动化和脚本编写
软件测试等

 Python 的特性
易于学习 Python 结构简单，关键字少，语法清晰。这使学生易于快速学习。用 Python 编写的代码更容易阅读和理解。
易于维护 Python 的源代码非常易于维护。
庞大的标准库 Python 的大部分库都很容易移动，可以在 UNIX、Windows 和 Mac 上运行。
可移植的 Python 可以在多种硬件平台上运行，而且所有平台都有相同的界面。
### C++ 是一种中级、大小写敏感、面向对象的编程语言。
Bjarne Stroustrup 在贝尔实验室创建了 C++。C++ 是一种独立于平台的编程语言，可在 Windows、Mac OS 和 Linux 上运行。C++ 接近硬件，允许底层编程。这为开发人员提供了对内存的控制、更高的性能和可靠的软件。

C++ 是一种静态类型的、编译的、多范式的通用编程语言，学习曲线非常陡峭。视频游戏、桌面应用程序和嵌入式系统都广泛使用它。C++ 与 C 兼容，几乎可以在不做任何修改的情况下编译所有 C 源代码。与 C 语言相比，面向对象编程使 C++ 成为一种结构更好、更安全的语言。

中间层语言 它是一种中间层语言，因为它既可用于系统开发，也可用于大型消费应用，如媒体播放器、Photoshop、游戏引擎等。

执行速度 C++ 代码运行速度快。因为它经过编译并广泛使用了过程。取消了垃圾回收、动态类型和其他等妨碍程序的执行的功能。

面向对象语言 面向对象编程灵活、易于管理。可以实现大型应用程序。不断增长的代码使过程式代码难以处理。C++ 相对于 C 语言的主要优势。

广泛的库支持 C++ 拥有庞大的库。支持第三方库，可实现快速开发。

### 比较
与 C++ 相比，Python 的语法更简单。Python 不使用大括号来标记语句块。相反，它使用缩进。缩进级别相似的语句会标记一个语句块。这使得 Python 程序更具可读性。

静态类型与动态类型
C++ 是一种静态类型语言。用于存储数据的变量类型需要在一开始就声明。未声明的变量不能使用。一旦变量被声明为某种类型，就只能在其中存储该类型的值。
Python 是一种动态类型语言。它不要求在给变量赋值之前先声明变量。因为变量可以存储任何类型的数据，所以它被称为动态类型。

面向对象编程概念
C++ 和 Python 都实现了面向对象编程概念。与 Python 相比，C++ 更接近 OOP 理论。C++ 支持数据封装的概念，因为变量的可见性可以定义为 public、private 和 protected。
而 Python 没有定义可见性的规定。与 C++ 不同，Python 不支持方法重载。因为它是动态类型的，所以所有方法默认都是多态的。
C++ 实际上是 C 语言的扩展。可以说，C 语言中添加了额外的关键字，使其支持 OOP。因此，我们可以在 C++ 中编写面向 C 类型过程的程序。
Python 是完全面向对象的语言。Python 的数据模型是这样的：即使您可以采用面向过程的方法，Python 内部也使用面向对象的方法。

垃圾回收
C++ 使用指针概念。C++ 程序中未使用的内存不会自动清除。在 C++ 中，垃圾回收过程是手动进行的。因此，C++ 程序很可能面临与内存相关的异常行为。
Python 具有自动垃圾回收机制。因此，Python 程序更健壮，更不容易出现与内存相关的问题。

应用领域
由于 C++ 程序可以直接编译成机器代码，因此它更适合系统编程、编写设备驱动程序、嵌入式系统和操作系统实用程序。
Python 程序适合应用编程。它目前的主要应用领域是数据科学、机器学习、API 开发等。

| Criteria           | Python                                                                                                                         | C++                                                                                                                           |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| Execution          | Python is an interpreted-based programming language. Python programs are interpreted by an interpreter.                        | C++ is a compiler-based programming language. C++ programs are compiled by a compiler.                                        |
| Typing             | Python is a dynamic-typed language.                                                                                            | C++ is a static-typed language.                                                                                               |
| Portability        | Python is a highly portable language, code written and executed on a system can be easily run on another system.               | C++ is not a portable language, code written and executed on a system cannot be run on another system without making changes. |
| Garbage collection | Python provides a garbage collection feature. You do not need to worry about the memory management. It is automatic in Python. | C++ does not provide garbage collection. You have to take care of freeing memories. It is manual in C++.                      |
| Syntax             | Python's syntaxes are very easy to read, write, and understand.                                                                | C++'s syntaxes are tedious.                                                                                                   |
| Performance        | Python's execution performance is slower than C++'s.                                                                           | C++ codes are faster than Python codes.                                                                                       |
| Application areas  | Python's application areas are machine learning, web applications, and more.                                                   | C++'s application areas are embedded systems, device drivers, and more.                                                       |
# 为什么 **Python 成为 AI/深度学习主流语言，而不是 C++ 或 C#**
## 1️⃣ Python 的核心优势：开发效率

1. **语法简单、易读**    
    - AI 算法本身就很复杂，如果用 C++ 写，每一行都要处理类型、内存管理，容易出错。        
    - Python 写法简洁，研究人员可以更专注于算法本身，而不是琐碎的语法。
        
2. **快速原型开发**    
    - 深度学习需要大量实验：调整网络结构、调参、测试新模型。        
    - Python 能几分钟写出实验代码，而 C++ 可能需要几小时甚至几天。      
## 2️⃣ 性能其实不完全依赖 Python
Python 本身慢，但 **深度学习的核心计算都是在 C/C++ / CUDA 库里跑的**：

| 框架             | 背后的实现                                  |
| -------------- | -------------------------------------- |
| **TensorFlow** | 核心计算用 C++ + CUDA 写，Python 只是调度和接口      |
| **PyTorch**    | 核心张量运算用 C++，GPU 部分用 CUDA，Python 负责控制逻辑 |
| **NumPy**      | 核心矩阵运算用 C 写，Python 只是调用                |
**解释型 Python 只是“胶水语言”**，把高性能底层库连接起来。  
所以，即便 Python 本身慢，也不会成为瓶颈。
## 3️⃣ 庞大的生态系统
Python 拥有：
- **丰富的 AI/深度学习库**（TensorFlow, PyTorch, Keras, NumPy, SciPy）    
- **数据处理库**（Pandas, OpenCV, scikit-learn）    
- **社区和文档**    
> AI 研究人员不用重写轮子，能快速实验最新论文成果。
如果用 C++/C#，就要自己实现大部分基础库，开发成本极高。
## 4️⃣ 跨平台与灵活性
- Python 在 Windows / Linux / macOS 都可以运行    
- 容易与 GPU（CUDA）、TPU 等硬件接口对接    
- 脚本语言的灵活性适合快速迭代模型和处理实验数据
## 5️⃣ C++ / C# 的局限
- **C++**：性能快，但开发慢，调试难，写深度学习框架成本高    
- **C#**：主要在 Windows 平台，生态不如 Python 丰富，跨平台和 GPU 支持相对弱
## ✅ 总结
**选择 Python 不是为了性能，而是为了：*
1. **快速开发、实验迭代**    
2. **调用高性能库完成计算密集型任务**    
3. **庞大的 AI/数据生态系统支持**    
> 换句话说：Python 负责“控制和逻辑”，C++/CUDA 负责“计算核心”，两者配合，既快又高效。

**AI 框架执行流程图**，展示 Python、C++/CUDA 在训练神经网络时的分工
![](/images/posts/20250824-install-python-1.jpeg)

# Install
download Python: https://www.python.org/downloads/
![](/images/posts/Pasted%20image%2020250824105307.png)
![](/images/posts/Pasted%20image%2020250824105339.png)Python 3.7+ 默认自带 pip
可在命令行测试：
python --version
![](/images/posts/Pasted%20image%2020250824111527.png)

pip --version
![](/images/posts/Pasted%20image%2020250824110029.png)可选 GPU 支持
如果希望加速，可安装 PyTorch GPU 版本
官网：https://pytorch.org/get-started/locally/
手动把scripts目录加到path
C:\Users\Owner\AppData\Roaming\Python\Python311\Scripts
![](/images/posts/Pasted%20image%2020250824110549.png)
python 自带一个IDE "IDLE"
![](images/posts/20250824-python-tutorial-(1).jpeg)

visual studio code
![](/images/posts/Pasted%20image%2020250824114808.png)
![](/images/posts/Pasted%20image%2020250824115753.png)
## Linux install
In Ubuntu Linux, the easiest way to install Python is to use apt Advanced Packaging Tool. 
```
$ sudo apt update
$ sudo apt-get install python3.11
```

# “The Zen of Python” （Python 之禅
## Beautiful is better than ugly.
美胜于丑。写出来的代码要美观、整洁，而不是乱七八糟。即便机器能跑，人也要能看懂。
## Explicit is better than implicit.
明确优于隐晦。代码意图要清晰，不要搞一些晦涩的黑魔法。别人看代码时应该一眼明白。
## Simple is better than complex.
简单优于复杂。能用简单方法解决，就不要写得太复杂。
## Complex is better than complicated.
复杂胜于杂乱。有些问题确实需要复杂的方案，但要保证逻辑清晰、结构合理，而不是乱糟糟的拼凑。
## Flat is better than nested.
扁平胜于嵌套。代码层次要尽量简单，不要深层嵌套（比如 10 层 if/for）。
## Sparse is better than dense.
稀疏胜于密集。代码里适当留空行和空格，便于阅读，不要写成一坨。
## Readability couts.
可读性很重要。这是 Python 最核心的原则之一。代码是写给人看的，顺便也能给计算机执行。
## Special cases aren't special enough to break the rules.
特殊情况也不足以打破规则。不要因为少数情况就写奇怪的“特例代码”，规则统一更重要。
## Although practicality beats purity.
但实用性胜于纯粹。如果理论上的“完美”方案不实用，那还是要选择能落地的方案。
## Errors should never pass silently.
错误不应该悄无声息地被忽略。发现错误要明确处理，避免埋坑。
## Unless explicitly silenced.
除非明确选择忽略。如果真的不重要，可以显式地写出“忽略错误”的逻辑（例如 try/except）。
## In the face of ambiguity, refuse the temptation to guess.
面对模棱两可时，拒绝乱猜。代码要明确表达含义，不要依赖推测或模糊的语义。
## There should be one-- and preferably only one --obvious way to do it.
应该有一种——最好只有一种——显而易见的方式去做。避免“十种写法都能跑”的混乱局面，让大家都遵循统一方式。
## Although that way may not be obvious at first unless you're Dutch.
尽管这种方式一开始可能并不明显，除非你是荷兰人。指的是 Guido van Rossum（Python 之父），意思是可能只有他一开始知道这种方式。
## Now is better than never.
现在做比永远不做好。不要拖延。
## Although never is often better than *right* now.
但有时“永不做”比“马上做”要好。如果没想清楚，不要急着写糟糕的代码。
## If the implementation is hard to explain, it's a bad idea.
如果实现难以解释，那就是个坏主意。说明逻辑太绕，不适合。
## If the implementation is easy to explain, it may be a good idea.
如果实现容易解释，那可能是个好主意。清晰的思路通常意味着代码也优雅。
## Namespaces are one honking great idea -- let's do more of those!
命名空间是个伟大的创意——让我们多多使用它！命名空间（namespace）可以避免命名冲突，让代码结构更清晰。
# FAQ
## windows 安装python结束后，显示 disable path length limit 什么意思
这是 Windows 安装 Python 时的一个选项，意思是：**“禁用路径长度限制”**。

在 Windows 里，默认情况下路径（文件夹+文件名）长度不能超过 **260 个字符**（即所谓的 `MAX_PATH` 限制）。  
很多 Python 第三方库（尤其是深层目录的，比如 `node_modules`、虚拟环境、科学计算库）可能会生成特别长的路径，如果超过限制就会报错。

👉 选择 **“Disable path length limit”**（禁用路径长度限制）：
- 安装程序会修改系统注册表，让 Windows 10/11 支持超过 260 个字符的路径。    
- 这样 Python（以及其他程序）就能使用更长的路径，避免因为路径太长而出错。    
- 一般建议 **勾选启用**，对系统没坏处。
    
如果你没点这个选项，Python 也能用，只是某些库可能因为路径过长报错。
要是你已经跳过了，也可以手动开启：
1. 按 **Win + R** 输入 `gpedit.msc` → 打开本地组策略编辑器  >设置为 **启用**
```
计算机配置 → 管理模板 → 系统 → 文件系统 → 启用 Win32 长路径
```
2. 或者直接修改注册表： 把 `LongPathsEnabled` 改为 `1`。
```
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem
```
## 什么是pip
pip 是 Python 官方推荐的 包管理工具，名字来源于 “Pip Installs Packages”。
它的作用和手机上的 应用商店 类似，用来安装、升级和卸载 Python 的第三方库。

Python 本身只带了最基础的功能（标准库），但是很多功能（比如爬虫、科学计算、机器学习、网页框架等）需要第三方库。这些库你可以通过 pip 下载并安装。
查看 pip 版本
```bash
pip --version
```
安装一个叫 `requests` 的库
```bash
pip install requests
```
升级库
```bash
pip install --upgrade requests
```
卸载库
```bash
pip uninstall requests
```
查看已经安装的库
```bash
pip list
```
**查看某个库的详细信息**
```
pip show requests
```
pip 安装的库到你 Python 的 **site-packages** 目录里。  
例如：
`C:\Users\你的用户名\AppData\Local\Programs\Python\Python311\Lib\site-packages`
## 什么是 Virtual Environment
在 Python 里，**Virtual Environment（虚拟环境）** 是一种 **隔离不同项目的 Python 运行环境** 的工具。
![](images/posts/20250824-python-tutorial-(1)-1.jpeg)
假设你有两个项目：
- **项目A** 用的是 `Django 2.2`    
- **项目B** 必须用 `Django 4.0`   

如果你只装在全局环境里，版本会冲突 ⚡。  
虚拟环境的作用就是：
- 给每个项目一个 **独立的小世界**，互不干扰。    
- 在虚拟环境里装的库，不会影响到全局 Python，也不会影响其他项目。
就像每个项目都有自己的 **小仓库**。
### 怎么创建和使用虚拟环境？
Python 自带一个模块 `venv`，直接用就行。
1. **创建虚拟环境** > - （会在当前目录下创建一个叫 `myenv` 的文件夹，里面就是独立的 Python 环境）
```bash
python -m venv myenv
```
![](images/posts/20250824-python-tutorial-(1)-2.jpeg)
2. **激活虚拟环境**
The utilities for activating and deactivating the virtual environment as well as the local copy of Python interpreter will be placed in the scripts folder
![](images/posts/20250824-python-tutorial-(1)-3.jpeg)
To enable this new virtual environment, execute **activate.bat**
- Windows (PowerShell)：    
    `.\myenv\Scripts\activate`  
- macOS / Linux：    
    `source myenv/bin/activate`    
![](images/posts/20250824-python-tutorial-(1)-4.jpeg)
激活后命令行前面会出现 `(myenv)` 提示符，说明进入了虚拟环境。
3. **在虚拟环境里安装库**
	`pip install requests`
- （这个 `requests` 只会装在 `myenv` 里，不会影响别的项目）
  
检查是否python运行在虚拟环境里，用sys.path：
![](images/posts/20250824-python-tutorial-(1)-5.jpeg)
  
4. **退出虚拟环境**
    `deactivate`
    
常用技巧
- 把当前虚拟环境的库记录下来：
    `pip freeze > requirements.txt`    
- 让别人复现同样的环境：    
    `pip install -r requirements.txt`
    
✅ 总结：  
**虚拟环境 = 每个项目的独立 Python 环境**，避免库冲突，让项目更容易维护和部署。
# Reference
[w3schools Python Tutorial](https://www.w3schools.com/python/default.asp)
[tutorialspoint Python Tutorial](https://www.tutorialspoint.com/python/index.htm)