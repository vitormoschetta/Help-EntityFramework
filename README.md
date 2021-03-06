# Entity Framework Core

## Pacotes via CLI:

```
dotnet add package Microsoft.EntityFrameworkCore.Tools --version 3.1.*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.1.*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.1.*
dotnet add package Pomelo.EntityFrameworkCore.MySql --version 3.1.*
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL --version 3.1.*
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 3.1.*

```

## Configurar Arquivo 'appsettings.json'

#### Sql Server LocalDb:

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

#### Sql Server / MySql / MariaDB :

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


## Criar / Configurar Contexto:

#### Deixar Mapeamento por conta do EF:
```
public class Contexto: DbContext
{
     public Contexto(DbContextOptions<Contexto> options) : base(options)
    { }
    
    public DbSet<Produto> Produto { get; set; }
}
```

#### Configurar Mapeamento:
```
public class Contexto: DbContext
{
    public Contexto(DbContextOptions<Contexto> options) : base(options)
    { }
    
    public DbSet<Equipe> Equipe { get; set; }       
    
    protected override void OnModelCreating(ModelBuilder builder)
    {
        builder.ApplyConfiguration(new EquipeMap());                      
    }    
}

public class EquipeMap : IEntityTypeConfiguration<Equipe>
    {
        public void Configure(EntityTypeBuilder<Equipe> builder)
        {
            builder.ToTable("Equipe");                              

            builder.Property(x => x.Nome)
                .IsRequired()
                .HasMaxLength(100)
                .HasColumnType("varchar(100)");

            builder.Property(x => x.Ano)
                .IsRequired()
                .HasMaxLength(4)
                .HasColumnType("char(4)");
            
            builder.Property(x => x.EscolaInep)
                .IsRequired()                
                .HasMaxLength(8)
                .HasColumnType("char(8)");        

            builder.HasOne(x => x.Modalidade);  // <-- tem uma Modalidade

            builder.HasMany(x => x.EstudantesEquipe);  // <-- tem muitos EstudantesEquipe         

            builder.HasIndex(x => x.EscolaInep); // <-- cria um indice para melhorar pesquisa de Equipes pelo INEP da Escola

            builder.HasKey(x => x.Id);  // <-- define Primary Key
        }
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


#### Controller - Construtor

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
dotnet ef migrations add <Nome_Migration_sua_preferencia> -o Data/Migrations

```
Obs: Você pode editar as migrations antes de executar o próximo passo.
Verifique os arquivos: 
  '<nome_migration>.Designer.cs
  'AppDbContextModelSnapshot.cs'
  
Eles são identicos, exceto no fato de que o 'AppDbContextModelSnapshot' cumula todas as Migrations adicionadas. 
Eles terão um codigo parecido com o exemplo abaixo:

```
protected override void BuildModel(ModelBuilder modelBuilder)
{
    modelBuilder
        .HasAnnotation("ProductVersion", "3.1.4")
        .HasAnnotation("Relational:MaxIdentifierLength", 128)
        .HasAnnotation("SqlServer:ValueGenerationStrategy", SqlServerValueGenerationStrategy.IdentityColumn);

    modelBuilder.Entity("DefaultDDDProject.Domain.Entities.Product", b =>
        {
            b.Property<Guid>("Id")
                .ValueGeneratedOnAdd()
                .HasColumnType("uniqueidentifier");

            b.Property<DateTime?>("Expiration")
                .HasColumnType("datetime");

            b.Property<string>("Nome")
                .HasColumnType("nvarchar(100)")
                .IsRequired();

            b.Property<decimal?>("Price")
                .HasColumnType("decimal(10,2)");

            b.HasKey("Id");

            b.ToTable("Product");
        });
}
```
Esses arquivos contém justamente as configurações das propriedades que irão constituir a Tabela do Banco de Dados. Você pode configurar o nome, tipo, tamanho, se é anulável ou não, etc.. 

Obs: Se fizer alterações, guarde bem essas migrations. 

#### Renomear coluna de uma Tabela:

###### O Entity te dará isso:
```
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);

```
###### Corrija para isso:
```
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

[Consulte a documentação oficial](https://docs.microsoft.com/pt-br/ef/core/managing-schemas/migrations/managing?tabs=dotnet-core-cli) para saber mais sobre mudança de nomes em bd existentes.

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



## Database First 


##### Fazer o scaffold de um Banco de Dados existente


###### É o trabalhos inverso. Ou seja, gerar suas classes de domínio/modelo a partir de um banco de dados existente. 

Veja esse procedimento na minha página, [clicando aqui](https://developer-jr.webnode.com/l/database-first/)




