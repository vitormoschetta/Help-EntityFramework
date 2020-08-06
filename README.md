## Ajuda Entity Framework Core

#### Adicionar EF Core a um projeto existente

###### Add Pacotes via CLI:

```
dotnet add package Microsoft.EntityFrameworkCore.Tools --version 3.1.*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.1.*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.1.*
dotnet add package Pomelo.EntityFrameworkCore.MySql --version 3.1.*
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL --version 3.1.*
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 3.1.*

```

#### Configurar Arquivo 'appsettings.json'

###### Sql Server LocalDb:

```
{
    "ConnectionStrings": {
      "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=SisaWeb2;Trusted_Connection=True;MultipleActiveResultSets=true"
    },
    "Logging": {
      "LogLevel": {
        "Default": "Information",
        "Microsoft": "Warning",
        "Microsoft.Hosting.Lifetime": "Information"
      }
    },
    "AllowedHosts": "*"
}

```

###### Sql Server / MySql / MariaDB :

```
{
    "ConnectionStrings": {
      "DefaultConnection": "Server=	nome/ip,porta;Database=nomeBanco;user=sa; password=***"
    },
    "Logging": {
      "LogLevel": {
        "Default": "Information",
        "Microsoft": "Warning",
        "Microsoft.Hosting.Lifetime": "Information"
      }
    },
    "AllowedHosts": "*"
}

```


#### Criar / Configurar Contexto:

```
public class Contexto: DbContext
{
     public Contexto(DbContextOptions<Contexto> options) : base(options)
    {
    }
    public DbSet<Produto> Produto { get; set; }
}
```


#### Configurar a injeção de dependência em Startup.cs:

```
public void ConfigureServices(IServiceCollection services)
{    
    // Sql Server
    services.AddDbContext<Contexto>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    //Conexao MySql
    services.AddDbContext<Contexto>(options =>
        options.UseMySql(Configuration["ConnectionStrings:DefaultConnection"]));
}

```


#### Configurar a injeção de dependência no Controller

```
public class LivrosController : Controller
{
    private readonly Contexto _context;

    public LivrosController(Contexto context
    {
        _context = context;    
    }
}

```



##  Migrations

#### Instalar tool do EF Core via CLI:

```
dotnet tool install --global dotnet-ef 

```

#### Gerar Migration :

```
dotnet ef migrations add <Nome_Migration_sua_preferencia>

```

#### Aplicar migration no banco de dados / Criar ou Atualizar (se já existir):

```
dotnet ef database update

```


#### Gerar Scripts Sql das Migrations:

```
dotnet ef migrations script

``` 

#### Alerta de versão do tool

###### Mensagem:

```
The EF Core tools version '3.1.1' is older than that of the runtime '3.1.4'. Update the tools for the latest features and bug fixes.
```

###### Solução:

```
dotnet tool update --global dotnet-ef --version ****
```



#### Database First 

##### Fazer o scaffold de um Banco de Dados existente

###### É o trabalhos inverso. Ou seja, gerar suas classes de domínio/modelo a partir de um banco de dados existente. 

Veja esse procedimento na minha página, [clicando aqui](https://developer-jr.webnode.com/l/database-first/)




