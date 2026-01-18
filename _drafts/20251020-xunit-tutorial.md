# Introduction
xUnit.net 是目前 .NET 平台上最流行的单元测试框架之一，由 NUnit 的原始作者之一创建。旨在纠正早期测试框架（如 NUnit v2 和 MSTest V1）中一些被认为会导致糟糕测试习惯的问题。其核心理念可以概括为：

| **理念**                    | **解释**                                                                                      |
| ------------------------- | ------------------------------------------------------------------------------------------- |
| **测试隔离 (Test Isolation)** | **最重要原则。** 每个测试方法（Fact 或 Theory）都应独立运行。xUnit 默认对每个测试方法**重新创建**测试类的新实例，以确保测试之间没有状态共享（即“沙箱”）。 |
| **惯例优于配置**                | 减少了大量用于设置和清理的属性（如 `[SetUp]` 和 `[TearDown]`），鼓励使用 C# 语言特性（如构造函数和 `IDisposable`）。             |
| **数据驱动测试**                | 强调 **`[Theory]`** 属性，鼓励使用参数化测试来测试同一逻辑的不同输入数据集。                                              |
xUnit.net 的属性比 NUnit 或 MSTest 少，但概念更清晰。
# Install
通过命令行
```bash
dotnet new xunit -n MyTestProject
```
通过visual studio
![](images/posts/20251020.jpeg)

# Basics
## 独立测试方法：`[Fact]`
`[Fact]` 是最基础的单元测试，标记一个**没有输入参数**的测试方法。
它表示一个“事实”，即测试的代码在特定条件下应该表现出固定的行为（始终为真）。

| **属性**   | **作用**               | **对应传统框架**                                 |
| -------- | -------------------- | ------------------------------------------ |
| `[Fact]` | 标记一个**没有输入参数**的测试方法。 | NUnit 的 `[Test]` / MSTest 的 `[TestMethod]` |
```csharp
using Xunit;
public class CalculatorTests
{
	// 这是一个简单的事实：2 加 3 应该等于 5
    [Fact]
    public void Add_TwoPlusThree_ReturnsFive()
    {
        // Arrange
        var calc = new Calculator();

        // Act
        var result = calc.Add(2, 3);

        // Assert
        Assert.Equal(5, result);
    }
}
```
## 使用 `[Theory]` + `[InlineData]` 参数化测试
`[Theory]`标记一个**带有输入参数**的测试方法。用于测试“假设”，即在不同输入数据集下，测试的代码应该始终表现出相同的行为。它必须与一个或多个数据源属性一起使用。

| **属性**         | **作用**                                                   |
| -------------- | -------------------------------------------------------- |
| `[Theory]`     | 标记一个**带有输入参数**的测试方法。                                     |
| `[InlineData]` | 为 `[Theory]` 提供**硬编码**的多个测试数据组。                          |
| `[MemberData]` | 引用同一个或不同类中的 `static IEnumerable<object[]>` 成员，以提供复杂的数据集。 |
```csharp
public class CalculatorTests
{
    [Theory]
    // 三组不同的输入数据，但都测试 Add 方法
    [InlineData(1, 2, 3)]
    [InlineData(-4, -6, -10)]
    [InlineData(0, 0, 0)]
    public void Add_VariousNumbers_ReturnsCorrectSum(int x, int y, int expected)
    {
        // Arrange
        var calculator = new Calculator();

        // Act
        var result = calculator.Add(x, y);

        // Assert
        Assert.Equal(expected, result);
    }
}
```
## 设置和清理 (Setup and Teardown)
xUnit.net 放弃了 `[SetUp]` / `[TearDown]` 属性，鼓励使用 C# 语言的特性来实现：

|**目的**|**xUnit.net 实现方式**|**解释**|
|---|---|---|
|**设置 (Setup)**|**构造函数** (`Constructor`)|用于执行所有 `[Fact]` 或 `[Theory]` **之前**的初始化代码。由于 xUnit.net 为每个测试方法实例化一个新类，因此构造函数会为每个测试运行一次。|
|**清理 (Teardown)**|实现 **`IDisposable`** 接口|`Dispose()` 方法用于执行所有 `[Fact]` 或 `[Theory]` **之后**的清理代码。`Dispose()` 也会在每个测试方法运行后被调用一次。|
```csharp
// 继承 IDisposable 接口以进行清理
public class DatabaseTests : IDisposable
{
    private readonly DbContext _context;

    // 1. 设置 (Setup): 构造函数 (每个测试运行前执行)
    public DatabaseTests()
    {
        // 模拟数据库连接或在内存中设置 DB
        _context = new DbContext("In-Memory-DB");
        // 插入测试数据
        _context.Users.Add(new User { Name = "Alice" });
        _context.SaveChanges();
    }

    [Fact]
    public void User_CanBeFoundByName()
    {
        var user = _context.Users.SingleOrDefault(u => u.Name == "Alice");
        Assert.NotNull(user);
    }

    // 2. 清理 (Teardown): IDisposable.Dispose (每个测试运行后执行)
    public void Dispose()
    {
        // 清理资源，确保测试之间不相互影响
        _context.Dispose();
    }
}
```
## 断言 (Assertions)
`Xunit.Assert` 类，用于验证测试结果。常见的断言方法包括：

| **方法**                                  | **描述**             |
| --------------------------------------- | ------------------ |
| `Assert.Equal(expected, actual)`        | 检查两个值是否相等。         |
| `Assert.True(condition)`                | 检查条件是否为真。          |
| `Assert.False(condition)`               | 检查条件是否为假。          |
| `Assert.NotNull(object)`                | 检查对象是否非空。          |
| `Assert.Throws<T>(Action)`              | 检查指定操作是否抛出特定类型的异常。 |
| `Assert.Contains(expected, collection)` | 检查集合中是否包含某个元素。     |
> **注意：** 许多开发者会搭配 **FluentAssertions** 库来编写更具可读性的断言，例如 `result.Should().Be(2)`。

# FAQ
## 如果测试项目依赖的项目是windows桌面项目
修改 **测试项目 `.csproj`**：
```xml
  <PropertyGroup>
    <TargetFramework>net9.0</TargetFramework>
    ...
  </PropertyGroup>
```
改成
```xml
  <PropertyGroup>
    <TargetFramework>net9.0-windows</TargetFramework>
    <UseWindowsForms>false</UseWindowsForms>
    <UseWPF>false</UseWPF>
    ...
```