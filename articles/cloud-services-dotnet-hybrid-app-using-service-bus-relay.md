<properties linkid="dev-net-tutorials-hybrid-solution" urlDisplayName="Aplicativo híbrido" pageTitle="Aplicativo híbrido local/de nuvem (.NET) - Azure" metaKeywords="Tutorial de Brramento de Serviço do Azure, híbrido .NET" description="Saiba como criar um aplicativo .NET híbrido local/de nuvem usando a Retransmissão do Brramento de Serviço do Azure." metaCanonical="" services="service-bus" documentationCenter=".NET" title="Aplicativo híbrido .NET local/de nuvem usando a Retransmissão do Brramento de Serviço" authors="sethm" solutions="" manager="dwrede" editor="mattshel" />





# Aplicativo .NET híbrido local/na nuvem usando a Retransmissão de Brramento de Serviço

<h2><span class="short-header">INTRODUÇÃO</span>INTRODUÇÃO</h2>

Desenvolver aplicativos híbridos de nuvem com o Azure é fácil usando o Visual Studio 2013 e o SDK do Azure para .NET gratuito. Este guia pressupõe que você não tem experiência anterior com o Azure. Em menos de 30 minutos, você terá um aplicativo que usa vários recursos do Azure em funcionamento na nuvem.

Você aprenderá a:

-   Como criar ou adaptar um serviço Web existente para consumo por uma solução Web.
-  Como usar a Retransmissão de Brramento de Serviço para compartilhar dados entre um aplicativo do Azure e um serviço Web hospedado em outro lugar.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

### COMO A RETRANSMISSÃO DO BARRAMENTO DE SERVIÇO AJUDA COM SOLUÇÕES HÍBRIDAS

As soluções de negócios geralmente são compostas por uma combinação de código personalizado escrito para lidar com os requisitos de negócios novos e exclusivos e a funcionalidade existente fornecida pelas soluções e sistemas que já estão estabelecidas.

Os arquitetos de solução estão começando a utilizar a nuvem para obter um manuseio mais fácil de requisitos de escala e custos operacionais mais baixos. Fazendo isso, eles descobrem que os serviços ativos existentes que gostariam de aproveitar como blocos de construção para suas soluções estão dentro do firewall corporativo e fora de alcance fácil para acesso pela solução de nuvem. Muitos serviços internos não são construídos ou hospedados de forma que possam ser facilmente expostos na borda da rede corporativa.

A *Retransmissão de Brramento de Serviço* foi desenvolvida para o caso de uso de utilização dos serviços Web do Windows Communication Foundation (WCF) existentes‏ e para tornar esses serviços acessíveis com segurança para soluções que residem fora do perímetro corporativo sem exigir alterações intrusivas na infraestrutura da rede corporativa. Esses serviços de Retransmissão de Brramento de Serviço ainda estão hospedados dentro de seu ambiente existente, mas delegam a escuta de sessões de entrada e de pedidos para o Brramento de Serviço Hospedado em nuvem. O Brramento de Serviço também protege esses serviços contra acesso não autorizado usando o Controle de Acesso do Active Directory do Azure.

### CENÁRIO DA SOLUÇÃO

Neste tutorial, você irá criar um site Web ASP.NET MVC 4 que permitirá ver uma lista de produtos na página de inventário de produtos.

![][0]

O tutorial pressupõe que você tem informações sobre produtos em um sistema local existente e usa a Retransmissão de Brramento de Serviço para acessar esse sistema. Isso é simulado por um serviço Web que está sendo executado em um aplicativo de console simples e é suportado por um conjunto de produtos na memória. Você poderá executar esse aplicativo de console em seu próprio computador e implantar a função web no Azure. Fazendo isso, você verá como a função web em execução no datacenter do Azure realmente chamará em seu computador, embora seu computador certamente resida atrás de pelo menos um firewall e de uma camada de NAT (conversão de endereços de rede).

A captura de tela da página inicial do aplicativo Web completo é mostrada abaixo.

![][1]

<h2><span class="short-header">CONFIGURAR O AMBIENTE</span>CONFIGURAR O AMBIENTE DE DESENVOLVIMENTO</h2>

Antes de começar a desenvolver seu aplicativo do Azure, você precisa
obter as ferramentas e configurar seu ambiente de desenvolvimento.

1.  Para instalar o SDK do Azure para .NET, clique no botão abaixo:

    [Obter as ferramentas e o SDK][]

2. 	Clique em **Instalar o SDK**.

3. 	Escolha o link para a versão do Visual Studio que você está usando. As etapas deste tutorial usam o Visual Studio 2013:

	![][42]

4.  Quando receber um prompt para executar ou salvar o **WindowsAzureSDKForNet.exe**, clique em **Executar**:

    ![][2]

5.  No Web Platform Installer, clique em **Instalar** e continue a instalação:

    ![][3]

6.  Quando a instalação for concluída, você terá tudo o que é necessário para iniciar o desenvolvimento. O SDK inclui ferramentas que permitem que você
    desenvolva facilmente aplicativos do Azure no Visual Studio. Se você
não tiver o Visual Studio instalado, ele também instala o
Visual Studio Express gratuito.

<h2><span class="short-header">CRIAR UM NAMESPACE</span>CRIAR UM NAMESPACE DE SERVIÇO</h2>

Para começar a usar os recursos do Brramento de Serviço no Azure, você deve primeiro criar um namespace de serviço. Um namespace de serviço fornece um contêiner de escopo para endereçamento dos recursos do Brramento de Serviço em seu aplicativo. 

Você pode gerenciar namespaces e entidades de mensagens do Brramento de Serviço usando o [Portal de Gerenciamento do Azure][] ou o Gerenciador de Servidores do Visual Studio, mas só pode criar novos namespaces de dentro do portal.

###Para criar um namespace de serviço usando o portal:

1.  Faça logon no [Portal de Gerenciamento do Azure][].

2.  No painel de navegação à esquerda do Portal de Gerenciamento, clique em **Brramento de Serviço**.

3.  No painel inferior do Portal de Gerenciamento, clique em **Criar**.   
    ![][5]

4.  Na caixa de diálogo **Adicionar um novo namespace**, digite um nome de namespace.
    O sistema imediatamente verifica para ver se o nome está disponível.   
    ![][6]

5.  Depois de verificar se o nome do namespace está disponível, escolha o país ou a região em que o namespace deve ser hospedado (certifique-se de usar o mesmo país/região em que você está implantando os recursos de computação).

    IMPORTANTE: selecione a **mesma região** que você pretende escolher para implantar seu aplicativo. Isso lhe dará o melhor desempenho.

6.	Clique na marca de seleção. Agora, o sistema cria o namespace de serviço e o habilita. Talvez você precise aguardar vários minutos, conforme o sistema fornece recursos para sua conta.

	![][38]

O namespace que você criou irá aparecer no Portal de Gerenciamento e leva um tempo para ser ativado. Aguarde até que o status seja **Ativo** antes de seguir em frente.

<h2><span class="short-header">OBTER CREDENCIAIS DE GERENCIAMENTO</span>OBTER AS CREDENCIAIS DE GERENCIAMENTO PADRÃO PARA O NAMESPACE</h2>

Para realizar operações de gerenciamento, como a criação de fila no novo namespace, no novo namespace, você precisa obter as credenciais de gerenciamento para o namespace.

1.  Na janela principal, clique no nome do seu namespace de serviço.   

	![][39]
  

2.  Clique em **Informações de Conexão**.   

	![][40]


3.  No painel **Informações de conexão de acesso**, localize as entradas **Emissor Padrão** e **Chave Padrão**.   
    

4.  Anote a chave ou copie a chave na área de transferência.

###Gerencie um namespace de serviço com o Gerenciador de Servidores do Visual Studio:

Para gerenciar um namespace e obter informações de conexão usando o Visual Studio em vez do Portal de Gerenciamento, siga o procedimento descrito [aqui](http://http://msdn.microsoft.com/pt-br/library/windowsazure/ff687127.aspx), na seção intitulada **Para conectar-se ao Azure no Visual Studio**. Quando você entra no Azure, o nó **Brramento de Serviço** na árvore **Microsoft Azure** no Gerenciador de Servidores é automaticamente populado com os namespaces que você criou. Clique com o botão direito do mouse em qualquer namespace e, em seguida, clique em **Propriedades** para ver a cadeia de conexão e outros metadados associados a esse namespace exibido no painel **Propriedades** do Visual Studio. 

![][44]

Anote o valor da **SharedAccessKey** ou copie-a na área de transferência.


<h2><span class="short-header">CRIAR UM SERVIDOR LOCAL</span>CRIAR UM SERVIDOR LOCAL</h2>

Primeiro você irá criar um sistema de catálogo de produtos (fictício) local. Será muito simples, você pode ver isso como a representação de um sistema de catálogo de produtos real local com um serviço de superfície completo que estamos tentando integrar.

Este projeto será iniciado como um aplicativo de console do Visual Studio. O projeto usa o pacote NuGet de Brramento de Serviço para incluir as bibliotecas de Brramento de Serviço e as definições de configuração. A extensão do Visual Studio do NuGet facilita a instalação e a atualização de bibliotecas e ferramentas no Visual Studio e no Visual Studio Express. O pacote NuGet do Brramento de Serviço é a maneira mais fácil
de obter a API do Brramento de Serviço e configurar seu aplicativo com todas
as dependências do Brramento de Serviço. Para obter detalhes sobre como usar o NuGet e o pacote do Brramento de Serviço, consulte [Usando o pacote NuGet do Brramento de Serviço][].

### CRIAR O PROJETO

1. Utilizando privilégios de administrador, inicie o Microsoft Visual Studio
    2013 ou o Microsoft Visual Studio Express. Para iniciar o Visual
    Studio com privilégios de administrador, clique com o botão direito do mouse em **Microsoft Visual
    Studio 2013 (ou em Microsoft Visual Studio Express)** e clique em **Executar como administrador**.
2.  No Visual Studio, no menu **Arquivo**, clique em **Novo** e, em seguida, clique em **Projeto**.

    ![][10]

3.  Em **Modelos Instalados**, sob **Visual C#**, clique em **Aplicativo de Console**. Na caixa **Nome**, digite o nome
    **ProductsServer**:

    ![][11]

4.  Clique em **OK** para criar o projeto **ProductsServer**.

5.  No **Gerenciador de Soluções**, clique com o botão direito do mouse em **ProductsServer** e, em seguida, clique em **Propriedades**.
6.  Clique na guia **Aplicativo** à esquerda, e certifique-se de que o **.NET Framework 4** ou o **.NET Framework 4.5** aparece no menu suspenso **Estrutura de destino**. Caso contrário, selecione-o na lista suspensa e, em seguida, clique em **Sim**
    quando for solicitado para recarregar o projeto.

    ![][12]

7.  Se você já tiver instalado o Gerenciador de Pacotes NuGet para Visual Studio, vá para a próxima etapa. Caso contrário, visite [NuGet][] e clique em [Instalar o NuGet (a página pode estar em inglês)](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Siga os prompts para instalar o Gerenciador de Pacotes NuGet e, em seguida, reinicie o Visual Studio.

7.  No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Referências** e clique em **Gerenciar Pacotes NuGet**...
8.  Na coluna à esquerda da caixa de diálogo NuGet, clique em **Online**.

9. 	Na coluna à direita, clique na caixa **Pesquisa**, digite "**WindowsAzure**" e selecione o item **Brramento de Serviço do Azure**. Clique em **Instalar** para concluir a
    instalação e, em seguida, feche essa caixa de diálogo.

    ![][13]

    Observe que os assemblies do cliente necessários agora são referenciados.

9.  Adicione uma nova classe para seu contrato de produto. Em **Gerenciador de Soluções**,
    clique com o botão direito do mouse no projeto **ProductsServer** e clique em **Adicionar** e, em seguida, clique em
    **Classe**.

    ![][14]

10. Na caixa **Nome**, digite o nome **ProductsContract.cs**. Em seguida,
    clique em **Adicionar**.
11. Em **ProductsContract.cs**, substitua definição do namespace pelo
    código a seguir, que define o contrato do serviço:

        namespace ProductsServer
        {
            using System.Collections.Generic;
            using System.Runtime.Serialization;
            using System.ServiceModel;

            // Define the data contract for the service
            [DataContract]
            // Declare the serializable properties
            public class ProductData
            {
                [DataMember]
                public string Id { get; set; }
                [DataMember]
                public string Name { get; set; }
                [DataMember]
                public string Quantity { get; set; }
            }

            // Define the service contract.
            [ServiceContract]
            interface IProducts
            {
                [OperationContract]
                IList<ProductData> GetProducts();

            }

            interface IProductsChannel : IProducts, IClientChannel
            {
            }
        }

12. Em Program.cs, substitua a definição do namespace pelo
    código a seguir, que define o contrato do serviço:

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface
            class ProductsService : IProducts
            {
                
                // Populate array of products for display on Web site
                ProductData[] products = 
                    new []
                        {
                            new ProductData{ Id = "1", Name = "Rock", 
                                             Quantity = "1"},
                            new ProductData{ Id = "2", Name = "Paper", 
                                             Quantity = "3"},
                            new ProductData{ Id = "3", Name = "Scissors", 
                                             Quantity = "5"},
                            new ProductData{ Id = "4", Name = "Well", 
                                             Quantity = "2500"},
                        };

                // Display a message in the service console application 
                // when the list of products is retrieved
                public IList<ProductData> GetProducts()
                {
                    Console.WriteLine("GetProducts called.");
                    return products;
                }

            }

            class Program
            {
                // Define the Main() function in the service application
                static void Main(string[] args)
                {
                    var sh = new ServiceHost(typeof(ProductsService));
                    sh.Open();

                    Console.WriteLine("Press ENTER to close");
                    Console.ReadLine();

                    sh.Close();
                }
            }
        }

13. No **Gerenciador de Soluções**, clique duas vezes no arquivo **app.config** para
    abri-lo no editor do **Visual Studio**. Substitua o conteúdo de
    **&lt;system.ServiceModel>** pelo código XML a seguir. Certifique-se de
    substituir *yourServiceNamespace* pelo nome do seu namespace de serviço
    e *yourIssuerSecret* pela chave que você recuperou anteriormente
    no Portal de Gerenciamento do Azure:

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </bindingExtensions>
          </extensions>
          <services>
             <service name="ProductsServer.ProductsService">
               <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts"
        behaviorConfiguration="products"/>
             </service>
          </services>
          <behaviors>
             <endpointBehaviors>
               <behavior name="products">
                 <transportClientEndpointBehavior>
                    <tokenProvider>
                       <sharedSecret issuerName="owner" issuerSecret="yourIssuerSecret" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

14. Pressione **F6** ou, no menu **Compilar**, clique em **Compilar Solução** para compilar o aplicativo para verificar a precisão de seu trabalho até o momento.

<h2><span class="short-header">CRIAR UM APLICATIVO ASP.NET MVC</span>CRIAR UM APLICATIVO ASP.NET MVC</h2>

Nesta seção você criará um aplicativo ASP.NET simples que irá
exibir os dados recuperados do seu serviço de produto.

### CRIAR O PROJETO

1.  Verifique se o Microsoft Visual Studio 2013 está em execução com privilégios de administrador. Caso contrário, para
    iniciar o Visual Studio com privilégios de administrador, clique com o botão direito do mouse em **Microsoft Visual Studio 2013 (ou no Microsoft Visual Studio Express)** e clique em **Executar como administrador**. O emulador de computação do Azure,
    discutido posteriormente neste guia, exige que o Visual Studio seja
    iniciado com privilégios de administrador.

2.  No Visual Studio, no menu **Arquivo**, clique em **Novo** e, em seguida, clique em **Projeto**.

3.  Em **Modelos Instalados**, sob **Visual C#**, clique em **Aplicativo Web ASP.NET MVC**. Nomeie o projeto **ProductsPortal**. Em seguida,
    clique em **OK**.

    ![][15]

4.  Na lista **Selecionar um modelo**, clique em **MVC** e, em seguida, clique em **OK**.

    ![][16]

5.  No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Modelos**, clique em **Adicionar** e, em seguida, clique em **Classe**. Na caixa **Nome**, digite o nome
    **Product.cs**. Clique em **Adicionar**.

    ![][17]

### MODIFICAR O APLICATIVO WEB

1.  No arquivo Product.cs, no Visual Studio, substitua a definição do namespace existente pelo seguinte código:

        // Declare properties for the products inventory
        namespace ProductsWeb.Models
        {
            public class Product
            {
                public string Id { get; set; }
                public string Name { get; set; }
                public string Quantity { get; set; }
            }
        }

2.  No arquivo HomeController.cs no Visual Studio, substitua a definição do namespace existente pelo código a seguir:

        namespace ProductsWeb.Controllers
        {
            using System.Collections.Generic;
            using System.Web.Mvc;
            using Models;

            public class HomeController : Controller
            {
                // Return a view of the products inventory
                public ActionResult Index(string Identifier, string ProductName)
                {
                    var products = new List<Product> 
                        {new Product {Id = Identifier, Name = ProductName}};
                    return View(products);
                }

            }
        }

3.  No **Gerenciador de Soluções**, expanda Views\Shared:

    ![][18]

4.  Em seguida, clique duas vezes em _Layout.cshtml para abri-lo no editor do Visual Studio.

5.  Altere todas as ocorrências de **Meu aplicativo ASP.NET** para **Produtos da LITWARE**.

6. Remova os links **Página Inicial**, **Sobre** e **Contato**. Exclua o código destacado:

	![][41]

7.  No **Gerenciador de Soluções**, expanda Views\Home:

    ![][20]

8.  Clique duas vezes em Index.cshtml para abri-lo no editor do Visual Studio.
    Substitua todo o conteúdo do arquivo pelo código a seguir:
	
		@model IEnumerable<ProductsWeb.Models.Product>

		@{
    		ViewBag.Title = "Index";
		}

		<h2>Prod Inventory</h2>

		<table>
    		<tr>
        		<th>
            		@Html.DisplayNameFor(model => model.Name)
        		</th>
                <th></th>
        		<th>
            		@Html.DisplayNameFor(model => model.Quantity)
        		</th>
    		</tr>
	
		@foreach (var item in Model) {
    		<tr>
        		<td>
            		@Html.DisplayFor(modelItem => item.Name)
        		</td>
        		<td>
            		@Html.DisplayFor(modelItem => item.Quantity)
        		</td>
    		</tr>	
		}

		</table>


9.  Para verificar a precisão de seu trabalho até o momento, você pode pressionar **F6** ou **Ctrl+Shift+B** para compilar o projeto.


### EXECUTAR O APLICATIVO LOCALMENTE

Execute o aplicativo para verificar se ele funciona.

1.  Certifique-se de que **ProductsPortal** é o projeto ativo. Clique com o botão direito do mouse no **Gerenciador de Soluções** e selecione **Definir como Projeto de Inicialização**
2.  No **Visual Studio**, pressione **F5**.
3.  Seu aplicativo deve aparecer em execução em um navegador:

    ![][21]

    <h2><span class="short-header">IMPLANTAR NO AZURE</span>TORNAR SEU APLICATIVO PRONTO PARA IMPLANTAR NO AZURE</h2>

    Você pode implantar seu aplicativo para um Serviço de Nuvem do Azure ou para um Site do Azure. Para saber mais sobre a diferença entre sites e serviços de nuvem, consulte [Modelos de execução do Azure] [executionmodels]. Para saber como implantar o aplicativo em um Site do Azure, consulte [Implantando um aplicativo Web ASP.NET em um Site do Azure](http://www.windowsazure.com/pt-br/develop/net/tutorials/get-started/). Esta seção contém etapas detalhadas para implantar o aplicativo em um Serviço de Nuvem do Azure.

    Para implantar seu aplicativo em um serviço de nuvem, você adicionará um projeto de implantação de projeto de serviço de nuvem à solução.
    O projeto de implantação contém as informações de configuração necessárias para executar corretamente o aplicativo na nuvem.

    1.  Para tornar seu aplicativo implantável na nuvem, clique com o botão direito do mouse no projeto **ProductsPortal** no **Gerenciador de Soluções** e clique em **Converter** e, em seguida, clique em **Converter em Projeto de Serviço de Nuvem do Azure**.

        ![][22]

    2.  Para testar o aplicativo, pressione **F5**.
    3.  Isso irá iniciar o emulador de computação do Azure. O emulador de computação usará o computador local para emular seu aplicativo em execução no Azure. Você pode confirmar se o emulador foi iniciado examinando a bandeja do sistema:

        ![][23]

    4.  Um navegador ainda exibirá seu aplicativo em execução localmente, e ele será semelhante e funcionará da mesma maneira como na execução anterior como um aplicativo ASP.NET MVC 4 normal.

    <h2><span class="short-header">JUNTAR AS PEÇAS</span>JUNTAR AS PEÇAS</h2>

    A próxima etapa é vincular o servidor de produtos local com o aplicativo ASP.NET MVC.

    1.  Se ainda não estiver aberto, no Visual Studio reabra o projeto **ProductsPortal** que você criou na seção "Criando um aplicativo ASP.NET MVC".

    2.  Semelhante à etapa na seção "Criar um servidor local", adicione o pacote NuGet às referências do projeto. No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** e clique em **Gerenciar Pacotes NuGet**.

    3. Pesquise por "WindowsAzure.ServiceBus" e selecione o item **Brramento de Serviço do Azure**. Conclua a instalação e feche a caixa de diálogo.


    4.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ProductsPortal**, clique em **Adicionar** e em **Item Existente**.

    5.  Navegue até o arquivo **ProductsContract.cs** do projeto de console **ProductsServer**. Clique para realçar ProductsContract.cs. Clique na seta para baixo próxima a **Adicionar** e, em seguida, clique em **Adicionar como Link**.

        ![][24]

    6.  Agora, abra o arquivo **HomeController.cs** no editor do Visual Studio e substitua a definição do namespace pelo código a seguir: Certifique-se de substituir *yourServiceNamespace* pelo nome do namespace do serviço e *yourIssuerSecret* pela chave. Isso permitirá que o cliente chame o serviço local, retornando o resultado da chamada.

            namespace ProductsWeb.Controllers
            {
                using System.Linq;
                using System.ServiceModel;
                using System.Web.Mvc;
                using Microsoft.ServiceBus;
                using Models;
                using ProductsServer;

                public class HomeController : Controller
                {
                    // Declare the channel factory
                    static ChannelFactory<IProductsChannel> channelFactory;

                    static HomeController()
                    {
                        // Create shared secret token credentials for authentication 
                        channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(), 
                            "sb://yourServiceNamespace.servicebus.windows.net/products");
                        channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior { 
                            TokenProvider = TokenProvider.CreateSharedSecretTokenProvider(
                                "owner", "yourIssuerSecret") });
                    }

                    public ActionResult Index()
                    {
                        using (IProductsChannel channel = channelFactory.CreateChannel())
                        {
                            // Return a view of the products inventory
                            return this.View(from prod in channel.GetProducts()
                                             select
                                                 new Product { Id = prod.Id, Name = prod.Name, 
                                                     Quantity = prod.Quantity });
                        }
                    }
                }
            }

    7.  No Gerenciador de Soluções, clique com o botão direito do mouse na solução **ProductsPortal**, clique em **Adicionar** e em **Projeto Existente**.

    8. Navegue até o projeto **ProductsServer** e clique duas vezes no arquivo de solução **ProductsServer.csproj** para adicioná-lo.

    9. No Gerenciador de Soluções, clique com o botão direito do mouse na solução **ProductsPortal** e, em seguida, clique em **Propriedades**.

    10. No lado esquerdo, clique em **Projeto de Inicialização**. No
        lado direito, clique em **Vários projetos de inicialização**. Verifique se
        **ProductsServer**, **ProductsPortal.Azure** e
        **ProductsPortal** aparecem nessa ordem com **Iniciar** definido como a ação de **ProductsServer** e **ProductsPortal.Azure**,
        e **Nenhum** definido como a ação de **ProductsPortal**. Por
        exemplo:

        ![][25]

    11. Ainda na caixa de diálogo Propriedades, clique em **ProjectDependencies** no lado esquerdo.

    12. Na lista suspensa **Projetos**, clique em **ProductsServer**. Verifique se **ProductsPortal** está desmarcado, e **ProductsPortal.Azure** está marcado. Em seguida, clique em **OK**.

        ![][26]

    <h2><span class="short-header">EXECUTAR O APLICATIVO</span>EXECUTAR O APLICATIVO</h2>

    1. No menu **Arquivo** no Visual Studio, clique em **Salvar Tudo**.

    2.  Pressione **F5** para compilar e executar o aplicativo. O servidor local (o aplicativo de console **ProductsServer**) deve ser iniciado
        primeiro e, em seguida, o aplicativo**ProductsWeb** deve ser iniciado em uma
        janela do navegador, conforme mostrado na captura de tela abaixo. Desta vez, você
        verá que o inventário de produtos lista os dados recuperados do
        sistema local de serviço de produtos.

        ![][1]

    <h2><span class="short-header">IMPLANTAR O APLICATIVO</span>IMPLANTAR O APLICATIVO NO AZURE</h2>

    1.  Clique com o botão direito do mouse no projeto **ProductsPortal** no **Gerenciador
        de Soluções** e clique em **Publicar no Azure**.

    2.  Talvez você precise entrar para ver todas as suas assinaturas.

        Clique em **Entrar para ver mais assinaturas**:

        ![][27]

    3.  Entre usando sua conta da Microsoft.


    8.  Clique em **Avançar**. Se sua assinatura ainda não contêm nenhum serviço hospedado, você será solicitado a criar um. O serviço hospedado
        atua como um contêiner para seu aplicativo em sua assinatura do
        Azure. Digite um nome que identifique seu
        aplicativo e escolha a região para a qual o aplicativo
        deve ser otimizado. (Você pode esperar um tempo de carregamento mais rápido para os
        usuários que o estão acessando nesta região).

        ![][32]

    9.  Selecione o serviço hospedado no qual você deseja publicar o aplicativo. Mantenha os padrões conforme mostrado abaixo para o
        restante das configurações. Clique em **Avançar**.

        ![][33]

    10. Na última página, clique em **Publicar** para iniciar o processo de implantação:

        ![][34]

        Isso levará aproximadamente cinco a sete minutos. Como esta é a
        primeira vez que você está publicando, o Azure provisiona
        uma VM (Máquina Virtual), executa a proteção de segurança, cria uma função web na VM para hospedar o aplicativo, implanta seu código nessa
        função web e, finalmente, configura o balanceador de carga e a rede para que seu aplicativo fique disponível para o público.

    11. Enquanto a publicação estiver em andamento, você poderá monitorar a
        atividade na janela **Log de Atividades do Azure**, que 
        normalmente está encaixada na parte inferior do Visual Studio ou do Visual Web
        Developer:

        ![][35]

    12. Quando a implantação for concluída, você poderá exibir seu site clicando no link **URL do Site** na janela de monitoramento.

        ![][36]

        Seu site depende de seu servidor local, portanto,
       você deve executar o aplicativo **ProductsServer** localmente para o site funcionar corretamente. À medida que você executar solicitações no site da nuvem, você verá solicitações chegando no seu aplicativo de console
        local, conforme indicado pela saída de "GetProducts called" exibida na captura de tela a seguir.

        ![][37]

Para saber mais sobre a diferença entre Sites e Serviços de Nuvem, consulte [Modelos de execução do Azure][executionmodels].

<h2><span class="short-header">EXCLUIR O APLICATIVO</span>PARAR E EXCLUIR SEU APLICATIVO</h2>

Depois de implantar seu aplicativo, você talvez queira desativá-lo para que
possa criar e implantar outros aplicativos com as 750 horas/mês (31 dias/mês) gratuitas do tempo do servidor.

O Azure cobra pelas instâncias de função web por hora de acordo com o tempo consumido do servidor. O tempo do servidor é consumido assim que aplicativo é implantado,
mesmo se as instâncias não estiverem sendo executadas e estiverem no estado parado.
Uma conta gratuita inclui 750 horas/mês (31 dias/mês) de tempo de servidor dedicado
da máquina virtual para hospedar essas instâncias da função web.

As etapas a seguir mostram como parar e excluir o aplicativo.

1.  Faça logon no [Portal de Gerenciamento do Azure], clique em Serviços de Nuvem e, em seguida, clique no nome do serviço.

2.  Clique na guia **Painel** e, em seguida, clique em **Parar** para suspender temporariamente o aplicativo. Você poderá
        iniciá-lo novamente apenas clicando em Iniciar. Clique em **Excluir** para remover completamente seu aplicativo do Azure sem possibilidade de restaurá-lo.

	![][43]

<h2><a name="nextsteps"></a><span class="short-header">Próximas etapas</span>Próximas etapas</h2>  

Para obter mais informações sobre o Brramento de Serviço, consulte os seguintes recursos:  
  
* [Brramento de Serviço do Azure][sbmsdn]  
* [Tutoriais de Brramento de Serviço][sbwacom]  
* [Como usar as filas do Brramento de Serviço][sbwacomqhowto]  


  [0]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Obter as ferramentas e o SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png
  
  
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png
  
  
  
  [Usando o pacote NuGet do Brramento de Serviço]: http://go.microsoft.com/fwlink/?LinkId=234589
  [10]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [11]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [12]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [13]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [15]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [18]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg
  
  [20]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [21]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [22]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [23]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [24]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png
  
  
  [30]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-36.png
  [31]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-37.png
  [32]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [33]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [34]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [35]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [36]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [39]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [40]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [41]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [42]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [43]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [44]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/VSProperties.png

  [sbmsdn]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee732537.aspx  
  [sbwacom]: /pt-br/manage/services/service-bus/  
  [sbwacomqhowto]: /pt-br/develop/net/how-to-guides/service-bus-queues/
  [executionmodels]: http://www.windowsazure.com/pt-br/develop/net/fundamentals/compute/

