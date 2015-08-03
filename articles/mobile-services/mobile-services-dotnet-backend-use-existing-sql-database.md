<properties 
	pageTitle="Compilar um serviço usando um banco de dados SQL existente com o back-end do .NET dos Serviços Móveis - Serviços Móveis do Azure" 
	description="Aprenda a usar uma nuvem existente ou banco de dados SQL local com seu serviço móvel baseado em .NET" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="glenga"/>


# Compilar um serviço usando um banco de dados SQL existente com o back-end do .NET dos Serviços Móveis

O back-end .NET dos Serviços Móveis facilita a obtenção dos benefícios dos ativos existentes ao compilar um serviço móvel. Um cenário particularmente interessante é usar um banco de dados SQL existente (ou local ou em nuvem), que já possa ser usado por outros aplicativos, para disponibilizar os dados existentes aos clientes móveis. Nesse caso, há um requisito de que o modelo do banco de dados (ou o *esquema*) permaneça inalterado, para que as soluções existentes continuem funcionando.

<a name="ExistingModel"></a>
## Explorando o modelo de banco de dados existente

Para esse tutorial, usaremos o banco de dados que foi criado com seu serviço móvel, mas não usaremos o modelo padrão que é criado. Em vez disso, criaremos manualmente um modelo arbitrário que representará um aplicativo existente que você possa ter. Para obter maiores detalhes sobre como se conectar a um banco de dados local, consulte [Conectar-se a um SQL Server local por meio de um serviço móvel do Azure utilizando Conexões Híbridas](mobile-services-dotnet-backend-hybrid-connections-get-started.md).

1. Inicie criando um projeto de servidor dos Serviços Móveis no **Visual Studio 2013 Update 2** ou usando o projeto de Início rápido que você pode baixar na guia de Serviços Móveis para seu serviço no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com). Para os propósitos deste tutorial, presumiremos que o nome do seu projeto de servidor seja **ShoppingService**.

2. Crie um arquivo **Customer.cs** dentro da pasta **Modelos** e use a seguinte implementação. Você precisará adicionar uma referência de assembly para **System.ComponentModel.DataAnnotations** em seu projeto.

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

3. Crie um arquivo **Order.cs** dentro da pasta **Modelos** e use a implementação a seguir:
    
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

    Você observará que essas duas classes têm uma *relação*: cada **Pedido** é associado a um único **Cliente** e um **Cliente** pode ser associado a vários **Pedidos**. Há relações em comum nos modelos de dados existentes.

4. Crie um arquivo **ExistingContext.cs** dentro da pasta **Modelos** e implemente-o como:

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

A estrutura acima aproxima um modelo Entity Framework existente que você pode já estar usando para um aplicativo existente. Observe que esse modelo não está ciente dos Serviços Móveis de forma alguma até esta etapa.

<a name="DTOs"></a>
## Criando objetos de transferência de dados (DTOs) para seu serviço móvel

O modelo de dados que você deseja usar com o seu serviço móvel pode ser arbitrariamente complexo; ele pode conter centenas de entidades com uma variedade de relações entre elas. Ao compilar um aplicativo móvel, geralmente é desejável simplificar o modelo de dados e eliminar as relações (ou tratá-las manualmente) para minimizar a carga a ser enviada para frente e para trás entre o aplicativo e o serviço. Nesta seção, criaremos um conjunto de objetos simplificados (conhecidos como “objetos de transferência de dados" ou "DTOs"), que são mapeados para os dados que você possui em seu banco de dados, embora contenham somente o conjunto mínimo de propriedades necessário a seu aplicativo móvel.

1. Crie o arquivo **MobileCustomer.cs** na pasta **DataObjects** de seu projeto de serviço e use a seguinte implementação:

        using Microsoft.WindowsAzure.Mobile.Service;

        namespace ShoppingService.DataObjects
        {
            public class MobileCustomer : EntityData
            {
                public string Name { get; set; }
            }
        }

    Observe que essa classe é semelhante à classe de **Cliente** no modelo, exceto que a propriedade de relação para o **Pedido** é removida. Para um objeto funcionar corretamente com sincronização offline de Serviços Móveis, é necessário um conjunto de *propriedades do sistema* para obter simultaneidade otimista, assim, você notará que o DTO é herdeiro do [**EntityData**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx), que contém tais propriedades. A propriedade **CustomerId** com base no modelo original é substituída pela propriedade **ID** com base em cadeia de caracteres de **EntityData**, que será a **ID** que os Serviços Móveis usarão.

2. Crie o arquivo **MobileOrder.cs** na pasta **DataObjects** de seu projeto de serviço.

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

    A propriedade de relação do **Cliente** foi substituída pelo nome do **Cliente** e uma propriedade **MobileCustomerId** que pode ser usada para modelar manualmente a relação no cliente. Por enquanto, você pode ignorar a propriedade **CustomerId**, ela só será usada mais tarde.

3. Você pode observar que com a adição das propriedades do sistema na classe base **EntityData**, nossos DTOs agora possuem mais propriedades do que os tipos de modelo. Precisamos claramente de um lugar para armazenar essas propriedades, de forma que adicionaremos algumas colunas extras ao banco de dados original. Enquanto fazemos essa mudança no banco de dados, não haverá uma pausa nos aplicativos existentes, visto que as alterações são puramente aditivas (adicionar novas colunas ao esquema). Para isso, adicione as seguintes instruções na parte superior de **Customer.cs** e **Order.cs**:
    
        using System.ComponentModel.DataAnnotations.Schema;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.ComponentModel.DataAnnotations;
        using System;

4. Em seguida, adicione essas propriedades extras a cada uma das classes:

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

4. As propriedades do sistema simplesmente adicionam alguns comportamentos integrados (por exemplo, a atualização automática de criados/atualizados em) que ocorrem de maneira transparente nas operações de banco de dados. Para habilitar esses comportamentos, precisamos fazer uma mudança em **ExistingContext.cs**. Na parte superior do arquivo, adicione o seguinte:
    
        using System.Data.Entity.ModelConfiguration.Conventions;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.Linq;

5. No corpo de **ExistingContext**, substitua [**OnModelCreating**](http://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx):

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceTableColumn", (property, attributes) => attributes.Single().ColumnType.ToString()));

            base.OnModelCreating(modelBuilder);
        } 

5. Vamos preencher o banco de dados com alguns dados de exemplo. Abra o arquivo **WebApiConfig.cs**. Crie um novo [**IDatabaseInitializer**](http://msdn.microsoft.com/library/gg696323.aspx) e configure-o no método do **Registro** como mostrado abaixo.

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
## Estabelecendo um mapeamento entre DTOs e o modelo

Agora temos os tipos de modelo **Cliente** e **Pedido** e os DTOs **MobileCustomer** e **MobileOrder**, mas precisamos instruir o back-end para se transformar automaticamente entre os dois. Aqui os Serviços Móveis contam com o [**AutoMapper**](http://automapper.org/), um mapeador relacional de objeto, que já está referenciado no projeto.

1. Adicione o seguinte à parte superior do **WebApiConfig.cs**:

        using AutoMapper;
        using ShoppingService.DataObjects;

2. Para definir o mapeamento, adicione o seguinte ao método do **Registro** da classe **WebApiConfig**.

        Mapper.Initialize(cfg =>
        {
            cfg.CreateMap<MobileOrder, Order>();
            cfg.CreateMap<MobileCustomer, Customer>();
            cfg.CreateMap<Order, MobileOrder>()
                .ForMember(dst => dst.MobileCustomerId, map => map.MapFrom(x => x.Customer.Id))
                .ForMember(dst => dst.MobileCustomerName, map => map.MapFrom(x => x.Customer.Name));
            cfg.CreateMap<Customer, MobileCustomer>();

        });

Agora, o AutoMapper mapeará os objetos para um outro. Todas as propriedades com nomes correspondentes serão correspondidas, por exemplo, **MobileOrder.CustomerId** será automaticamente mapeado para **Order.CustomerId**. Os mapeamentos personalizados podem ser configurados como acima, em que mapeamos a propriedade **MobileCustomerName** para a propriedade **Nome** da propriedade de relação do **Cliente**.

<a name="DomainManager"></a>
## Implementando lógica específica de domínio

A próxima etapa é implementar um [**MappedEntityDomainManager**](http://msdn.microsoft.com/library/dn643300.aspx), que serve como uma camada de abstração entre o nosso armazenamento de dados mapeados e o controlador que servirá ao tráfego HTTP de nossos clientes. Poderemos gravar nosso controlador na próxima seção meramente em termos de DTOs e o **MappedEntityDomainManager** que adicionamos aqui tratará da comunicação com o armazenamento de dados original, enquanto também nos dará um lugar para implementar uma lógica específica para ele.

1. Adicione um **MobileCustomerDomainManager.cs** à pasta **Modelos** de seu projeto. Cole na seguinte implementação:

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

    Uma parte importante dessa classe é o método **GetKey** em que indicamos como localizar a propriedade da ID do objeto no modelo de dados original.

2. Adicione um **MobileOrderDomainManager.cs** à pasta **Modelos** de seu projeto:

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

    Nesse caso, os métodos **InsertAsync** e **UpdateAsync** são interessantes; é neles que reforçamos a relação de que cada **Pedido** deve ter um **Cliente** associado válido. Em **InsertAsync**, observaremos que a propriedade **MobileOrder.CustomerId** foi preenchida, a qual mapeia para a propriedade **Order.CustomerId**. Obtivemos esse valor com base na procura pelo **Cliente** com o **MobileOrder.MobileCustomerId** correspondente. Isso porque, por padrão, o cliente se preocupa somente com a ID dos Serviços Móveis (**MobileOrder.MobileCustomerId**) do **Cliente**, que é diferente de sua chave primária real necessária para definir a chave estrangeira (**MobileOrder.CustomerId**) do **Pedido** para o **Cliente**. Isso é usado somente internamente no serviço para facilitar a operação de inserção.

Agora estamos prontos para criar controladores para expor nossos DTOs a nossos clientes.

<a name="Controller"></a>
## Implementando um TableController usando DTOs

1. Na pasta **Controladores**, adicione o arquivo **MobileCustomerController.cs**:

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

    Você observará o uso do atributo AuthorizeLevel para restringir o acesso público às operações de Inserir/Atualizar/Excluir no controlador. Para os fins desse cenário, a lista de Clientes será somente leitura, mas nós permitiremos a criação de novos Pedidos e a associação deles aos clientes existentes.

2. Na pasta **Controladores**, adicione o arquivo **MobileOrderController.cs**:

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

3. Agora você está pronto para executar seu serviço. Pressione **F5** e use o cliente de teste compilado na página de ajuda para modificar os dados.

Observe que as duas implementações do controlador tornam exclusivo o uso dos DTOs **MobileCustomer** e **MobileOrder** e são agnósticos do modelo subjacente. Esses DTOs estão prontamente serializados para JSON e podem ser usados para trocar dados com o SDK do cliente dos Serviços Móveis em todas as plataformas. Por exemplo, se compilar um aplicativo da Windows Store, o tipo do lado do cliente correspondente ficará parecido com o mostrado abaixo. O tipo seria análogo a outras plataformas clientes.

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

Como uma próxima etapa, agora você pode compilar o aplicativo cliente para acessar o serviço. Para obter mais informações, consulte [Adicionar Serviços Móveis a um aplicativo existente](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md#update-the-app-to-use-the-mobile-service).

<!---HONumber=July15_HO4-->