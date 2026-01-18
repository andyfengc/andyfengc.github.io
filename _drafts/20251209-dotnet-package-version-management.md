# Introduction
一系列 $\text{MSBuild}$ 约定文件
- **`Directory.Build.props`:** 用于定义**共享的构建属性**（例如编译器设置、输出路径等），它在项目文件**之前**被导入。
- **`Directory.Packages.props`:** 专门用于定义**共享的 $\text{NuGet}$ 包版本**，它在项目文件被加载时专门处理 $\text{NuGet}$ 相关的属性和 `<PackageVersion>` 列表。
- $\text{Directory.Build.targets}$
# 统一版本管理CPM (Central Package Version Management)
#### 核心原理：`Directory.Packages.props` 文件
- **集中定义:** 你在解决方案的根目录下创建一个名为 **`Directory.Packages.props`** 的文件，必须叫这个名字。    
- **版本锁定:** 在此文件中，集中定义所有 $\text{NuGet}$ 包的版本。    
- **项目简化:** 所有的 `.csproj` 文件不再指定包的版本号，只指定包的名称。
- 不再在每个项目的 `.csproj` 文件中定义 `<PackageReference Include="Newtonsoft.Json" Version="13.0.3" />`，而是将版本号集中到解决方案根目录下的一个文件：`Directory.Packages.props`。
- **升级简化:** 当需要升级库时，你只需修改 **`Directory.Packages.props`** 文件中的版本号，所有引用该包的项目都会自动使用新版本。
```
/SolutionRoot
   Directory.Packages.props    👈 放这里
   ProjectA/ProjectA.csproj
   ProjectB/ProjectB.csproj
   ...
```
Directory.Packages.props
> 每一行定义一个全局可用 NuGet 包版本。
```
<Project>
  <PropertyGroup>
    <ManagePackageVersionsCentrally>true</ManagePackageVersionsCentrally>
  </PropertyGroup>
  <ItemGroup>
    <!-- 在这里管理所有版本 -->
    <PackageVersion Include="Microsoft.Extensions.Hosting" Version="8.0.0" />
    <PackageVersion Include="Newtonsoft.Json" Version="13.0.3" />
    <PackageVersion Include="System.Text.Json" Version="8.0.0" />
  </ItemGroup>
</Project>
```
所有子项目引用包时，不写版本号：**`.csproj` 示例:**
```
<Project Sdk="Microsoft.NET.Sdk">
  <ItemGroup>
    <PackageReference Include="Newtonsoft.Json" /> 
  </ItemGroup>
</Project>
```
**版本取自 Directory.Packages.props** 

某个项目想用旧版（单点覆盖）
ProjectA.csproj：
```
<PackageReference Include="Newtonsoft.Json" VersionOverride="12.0.1" />
```

怎么自动批量升级版本？
```
dotnet list package --outdated
Directory.Packages.props
```
升级后只改：
```
Directory.Packages.props
```
CPM (Directory.Packages.props) 文件是专为 $\text{NuGet}$ **包 ($\text{Package}$)** 的**版本管理**和**集中引用**设计的，它**不能**用来添加对本地 **DLL 文件**的直接引用。不能添加本地 DLL 引用

# FAQ
## 启用了 Directory.Packages.props，是不是所有项目都必须去掉版本号？包括单元测试项目？
是的——启用了 Directory.Packages.props 后，所有项目（包括单元测试项目）都应该去掉版本号。
这是最佳实践。项目文件里仍然可以保留版本号，但不建议，因为会造成冲突。