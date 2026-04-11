# Introduction
Smartstore 是一个基于 .NET 的开源电商平台（ASP.NET Core），主要面向中大型、可高度定制的 B2C / B2B 场景。
Smartstore 提供了强大的**插件（Plugins）**和**主题（Themes）**机制。
凡是能通过插件或主题实现的功能，绝不要去动 
`SmartStore.Core` 核心底层逻辑
`SmartStore.Services` 核心业务逻辑
`Smartstore.Web`
可以修改：
`SmartStore.Modules` 所有的功能开发都应该在这里以独立插件形式存在。
`SmartStore.Web/Themes` UI 和样式的定制地
# Setup
![](images/posts/20260117.jpeg)
download
```
git clone https://github.com/andyfengc/Smartstore
cd Smartstore 
git remote add upstream https://github.com/smartstore/Smartstore.git
git remote -v
```
![](images/posts/20260117-1.jpeg)
二次开发
main 分支：保持与 `upstream/main` 完全一致，只用来接收官方更新，不要在这里写任何代码。
创建develop分支进行开发
```
git checkout -b develop
```
当官方发布新功能或补丁时，保持同步：

## 具体开发路径的最佳实践

### A. 功能扩展：使用 Plugins
Smartstore 的一切几乎都是插件。如果你想增加一个新的支付方式、一种新的促销逻辑：
- 在 `src/SmartStore.Modules` 目录下建立你自己的模块。    
- **Dependency Injection (DI)**：利用其内置的依赖注入，拦截或扩展现有服务。    
- **Override Services**：如果必须修改现有逻辑，检查是否可以通过继承并重新注册服务来实现，而不是修改原类。
常见目标包括：
- 新的支付方式    
- 新的物流规则    
- 定制商品模型    
- 特殊价格 / 会员 / 积分逻辑    
- 深度后台 UI 定制    
- 与 ERP / CRM / AI 系统集成
### B. 界面修改：使用 Themes
不要直接修改 `SmartStore.Web` 里的 Views。
- 创建一个新的 **Theme**。    
- Smartstore 的模板引擎支持 **View Overriding**。你只需要在你的主题文件夹下创建同名的 `.cshtml` 文件，系统会自动优先调用你的版本，而忽略官方默认版本。 
### C. 数据库扩展
- 避免手动去 SQL Server 改表。    
- 使用 Smartstore 内置的 **Entity Framework Code-First Migrations**。在你的插件里定义新的实体和迁移脚本，这样你的数据库结构扩展也可以通过代码进行版本控制。
### 4. 避坑指南（新移民/初学者必看）

- **NuGet 管理**：尽量不要手动下载 DLL。确保使用 NuGet 管理所有包。
    
- **配置文件**：不要在 `appsettings.json` 里写死私密信息，使用环境变量或 `user-secrets`，防止 Fork 到公共仓库后泄露密码。
    
- **多语言（Localization）**：Smartstore 有完善的资源系统。不要在代码里硬编码中文或英文，务必使用 `T("Your.Resource.Key")`，然后在后台导入语言包。
# Develop
## 首次运行（Setup Wizard）

1. 安装.net sdk v10：
2. **打开解决方案**：双击 `Smartstore.sln`。    
![](images/posts/20260117-2.jpeg)
3. **设置启动项**：将 **`SmartStore.Web`** 项目设为“启动项目”。    
4. **运行 (F5)**：    
way1
```
dotnet run --urls "http://localhost:5000;https://localhost:5001"
```
way2: properties > launchsettings.json:
```json
"Smartstore.Web": {
  "commandName": "Project",
  "launchBrowser": true,
  "launchUrl": "http://localhost:5000",
  "applicationUrl": "http://localhost:5000;https://localhost:5001",
  "environmentVariables": {
    "ASPNETCORE_ENVIRONMENT": "Development"
  }
}
```
![](images/posts/20260118.jpeg)
visualstudio >  
发布
way1 visual studio:
```
release 模式 > rebuild 整个solution > Modules文件夹编译到Smartstore.Web下
publish Smartstore.Web
```
way 2 dotnet:
```
cd c:\Workspace\Smartstore\src\Smartstore.Web\
dotnet publish Smartstore.Web.csproj -c Release -o C:\Workspace\Smartstore_publish
```
way3 build:
```
c:\Workspace\Smartstore\build>build.win-x64.cmd
or
.\build.ps1 Deploy
```
找到生成的 artifacts 目录。
将artifacts 里的内容创建iis网站。
应用 icacls 权限命令。

 第一次运行会进入 **Installation Wizard**（安装向导）。
    ![](images/posts/20260118-1.jpeg)
    - 输入你的数据库连接信息。        
    - 设置管理员账号密码。        
4. **检查结果**：安装完成后，浏览器能看到前台商城和 `/admin` 后台，说明环境搭建成功。
![](images/posts/20260118-2.jpeg)
## 二次开发路线
### 阶段 1：熟悉扩展点
- Plugin    
- Event    
- Service override    
### 阶段 2：做“业务插件”
- 支付    
- 税    
- 物流    
- AI / ERP 接口   
### 阶段 3：平台级增强
- 自定义后台模块    
- 规则引擎    
- 自动化
### 核心开发模式——创建插件 (Modules)
**这是二次开发最关键的一步。** 假设你要增加一个“自定义运费计算”功能。
1. **定位目录**：找到 `/src/SmartStore.Modules` 文件夹。    
2. **使用模板/复制**：    
    - 推荐做法：复制一个简单的官方插件（如 `SmartStore.PayPal`）的文件夹，重命名为 `SmartStore.MyCustomModule`。        
3. **修改标识**：    
    - 修改项目文件中的 `Module.json`。这个文件决定了插件的名称、版本和作者。        
    - 修改命名空间（Namespace）。        
4. **编写逻辑**：    
    - **Dependency Injection**：在插件的 `Starter.cs` 中注册你的服务。        
    - **Routing**：定义你的 Controller 和 Action。
### 第五步：前端定制——创建主题 (Themes)
如果你需要改 UI，不要动官方主题：
1. **目录**：`/src/SmartStore.Web/Themes`。    
2. **继承主题**：Smartstore 支持主题继承。你可以创建一个新文件夹 `MyTheme`，在 `theme.json` 中设置 `BaseTheme: "Flex"`。    
3. **覆盖 View**：如果你想改商品详情页，只需把原主题的 `Product.cshtml` 复制到你主题文件夹的对应路径下进行修改。系统会**自动覆盖**官方样式。
### 第六步：数据库迁移 (Migrations)
如果你在插件里增加了新的数据表：
1. **定义 Entity**：创建继承自 `BaseEntity` 的类。    
2. **配置 Mapping**：使用 Fluent API 定义表结构。    
3. **运行迁移**：Smartstore 在插件激活时会自动检测并运行 `Migration`。你不需要手动在数据库里敲 SQL。
### 第七步：编译与预览
1. **编译模块**：右键点击你的模块项目 -> **Build**。编译后的 DLL 会自动输出到 `SmartStore.Web/Modules` 目录下。    
2. **后台激活**：登录商城后台 -> **CMS** -> **Modules** -> 找到你的插件，点击 **Install**。    
### 💡 给二次开发者的“金律良言”：
- **观察原有的代码**：Smartstore 的代码非常整洁。写代码前，先看官方插件是怎么写的（比如 `SmartStore.Google.Analytics`），模仿其结构。
    
- **日志打印**：利用内置的 `Logger`。如果程序报错，第一时间去后台 **System** -> **Log** 查看错误堆栈。
    
- **保持同步**：每隔几天，执行一次同步操作：
## 开发插件
如果你想在用户注册时额外触发一些复杂的业务逻辑（如给用户发放一张优惠券），你可以通过以下方式实现：
- **插件系统**：创建一个插件，并订阅 `CustomerRegisteredEvent` 事件。    
- **代码位置**：`src/Smartstore.Core/Identity/Events/CustomerRegisteredEvent.cs`
# 配置网站

| 动作              | 位置                                                                                 |
| --------------- | ---------------------------------------------------------------------------------- |
| 安装 Smartstore   | 搭建基础环境并连接数据库。                                                                      |
| 设置store 名称，logo | configuration > stores                                                             |
| 配置货币            | Configuration > regional > currencies                                              |
| 语言              | Configuration > regional > language<br>Configuration > settings > localization     |
| 创建目录            | 设置 `Categories` (分类) 和 `Products` (产品)。                                            |
| 配置支付方式          | Configuration > payment method<br>Plugins                                          |
| 物流              | Configuration → Shipping → Shipping Methods                                        |
| 安装插件            | https://community.smartstore.com/index.php?/files/<br><br>                         |
| 配置用户注册方式        | Configuration > settings > Customer                                                |
| 配置邮件            | Configuration > Email-account<br>CMS > Message Templates<br>System > Message Queue |
| 配置社交媒体登录        | Plugins > manage plugins<br>customer > External authentication methods             |
| 使用 Page Builder | 装修你的首页和自定义营销页面。                                                                    |
# 升级
## 核心程序升级（Web / Data / Services）
Smartstore.Web
Smartstore.Core
Smartstore.Data
Smartstore.Services
Smartstore.Infrastructure

v6.3升级到v7
git remote -v
确认有upstream, 如果没有就加上

切换到develop 或feature分支，从upstream pull
git checkout develop
git fetch upstream

查看官方版本，确认有v7
git tag

开始merge v7
git merge 7.0.0

最后可以推送到自己的fork
git push origin develop

给自己的fork打tag
git tag my-7.0-custom
git push origin my-7.0-custom
## 数据库升级（Migration）
这是最标准的操作流程，你可以生成一个包含所有变更的 .sql 文件，并在生产数据库上执行。
```
dotnet ef migrations script <上次迁移名> <本次迁移名> -o update.sql
```
## 插件升级（ Modules）
SmartStore 具有高度的模块化特性。如果你修改的是某个插件中的实体：
- **修改描述文件：** 必须更新插件根目录下的 `plugin.json` 文件中的 **`Version`** 字段。如果版本号不更新，系统可能不会触发升级逻辑。    
- **部署与重启：** 将插件文件夹上传至 `/Plugins` 目录，然后重启 Web 应用。    
- **后台触发：** 登录管理员后台：    
    1. 进入 Plugins 列表。        
    2. 系统会识别出版本差异。点击对应的 **"Update"** 或 **"Install"** 按钮。        
    3. 此操作会执行插件内部的 `Migration` 逻辑。
## 静态资源与主题升级 (Static Assets & Themes)
SmartStore 采用灵活的主题系统，升级时需注意 CSS/JS 的缓存问题。

- **主题覆盖：** 检查 `/Themes/<ThemeName>` 文件夹。如果有自定义修改，需手动合并 `.cshtml` 文件的差异，防止新功能失效。
    
- **清除缓存：** 升级完成后，务必清除浏览器缓存或使用 SmartStore 后台的 **"Clear Cache"** 工具。
    
- **重新编译 Bundle：** 如果修改了 `.less` 或 `.sass` 文件，确保在构建阶段已正确编译成 `.css`。
## 任务与索引维护 (Background Tasks & Search)
升级后，底层数据结构的变化可能影响搜索索引。
- **重建索引：** 进入后台 **Search > Indexing**，手动执行 **"Rebuild All Indices"**，确保 Lucene 或 Elasticsearch 中的数据与升级后的数据库一致。
    
- **检查计划任务：** 检查 **System > Scheduled Tasks**，确保所有任务状态正常，没有因为 API 变更而导致的任务挂起。
# FAQ
## 数据库
App_Data\Tenants\Default\Settings.txt
```
AppVersion: 6.3.0.0
DataProvider: SQLite
DataConnectionString: Data Source=App_Data/Tenants/Default/test-smartstore.db;Cache=Shared;Pooling=True
```
或
```
AppVersion: 6.3.0.0
DataProvider: SqlServer
DataConnectionString: Data Source=(local);Initial Catalog=smartstore_empty;Integrated Security=False;User ID=sa;Password=password;Enlist=False;Pooling=True;Min Pool Size=1;Max Pool Size=1024;Multiple Active Result Sets=True;Encrypt=False;User Instance=False;MultipleActiveResultSets=True
```
## IIS网站无法启动，报错
![](images/posts/20260118-3.jpeg)
先在window资源管理器中，手工创建项目，再发布。
设置IIS_USER有读写权限（optional）
安装Hosting Bundle v10
创建空目录 Modules, Plugins,
设置数据库配置，信任本地ssl证书
```
AppVersion: 6.3.0.0
DataProvider: SqlServer
DataConnectionString: Data Source=.;...Integrated Security=False;User ID=sa;Password=password;...Encrypt=True;TrustServerCertificate=True
```
## 基于release版本进行二次开发
查看release tag
```
c:\Workspace\Smartstore>git tag
5.0.0
5.0.0beta
5.0.1
5.0.2
5.0.3
5.0.4
5.0.5
5.1.0
6.0.0
6.1.0
6.2.0
6.3.0
```
切换到具体版本
```
git checkout 6.3.0

Note: switching to '6.3.0'.
You are in 'detached HEAD' state. 
```
在这个版本基础上，创建一个新分支，并继续开发：
```
git checkout -b feature-test
```
## 备份附件资源
smartstore 第一次初始化时，选的Media storage是file system。
那么上传的产品图片，网站logo图片等等各种media，分别都在哪里？如何备份？

所有通过后台上传的商品图、分类图、品牌 Logo 等，都存放在：
```
/wwwroot/App_Data/Tenants/Default/Media/Storage

# 目录结构
QueuedEmailAttachment
Storage (备份)
Thumbs
```
`_Thumbs`是缓存目录，可以通过清理cache清空。不需要备份。如果这个文件夹丢失，系统会根据原图自动重新生成。不备份它能节省磁盘空间和备份时间。
System > Maintenance > Clear image cache


