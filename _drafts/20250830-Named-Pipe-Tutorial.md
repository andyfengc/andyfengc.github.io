Named Pipe（命名管道）是一种 **进程间通信（IPC, Inter-Process Communication）机制**。它的作用是让不同的进程之间可以像读写文件一样进行数据交换。主要用于本地，单机。
**Named Pipe 就是一个带名字的、文件系统可见的管道，常用于不同进程之间高效传输数据，支持客户端/服务端模式。**

简单说，它就是一个 **有名字的“管道”**，进程 A 可以往里面写数据，进程 B 可以从里面读数据。和匿名管道（pipe `|`）相比，Named Pipe 更灵活，
# Introduction

**管道（Pipe）概念**最早出现在 **Unix 系统**，1970年代由 **Bell Labs** 的 Ken Thompson 等人引入。   
最初是 **匿名管道（Anonymous Pipe）**，只能在父子进程之间通信。    
**Named Pipe**（命名管道）出现是为了解决：    
1. 进程不是父子关系；        
2. 想让多个客户端进程访问同一个通信通道。

Unix/Linux
- 在 **System V / BSD Unix** 中，通过 `mkfifo` 或 `mknod` 创建 FIFO 文件实现命名管道。    
- Linux 内核从 2.x 版本开始完善了管道机制，命名管道成为标准 IPC 方式之一。    
- 优点：简单、轻量、系统调用少。    
- 局限：主要用于 **单机通信**，不适合跨网络。

Windows
- Windows NT 系列开始（1993年左右）引入了 **Named Pipe**。    
- 采用 **客户端/服务端模式**；        
 - 支持 **跨会话、跨用户甚至跨机器**（通过 `\\Server\pipe\PipeName`）。        
- Windows 内核对 Named Pipe 支持非常完善，是很多系统服务通信的首选方式，如：    
    - **SMB（Server Message Block）协议**；        
    - **远程管理（WMI, RPC）**；        
    - **SQL Server 客户端/服务端通信**。

- **本地 Named Pipe**：属于 **操作系统内核 IPC 层（应用层 API 级别的通信抽象）**，不在 OSI 七层模型中。    
- **远程 Named Pipe（Windows）**：基于 **SMB 协议**，运行在 OSI 模型的 **应用层**，底层依赖 TCP/IP。

1. Named Pipe 是 **稳定成熟的本地进程间通信技术**。    
2. 优势：低延迟、轻量、跨进程、可跨用户/网络（Windows）。    
3. 局限：跨机器互联网通信能力有限，现代微服务、容器环境中使用减少。    
4. 未来趋势：**仍然会存在于系统级和同机应用中，不会被完全淘汰**，但在互联网级通信中会逐步被 socket/HTTP/gRPC/消息队列取代。    
5. 支持：Windows、Linux、Unix 都支持，.NET、Python、C/C++ 都有封装，Java 需要额外方案。
## 命名管道特点
- **有名字**    
    - 存在于文件系统命名空间里（Windows 下通常是 `\\.\pipe\PipeName`，Linux/Unix 下是特殊的设备文件 `mkfifo mypipe`）。        
    - 进程只要知道这个名字，就能访问管道，不需要父子进程关系。
        
- **双向通信**    
    - 匿名管道一般是单向的（要双向就得建两条），而 Named Pipe 通常支持全双工读写。
        
- **跨进程、跨会话甚至跨机器**    
    - 在 Windows 上，可以用于不同用户会话，甚至支持通过网络在两台机器之间通信（基于 SMB 协议）。        
    - 在 Unix/Linux 上，主要是本机通信（跨主机需要 socket）。
        
- **像文件一样操作**    
    - 创建 Named Pipe 后，进程用 `open / read / write / close`（Unix/Linux）或 `CreateFile / ReadFile / WriteFile / CloseHandle`（Windows）来操作，API 接口和文件非常类似。

## 未来发展趋势

技术角度
- Named Pipe 本身是 **成熟、稳定的技术**，几乎没有再大幅改进的空间。    
- 在单机通信中，它依然比 TCP/UDP 更轻量、延迟更低。    
- Windows 的跨网络能力依然有价值，但在互联网场景中逐渐被 **socket、REST API、gRPC** 替代。
    
## 系统/平台支持情况
| 平台                   | 支持情况    | 特点                                                     |
| -------------------- | ------- | ------------------------------------------------------ |
| Windows              | ✅ 完全支持  | 支持跨进程、跨用户、跨网络，客户端/服务端模式，集成在 Win32 API、.NET 中           |
| Linux / Unix / macOS | ✅ 支持    | 通过 `mkfifo` 或 `mknod` 创建 FIFO 文件，单机通信，像文件操作一样读写        |
| .NET / C#            | ✅ 完全支持  | 提供 `NamedPipeServerStream` 和 `NamedPipeClientStream` 类 |
| Python               | ✅ 支持    | `os.mkfifo`（Linux）、`pywin32`（Windows）                  |
| Java                 | ⚠️ 间接支持 | 通过 JNI 或 UnixDomainSocket 模拟，标准库没有直接 Named Pipe 类      |
| 跨容器 / 云              | ⚠️ 有限制  | 主要依赖宿主机共享文件或通过 socket 替代                               |
普及情况

Windows 系统：
非常普及，几乎所有系统级服务、数据库（SQL Server）、应用间通信都有使用。

Linux / Unix：
命名管道普及率高，但更多作为 简单 IPC 或脚本工具。
高并发场景通常使用 socket、消息队列。

互联网应用：
普及率低，主要还是 TCP/HTTP/gRPC 为主。

开发者视角：
学习成本低，API 简单，调试方便，但应用场景相对局限于同机进程。
### 潜在影响因素

1. **微服务和云计算**：
    
    - 大多数新架构倾向于 **HTTP/gRPC** 或 **消息队列（Kafka、RabbitMQ、Redis）**，减少了对本地 IPC 的依赖。
        
2. **容器化**：
    
    - Docker、Kubernetes 中，容器间通信多用 **TCP/Unix Socket/消息队列**，Named Pipe 在容器间跨主机使用受限。
        
3. **系统级服务通信仍然存在需求**：
    
    - Windows 系统服务、数据库内部模块、同机应用仍会使用 Named Pipe，因为速度快、可靠。
        

✅ 结论：**不会被彻底淘汰，但更多是作为系统级通信手段，而非互联网分布式通信首选。**
# 使用场景

- **客户端/服务器模型**    
    - 服务器进程创建一个 Named Pipe 并等待连接；        
    - 客户端进程连接这个管道并读写数据。
        
- **替代 Socket 的轻量通信**    
    - 在同一台机器上，Named Pipe 比 TCP/UDP socket 更高效。
        
- **系统服务与应用通信**    
    - Windows 中，很多系统服务与应用的交互就是用 Named Pipe。
# 用法
## Linux
```bash
# 创建命名管道
mkfifo mypipe

# 进程 A 写入数据
echo "Hello Pipe" > mypipe

# 进程 B 读取数据
cat < mypipe
```
 ## C#
```csharp
 // 服务端
using (var server = new NamedPipeServerStream("mypipe"))
{
    server.WaitForConnection();
    using (var writer = new StreamWriter(server))
    {
        writer.WriteLine("Hello from server");
    }
}

// 客户端
using (var client = new NamedPipeClientStream(".", "mypipe", PipeDirection.In))
{
    client.Connect();
    using (var reader = new StreamReader(client))
    {
        Console.WriteLine(reader.ReadLine());
    }
}
```

# FAQ 
## 跟命名管道类似的技术还有哪些？
命名管道（Named Pipe）是一种经典的 IPC（Inter-Process Communication, 进程间通信）技术，它不是唯一的。

和它类似的 IPC 技术，其核心目标都是：**让不同进程之间交换数据、共享状态或协调动作**。

|技术|特点|与 Named Pipe 的关系|
|---|---|---|
|匿名管道|简单，父子进程单向通信|Named Pipe 的前身|
|FIFO (mkfifo)|Linux 的命名管道|本质就是 Named Pipe|
|Unix Domain Socket|高效，支持双向和多路|Linux 上比 Named Pipe 更常用|
|TCP/UDP Socket|跨机器通信|可替代远程 Named Pipe|
|消息队列|有消息边界，支持优先级|比管道更适合复杂场景|
|共享内存|最高效，但需要同步机制|更适合大数据传输|
|D-Bus|Linux 系统服务通信|类似 Windows 的 Named Pipe + RPC|
|gRPC/REST|跨平台/跨机器通信|属于应用层新一代 IPC|
### 1️⃣ 基于内核缓冲/数据流的通信

这些方式和管道类似，利用操作系统内核中的缓存区来传输字节流：

- **匿名管道（Anonymous Pipe）**
    
    - 只能在父子进程之间使用，单向通信（要双向得建两条）。
        
- **FIFO（命名管道的 Unix 实现）**
    
    - Linux/Unix 的 `mkfifo`，本质上就是命名管道。
        
- **Socket（套接字）**
    
    - 可用于本机进程（Unix Domain Socket）或跨机器进程（TCP/UDP/IP）。
        
    - 在 Linux 下，本机 IPC 时推荐 Unix Domain Socket，功能类似 Named Pipe，但更灵活。
        
- **消息队列（Message Queue）**
    
    - 比管道更高级，支持消息边界（不像管道只是字节流）。
        
    - 有 **System V Message Queue** 和 **POSIX Message Queue** 两种。
        

---

### 2️⃣ 基于共享内存的通信

比管道更高效，因为数据不需要复制，直接在共享内存区读写：

- **共享内存（Shared Memory, shmget/mmap）**
    
    - 多个进程映射同一块物理内存进行通信。
        
    - 通常需要配合 **信号量 / 互斥锁** 来控制同步。
        
- **内存映射文件（Memory-Mapped File）**
    
    - Windows 下有 `CreateFileMapping`/`MapViewOfFile`；
        
    - Linux 下用 `mmap`，可以用文件或匿名映射实现。
        

---

### 3️⃣ 基于信号或事件通知的通信

更轻量，用来 **通知** 而非大数据传输：

- **信号（Signal）**
    
    - Unix/Linux 下的进程间通知（如 `SIGKILL`, `SIGUSR1`）。
        
    - 只能传很少量信息。
        
- **事件对象 / 信号量 / 互斥锁**
    
    - Windows 下有 `Event`, `Semaphore`, `Mutex`；
        
    - Linux 下有 POSIX `sem_t`，用于进程同步。
        

---

### 4️⃣ 高级 IPC / 中间件

在分布式或复杂系统里，经常用更高级的 IPC 技术：

- **D-Bus（Linux 桌面常见）**
    
    - 现代 Linux 桌面环境（GNOME、KDE）的系统服务通信。
        
- **gRPC / Thrift / REST API**
    
    - 更现代的跨进程/跨机器通信，基于网络协议。
        
- **消息中间件**
    
    - ZeroMQ、RabbitMQ、Kafka、Redis Pub/Sub，属于“加强版消息队列”。
### ✅ 总结
- **和 Named Pipe 最接近的**：匿名管道、FIFO、Unix Domain Socket。    
- **更高效的替代方案**：共享内存（大数据）、消息队列（复杂通信）。    
- **更现代的进化版**：D-Bus（Linux）、RPC/gRPC/REST（跨平台/分布式）。