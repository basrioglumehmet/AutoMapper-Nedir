
# AutoMapper Kullanımı ile Web API

Bu projede, ASP.NET Core kullanarak basit bir Web API oluşturacağız ve AutoMapper'ı kullanarak veri transfer nesneleri (DTO'lar) ve domain modelleri arasında veri haritalaması yapacağız.

## AutoMapper Nedir?

AutoMapper, .NET uygulamalarında nesneler arasında haritalama yapmak için kullanılan bir kütüphanedir. Genellikle veri transfer nesneleri (DTO'lar) ile domain modelleri arasında veri taşıma işlemlerini kolaylaştırmak için kullanılır. AutoMapper, manuel olarak yapılan haritalama işlemlerini otomatikleştirir ve kodun daha temiz ve bakımı kolay olmasını sağlar.

## Proje Kurulumu

1. **Yeni Bir Proje Oluşturma**:
   ```bash
   dotnet new webapi -n AutoMapperExample
   cd AutoMapperExample
   ```

2. **Gerekli Paketleri Yükleme**:
   ```bash
   dotnet add package AutoMapper.Extensions.Microsoft.DependencyInjection
   ```

## Kod Yapısı

### Modeller

**Models/Person.cs**
```csharp
namespace AutoMapperExample.Models
{
    public class Person
    {
        public int Id { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public int Age { get; set; }
    }
}
```

**DTOs/PersonDto.cs**
```csharp
namespace AutoMapperExample.DTOs
{
    public class PersonDto
    {
        public string FullName { get; set; }
        public int Age { get; set; }
    }
}
```

### AutoMapper Profili

**Profiles/PersonProfile.cs**
```csharp
using AutoMapper;
using AutoMapperExample.Models;
using AutoMapperExample.DTOs;

namespace AutoMapperExample.Profiles
{
    public class PersonProfile : Profile
    {
        public PersonProfile()
        {
            CreateMap<Person, PersonDto>()
                .ForMember(dest => dest.FullName, opt => opt.MapFrom(src => $"{src.FirstName} {src.LastName}"));
        }
    }
}
```

### AutoMapper Konfigürasyonu

**Program.cs**
```csharp
var builder = WebApplication.CreateBuilder(args);

// AutoMapper'ı ekliyoruz
builder.Services.AddAutoMapper(typeof(PersonProfile));

// Add services to the container.
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

app.UseAuthorization();

app.MapControllers();

app.Run();
```

### Controller

**Controllers/PersonController.cs**
```csharp
using AutoMapper;
using AutoMapperExample.Models;
using AutoMapperExample.DTOs;
using Microsoft.AspNetCore.Mvc;

namespace AutoMapperExample.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class PersonController : ControllerBase
    {
        private readonly IMapper _mapper;

        public PersonController(IMapper mapper)
        {
            _mapper = mapper;
        }

        [HttpGet]
        public ActionResult<PersonDto> Get()
        {
            // Örnek bir Person nesnesi oluşturuyoruz
            var person = new Person
            {
                Id = 1,
                FirstName = "John",
                LastName = "Doe",
                Age = 30
            };

            // Person nesnesini PersonDto'ya haritalıyoruz
            var personDto = _mapper.Map<PersonDto>(person);

            return Ok(personDto);
        }
    }
}
```

## Projeyi Çalıştırma

1. Proje klasörüne gidin:
   ```bash
   cd AutoMapperExample
   ```

2. Projeyi çalıştırın:
   ```bash
   dotnet run
   ```

Web tarayıcınızda `https://localhost:5001/person` adresine gidin. JSON formatında aşağıdaki gibi bir çıktı almanız gerekir:
```json
{
    "fullName": "John Doe",
    "age": 30
}
```

Bu dosya, AutoMapper kullanarak veri transfer nesneleri ve domain modelleri arasında veri haritalama işleminin nasıl yapıldığını göstermektedir.
