# InspectionAPIapp
This is a youtube tutorial to create an api using ASP.NET CORE 6, Entity Framework 6, and SQL Server DONE SUCCESSFULLY
URL of the Tutorial => [tutorial-link](https://www.youtube.com/watch?v=rzPFEuKlPhM&ab_channel=JamesSchneider) .

## Packages Installed: 
* Microsoft.AspNet.WebApi.Cors
* Microsoft.EntityFrameworkCore
* Microsoft.EntityFrameworkCore.Design
* Microsoft.EntityFrameworkCore.SqlServer
* Microsoft.EntityFrameworkCore.Tools
* Microsoft.VisualStudio.Web.CodeGeneration.Design
* Swashbuckle.AspNetCore

## STEPS:
### Create ASP.NET CORE WEB API Project:
Create an ASP.NET Core Web API Projet, using Donet 6, EF 6.
### Install Packages
install all the packages mentionned above.
### Create Models
Create C# classes, you can add them in models folders or in the root, in this project, we have 3 models on the root folder:
* Inspection
* InspectionType
* Status

### Write the models code
*Status.cs*
```csharp
using System.ComponentModel.DataAnnotations;

namespace InspectionAPI
{
    public class Status
    {
        public int Id { get; set; }

        [StringLength(20)]
        public string StatusOption { get; set; } = String.Empty;

    }
}
```
*InspectionType.cs*
```csharp
using System.ComponentModel.DataAnnotations;

namespace InspectionAPI
{
    public class InspectionType
    {
        public int Id { get; set; }

        [StringLength(20)]
        public string InspectName { get; set; } = string.Empty;
    }
}

```
*Inspection*
```csharp
using System.ComponentModel.DataAnnotations;

namespace InspectionAPI
{
    public class Inspection
    {
        public int Id { get; set; }

        [StringLength(20)]
        public string Status { get; set; } = String.Empty;

        [StringLength(20)]
        public string Comments { get; set; } = String.Empty;
        
        public int InspectionTypeId { get; set; }

        public InspectionType? InspectionType { get; set; }
    }
}
```
NOTE:
Entity Framework will detect that `InspectionTypeId`is a foreign key from the `InspectionType`table.
### Create the DataContext
Create a folder on the root called `data` and create inside it a class called: `DataContext`
Add this code to *DataContext* file:
```csharp
using Microsoft.EntityFrameworkCore;

namespace InspectionAPI.Data
{
    public class DataContext: DbContext
    {
        public DataContext(DbContextOptions<DataContext> options) : base(options) { }

        public DbSet<Inspection> Inspections { get; set; }

        public DbSet<InspectionType> InspectionTypes { get; set; }  

        public DbSet<Status> Statuses { get; set; }
    }
}

```
### Add your connection String:
**NOTE: Add the Code blocks where its wrapped between comments `add code from here` to the comment `to here`**  
*appsettings.json*
```csharp
{
// add this code from here
  "ConnectionStrings": {
    "DefaultConnection": "server=SAFWEN-ASUS-11\\SQLEXPRESS;database=inspectionapidb;trusted_connection=true"
  },
 // to here 
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```
### Update program.cs
Now we need to update program.cs and configure Cors and Connection String:
**NOTE: Add the Code blocks where its wrapped between comments `add code from here` to the comment `to here`**  
*program.cs*
```csharp
using InspectionAPI.Data;
using Microsoft.EntityFrameworkCore;

// 1- add this from here
var myAllowSpecificOrigins = "_myAllowSpecificOrigins";
// 1- to here
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.

builder.Services.AddControllers();
// Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();
// 2- add this code from here
builder.Services.AddDbContext<DataContext>(options =>
{
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"));
});

// Enable Cors
builder.Services.AddCors(options =>
{
    options.AddPolicy(name: myAllowSpecificOrigins, builder =>
    {
        builder.WithOrigins("http://localhost:4200")
        .AllowAnyMethod()
        .AllowAnyHeader();
    });
});
// 2- to here

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
// 3- add this code from here
app.UseCors(myAllowSpecificOrigins);
// to here
app.UseAuthorization();

app.MapControllers();

app.Run();
```
### Add Migrations:
Now our DataContext, Models and the configuration to connect to database is done, we can now create the first migrations:
open `package manager console` and run:  


**IMPORTANT**
dotnet-ef is no longer a part of .NET SDK so you have to install it globally:
```bash
dotnet tool install --global dotnet-ef
```
Now let's add migrations:
```bash
dotnet ef migrations add InitialCreate
```
This command will create a folder on the root called *migrations* but it will not create or make any changes to the database, so to do it, run this command:
```bash
dotnet ef database update
```
Now you can check and see our database and tables are created.
### Add Controllers
Now its time to add the controllers, right click on controllers folders, select *add* then select *controller*, then select *API* on the left and select *API Controllers with actions, using Entity Framework*. Inside the dialog:
* For `Model class`: select *Inspection(InspectionAPI)*
* For `Data context class`: select *DataContext(InspectionAPI.data)*
* For `Controller name`: keep the default name generated *InspectionsController*  


**IMPORTANT:** 
**DO THE SAME FOR ALL THE MODELS: STATUS, INSPECTIONS, AND INSPECTIONS**
### Run The app and use swagger to make your CRUD Operations:
Thanks.
