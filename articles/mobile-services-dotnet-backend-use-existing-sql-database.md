<properties 
	pageTitle="Erstellen eines Diensts mithilfe einer vorhandenen SQL-Datenbank mit dem Mobile Services .NET-Back-End - Azure Mobile Services" 
	description="Erfahren Sie, wie Sie eine vorhandene Cloud- oder lokale SQL-Datenbank mit Ihrem .NET-basierten mobilen Dienst verwenden." 
	services="mobile-services, biztalk-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="glenga"/>


# Erstellen eines Diensts mithilfe einer vorhandenen SQL-Datenbank mit dem Mobile Services .NET-Back-End

Das Mobile Services .NET-Back-End macht die Nutzung vorhandener Objekte beim Erstellen eines mobilen Diensts ganz einfach. Ein besonders interessantes Szenario ist die Verwendung einer vorhandenen SQL-Datenbank (entweder lokal oder in der Cloud), die bereits von anderen Anwendungen verwendet wird, um die vorhandenen Daten auch für mobile Clients nutzbar zu machen. In diesem Fall ist es erforderlich, dass das Datenbankmodell (oder  *schema*) unverändert bleibt, damit die vorhandenen Lösungen weiter damit arbeiten können.

Dieses Lernprogramm umfasst die folgenden Abschnitte:

1. [Erkunden des vorhandenen Datenbankmodells](#ExistingModel)
2. [Erstellen von Datentransferobjekten (DTOs) für Ihre mobilen Dienste](#DTOs)
3. [Erstellen einer Zuordnung zwischen DTOs und Modell](#Mapping)
4. [Implementieren einer domänenspezifischen Logik](#DomainManager)
5. [Implementieren eines TableControllers mithilfe von DTOs](#Controller)

<a name="ExistingModel"></a>
## Erkunden des vorhandenen Datenbankmodells

Für dieses Lernprogramm verwenden wir die Datenbank, die mit Ihrem mobilen Dienst erstellt wurde, aber wir verwenden nicht das Standardmodell. Stattdessen erstellen wir manuell ein beliebiges Modell, das eine vorhandene Anwendung darstellt. Ausführliche Informationen zur alternativen Verbindung mit einer lokalen Datenbank finden Sie unter [Verbindung mit einem lokalen SQL Server von einem mobilen Azure-Dienst mithilfe von Hybridverbindungen](mobile-services-dotnet-backend-hybrid-connections-get-started.md).

1. Erstellen Sie zuerst ein Mobile Services-Serverprojekt in **Visual Studio 2013 Update 2** oder mithilfe des Schnellstartprojekts, das Sie auf der Mobile Services-Registerkarte für Ihren Dienst im [Azure-Verwaltungsportal](http://manage.windowsazure.com) herunterladen können. Zum Zweck dieses Lernprogramms verwenden wir den Serverprojektnamen **ShoppingService**.

2. Erstellen Sie eine Datei **Customer.cs** im Ordner **Models**, und verwenden Sie die folgende Implementierung. Sie müssen einen Assemblyverweis auf **System.ComponentModel.DataAnnotations** zum Projekt hinzufügen.

        using System.Collections.Generic;
        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Customer
            {
                [Key]
                public int CustomerId { get; set; }
                
                public string Name { get; set; }

                public virtual ICollection<Order> Orders { get; set; }

            }
        }

3. Erstellen Sie eine Datei **Order.cs** im Ordner **Models**, und verwenden Sie die folgende Implementierung.
    
        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Order
            {
                [Key]
                public int OrderId { get; set; }

                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                public int CustomerId { get; set; }
              
                public virtual Customer Customer { get; set; }

            }
        }

    Beachten Sie, dass diese beiden Klassen eine  *relationship* haben: jede **Bestellung** bezieht sich auf einen einzelnen **Kunden**, und ein **Kunde** kann mehreren **Bestellungen** zugeordnet werden. Beziehungen kommen in vorhandenen Datenmodellen häufig vor.

4. Erstellen Sie eine Datei **ExistingContext.cs** im Ordner **Models**, und implementieren Sie sie wie folgt:

        using System.Data.Entity;

        namespace ShoppingService.Models
        {
            public class ExistingContext : DbContext
            {
                public ExistingContext()
                    : base("Name=MS_TableConnectionString")
                {
                }

                public DbSet<Customer> Customers { get; set; }

                public DbSet<Order> Orders { get; set; }

            }
        }

Die oben dargestellte Struktur ähnelt einem vorhandenen Entity Framework-Modell, das Sie möglicherweise bereits für eine vorhandene Anwendung verwenden. Beachten Sie, dass das Modell zu diesem Zeitpunkt keine Kenntnis von Mobile Services hat. 

<a name="DTOs"></a>
## Erstellen von Datentransferobjekten (DTOs) für Ihre mobilen Dienste

Das Datenmodell, welches Sie mit Ihrem mobilen Dienst verwenden möchten, ist möglicherweise komplex; es könnte Hunderte von Entitäten mit verschiedenen Beziehungen untereinander enthalten. Beim Erstellen einer mobilen App ist es in der Regel von Vorteil, das Datenmodell zu vereinfachen und Beziehungen zu entfernen (oder diese manuell zu verarbeiten), um die zwischen App und Dienst hin und her gesendete Nutzlast zu minimieren. In diesem Abschnitt erstellen wir eine Gruppe vereinfachter Objekte (so genannte "Datentransferobjekte" oder "DTOs"), die den in der Datenbank vorhandenen Daten zugeordnet werden, aber nur ein Minimum an Eigenschaften enthalten, die für die mobile App erforderlich sind.

1. Erstellen Sie die Datei **MobileCustomer.cs** im Ordner **DataObjects** des Dienstprojekts, und verwenden Sie die folgende Implementierung:

        using Microsoft.WindowsAzure.Mobile.Service;

        namespace ShoppingService.DataObjects
        {
            public class MobileCustomer : EntityData
            {
                public string Name { get; set; }
            }
        }

    Sie sehen, dass diese Klasse der Klasse **Customer** im Modell ähnlich ist, außer dass die Beziehungseigenschaft zu **Order** entfernt wurde. Damit ein Objekt ordnungsgemäß mit der Mobile Services-Offlinesynchronisierung funktioniert, benötigt es einen Satz  *system properties* für optimistische Parallelität, daher werden Sie feststellen, dass das DTO von [**EntityData**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx) erbt, welches diese Eigenschaften enthält. Die INT-basierte Eigenschaft **CustomerId** vom ursprünglichen Modell wird durch die zeichenfolgenbasierte Eigenschaft **Id** aus **EntityData** ersetzt. Dabei handelt es sich um die **Id**, die von Mobile Services verwendet wird.

2. Erstellen Sie die Datei **MobileOrder.cs** im Ordner **DataObjects** des Dienstprojekts.

        using Microsoft.WindowsAzure.Mobile.Service;
        using Newtonsoft.Json;
        using System.ComponentModel.DataAnnotations;
        using System.ComponentModel.DataAnnotations.Schema;

        namespace ShoppingService.DataObjects
        {
            public class MobileOrder : EntityData
            {
                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                [NotMapped]
                public int CustomerId { get; set; }

                [Required]
                public string MobileCustomerId { get; set; }

                public string MobileCustomerName { get; set; }
            }
        }

    Die **Customer**-Beziehungseigenschaft wurde durch den **Customer**-Namen ersetzt, und eine **MobileCustomerId**-Eigenschaft kann verwendet werden, um die Beziehung auf dem Client manuell zu modellieren. Momentan können Sie die Eigenschaft **CustomerId** ignorieren, sie wird erst später verwendet. 

3. Sie haben vielleicht festgestellt, dass unsere DTOs durch Hinzufügen der Systemeigenschaften für die Basisklasse **EntityData** jetzt mehr Eigenschaften haben als die Modelltypen. Natürlich brauchen wir einen Ort, um diese Eigenschaften zu speichern, daher fügen wir einige Extraspalten zur Originaldatenbank hinzu. Obwohl die Datenbank dadurch geändert wird, werden die vorhandenen Anwendungen nicht beeinträchtigt, da die Änderungen lediglich aus Hinzufügungen bestehen (neue Spalten werden zum Schema hinzugefügt). Fügen Sie dazu folgende Anweisungen oben in **Customer.cs** und **Order.cs** hinzu:
    
        using System.ComponentModel.DataAnnotations.Schema;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.ComponentModel.DataAnnotations;
        using System;

    Anschließend fügen Sie diese zusätzlichen Eigenschaften in die Klassen ein:

        [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        [Index]
        [TableColumn(TableColumnType.CreatedAt)]
        public DateTimeOffset? CreatedAt { get; set; }

        [TableColumn(TableColumnType.Deleted)]
        public bool Deleted { get; set; }

        [Index]
        [TableColumn(TableColumnType.Id)]
        [MaxLength(36)]
        public string Id { get; set; }

        [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
        [TableColumn(TableColumnType.UpdatedAt)]
        public DateTimeOffset? UpdatedAt { get; set; }

        [TableColumn(TableColumnType.Version)]
        [Timestamp]
        public byte[] Version { get; set; }

4. Die soeben hinzugefügten Systemeigenschaften haben integrierte Verhaltensweisen (z. B. automatische Aktualisierung von "Erstellt/Aktualisiert am"), die transparent mit den Datenbankvorgängen erfolgen. Um dieses Verhalten zu aktivieren, müssen wir eine Änderung an **ExistingContext.cs** vornehmen. Fügen Sie am Anfang der Datei Folgendes hinzu:
    
        using System.Data.Entity.ModelConfiguration.Conventions;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.Linq;

    Anschließend überschreiben Sie im Text von **ExistingContext** den Eintrag [**OnModelCreating**](http://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx):

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {	
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceTableColumn", (property, attributes) => attributes.Single().ColumnType.ToString()));

            base.OnModelCreating(modelBuilder);
        } 

5. Lassen Sie uns die Datenbank mit einigen Beispieldaten ausfüllen. Öffnen Sie die Datei **WebApiConfig.cs**. Erstellen Sie einen neuen [**IDatabaseInitializer**](http://msdn.microsoft.com/library/gg696323.aspx), und konfigurieren Sie ihn in der Methode **Register** wie unten dargestellt.

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.Models;
        using System;
        using System.Collections.Generic;
        using System.Collections.ObjectModel;
        using System.Data.Entity;
        using System.Web.Http;

        namespace ShoppingService
        {
            public static class WebApiConfig
            {
                public static void Register()
                {
                    ConfigOptions options = new ConfigOptions();

                    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

                    Database.SetInitializer(new ExistingInitializer());
                }
            }

            public class ExistingInitializer : ClearDatabaseSchemaIfModelChanges<ExistingContext>
            {
                protected override void Seed(ExistingContext context)
                {
                    List<Order> orders = new List<Order>
                    {
                        new Order { OrderId = 10, Item = "Guitars", Quantity = 2, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 20, Item = "Drums", Quantity = 10, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 30, Item = "Tambourines", Quantity = 20, Id = Guid.NewGuid().ToString() }
                    };

                    List<Customer> customers = new List<Customer>
                    {
                        new Customer { CustomerId = 1, Name = "John", Orders = new Collection<Order> { 
                            orders[0]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 2, Name = "Paul", Orders = new Collection<Order> { 
                            orders[1]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 3, Name = "Ringo", Orders = new Collection<Order> { 
                            orders[2]}, Id = Guid.NewGuid().ToString()},
                    };

                    foreach (Customer c in customers)
                    {
                        context.Customers.Add(c);
                    }

                    base.Seed(context);
                }
            }
        }

<a name="Mapping"></a>
## Erstellen einer Zuordnung zwischen DTOs und Modell

Wir haben jetzt die Modelltypen **Customer** und **Order** sowie die DTOs **MobileCustomer** und **MobileOrder**, aber wir müssen das Back-End noch anweisen, zwischen den beiden automatisch zu transformieren. Mobile Services verlässt sich hier auf [**AutoMapper**](http://automapper.org/), eine relationale Objektzuordnung, auf die im Projekt bereits verwiesen wird.

1. Fügen Sie am Anfang von **WebApiConfig.cs** Folgendes ein:

        using AutoMapper;
        using ShoppingService.DataObjects;

2. Zur Definition der Zuordnung fügen Sie Folgendes zur Methode **Register** der Klasse **WebApiConfig** hinzu. 

        Mapper.Initialize(cfg =>
        {
            cfg.CreateMap<MobileOrder, Order>();
            cfg.CreateMap<MobileCustomer, Customer>();
            cfg.CreateMap<Order, MobileOrder>()
                .ForMember(dst => dst.MobileCustomerId, map => map.MapFrom(x => x.Customer.Id))
                .ForMember(dst => dst.MobileCustomerName, map => map.MapFrom(x => x.Customer.Name));
            cfg.CreateMap<Customer, MobileCustomer>();

        });

AutoMapper ordnet nun die Objekte einander zu. Alle Eigenschaften mit übereinstimmenden Namen werden zugeordnet, beispielsweise wird **MobileOrder.CustomerId** automatisch **Order.CustomerId** zugeordnet. Benutzerdefinierte Zuordnungen können wie oben gezeigt konfiguriert werden, wobei wir die Eigenschaft **MobileCustomerName** der Eigenschaft **Name** der Beziehungseigenschaft **Customer** zuordnen.

<a name="DomainManager"></a>
## Implementieren einer domänenspezifischen Logik

Der nächste Schritt ist das Implementieren eines [**MappedEntityDomainManager**](http://msdn.microsoft.com/library/dn643300.aspx), der als Abstraktionsschicht zwischen dem zugeordneten Datenspeicher und dem Controller, der den HTTP-Verkehr von unseren Clients abwickelt, dient. Wir können unseren Controller im nächsten Abschnitt komplett in Form von DTOs schreiben, und der hier hinzugefügte **MappedEntityDomainManager** verarbeitet die Kommunikation mit dem ursprünglichen Datenspeicher und bietet gleichzeitig einen Ort zur Implementierung seiner spezifischen Logik.

1. Fügen Sie die Datei **MobileCustomerDomainManager.cs** zum Ordner **Models** des Projekts hinzu. Verwenden Sie die folgenden Implementierung:

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Data.Entity;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileCustomerDomainManager : MappedEntityDomainManager<MobileCustomer, Customer>
            {
                private ExistingContext context;

                public MobileCustomerDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                public static int GetKey(string mobileCustomerId, DbSet<Customer> customers, HttpRequestMessage request)
                {
                    int customerId = customers
                       .Where(c => c.Id == mobileCustomerId)
                       .Select(c => c.CustomerId)
                       .FirstOrDefault();

                    if (customerId == 0)
                    {
                        throw new HttpResponseException(request.CreateNotFoundResponse());
                    }
                    return customerId;
                }

                protected override T GetKey<T>(string mobileCustomerId)
                {
                    return (T)(object)GetKey(mobileCustomerId, this.context.Customers, this.Request);
                }
                
                public override SingleResult<MobileCustomer> Lookup(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return LookupEntity(c => c.CustomerId == customerId);
                }

                public override async Task<MobileCustomer> InsertAsync(MobileCustomer mobileCustomer)
                {
                    return await base.InsertAsync(mobileCustomer);
                }

                public override async Task<MobileCustomer> UpdateAsync(string mobileCustomerId, Delta<MobileCustomer> patch)
                {
                    int customerId = GetKey<int>(mobileCustomerId);

                    Customer existingCustomer = await this.Context.Set<Customer>().FindAsync(customerId);
                    if (existingCustomer == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileCustomer existingCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);
                    patch.Patch(existingCustomerDTO);
                    Mapper.Map<MobileCustomer, Customer>(existingCustomerDTO, existingCustomer);

                    await this.SubmitChangesAsync();

                    MobileCustomer updatedCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);

                    return updatedCustomerDTO;
                }

                public override async Task<MobileCustomer> ReplaceAsync(string mobileCustomerId, MobileCustomer mobileCustomer)
                {
                    return await base.ReplaceAsync(mobileCustomerId, mobileCustomer);
                }

                public override async Task<bool> DeleteAsync(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return await DeleteItemAsync(customerId);
                }
            }
        }

    Ein wichtiger Teil dieser Klasse ist die Methode **GetKey**, mit der wir angeben, wo sich die ID-Eigenschaft des Objekts im ursprünglichen Datenmodell befindet. 

2. Fügen Sie die Datei **MobileOrderDomainManager.cs** zum Ordner **Models** des Projekts hinzu:

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileOrderDomainManager : MappedEntityDomainManager<MobileOrder, Order>
            {
                private ExistingContext context;

                public MobileOrderDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                protected override T GetKey<T>(string mobileOrderId)
                {
                    int orderId = this.context.Orders
                        .Where(o => o.Id == mobileOrderId)
                        .Select(o => o.OrderId)
                        .FirstOrDefault();

                    if (orderId == 0)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }
                    return (T)(object)orderId;
                }

                public override SingleResult<MobileOrder> Lookup(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return LookupEntity(o => o.OrderId == orderId);
                }

                private async Task<Customer> VerifyMobileCustomer(string mobileCustomerId, string mobileCustomerName)
                {
                    int customerId = MobileCustomerDomainManager.GetKey(mobileCustomerId, this.context.Customers, this.Request);
                    Customer customer = await this.context.Customers.FindAsync(customerId);
                    if (customer == null)
                    {
                        throw new HttpResponseException(Request.CreateBadRequestResponse("Customer with name '{0}' was not found", mobileCustomerName));
                    }
                    return customer;
                }

                public override async Task<MobileOrder> InsertAsync(MobileOrder mobileOrder)
                {
                    Customer customer = await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);
                    mobileOrder.CustomerId = customer.CustomerId;
                    return await base.InsertAsync(mobileOrder);
                }

                public override async Task<MobileOrder> UpdateAsync(string mobileOrderId, Delta<MobileOrder> patch)
                {
                    Customer customer = await VerifyMobileCustomer(patch.GetEntity().MobileCustomerId, patch.GetEntity().MobileCustomerName);

                    int orderId = GetKey<int>(mobileOrderId);

                    Order existingOrder = await this.Context.Set<Order>().FindAsync(orderId);
                    if (existingOrder == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileOrder existingOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);
                    patch.Patch(existingOrderDTO);
                    Mapper.Map<MobileOrder, Order>(existingOrderDTO, existingOrder);

                    // This is required to map the right Id for the customer
                    existingOrder.CustomerId = customer.CustomerId;

                    await this.SubmitChangesAsync();

                    MobileOrder updatedOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);

                    return updatedOrderDTO;
                }

                public override async Task<MobileOrder> ReplaceAsync(string mobileOrderId, MobileOrder mobileOrder)
                {
                    await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);

                    return await base.ReplaceAsync(mobileOrderId, mobileOrder);
                }

                public override Task<bool> DeleteAsync(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return DeleteItemAsync(orderId);
                }
            }
        }

    In diesem Fall sind die Methoden **InsertAsync** und **UpdateAsync** interessant. Hier erzwingen wir die Beziehung, sodass jede **Order** einen gültigen zugeordneten Wert für **Customer** haben muss. In **InsertAsync** werden Sie feststellen, dass wir die Eigenschaft **MobileOrder.CustomerId** ausfüllen, die der Eigenschaft **Order.CustomerId** zugeordnet ist. Diesen Wert erhalten wir durch die Suche nach **Customer** mit der zugehörigen **MobileOrder.MobileCustomerId**. Der Grund dafür ist, dass der Client standardmäßig nur die Mobile Services-ID (**MobileOrder.MobileCustomerId**) von **Customer** berücksichtigt, die sich vom tatsächlichen Primärschlüssel unterscheidet, der zum Festlegen des Fremdschlüssels (**MobileOrder.CustomerId**) von **Order** auf **Customer** benötigt wird. Dies wird nur intern innerhalb des Diensts verwendet, um den Einfügevorgang zu ermöglichen.

Nun können wir Controller erstellen, um unsere DTOs den Clients anzuzeigen.

<a name="Controller"></a>
## Implementieren eines TableControllers mithilfe von DTOs

1. Fügen Sie im Ordner **Controllers** die Datei **MobileCustomerController.cs** hinzu:

        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileCustomerController : TableController<MobileCustomer>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    var context = new ExistingContext();
                    DomainManager = new MobileCustomerDomainManager(context, Request, Services);
                }

                public IQueryable<MobileCustomer> GetAllMobileCustomers()
                {
                    return Query();
                }

                public SingleResult<MobileCustomer> GetMobileCustomer(string id)
                {
                    return Lookup(id);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PatchAsync(string id, Delta<MobileCustomer> patch)
                {
                    return base.UpdateAsync(id, patch);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PostAsync(MobileCustomer item)
                {
                    return base.InsertAsync(item);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task DeleteAsync(string id)
                {
                    return base.DeleteAsync(id);
                }
            }
        }

    Beachten Sie die Verwendung des Attributs AuthorizeLevel, um den öffentlichen Zugriff auf die Insert/Update/Delete-Operationen im Controller zu beschränken. Für den Zweck dieses Szenarios ist die Kundenliste schreibgeschützt, aber die Erstellung neuer Bestellungen ist zulässig; diese werden vorhandenen Kunden zugeordnet. 

2. Fügen Sie im Ordner **Controllers** die Datei **MobileOrderController.cs** hinzu:

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Description;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileOrderController : TableController<MobileOrder>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    ExistingContext context = new ExistingContext();
                    DomainManager = new MobileOrderDomainManager(context, Request, Services);
                }

                public IQueryable<MobileOrder> GetAllMobileOrders()
                {
                    return Query();
                }

                public SingleResult<MobileOrder> GetMobileOrder(string id)
                {
                    return Lookup(id);
                }

                public Task<MobileOrder> PatchMobileOrder(string id, Delta<MobileOrder> patch)
                {
                    return UpdateAsync(id, patch);
                }

                [ResponseType(typeof(MobileOrder))]
                public async Task<IHttpActionResult> PostMobileOrder(MobileOrder item)
                {
                    MobileOrder current = await InsertAsync(item);
                    return CreatedAtRoute("Tables", new { id = current.Id }, current);
                }

                public Task DeleteMobileOrder(string id)
                {
                    return DeleteAsync(id);
                }
            }
        }

3. Sie können nun den Dienst ausführen. Drücken Sie **F5**, und verwenden Sie den in die Hilfeseite integrierten Testclient, um die Daten zu ändern.

Beachten Sie, dass beide Controllerimplementierungen die DTOs **MobileCustomer** und **MobileOrder** exklusiv verwenden und das zugrunde liegende Modell nicht kennen. Diese DTOs sind für JSON serialisiert und können verwendet werden, um Daten mit dem Mobile Services-Client-SDK auf allen Plattformen auszutauschen. Wenn Sie beispielsweise eine Windows Store-App erstellen, sieht der zugehörige clientseitige Typ wie unten dargestellt aus. Dieser Typ sieht auf anderen Clientplattformen ähnlich aus. 

    using Microsoft.WindowsAzure.MobileServices;
    using System;

    namespace ShoppingClient
    {
        public class MobileCustomer
        {
            public string Id { get; set; }

            public string Name { get; set; }

            [CreatedAt]
            public DateTimeOffset? CreatedAt { get; set; }

            [UpdatedAt]
            public DateTimeOffset? UpdatedAt { get; set; }

            public bool Deleted { get; set; }
            
            [Version]
            public string Version { get; set; }

        }

    }

Als nächsten Schritt können Sie nun die Client-App erstellen, um auf den Dienst zuzugreifen. 




<!--HONumber=42-->
