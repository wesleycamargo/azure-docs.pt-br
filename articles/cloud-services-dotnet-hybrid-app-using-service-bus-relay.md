<properties 
	pageTitle="Aplicativo local/na nuvem híbrido (.NET) - Azure" 
	description="Aprenda a criar um aplicativo .NET local/na nuvem híbrida usando a Retransmissão do Barramento de Serviço do Azure." 
	services="service-bus" 
	documentationCenter=".net" 
	authors="sethmanheim" 
	manager="timlt" 
	editor="mattshel"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="03/17/2015" 
	ms.author="sethm"/>






#Aplicativo .NET híbrido local/na nuvem usando a Retransmissão do Barramento de Serviço

##Introdução

É fácil desenvolver aplicativos de nuvem híbrida com o Microsoft Azure usando o
Visual Studio 2013 e o SDK do Azure para .NET gratuito. Este guia pressupõe que você não tem experiência anterior com o Azure. Em menos de 30 minutos, você terá um aplicativo que usa vários recursos do Azure em funcionamento na nuvem.

Você aprenderá:

-   Criar ou adaptar um serviço Web existente para consumo por uma
    solução da Web.
-   Como usar a retransmissão do Barramento de Serviço do Azure para compartilhar dados entre
    um aplicativo do Azure e um serviço Web hospedado em outro lugar.

[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]

##Como a retransmissão do Barramento de Serviço ajuda com soluções híbridas

As soluções de negócios geralmente são compostas por uma combinação de código personalizado escrito para lidar com os requisitos de negócios novos e exclusivos e a funcionalidade existente fornecida pelas soluções e sistemas que já estão estabelecidos.

Os arquitetos de solução estão começando a utilizar a nuvem para obter um manuseio mais fácil de requisitos de escala e custos operacionais mais baixos. Ao fazer isso, eles descobrem serviços ativos existentes que gostariam de aproveitar, como blocos de construção para suas soluções estão dentro do firewall corporativo e fora de alcance fácil para acesso pela solução de nuvem. Muitos serviços internos não são construídos ou hospedados de forma que possam ser facilmente expostos na borda da rede corporativa.

O *Service Bus Relay* foi projetado para o caso de uso de obter
Serviços Web do WCF (Windows Communication Foundation) existentes e tornar esses serviços acessíveis com segurança para soluções que residem fora do perímetro corporativo, sem exigir alterações intrusivas na infraestrutura da rede corporativa. Tais serviços de retransmissão do Barramento de Serviço ainda são hospedados dentro de seu ambiente existente, mas delegam a escuta para sessões de entrada e solicitações ao Barramento de Serviço hospedado na nuvem. O Barramento de Serviço também protege esses serviços contra acesso não autorizado usando a autenticação [SAS (Assinatura de Acesso Compartilhado)](https://msdn.microsoft.com/library/dn170478.aspx).

##O cenário da solução

Neste tutorial, você criará um site ASP.NET MVC 4 que o habilitará a ver uma lista de produtos na página de inventário de produtos.

![][0]

O tutorial assume que você tem informações sobre produtos em um sistema local existente e utiliza Retransmissão do Barramento de Serviço para acessar esse sistema. Isso é simulado por um serviço Web executado em um aplicativo de console simples e com o suporte de um conjunto de produtos na memória. Você poderá executar esse aplicativo de console em seu próprio computador e implantar a função web no Azure. Fazendo isso, você verá como a função de web em execução no datacenter do Azure realmente será chamada em seu computador, embora seu computador certamente resida atrás de pelo menos um firewall e de uma camada de NAT (conversão de endereços de rede).

A captura de tela da página inicial do aplicativo Web completo é mostrada abaixo.

![][1]

##Configurar o ambiente de desenvolvimento

Para poder começar a desenvolver seu aplicativo do Azure, baixe as ferramentas e configure seu ambiente de desenvolvimento.

1.  Para instalar o SDK do Azure para .NET, clique no link a seguir:

    [Obter as ferramentas e o SDK][]

2. 	Clique em **Instalar o SDK** para a versão do Visual Studio que você está usando. As etapas deste tutorial usam o Visual Studio 2013:

	![][42]

4.  Quando for solicitado a executar ou salvar o instalador, clique em **Executar**:

    ![][2]

5.  No **Web Platform Installer,** clique em **Instalar** e continue a instalação:

    ![][3]

6.  Assim que a instalação estiver concluída, você terá tudo
    que é necessário para iniciar o desenvolvimento. O SDK inclui ferramentas que permitem a você
    desenvolver facilmente aplicativos do Azure no Visual Studio. Se você
    não tiver o Visual Studio instalado, o SDK também instalará o
    Visual Studio Express, que é gratuito.

## Criar um namespace de serviço

Para começar a usar os recursos de Barramento de Serviço no Azure, você deve primeiro criar um namespace de serviço. Um namespace fornece um contêiner de escopo para endereçar recursos do barramento de serviço dentro de seu aplicativo. 

Você pode gerenciar namespaces e entidades de mensagens do Barramento de Serviço usando o [Portal de Gerenciamento do Azure][] ou o Gerenciador de Servidores do Visual Studio, mas só pode criar novos namespaces de dentro do portal.

### Para criar um namespace usando o portal:

1.  Faça logon no [Portal de Gerenciamento do Azure][].

2.  No painel de navegação esquerdo do portal de gerenciamento, clique em
    **Barramento de Serviço**.

3.  No painel inferior do portal de gerenciamento, clique em **Criar**.   
    ![][5]

4.  Na caixa de diálogo **Adicionar um novo namespace**, digite um nome de namespace.
    O sistema imediatamente verifica para ver se o nome está disponível.   
    ![][6]

5.  Depois de verificar se o nome do namespace está disponível, escolha o
    país ou região no qual o namespace deve ser hospedado (certifique-se de
    usar o mesmo país/região em que você está implantando os
    recursos de computação).

    IMPORTANTE: Selecione a **mesma região** que você pretende selecionar para
    implantar seu aplicativo. Isso lhe dará o melhor desempenho.

6.	Deixe os outros campos na caixa de diálogo com seus valores padrão (**Mensagens** e **Camada padrão**), em seguida, clique na marca de seleção. Agora, o sistema cria o seu namespace e o habilita. Talvez você precise aguardar vários minutos, enquanto o sistema provisiona recursos para sua conta.

	![][38]

O namespace que você criou aparecerá no portal de gerenciamento, no entanto, ele pode levar um tempo para ser ativado. Aguarde até que o status seja **Ativo** antes de prosseguir.

## Obter as credenciais de gerenciamento padrão do namespace

Para executar operações de gerenciamento no novo namespace, como a criação de entidades de mensagens, você precisa obter as credenciais para o namespace.

1.  Na janela principal, clique no nome do seu namespace de serviço.   

	![][39]
  

2.  Clique em **Informações de Conexão**.   

	![][40]


3.  No painel **Acessar as informações de conexão**, encontre a cadeia de conexão que contém a chave SAS e o nome da chave.   

	![][45]
    

4.  Anote estas credenciais ou copie-as para a área de transferência.

## Gerencie um namespace de serviço com o Gerenciador de Servidores do Visual Studio

Para gerenciar um namespace e obter informações de conexão usando o Visual Studio em vez do Portal de Gerenciamento, siga o procedimento descrito [aqui](http://http://msdn.microsoft.com/library/ff687127.aspx), na seção intitulada **Para conectar-se ao Azure no Visual Studio**. Quando você entra no Azure, o nó **Barramento de Serviço** na árvore **Microsoft Azure** no Gerenciador de Servidores é automaticamente populado com os namespaces que você criou. Clique com o botão direito do mouse em qualquer namespace e, em seguida, clique em **Propriedades** para ver a cadeia de conexão e outros metadados associados a esse namespace exibido no painel **Propriedades** do Visual Studio. 

![][44]

Anote o valor **SharedAccessKey** ou copie-o para a área de transferência.


## Criar um servidor local

Primeiro você criará um sistema de catálogo de produtos (fictício) local. Será muito simples, você pode ver isso como uma representação de um sistema de catálogo de produtos real local com uma superfície de serviço completa que estamos tentando integrar.

Esse projeto é iniciado como um aplicativo de console do Visual Studio. O projeto utiliza o pacote NuGet de Barramento de Serviço para incluir as bibliotecas de Barramento de Serviço e as definições de configuração. A extensão do Visual Studio do NuGet facilita a instalação e a atualização de bibliotecas e ferramentas no Visual Studio e no Visual Studio Express. O pacote NuGet de Barramento de serviço é a maneira mais fácil de obter a API do Barramento de serviço e configurar seu aplicativo com todas as dependências de Barramento de serviço. Para obter mais detalhes sobre como usar o NuGet e o pacote de Barramento de Serviço, consulte [Utilizando o pacote NuGet de Barramento de Serviço][].

### Criar o projeto

1.  Utilizando privilégios de administrador, inicie o Microsoft Visual
    Studio 2013 ou então o Microsoft Visual Studio Express. Para
    iniciar o Visual Studio com privilégios de administrador, clique com o botão direito do mouse em
    **Microsoft Visual Studio 2013 (ou Microsoft Visual Studio Express)** e clique em **Executar como administrador**.

2.  No Visual Studio, no menu **Arquivo**, clique em **Novo** e, em seguida,
    clique em **Projeto**.

    ![][10]

3.  Em **Modelos Instalados**, em **Visual C#**, clique em **Aplicativo de
    Console**. Na caixa **Nome**, digite o nome
    **ProductsServer**:

    ![][11]

4.  Clique em **OK** para criar o projeto **ProductsServer**.

5.  No **Gerenciador de Soluções**, clique com o botão direito do mouse em **ProductsServer** e
    clique em **Propriedades**.

6.  Clique na guia **Aplicativo** à esquerda, e verifique se o **.NET
    Framework 4** ou **.NET Framework 4.5** aparece no menu suspenso **Estrutura de destino:**. Se ele não aparecer, selecione o menu suspenso e, em seguida, clique em **Sim**
    quando for solicitado que você recarregue o projeto.

    ![][12]

7.  Se você já tiver instalado o Gerenciador de Pacotes NuGet para Visual Studio, vá para a próxima etapa. Caso contrário, visite [NuGet][] e clique em [Instalar o NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Siga os prompts para instalar o Gerenciador de Pacotes NuGet e, em seguida, reinicie o Visual Studio.

7.  No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Referências** e, em seguida, clique em
    **Gerenciar Pacotes NuGet**...

8.  Na coluna à esquerda da caixa de diálogo NuGet, clique em **Online**.

9. 	Na coluna à direita, clique na caixa **Pesquisar**, digite "**Barramento de Serviço**" e selecione o item **Barramento de Serviço do Microsoft
    Azure**. Clique em **Instalar** para concluir a
    instalação, em seguida feche essa caixa de diálogo.

    ![][13]

    Observe que os assemblies do cliente necessários agora são referenciados.

9.  Adicione uma nova classe para seu contrato de produto. No **Gerenciador de Soluções**,
    clique com o botão direito do mouse no projeto **ProductsServer** e clique em **Adicionar**. Em seguida, clique em
    **Classe**.

    ![][14]

10. Na caixa **Nome**, digite o nome **ProductsContract.cs**. Em seguida,
    clique em **Adicionar**.

11. Em **ProductsContract.cs**, substitua a definição do namespace pelo
    código a seguir, que define o contrato para o serviço:

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

12. Em Program.cs, substitua a definição do namespace pelo seguinte
    código, que adiciona o serviço de perfil e o host para ele:

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

13. No **Gerenciador de Soluções**, clique duas vezes no arquivo **App.config** para
    abri-lo no editor do **Visual Studio**. Substitua o conteúdo de
    **&lt;system.ServiceModel&gt;** pelo código XML a seguir. Certifique-se de
    substituir *yourServiceNamespace* pelo nome do namespace de seu serviço
    e *yourKey* pela chave SAS que você recuperou anteriormente
    do portal de gerenciamento do Azure:

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
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
                       <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

14. Pressione **F6** ou, no menu **Compilar**, clique em **Compilar Solução** para compilar o aplicativo a usar para verificar a precisão de seu trabalho até o momento.

## Criar um aplicativo ASP.NET MVC

Nesta seção você criará um aplicativo ASP.NET simples que exibe os dados recuperados do seu serviço de produto.

### Criar o projeto

1.  Certifique-se de que o Visual Studio está sendo executado com os privilégios de administrador. Caso contrário, para
    iniciar o Visual Studio com privilégios de administrador, clique com botão direito em
    **Microsoft Visual Studio 2013 (ou Microsoft Visual Studio Express)** e clique em **Executar como administrador**. O emulador de computação do Microsoft Azure, discutido posteriormente neste guia, requer que
    o Visual Studio seja iniciado com privilégios de administrador.

2.  No Visual Studio, no menu **Arquivo**, clique em **Novo** e, em seguida,
    clique em **Projeto**.

3.  Em **Modelos Instalados**, em **Visual C#**, clique em **Aplicativo Web ASP.NET MVC**. Nomeie o projeto **ProductsPortal**. Em seguida,
    clique em **OK**.

    ![][15]

4.  Na lista **Selecionar um modelo**, clique em **MVC** e
    clique em **OK**.

    ![][16]

5.  No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Modelos** e clique em **Adicionar**.
    Em seguida, clique em **Classe**. Na caixa **Nome**, digite o nome
    **Product.cs**. Clique em **Adicionar**.

    ![][17]

### Modificar o aplicativo web

1.  No arquivo Product.cs no Visual Studio, substitua a
    definição de namespace existente pelo seguinte código:

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

2.  No arquivo HomeController.cs no Visual Studio, substitua a
    definição de namespace existente pelo seguinte código:

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


9.  Para verificar a precisão de seu trabalho até agora, você pode pressionar **F6** ou
    **Ctrl+Shift+B** para compilar o projeto.


### Execute seu aplicativo localmente

Execute o aplicativo para verificar se ele funciona.

1.  Certifique-se de que **ProductsPortal** é o projeto ativo. Clique com o botão direito do mouse
    no nome do projeto no **Gerenciador de Soluções** e selecione **Definir como
    Projeto de Inicialização**
2.  No **Visual Studio**, pressione **F5**.
3.  Seu aplicativo deve aparecer em execução em um navegador:

    ![][21]

## Tornar seu aplicativo pronto para implantação no Azure

É possível implantar seu aplicativo em um serviço de nuvem do Azure ou em um site do Azure. Para saber mais sobre a diferença entre sites e serviços de nuvem, consulte [Modelos de execução do Azure][executionmodels]. Para saber como implantar o aplicativo em um site do Azure, consulte [Implantando um aplicativo Web ASP.NET em um site do Azure](http://azure.microsoft.com/develop/net/tutorials/get-started/). Esta seção contém etapas detalhadas para implantar o aplicativo em um serviço de nuvem do Azure.

Para implantar seu aplicativo em um serviço de nuvem, você adicionará um projeto de implantação de projeto de serviço de nuvem à solução. O projeto de implantação contém as informações de configuração necessárias para executar corretamente o aplicativo na nuvem.

1.  Para tornar seu aplicativo implantável na nuvem, clique com o botão direito do mouse no projeto **ProductsPortal** no **Gerenciador de Soluções**, clique em **Converter** e, em seguida, clique em **Converter para projeto de serviço de nuvem do Microsoft Azure**.

    ![][22]

2.  Para testar o aplicativo, pressione **F5**.

3.  Isso iniciará o emulador de computação do Azure. O emulador de
        computação usará o computador local para emular seu aplicativo
        em execução no Azure. Você pode confirmar que o emulador
        foi iniciado examinando a bandeja do sistema:

       ![][23]

4.  Um navegador exibirá ainda seu aplicativo sendo executado localmente,
        e ele terá aparência e funcionamento semelhantes aos que tinha quando você o executava,
        anteriormente, como um aplicativo ASP.NET MVC 4 normal.

## Juntar as peças

A próxima etapa é vincular o servidor de produtos local com o aplicativo ASP.NET MVC.

1.  Se ainda não estiver aberto, no Visual Studio, reabra o projeto
        **ProductsPortal** que você criou na seção "Criando um
        aplicativo ASP.NET MVC".

2.  Semelhante à etapa na seção "Criar Um Servidor
        Local", adicione o pacote NuGet às Referências do projeto. No
        Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** e, em seguida, clique em
        **Gerenciar Pacotes NuGet**.

3.  Pesquise "Barramento de Serviço" e selecione o item **Barramento de Serviço do Microsoft Azure**. Em seguida, conclua a instalação e feche essa caixa de diálogo.

4.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ProductsPortal**, depois clique em **Adicionar** e, em seguida, em **Item existente**.

5.  Navegue até o arquivo **ProductsContract.cs** do projeto de console **ProductsServer**. Clique para realçar   ProductsContract.cs. Clique na seta para baixo próxima a **Adicionar** e, em seguida, clique em **Adicionar como Link**.

	![][24]

6.  Agora abra o arquivo **HomeController.cs** no editor do Visual Studio e substitua a definição do namespace com o código a seguir. Substitua *yourServiceNamespace* pelo nome do namespace de seu serviço e *yourKey* por sua chave SAS. Isso permitirá que o cliente chame o serviço local, retornando o resultado da chamada.

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
                            TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                                "RootManageSharedAccessKey", "yourKey") });
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
7.  No Gerenciador de Soluções, clique com o botão direito do mouse na solução **ProductsPortal**,
        clique em **Adicionar** e, por fim, clique em **Projeto Existente**.

8.  Navegue até o projeto **ProductsServer** e clique duas vezes
        no arquivo de solução **ProductsServer.csproj** para adicioná-lo.

9.  No Gerenciador de Soluções, clique com o botão direito do mouse na solução **ProductsPortal**
        e clique em **Propriedades**.

10. No lado esquerdo, clique em **Projeto de Inicialização**. No lado
        direito, clique em **Vários projetos de inicialização**. Verifique se
        **ProductsServer**, **ProductsPortal.Azure** e
        **ProductsPortal** aparecem, nessa ordem, com **Iniciar** definido como
        a ação para **ProductsServer** e **ProductsPortal.Azure**
        e **Nenhuma** definida como a ação para **ProductsPortal**. Por
        exemplo:

      ![][25]

11. Ainda na caixa de diálogo Propriedades, clique em **ProjectDependencies** no
        lado esquerdo.

12. No menu suspenso **Projetos**, clique em
        **ProductsServer**. Verifique se **ProductsPortal** está desmarcado
        e **ProductsPortal.Azure** está marcado. Em seguida, clique em **OK**:

    ![][26]

## Executar o aplicativo

1.  No menu **Arquivo** no Visual Studio, clique em **Salvar Tudo**.

2.  Pressione **F5** para compilar e executar o aplicativo. O servidor
        local (o aplicativo de console **ProductsServer**) deve ser iniciado
        primeiro e, em seguida o aplicativo **ProductsWeb** deve ser iniciado em uma
        janela de navegador, conforme mostrado na captura de tela abaixo. Dessa vez, você
        verá que o inventário de produtos lista os dados recuperados do
        sistema local do serviço de produto.

    ![][1]

## Implantar seu aplicativo no Azure

1.  Clique com o botão direito do mouse no projeto **ProductsPortal** no **Gerenciador de
        Soluções** e clique em **publicar no Microsoft Azure**.

2.  Talvez seja necessário entrar para ver todas as suas assinaturas.

    Clique em **Entrar para ver mais assinaturas**:

    ![][27]

3.  Entre usando sua Conta da Microsoft.


8.  Clique em **Avançar**. Se sua assinatura ainda não contém nenhum serviço
        hospedado, será solicitado que você crie um. O serviço hospedado
        atua como um contêiner para seu aplicativo em sua 
        assinatura do Azure. Digite um nome que identifica seu
        aplicativo e escolha a região para a qual o aplicativo
        deve ser otimizado. (Você pode esperar tempos de carregamento mais rápidos para
        usuários acessando-o desta região.)

9.  Selecione o serviço hospedado para o qual você deseja publicar seu
        aplicativo. Mantenha os padrões conforme mostrado abaixo para o
        restante das configurações. Clique em **Avançar**:

    ![][33]

10. Na última página, clique em **Publicar** para iniciar o processo de
        implantação:

    ![][34]

Isso levará aproximadamente cinco a sete minutos. Como essa é a
        primeira vez que você está publicando, o Azure provisiona uma
        máquina virtual (VM), executa o robustecimento da segurança, cria uma função
        da Web na VM para hospedar o aplicativo, implanta seu código
        nessa função Web e finalmente configura o balanceador de carga e
        rede para que seu aplicativo esteja disponível ao público.

11. Enquanto a publicação estiver em andamento, você será capaz de monitorar a
        atividade na janela **Log de Atividades do Azure**, que, normalmente,
        fica encaixada na parte inferior do Visual Studio ou Visual Web
        Developer:

    ![][35]

12. Quando a implantação for concluída, você poderá exibir seu site
        clicando no link **URL do Site** na janela de monitoramento.

    ![][36]

    Seu site depende de seu servidor local, por isso, você deve
        executar o aplicativo **ProductsServer** localmente para que o site
        funcione corretamente. Já que você realiza solicitações no site de nuvem,
        você verá solicitações que chegam ao seu aplicativo de console
        local, conforme indicado pela saída "GetProducts called"
        exibida na captura de tela abaixo.

    ![][37]

Para saber mais sobre a diferença entre sites e serviços de nuvem, consulte [Modelos de execução do Azure][executionmodels].

## Parar e excluir seu aplicativo

Depois de implantar seu aplicativo, você talvez queira desabilitá-lo para que possa criar e implantar outros aplicativos com as 750horas/mês (31 dias/mês) gratuitas do tempo do servidor.

O Azure cobra as instâncias de função web por hora de acordo com o tempo consumido do servidor. O tempo do servidor é consumido quando seu aplicativo é implantado, mesmo se as instâncias não estiverem sendo executadas e estiverem no estado parado.
Uma conta gratuita inclui 750 horas/mês (31 dias/mês) de tempo de servidor dedicado da máquina virtual para hospedar essas instâncias da função web.

As etapas a seguir mostram como parar e excluir o aplicativo.

1.  Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços de Nuvem** e, em seguida, clique no nome do serviço.

2.  Clique na guia **Painel** e, em seguida, clique em **Parar** para suspender temporariamente o aplicativo. Você poderá
        iniciá-lo novamente apenas clicando em Iniciar. Clique em **Excluir** para remover completamente seu aplicativo do Azure sem possibilidade de restaurá-lo.

	![][43]

## Próximas etapas  

Para obter mais informações sobre o Barramento de Serviço, consulte os seguintes recursos:  
  
* [Barramento de Serviço do Azure][sbmsdn]  
* [Tutoriais do Barramento de Serviço][sbwacom]  
* [Como usar filas do Barramento de Serviço][sbwacomqhowto]  


  [0]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Obter as ferramentas e o SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png
  
  
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png
  
  
  
  [Usando o Pacote NuGet de Barramento de Serviço]: http://go.microsoft.com/fwlink/?LinkId=234589
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
  [45]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-45.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx  
  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: /develop/net/how-to-guides/service-bus-queues/
  [executionmodels]: http://azure.microsoft.com/develop/net/fundamentals/compute/


<!--HONumber=52-->