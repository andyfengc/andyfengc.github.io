# 在 Python 里调用 **Windows 命令行
- **快速执行**：`os.system`    
- **需要获取输出**：`subprocess.run`（推荐）    
- **异步/实时输出**：`subprocess.Popen`
## os.system

最简单直接，但功能有限，只能执行命令，返回值是退出码。
```python
import os
os.system("dir")   # 执行 Windows 的 dir 命令
```
## subprocess.run
推荐方式，功能更强，可以获取输出。
```python
import subprocess

# 运行命令并等待完成
result = subprocess.run("dir", shell=True, capture_output=True, text=True)

print("命令输出：", result.stdout)
print("错误输出：", result.stderr)
```
传参数 用字符串形式（`shell=True`）
```python
import subprocess

# 参数直接写在命令字符串里
result = subprocess.run("ping www.baidu.com -n 3", shell=True, capture_output=True, text=True)
print(result.stdout)
```
传参数 用列表形式（推荐，更安全）
不用 shell=True，把命令和参数分开
```python
import subprocess

# 参数作为列表传递
result = subprocess.run(["ping", "www.baidu.com", "-n", "3"], capture_output=True, text=True)
print(result.stdout)
```
这里 `"ping"` 是命令，后面的都是参数。这样可以避免空格、转义符出错。
如果参数里有路径（带空格）
要么加引号，要么用列表传递：
```python
# 错误：路径有空格会出问题
# subprocess.run("notepad C:\\Program Files\\test.txt", shell=True)

# 正确
subprocess.run(["notepad", "C:\\Program Files\\test.txt"])
```
参数封装成函数
```python
import subprocess

def run_cmd(cmd, args=None):
    if isinstance(cmd, str):
        command = [cmd] + (args or [])
    else:
        command = cmd + (args or [])

    result = subprocess.run(command, capture_output=True, text=True)
    return result.stdout, result.stderr, result.returncode

# 示例
out, err, code = run_cmd("ping", ["www.baidu.com", "-n", "2"])
print("输出:", out)
```
## subprocess.Popen
如果你想异步执行命令，或实时读取输出：
```python
import subprocess

process = subprocess.Popen("ping www.google.com", shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)

for line in process.stdout:
    print(line.strip())
```
## `os.popen`（旧方法，不推荐，但有时简单好用）
```python
import os

output = os.popen("echo Hello from CMD").read()
print(output)
```
# FAQ