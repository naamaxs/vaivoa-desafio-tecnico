# Desafio tecnico Vaivoa

ASP.NET Core é uma estrutura de software livre de plataforma cruzada, de alto desempenho para a criação de aplicativos modernos conectados à Internet e em nuvem. Com o ASP.NET Core, você consegue criar aplicativos Web e serviços usando suas ferramentas de desenvolvimento favoritas no Windows, macOS e Linux, consegue Implantar na nuvem ou local executando no .NET Core.
Milhões de desenvolvedores usam ou usaram o ASP.NET para criar aplicativos Web. ASP.NET Core é um redesign de ASP.NET 4, incluindo alterações arquitetônicas que resultam em uma estrutura mais Lean e mais modular.

para criar uma api adiciona os pacotes do NuGet que são exigidos, abra o editor de codigo eu utilizei o Visual studio code, primeiramente abri o terminal integrado, altere os diretórios para a pasta que conterá a pasta do projeto.
execute o comandos:

dotnet new webapi -o TodoApi
cd TodoApi
dotnet add package Microsoft.EntityFrameworkCore.InMemory
code -r ../TodoApi

Quando a caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione Sim.

REST significa Em português, Transferência de Estado Representacional. Trata-se de uma abstração da arquitetura da Web. Resumidamente, o REST consiste em princípios/regras/constraints que, quando seguidas, permitem a criação de um projeto com interfaces bem definidas. Desta forma, permitindo, por exemplo, que aplicações se comuniquem.

sendo assim o modelo de projeto cria uma WeatherForecastAPI com suporte para Swagger que é o (OpenAPI) é uma especificação independente de linguagem para descrever APIs REST.
Confie no certificado de desenvolvimento HTTPS executando o seguinte comando
dotnet dev-certs https --trust
tendo em vista que esse comando nao funciona para linux.
O comando abrirá uma caixa de dialogo mostrando o certificado
selecione SIM para concordar, em seguida aperta ctrol+f5 para executar o aplicativo em https://localhost:5001/swagger

A página Swagger /swagger/index.html é exibida. Selecione OBTER > Experimentar > Executar . A página exibe:

O comando Curl para testar a API WeatherForecast, o URL para testar a API WeatherForecast, o código de resposta, corpo e cabeçalhos.
Uma caixa de lista suspensa com tipos de mídia e o valor e esquema de exemplo.

Copie e cole o URL de solicitação no navegador: https://localhost:<port>/WeatherForecast

JSON semelhante ao seguinte é retornado:

JSON
```
[
    {
        "date": "2021-05-25T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2021-05-26T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2021-05-27T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2021-05-28T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": 2021-05-29T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

Atualize o launchUrl
Em Properties \ launchSettings.json , atualize launchUrlde "swagger"para "api/TodoItems":

JSON
"launchUrl": "api/TodoItems",

Como o Swagger foi removido, a marcação anterior altera a URL que é iniciada para o método GET do controlador.
Um modelo é um conjunto de classes que representam os dados que o aplicativo gerencia. O modelo para este aplicativo é uma única TodoItemclasse.

Adicione uma pasta chamada Modelos .

Adicione uma TodoItemclasse à pasta Modelos com o seguinte código:
C#
```
namespace TodoApi.Models
{
   public class CartaoDeCredito
    {
        public int Id { get; set; }
        public string email { get; set; }
        public int numero { get; set; }
    }
}
```
A "Id" propriedade funciona como a chave exclusiva em um banco de dados relacional, As classes de modelo podem ir a qualquer lugar no projeto, mas a pasta Modelos é usada por convenção.

O contexto do banco de dados é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados. Essa classe é criada derivando da classe Microsoft.EntityFrameworkCore.DbContext.
Adicione uma TodoContextclasse à pasta Modelos .
Insira o seguinte código:

C#
using Microsoft.EntityFrameworkCore;
```
namespace TodoApi.Models
{
    public class TodoContext : DbContext
    {
        public TodoContext(DbContextOptions<TodoContext> options)
            : base(options)
        {
        }

        public DbSet<CartaoDeCredito> CartaoDeCredito { get; set; }
    }
}
```
No ASP.NET Core, serviços como o contexto de banco de dados devem ser registrados com o contêiner de injeção de dependência (DI) . O contêiner fornece o serviço aos controladores.

Atualize Startup.cs com o seguinte código:

C#
// Unused usings removed
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.EntityFrameworkCore;
using TodoApi.Models;
```
namespace TodoApi
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContext<TodoContext>(opt =>
                                               opt.UseInMemoryDatabase("TodoList"));
            services.AddControllers();
        }

        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseHttpsRedirection();
            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllers();
            });
        }
    }
}
```
Com esse codigo voce Remove as chamadas Swagger, Remove "using" declarações não utilizadas, adiciona o contexto do banco de dados ao contêiner DI e 
especifica que o contexto do banco de dados usará um banco de dados na memória.

Scaffold um controlador
Execute os seguintes comandos na pasta do projeto TodoApi/TodoApi:
.NET CLI
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers

Os comandos utilizados foram utilizados para Adicione os pacotes NuGet necessários para o scaffolding, Instala o scaffolding engine ( dotnet-aspnet-codegenerator), Scaffolds o TodoItemsController.

O código gerado:

Marca a classe com o [ApiController] atributo este atributo indica que o controlador responde às solicitações da API da web, Usa DI para injetar o contexto do banco de dados ( TodoContext) no controlador. 
O contexto do banco de dados é usado em cada um dos métodos CRUD no controlador.
Os modelos ASP.NET Core para:

Controladores com visualizações incluem [action]no modelo de rota.
Os controladores de API não incluem [action]no modelo de rota.
Quando o [action]token não está no modelo de rota, o nome da ação é excluído da rota. Ou seja, o nome do método associado à ação não é usado na rota correspondente.

Atualize a instrução de retorno no PostTodoItempara usar o operador nameof :
```
// POST: api/TodoClientes
[HttpPost]
public async Task<ActionResult<TodoClientes>> PostTTodoClientes(TodoClientes TodoClientes)
{
    _context.TodoClientes.Add(TodoClientes);
    await _context.SaveChangesAsync();

    //return CreatedAtAction("GetTodoClientes", new { id = TodoClientes.Id }, TodoClientes);
    return CreatedAtAction(nameof(GetTodoClientes), new { id = TodoClientes.Id }, TodoClientes);
}
```
O código é um método HTTP POST, conforme indicado pelo [HttpPost] atributo o método obtém o valor do item de pendências do corpo da solicitação HTTP.

O método CreatedAtAction :

Retorna um código de status HTTP 201 se for bem-sucedido. HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.
Adiciona um cabeçalho Location à resposta. O Locationcabeçalho especifica o URI do item de pendências recém-criado.
Faz referência à GetTodoItemação para criar o LocationURI do cabeçalho. A "nameof" palavra-chave C # é usada para evitar a codificação permanente do nome da ação na CreatedAtActionchamada.

inicie o Postman, desative a verificação de certificado SSL
Em Arquivo > Configurações ( guia Geral ), desative a verificação do certificado SSL .
apos testar o controlador Reative a verificação do certificado SSL 
Dois endpoints GET são implementados:

GET /api/TodoClientes
GET /api/TodoClientes/{id}
Teste o aplicativo chamando os dois endpoints de um navegador. Por exemplo:

https://localhost:5001/api/TodoClientes	
https://localhost:5001/api/TodoClientes/1
Uma resposta semelhante à seguinte é produzida pela chamada para GetTodoClientes:
  ```
[
  {
    "id":1,
    "name": "Diego",
    "email":"diego@gmail.com",
    "CartaoDeCredito":"5390 6635 9897 2216"
  }
]
  ```
