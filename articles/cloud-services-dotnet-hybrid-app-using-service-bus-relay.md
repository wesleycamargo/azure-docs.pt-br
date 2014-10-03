<properties linkid="dev-net-tutorials-hybrid-solution" urlDisplayName="Hybrid Application" pageTitle="Hybrid On-Premises/ Cloud Application (.NET) - Azure" metaKeywords="Azure Service Bus tutorial,hybrid .NET" description="Learn how to create a .NET On-Premises/Cloud Hybrid Application Using the Azure Service Bus Relay." metaCanonical="" services="service-bus" documentationCenter=".NET" title=".NET On-Premises/Cloud Hybrid Application Using Service Bus Relay" authors="sethm" solutions="" manager="dwrede" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Aplicativo .NET híbrido local/na nuvem usando a Retransmissão do Barramento de Serviço

## <span class="short-header">INTRODUÇÃO</span>

Desenvolver aplicativos híbridos de nuvem com o Azure é fácil usando o
Visual Studio 2013 e o SDK do Azure gratuito para .NET. Este guia
pressupõe que você não tem experiência anterior com o Windows Azure. Em menos de
30 minutos, você terá um aplicativo que usa vários recursos do Windows Azure
em funcionamento na nuvem.

Você aprenderá a:

-   Criar ou adaptar um serviço Web existente para consumo por uma
    solução de Web.
-   Usar a Retransmissão do Barramento de Serviço para compartilhar dados entre
    um aplicativo do Azure e um serviço Web hospedado em outro lugar.

[WACOM.INCLUDE [create-account-note][]]

### COMO A RETRANSMISSÃO DO BARRAMENTO DE SERVIÇO AJUDA COM SOLUÇÕES HÍBRIDAS

As soluções de negócios geralmente são compostas por uma combinação de código
personalizado escrito para lidar com os requisitos de negócios novos e exclusivos e a funcionalidade existente
fornecida pelas soluções e sistemas que já estão
estabelecidos.

Os arquitetos de solução estão começando a utilizar a nuvem para obter um manuseio mais fácil de
requisitos de escala e custos operacionais mais baixos. Ao fazer isso, eles descobrem
 serviços ativos existentes que gostariam de aproveitar, como blocos de construção
para suas soluções estão dentro do firewall corporativo e fora de alcance fácil para acesso
 pela solução de nuvem. Muitos serviços internos não são
construídos ou hospedados de forma que possam ser facilmente expostos na
borda da rede corporativa.

A *Retransmissão do Barramento de Serviço* foi desenvolvida para o caso de utilização dos
serviços Web do Windows Communication Foundation (WCF) existentes e para tornar esses
serviços acessíveis com segurança para soluções que residem fora do
perímetro corporativo, sem exigir alterações intrusivas na infraestrutura da
rede corporativa. Tais serviços de retransmissão do Barramento de Serviço ainda estão hospedados
dentro de seu ambiente de desenvolvimento, mas delegam a escuta para
sessões de entrada e de solicitações para o Barramento de Serviço hospedado na nuvem. O
 Barramento de Serviço também protege esses serviços de acesso sem autorização
usando o Controle de Acesso do Active Directory do Azure.

### O CENÁRIO DA SOLUÇÃO

Neste tutorial, você irá criar um site ASP.NET MVC 4 que lhe permitirá
 ver uma lista de produtos na página de inventário de produtos.

![][0]

O tutorial assume que você tem informações sobre produtos em um
sistema local existente e utiliza Retransmissão do Barramento de Serviço para acessar esse
sistema. Isso é simulado por um serviço Web executado em um
aplicativo de console simples e com o suporte de um conjunto de produtos na memória. Você
poderá executar esse aplicativo de console em seu próprio computador e
implantar a função web no Azure. Fazendo isso, você verá como
a função de web em execução no datacenter do Azure realmente será chamada
em seu computador, embora seu computador certamente
resida atrás de pelo menos um firewall e de uma camada de
NAT (conversão de endereços de rede).

A captura de tela da página inicial do aplicativo Web completo é mostrada abaixo.

![][1]

## <span class="short-header">CONFIGURAR O AMBIENTE</span>

Antes de começar a desenvolver seu aplicativo do Azure, é necessário
obter as ferramentas e configurar seu ambiente de desenvolvimento.

1.  Para instalar o SDK do Azure para .Net, clique no botão abaixo:

    [Obter as ferramentas e o SDK][]

2.  Clique em **Instalar o SDK**.

3.  Escolha o link para a versão do Visual Studio que você está usando. As etapas deste tutorial usam o Visual Studio 2013:

    ![][2]

4.  Quando solicitado a executar ou salvar o **WindowsAzureSDKForNet.exe**, clique em
    **Executar**:

    ![][3]

5.  No Web Platform Installer, clique em **Instalar** e prossiga com a instalação:

    ![][4]

6.  Quando a instalação for concluída, você terá tudo o que é
    necessário para iniciar o desenvolvimento. O SDK inclui ferramentas que permitem que você
    desenvolva facilmente aplicativos do Azure no Visual Studio. Se você
    não tiver o Visual Studio instalado, ele também instalará o
    Visual Studio Express gratuito.

## <span class="short-header">CRIAR UM NAMESPACE</span>

Para começar a usar os recursos de Barramento de Serviço no Azure, você deve primeiro
criar um namespace de serviço. Um namespace de serviço fornece um
contêiner de controle para endereçamento dos recursos do Barramento de Serviço em seu aplicativo.

Você pode gerenciar namespaces e entidades de mensagens do Brramento de Serviço usando o [Portal de Gerenciamento do Azure][] ou o Gerenciador de Servidores do Visual Studio, mas só pode criar novos namespaces de dentro do portal.

### Criar um namespace de serviço usando o portal:

1.  Faça logon no [Portal de Gerenciamento do Azure][].

2.  No painel de navegação esquerdo do Portal de Gerenciamento, clique em
    **Service Bus**.

3.  No painel inferior do Portal de Gerenciamento, clique em **Criar**.   
    ![][5]

4.  No diálogo **Adicionar um novo namespace**, insira um nome de namespace.
    O sistema imediatamente verifica se o nome está disponível.   
    ![][6]

5.  Depois de verificar se o nome do namespace está disponível, escolha o
    país ou a região em que o namespace deve ser hospedado (certifique-se
    de usar o mesmo país/região em que você está implantando seus
    recursos de computação).

    IMPORTANTE: Selecione a **mesma região** que você pretende escolher para
    implantar seu aplicativo. Isso lhe dará o melhor desempenho.

6.  Clique na marca de seleção. Agora, o sistema cria o seu namespace
    de serviço e o habilita. Talvez você precise aguardar vários minutos, enquanto
    o sistema provisiona recursos para sua conta.

    ![][7]

O namespace criado aparecerá no Portal de Gerenciamento e
demora algum tempo para ser ativado. Aguarde até que o status seja **Ativo** antes de
prosseguir.

## <span class="short-header"></span>OBTER AS CREDENCIAIS DE GERENCIAMENTO PADRÃO PARA O NAMESPACE

Para executar operações de gerenciamento, como a criação de uma fila, no
novo namespace, é necessário obter as credenciais de gerenciamento para o
namespace.

1.  Na janela principal, clique no nome do seu namespace de serviço.

    ![][8]

2.  Clique em **Informações de conexão**.

    ![][9]

3.  No painel **Informações de conexão de acesso**, localize as entradas **Emissor Padrão** e **Chave Padrão**.

4.  Anote a chave ou copie-a na área de transferência.

### Gerencie um namespace de serviço com o Gerenciador de Servidores do Visual Studio:

Para gerenciar um namespace e obter informações de conexão usando o Visual Studio em vez do Portal de Gerenciamento, siga o procedimento descrito [aqui][], na seção intitulada **Para conectar-se ao Azure no Visual Studio**. Quando você entra no Azure, o nó **Brramento de Serviço** na árvore **Microsoft Azure** no Gerenciador de Servidores é automaticamente populado com os namespaces que você criou. Clique com o botão direito do mouse em qualquer namespace e, em seguida, clique em **Propriedades** para ver a cadeia de conexão e outros metadados associados a esse namespace exibido no painel **Propriedades** do Visual Studio.

![][10]

Anote o valor da **SharedAccessKey** ou copie-a na área de transferência.

## <span class="short-header"></span>CRIAR UM SERVIDOR LOCAL

Primeiro você irá criar um sistema de catálogo de produtos (fictício) local. Será
muito simples, você pode ver isso como uma representação de um
sistema de catálogo de produtos real local com uma superfície de serviço completa que
estamos tentando integrar.

Este projeto será iniciado como um aplicativo de console do Visual Studio. O
projeto utiliza o pacote NuGet de Barramento de Serviço par incluir
as bibliotecas de Barramento de Serviço e as definições de configuração. A extensão do Visual Studio do NuGet
facilita a instalação e a atualização de bibliotecas e ferramentas no Visual Studio
e no Visual Studio Express. O pacote NuGet de Barramento de Serviço é a maneira mais fácil
de obter a API de Barramento de Serviço e configurar seu aplicativo com
todas as dependências de Barramento de Serviço. Para obter mais detalhes sobre como usar o NuGet e
o pacote de Barramento de Serviço, consulte [Utilizando o pacote NuGet de Barramento de Serviço][].

### CRIAR O PROJETO

1.  Utilizando os privilégios de administrador, inicialize o Microsoft Visual
    Studio 2013 ou o Microsoft Visual Studio Express. Para
    iniciar o Visual Studio com os privilégios de administrador, clique com o botão direito em
    **Microsoft Visual Studio 2013 (ou Microsoft Visual Studio Express)** e, em seguida, clique em **Executar como administrador**.
2.  No Visual Studio, no menu **Arquivo**, clique em **Novo** e, em seguida,
    clique em **Projeto**.

    ![][11]

3.  Em **Modelos instalados**, abaixo de**Visual C\#**, clique em **Aplicativo de
    console**. Na caixa **Nome**, digite o nome
    **ProductsServer**:

    ![][12]

4.  Clique em **OK** para criar o projeto **ProductsServer**.

5.  Em **Gerenciador de Soluções**, clique com o botão direito em **ProductsServer** e, em seguida,
    clique em **Propriedades**.
6.  Clique na guia **Aplicativo** à esquerda e, em seguida, certifique-se de que o **.NET
    Framework 4** ou o **.NET Framework 4.5** aparece na **Estrutura de destino:** lista suspensa. Caso contrário, selecione-o na lista suspensa e, em seguida, clique em **Sim**
     quando for solicitado para recarregar o projeto.

    ![][13]

7.  Se você já tiver instalado o Gerenciador de Pacotes NuGet para Visual Studio, vá para a próxima etapa. Caso contrário, visite [NuGet][] e clique em [Instalar o NuGet (a página pode estar em inglês)][]. Siga os prompts para instalar o Gerenciador de Pacotes NuGet e, em seguida, reinicie o Visual Studio.

8.  No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Referências** e, em seguida, clique em
    **Gerenciar Pacotes NuGet**...
9.  Na coluna à esquerda da caixa de diálogo NuGet, clique em **Online**.

10. Na coluna à direita, clique na caixa **Pesquisar**, digite "**WindowsAzure**" e selecione o item Barramento de Serviço do **Windows
    Azure**. Clique em **Instalar** para concluir a
    instalação e, em seguida, feche essa caixa de diálogo.

    ![][14]

    Observe que os assemblies do cliente necessários agora são referenciados.

11. Adicione uma nova classe para seu contrato de produto. No **Gerenciador de Soluções**,
    clique com o botão direito no projeto **ProductsServer** e clique em **Adicionar** e, em seguida clique em
    **Classe**.

    ![][15]

12. Na caixa **Nome**, digite o nome **ProductsContract.cs**. Em seguida,
    clique em **Adicionar**.
13. Em **ProductsContract.cs**, substitua a definição do namespace pelo
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

14. Em Program.cs, substitua a definição do namespace pelo
    código a seguir, que adiciona o serviço de perfil e o host para ele:

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface
            class ProductsService : IProducts
            {

                // Populate array of products for display on Website
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

15. Em **Gerenciador de Soluções**, clique duas vezes no arquivo **app.config** para
    abri-lo no editor do **Visual Studio**. Substitua o conteúdo de
    **\<system.ServiceModel\>** pelo código XML a seguir. Certifique-se de
    substituir *yourServiceNamespace* pelo nome de seu
    namespace de serviço e *yourIssuerSecret* pela chave que você recuperou anteriormente
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

16. Pressione **F6** ou, no menu **Compilar**, clique em **Compilar Solução** para compilar o aplicativo para verificar a precisão de seu trabalho até o momento.

## <span class="short-header"></span>CRIAR UM APLICATIVO ASP.NET MVC

Nesta seção você criará um aplicativo ASP.NET simples que exibirá
os dados recuperados do seu serviço de produto.

### CRIAR O PROJETO

1.  Verifique se o Microsoft Visual Studio 2013 está sendo executado com os privilégios de administrador. Se não estiver,
    inicie o Visual Studio com privilégios de administrador, clique com o botão direito em
    **Microsoft Visual Studio 2013 (ou Microsoft Visual Studio Express)** e, em seguida, clique em **Executar como administrador**. O emulador
    de computação do Windows Azure, discutido posteriormente neste guia, exige que o
    Visual Studio seja iniciado com privilégios de administrador.

2.  No Visual Studio, no menu **Arquivo**, clique em **Novo** e, em seguida,
    clique em **Projeto**.

3.  Em **Modelos instalados**, em **Visual C\#**, clique em **Aplicativo Web ASP.NET**. Nomeie o projeto **ProductsPortal**. Em seguida
    clique em **OK**.

    ![][16]

4.  Na lista **Selecione um modelo**, clique em **MVC** e,
     em seguida, clique em **OK**.

    ![][17]

5.  Em **Gerenciador de Soluções**, clique com o botão direito em **Modelos** e clique em **Adicionar** e,
     em seguida, clique em **Classe**. Na caixa **Nome**, digite o nome
    **Product.cs**. Clique em **Adicionar**.

    ![][18]

### MODIFICAR O APLICATIVO WEB

1.  No arquivo Product.cs no Visual Studio, substitua a definição do
    namespace existente com o código a seguir:

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

2.  No arquivo HomeController.cs no Visual Studio, substitua a definição do
    namespace existente pelo código a seguir:

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

3.  Em **Gerenciador de Soluções**, expanda Views\\Shared:

    ![][19]

4.  Em seguida, clique duas vezes em \_Layout.cshtml para abri-lo no editor do Visual Studio.

5.  Altere todas as ocorrências de **Meu aplicativo ASP.NET** para **Produtos da LITWARE**.

6.  Remova os links **Página Inicial**, **Sobre** e **Contato**. Exclua o código destacado:

    ![][20]

7.  No **Gerenciador de Soluções**, expanda Views\\Shared:

    ![][21]

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

9.  Para verificar a precisão de seu trabalho até o momento, você pode pressionar **F6** ou
    **Ctrl+Shift+B** para compilar o projeto.

### EXECUTAR O APLICATIVO LOCALMENTE

Execute o aplicativo para verificar se ele funciona.

1.  Certifique-se de que **ProductsPortal** é o projeto ativo. Clique com o botão direito no
    nome do projeto em **Gerenciador de Soluções** e selecione **Definir como
     Projeto de inicialização**
2.  No **Visual Studio**, pressione **F5**.
3.  Seu aplicativo deve aparecer em execução em um navegador:

    ![][22]

    ## <span class="short-header"></span>TORNE SEU APLICATIVO PARA IMPLANTAR NO AZURE

    É possível implantar seu aplicativo em um serviço de nuvem do Azure ou em um site do Azure. Para saber mais sobre a diferença entre sites e serviços de nuvem, consulte [Modelos de execução do Azure][]. Para saber como implantar o aplicativo em um site do Azure, consulte [Implantando um aplicativo Web ASP.NET em um site do Azure][]. Esta seção contém etapas detalhadas para implantar o aplicativo em um Serviço de Nuvem do Azure.

    Para implantar seu aplicativo em um serviço de nuvem, você adicionará um projeto de implantação de projeto de serviço de nuvem à solução.
    O projeto de implantação contém as informações
    de configuração necessárias para executar corretamente o aplicativo
    na nuvem.

    1.  Para tornar seu aplicativo implantável na nuvem, clique com o botão direito do mouse
        no projeto **ProductsPortal** no **Gerenciador de Soluções**,
        clique em **Converter** e, em seguida, clique em **Converter para projeto de serviço de nuvem do Azure**.

        ![][23]

    2.  Para testar seu aplicativo, pressione **F5**.
    3.  Isso iniciará o emulador de computação do Azure. O emulador
         de computação usará o computador local para emular seu aplicativo
        em execução no Azure. É possível confirmar se o emulador foi
        iniciado examinando a bandeja do sistema:

        ![][24]

    4.  Um navegador ainda exibirá seu aplicativo em execução localmente,
        e ele será semelhante e funcionará da mesma maneira como na execução
        anterior como um aplicativo ASP.NET MVC 4 normal.

    ## <span class="short-header"></span>JUNTAR AS PEÇAS

    A próxima etapa é vincular o servidor de produtos local com o
    aplicativo ASP.NET MVC.

    1.  Se ele ainda não estiver aberto, no Visual Studio reabra o projeto
        **ProductsPortal** criado na sessão “Criando um aplicativo
        ASP.NET MVC Application".

    2.  Semelhante à etapa na seção "Criar um servidor local"
        , adicione o pacote NuGet às referências do projeto. No
        Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** e, em seguida clique em
        **Gerenciar pacotes NuGet**.

    3.  Pesquise por "WindowsAzure.ServiceBus" e selecione o item **Barramento de Serviço do Windows
        Azure**. Conclua a instalação e
        feche a caixa de diálogo.

    4.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ProductsPortal**
        , depois clique em **Adicionar** e, em seguida em **Item existente**.

    5.  Navegue até o arquivo **ProductsContract.cs** do projeto de console
        **ProductsServer**. Clique para realçar
        ProductsContract.cs. Clique na seta para baixo próxima a **Adicionar** e, em seguida,
        clique em **Adicionar como Link**.

        ![][25]

    6.  Agora abra o arquivo **HomeController.cs** no editor do Visual Studio
        e substitua a definição do namespace com o
        código a seguir. Certifique-se de substituir *yourServiceNamespace* pelo nome de
        seu namespace de serviço e *yourIssuerSecret* pela sua chave.
        Isso habilitará o cliente a chamar o serviço local
        retornando o resultado da chamada.

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

    7.  No **Gerenciador de Soluções**,
        clique com o botão direito do mouse na solução **ProductsPortal**, clique em **Adicionar** e em <strong>Projeto Existente<strong>.

    8.  Navegue até o projeto **ProductsServer** e clique duas vezes
        no **arquivo de solução ProductsServer.csproj** para adicioná-lo.

    9.  No Gerenciador de Soluções, clique com o botão direito do mouse na solução **ProductsPortal**
         e, em seguida, clique em **Propriedades**.

    10. No lado esquerdo, clique em **Projeto de Inicialização**. No
        lado direito, clique em **Vários projetos de inicialização**. Verifique se
        **ProductsServer**, **ProductsPortal.Azure** e
        **ProductsPortal** apareceram, nessa ordem, com a opção **Iniciar** definida como
        a ação para **ProductsServer** e **ProductsPortal.Azure**,
         e **Nenhuma** definida como a ação para **ProductsPortal**. Por
        exemplo:

        ![][26]

    11. Ainda na caixa de diálogo Propriedades, clique em **ProjectDependencies** no
        lado esquerdo.

    12. Na lista suspensa **Projetos**, clique em
        **ProductsServer**. Certifique-se de que **ProductsPortal** está desmarcado
        e **ProductsPortal.Azure** está marcado. Em seguida, clique em **OK**.

        ![][27]

    ## <span class="short-header"></span>EXECUTAR O APLICATIVO

    1.  No menu **Arquivo** no Visual Studio, clique em **Salvar Tudo**.

    2.  Pressione **F5** para compilar e executar o aplicativo. O servidor
        local (o aplicativo do console **ProductsServer**) deve iniciar
        primeiro, em seguida, o aplicativo **ProductsWeb** deve iniciar em uma
        janela do navegador, como mostrado na captura de tela abaixo. Desta vez, você
        verá que o inventário de produtos lista dados recuperados do
        sistema local de serviço de produto.

        ![][1]

    ## <span class="short-header"></span>IMPLANTAR SEU APLICATIVO NO AZURE

    1.  Clique com o botão direito do mouse no projeto **ProductsPortal** no **Gerenciador de
        Soluções** e depois clique em **Publicar no Azure**.

    2.  Talvez seja necessário entrar para ver todas as suas assinaturas.

        Clique em **Entrar para ver mais assinaturas**:

        ![][28]

    3.  Entre utilizando sua conta da Microsoft.

    4.  Clique em **Próximo**. Se sua assinatura ainda não contém nenhum serviço
        hospedado, será solicitada a criação de um. O serviço hospedado
        atua como um contêiner para seu aplicativo em sua assinatura do Windows
        Azure. Insira um nome que identifique seu
        aplicativo e escolha a região para a qual o aplicativo
        deve ser otimizado. (Você pode esperar um tempo de carregamento mais rápido para
        os usuários que o estão acessando nesta região).

        ![][29]

    5.  Selecione o serviço hospedado no qual você deseja publicar
        seu aplicativo. Mantenha os padrões conforme mostrado abaixo para o
        restante das configurações. Clique em **Avançar**.

        ![][30]

    6.  Na última página, clique em **Publicar** para iniciar o processo de
        implantação:

        ![][31]

        Isso levará aproximadamente cinco a sete minutos. Como esta é a
        primeira vez que você está publicando, o Azure provisiona uma
        máquina virtual (VM), executa a proteção de segurança, cria uma função de
         Web no computador virtual para hospedar seu aplicativo, implanta seu código
         para a função de Web e, finalmente, configura o balanceador de carga e
        a rede para que seu aplicativo fique disponível para o público.

    7.  Enquanto a publicação estiver em andamento é possível monitorar a
        atividade na janela **Log de atividades do Azure**, que
        normalmente é encaixada na parte inferior do Visual Studio ou do Visual Web
        Developer:

        ![][32]

    8.  Quando a implantação for concluída, você poderá exibir seu site
        clicando no link **URL do site** na janela de monitoramento.

        ![][1]

        Seu site depende de seu servidor local, portanto, você deve
        executar o aplicativo **ProductsServer** localmente para o site
        funcionar corretamente. À medida que você executar solicitações no site de nuvem,
        verá solicitações chegando no seu aplicativo de console local
        conforme indicado pela saída de "GetProducts called"
        exibida na captura de tela a seguir.

        ![][33]

Para saber mais sobre a diferença entre sites e serviços de nuvem, consulte [Modelos de execução do Azure][].

## <span class="short-header"></span>PARAR E EXCLUIR O APLICATIVO

Depois de implantar seu aplicativo, você talvez queira desabilitá-lo para que possa
criar e implantar outros aplicativos com as 750
horas/mês (31 dias/mês) gratuitas do tempo do servidor.

O Azure cobra as instâncias de função de Web por hora de acordo com o tempo
de servidor consumido. O tempo de servidor é consumido quando seu aplicativo é implantado,
mesmo se as instâncias não estiverem sendo executadas e estiverem no estado parado.
Uma conta gratuita inclui 750 horas/mês (31 dias/mês) de tempo dedicado
de servidor de máquina virtual para hospedagem dessas instâncias de função de Web.

As etapas a seguir mostram como parar e excluir o
aplicativo.

1.  Faça logon no [Portal de Gerenciamento do Azure][],
     clique em Serviços de Nuvem e, em seguida, clique no nome do serviço.

2.  Clique na guia **Painel** e, em seguida, clique em **Parar** para suspender temporariamente o aplicativo. Você poderá
     iniciá-lo novamente apenas clicando em Iniciar. Clique em **Excluir** para remover completamente seu aplicativo do Azure
     sem possibilidade de restaurá-lo.

    ![][34]

## <a name="nextsteps"></a><span class="short-header"></span>Próximas etapas

Para obter mais informações sobre o Brramento de Serviço, consulte os seguintes recursos:

-   [Service Bus do Azure][]
-   [Como é o Barramento de Serviço][]
-   [Como usar as filas do Service Bus][]

  [create-account-note]: ../includes/create-account-note.md
 
  [0]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Obter as ferramentas e o SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [4]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png
  [7]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [8]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [9]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [aqui]: http://http://msdn.microsoft.com/en-us/library/windowsazure/ff687127.aspx
  [10]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/VSProperties.png
  [Utilizando o pacote NuGet de Barramento de Serviço]: http://go.microsoft.com/fwlink/?LinkId=234589
  [11]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [12]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [NuGet]: http://nuget.org
  [Instalar o NuGet (a página pode estar em inglês)]: http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
  [14]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [16]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [17]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [18]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [19]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg
  [20]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [21]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [22]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [Modelos de execução do Azure]: http://www.windowsazure.com/en-us/develop/net/fundamentals/compute/
  [Implantando um aplicativo Web ASP.NET em um site do Azure]: http://www.windowsazure.com/en-us/develop/net/tutorials/get-started/
  [23]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [24]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [25]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [26]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [27]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [28]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png
  [29]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [30]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [31]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [32]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [33]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [34]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [Service Bus do Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/ee732537.aspx
  [Como é o Barramento de Serviço]: /en-us/documentation/services/service-bus/
  [Como usar as filas do Service Bus]: /en-us/develop/net/how-to-guides/service-bus-queues/
