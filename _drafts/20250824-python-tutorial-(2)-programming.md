# Syntax
- Two leading and trailing underscores are used in language itself for special purpose. For example, `__add__`, `__init__`
- 
多行注释
```python
"""  
This is a comment  
written in  
more than just one line  
"""  
print("Hello, World!")
```
## Data type

Python **类型丰富**，既有简单数据类型，也有序列、集合、映射等高级类型。    

| Text Type:      | `str`                              |
| --------------- | ---------------------------------- |
| Numeric Types:  | `int`, `float`, `complex`          |
| Sequence Types: | `list`, `tuple`, `range`, str      |
| Mapping Type:   | `dict`                             |
| Set Types:      | `set`, `frozenset`                 |
| Boolean Type:   | `bool`                             |
| Binary Types:   | `bytes`, `bytearray`, `memoryview` |
| None Type:      | `NoneType`                         |
## function
![](images/posts/20250824-python-tutorial-(2).jpeg)1. - Python 支持多种参数类型：        
- **位置参数**：            
	`def add(a, b):
	 return a + b
	add(2,3)  # 5`
	
- **默认参数**：            
	`def greet(name="Guest"):
	     print(f"Hello, {name}!") 
	greet()      # Hello, Guest! 
	greet("Tom") # Hello, Tom!`
	
- **可变参数 `*args`**（接收任意数量位置参数）：            
	`def sum_all(*args):     
		return sum(args) 
	sum_all(1,2,3,4)  # 10`
	
- **关键字参数 `**kwargs`**（接收任意数量的关键字参数）：            
	`def print_info(**kwargs):
	     for k,v in kwargs.items(): 
	print(k,v) print_info(name="Alice", age=25)`
            
2. **函数体**    
    - 由缩进代码块组成        
    - 执行函数逻辑        
    
3. **返回值**    
    - 使用 `return` 返回结果        
    - 不写 `return` 默认返回 `None`
        
`def add(a,b):     
	return a + b  
result = add(3,4)  # result = 7`

4️⃣ 匿名函数（Lambda 函数）
- 用于写简单的一行函数    
- 语法：`lambda 参数: 表达式
`square = lambda x: x**2 print(square(5))  # 25`

5️⃣ 高阶函数
- **函数可以作为参数传递**    
- Python 支持：`map()`, `filter()`, `reduce()`, `sorted(key=...)`
    
`def square(x):
	return x**2  
nums = [1,2,3,4] 
squared = list(map(square, nums)) 
print(squared)  # [1,4,9,16]`

6️⃣ 小结
- 函数 = 可复用的代码块    
- 核心关键词：`def`、`return`    
- 参数类型丰富：位置参数、默认参数、可变参数、关键字参数    
- 支持 **匿名函数（lambda）** 和 **高阶函数**    
- Python 函数非常灵活，是写模块化、可维护代码的基础