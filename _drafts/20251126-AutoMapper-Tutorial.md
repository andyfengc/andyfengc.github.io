# Introduction
AutoMapper 是 .NET 分层架构中的一个**生产力工具**，用于处理 Entity、DTO 和 ViewModel 之间的数据转换。
AutoMapper 的主要目标是**解决对象之间相互赋值的重复性工作**。比如在分层架构中，我们经常需要在以下对象类型之间转换数据：
- **Entity** (数据库实体) $\leftrightarrow$ **DTO** (数据传输对象)    
- **Entity** $\leftrightarrow$ **ViewModel** (视图模型)    
- **DTO** $\leftrightarrow$ **ViewModel**
通常需要手动编写大量的代码，例如
```csharp
// 手动映射
var viewModel = new UserViewModel
{
    Id = userEntity.Id,
    FullName = userEntity.FirstName + " " + userEntity.LastName,
    EmailAddress = userEntity.Email // 每次字段有变化，都需要修改这段代码
};
```
AutoMapper 就是来消除这种样板代码（boilerplate code）。AutoMapper 是 .NET 中最常用的对象映射库，尤其在分层架构中非常有用，用于：
- **VM ↔ DTO ↔ Entity 转换**    
- **简化属性拷贝**    
- **减少重复代码**    
- **提高分层架构解耦性**
例如：
```csharp
var vm = _mapper.Map<ProductViewModel>(productEntity);
```
AutoMapper 会帮你自动把同名属性映射过去，无需手写：
```csharp
vm.Name = productEntity.Name;
vm.Price = productEntity.Price;
```
AutoMapper 的优势在于

|**优势**|**描述**|
|---|---|
|**简化代码**|消除手动属性赋值的样板代码。|
|**提高可维护性**|集中管理映射配置，当模型变更时，只需要修改 Profile 文件。|
|**职责分离**|帮助强制执行表示层和业务逻辑层之间的数据模型隔离。|
|**性能高效**|尽管使用反射，但它在运行时会编译表达式树（Expression Trees）来生成映射代码，性能接近于手动赋值。|
## How to work?
1. 约定优于配置: AutoMapper通过**约定优于配置（Convention Over Configuration）**的原则来工作。只要属性名字和类型一致，就能自动映射。
下面2个对象无需任何额外代码，AutoMapper 能自动映射。
```csharp
public class Product {
    public string Name { get; set; }
    public decimal Price { get; set; }
}
public class ProductDto {
    public string Name { get; set; }
    public decimal Price { get; set; }
}
```
2. 手工配置：**配置阶段 (Configuration):** 在应用程序启动时，您只需告诉 AutoMapper **“请为类型 A 映射到类型 B”**。    
**定义映射关系:** 
```csharp
CreateMap<Source, Destination>()
```
**执行阶段 (Execution):** 当您需要进行映射时，AutoMapper 会使用其内部配置，通过反射（Reflection）高效地将源对象的属性值复制到目标对象中。
```csharp
mapper.Map<Destination>(sourceObject)
```
# Install
通常需要安装主包和用于依赖注入的扩展包：
```bash
dotnet add package AutoMapper
```
注意13.x开始，AutoMapper.Extensions.Microsoft.DependencyInjection已经并入主包
![](images/posts/20251128.jpeg)
# How to use

## 定义 Profile (配置映射)
Profile 用来定义映射规则。
在项目中创建一个继承自 `Profile` 的类来组织映射规则。这通常放在应用服务层或共享项目中。
```csharp
public class MappingProfile : Profile
{
    public MappingProfile()
    {        
        // 约定映射：Id 映射到 Id，Email 映射到 Email
        CreateMap<UserEntity, UserViewModel>();
                
        // 逆向映射：允许从 ViewModel 映射回 Entity
        CreateMap<UserViewModel, UserEntity>();
        
        // 复杂的或非约定的映射，需要手动配置 (ForMember)
        CreateMap<UserEntity, UserDetailViewModel>()
            .ForMember(
                // 目标属性：FullName
                dest => dest.FullName,
                // 如何计算：使用源对象的 FirstName 和 LastName
                opt => opt.MapFrom(src => $"{src.FirstName} {src.LastName}")
            );
    }
}
```
- 继承 `Profile`    
- 在构造函数中写 CreateMap()
### 自定义规则
自定义成员
```
CreateMap<Product, ProductDto>()
    .ForMember(dest => dest.PriceText,
               opt => opt.MapFrom(src => "$" + src.Price));
```
忽略字段
```
CreateMap<ProductDto, Product>()
    .ForMember(dest => dest.Id, opt => opt.Ignore());
```
多源映射
```
CreateMap<(Order order, User user), OrderViewModel>()
    .ForMember(dest => dest.OrderId, opt => opt.MapFrom(x => x.order.Id))
    .ForMember(dest => dest.UserName, opt => opt.MapFrom(x => x.user.Name));
```
Projection（EF Core 中使用 AutoMapper 的最佳方式，但是不符合分层架构规范，因为dto不应该传给entity framework）
AutoMapper 的 `ProjectTo` 能把映射转换成 SQL 查询，在数据库层处理，而不是在内存中。
```
var list = _context.Products
    .ProjectTo<ProductDto>(_mapper.ConfigurationProvider)
    .ToList();
```
- 优点是可以只查询 DTO 需要的字段（不是整个表）    
- 性能更好    
- 减少无用列传输

## 注册profile
注册方式取决于你是否使用 **依赖注入（DI）**。
### 在DI中注册（`Program.cs` 或 `Startup.cs`）
注册单个Profile
```csharp
services.AddAutoMapper(typeof(UserProfile));
```
自动扫描当前程序集中的所有 Profile 
```csharp
builder.Services.AddAutoMapper(typeof(Program).Assembly);
```
自动扫描指定类所在的程序集 注册所有映射 Profile：
```csharp
services.AddAutoMapper(cfg => {}, typeof(MappingProfile).Assembly);
// AutoMapper 会扫描给定程序集，找到所有继承自 Profile 的类并加载它们的配置
```
这会自动扫描：
- 所有继承 `Profile` 的类    
- 并注册到 AutoMapper
#### 在 Controller 或 Service 中使用方法
通过依赖注入获取 `IMapper` 接口，然后执行映射。
例如UI层
```csharp
public class UserController : ControllerBase
{
    private readonly IUserService _userService;
    private readonly IMapper _mapper;

    public UserController(IUserService userService, IMapper mapper)
    {
        _userService = userService;
        _mapper = mapper;
    }

    [HttpGet("{id}")]
    public ActionResult<UserViewModel> GetUser(int id)
    {
        // 1. 调用服务层，返回 Entity
        var userEntity = _userService.GetUserById(id);

        if (userEntity == null)
        {
            return NotFound();
        }

        // 2. 使用 AutoMapper 映射到 ViewModel (仅在表示层进行)
        var userViewModel = _mapper.Map<UserViewModel>(userEntity); 

        // 3. 返回 ViewModel 给前端
        return Ok(userViewModel);
    }
}
```
例如service层：
```csharp
private readonly IMapper _mapper;

public ProductsService(IMapper mapper)
{
    _mapper = mapper;
}

public ProductDto Get(int id)
{
    var product = _repository.Get(id);
    return _mapper.Map<ProductDto>(product);
}
```
### 不使用 DI（WPF / 控制台）
手动创建 MapperConfiguration
```csharp
var config = new MapperConfiguration(cfg =>
{
	cfg.LicenseKey = "";
    cfg.AddProfile<UserProfile>();
    cfg.AddProfile<OrderProfile>();
    // 或者 cfg.AddMaps(typeof(Program).Assembly); // 批量扫描
});
```

使用方法
```csharp
// 创建 IMapper
IMapper mapper = config.CreateMapper();
var dto = mapper.Map<UserDto>(user);
```
## 使用映射
一对一映射
```
var dto = _mapper.Map<ProductDto>(entity);
```
更新现有对象（常用于 PATCH/PUT）
```
_mapper.Map(updateDto, entity);
```
集合映射
```
var dtos = _mapper.Map<List<ProductDto>>(entities);
```
## Profile中注入DI实例
❌ 不建议在 AutoMapper.Profile 里直接注入其他实例（尤其是业务服务）
`Profile` 的设计目标是：
- 只描述映射规则    
- 应该是 **无状态、轻量、可复用的**    
- 不应该依赖业务服务

而且在实际使用中：
- `Profile` **不是每次映射都由 DI 创建**    
- 容易出现：    
    - 生命周期不一致        
    - 单元测试困难        
    - 隐性依赖（维护成本高）

✅ 可以通过 Resolver / Converter / AfterMap 间接使用 DI
✅ 推荐把需要注入的依赖放到 Resolver / ValueConverter 中，由 DI 管理

| 方式                       | 是否推荐 | 说明       |
| ------------------------ | ---- | -------- |
| Profile 构造函数注入           | ❌    | 设计不合理    |
| `IValueResolver`         | ✅✅   | 推荐单个属性转换 |
| `ITypeConverter`         | ✅    | 适合类整体转换  |
| `AfterMap + ServiceCtor` | ⚠️   | 临时方案     |
### 使用 **ValueResolver（最常用）**
常用于：    
- 单个属性需要注入服务        
- 需要查库 / 调接口        
- 计算字段
定义 Resolver（可以注入任何服务）
```csharp
public class UserNameResolver : IValueResolver<User, UserDto, string>
{
    private readonly IUserService _userService;

    public UserNameResolver(IUserService userService)
    {
        _userService = userService;
    }

    public string Resolve(User source, UserDto destination, string destMember, ResolutionContext context)
    {
        return _userService.GetName(source.Id);
    }
}
```
di注册
```csharp
services.AddTransient(typeof(UserNameResolver));
```
在 Profile 中使用 Resolver（Profile 本身不注入）
```csharp
public class UserProfile : Profile
{
    public UserProfile()
    {
        CreateMap<User, UserDto>()
            .ForMember(d => d.Name, opt => opt.MapFrom<UserNameResolver>());
    }
}
```
- Resolver 由 DI 管理    
- Profile 是纯配置
- 你只负责这个属性，其他属性AutoMapper 自动处理。
### `ITypeConverter`（适合复杂对象整体转换）
常用于：
- 映射规则非常复杂  
- source 和 destination 结构差异很大
- 多个字段强相关    
- 想完全接管映射过程
定义类型转换器
```csharp
public class UserConverter : ITypeConverter<User, UserDto>
{
    private readonly IUserService _userService;

    public UserConverter(IUserService userService)
    {
        _userService = userService;
    }

    public UserDto Convert(User source, UserDto destination, ResolutionContext context)
    {
        return new UserDto
        {
            Id = source.Id,
            Name = _userService.GetName(source.Id)
        };
    }
}
```
di注册
```csharp
services.AddTransient(typeof(UserConverter));
```
使用类型转换器
```csharp
CreateMap<User, UserDto>().ConvertUsing<UserConverter>();
```
🔎 **AutoMapper 不再为这个类自动映射任何字段**，一切你说了算。
### AfterMap + context.Options.ServiceCtor
适合不想单独写 Resolver的情况：
```csharp
CreateMap<User, UserDto>()
    .AfterMap((src, dest, ctx) =>
    {
        var service = ctx.Options.ServiceCtor(typeof(IUserService)) as IUserService;
        dest.Name = service.GetName(src.Id);
    });
```
⚠️ 可用，但：
- 可读性一般    
- 不如 Resolver 清晰
# Demo
Entity
```
public class Product {
    public int Id { get; set; }
    public string Name { get; set; }
}
```
DTO
```
public class ProductDto {
    public string Name { get; set; }
}
```
ViewModel
```
public class ProductViewModel {
    public string Name { get; set; }
    public string DisplayName { get; set; }
}
```
Profile
```
public class ProductProfile : Profile
{
    public ProductProfile()
    {
        CreateMap<Product, ProductDto>();
        CreateMap<ProductDto, Product>();

        CreateMap<ProductDto, ProductViewModel>()
            .ForMember(vm => vm.DisplayName, opt => opt.MapFrom(dto => "【" + dto.Name + "】"));
    }
}
```
## 高级功能
Flattening（嵌套对象扁平化）自动完成，无需配置。
```
Order.Customer.Name → OrderDto.CustomerName
```
逆向映射 (Reverse Mapping)，使用 `.ReverseMap()` 可以让映射配置双向工作，减少重复配置。
```
CreateMap<Product, ProductDto>().ReverseMap();
```
双向映射一次写完。AfterMap / BeforeMap
```
CreateMap<Order, OrderDto>()
    .AfterMap((src, dest) => dest.ProcessTime = DateTime.Now);
```
条件映射 (Conditional Mapping)可以根据某些条件来决定是否映射一个属性。
```
CreateMap<Source, Destination>()
    .ForMember(
        dest => dest.PropertyB,
        opt => opt.Condition(src => src.PropertyA != null) // 只有当 PropertyA 不为空时才进行映射
    );
    
.ForMember(d => d.Code, opt => opt.Condition(src => src.Code != null));
```
值解析器 Value Resolvers / Type Converters 用于复杂业务转换。
对于那些需要复杂逻辑计算才能得到值的目标属性，可以创建自定义的 `IValueResolver<TSource, TDestination, TMember>` 实现类来处理。

验证映射是否正确（强烈推荐）
```csharp
config.AssertConfigurationIsValid();
```
# FAQ
## Best practice
**3 步就够：**
1️⃣ `class XxxProfile : Profile`  
2️⃣ 在构造函数里 `CreateMap<,>`  
3️⃣ 启动时 `AddAutoMapper(...)` 或 `AddProfile(...)`
## 常见错误 & 注意事项
❌ 忘了注册 Profile
```
Missing type map configuration or unsupported mapping.
```
❌ Profile 构造函数带参数
```csharp
public UserProfile(IMyService service) ❌
```
- Profile **必须是无参构造**（或只用配置）
❌ 在运行时修改配置
```csharp
cfg.CreateMap<...>(); // 运行中
```
- AutoMapper 配置应在 **启动时一次性完成**

|问题|解决办法|
|---|---|
|**报 Missing type map configuration**|Profile 没注册 或 属性名不同|
|**循环引用导致 StackOverflow**|使用 `.PreserveReferences()`|
|**EF Core 实体不能直接 MapTo DTO**|用 `ProjectTo()` 代替|
|**性能问题**|避免映射大量大对象，预编译配置|
# Reference
[AutoMapper docs](https://docs.automapper.io/)
