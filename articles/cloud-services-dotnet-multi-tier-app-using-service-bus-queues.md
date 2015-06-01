<properties
	pageTitle="Aplicativo multicamada .NET - tutorial do Azure"
	description="Um tutorial que ajuda você a desenvolver um aplicativo de várias camadas no Azure que usa filas do barramento de serviço para se comunicar entre camadas. Amostras no .NET."
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/17/2015"
	ms.author="sethm"/>





# Aplicativo multicamada .NET usando filas do Barramento de Serviço

## Introdução

O desenvolvimento para o Microsoft Azure é fácil usando o Visual Studio 2013 e o SDK do Azure gratuito para o .NET. Se ainda não tiver o Visual Studio 2013, o SDK instalará automaticamente o Visual Studio Express, para que você possa iniciar o desenvolvimento no Microsoft Azure de forma totalmente gratuita. Este guia pressupõe que você não tenha experiência prévia no uso do Microsoft Azure. No final deste tutorial, você terá um aplicativo que usa vários recursos do Microsoft Azure em execução em seu ambiente local e que demonstram como funciona um aplicativo de multicamadas.

Você aprenderá a:

-   Como habilitar seu computador para o desenvolvimento do Microsoft Azure com um único download e instalação
-   Como usar o Visual Studio para desenvolver para o Azure
-   Como criar um aplicativo de multicamadas no Azure usando funções da web e de trabalho
-   Como comunicar-se entre camadas usando filas do Barramento de Serviço

[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]

Neste tutorial você compilará e executará o aplicativo de multicamadas em um serviço de nuvem do Microsoft Azure. O front-end será uma função web MVC do ASP.NET e o back-end será uma função de trabalho. Você pode criar o mesmo aplicativo de multicamadas com o front-end como um projeto Web que deveria ser implantado em um site do Azure, em vez de um serviço de nuvem. Para obter instruções sobre o que fazer de forma diferente no front-end de um site do Azure, consulte a seção [Próximas etapas](#nextsteps).

A seguir está uma captura de tela do aplicativo concluído:

![][0]

**Observação** O Azure também oferece a funcionalidade da fila de armazenamento. Para obter mais informações sobre filas de armazenamento do Azure e o Barramento de Serviço, consulte [Filas do Azure e filas do Barramento de Serviço do Azure - comparadas e contrastadas][sbqueuecomparison].

## Visão geral de cenário: comunicação interfunções

Para enviar um pedido para processamento, o componente de UI de front-end, executando a função web, é necessário interagir com a lógica de camada intermediária em execução na função de trabalho. Este exemplo usa o sistema de mensagens agenciado Barramento de Serviço para a comunicação entre as camadas.

Usar o sistema de mensagens agenciado entre as camadas intermediárias e a Web separa os dois componentes. Ao contrário das mensagens diretas (isto é, TCP ou HTTP), a camada da Web não se conecta com a camada intermediária diretamente; em vez disso, envia unidades de trabalho, como mensagens, para o Barramento de Serviço, que mantém confiável até a camada intermediária estar pronta para os consumir e processar.

O Barramento de Serviço fornece duas entidades para dar suporte ao sistema de mensagens agenciado: filas e tópicos. Com filas, cada mensagem enviada para a fila é consumida por um único destinatário. Tópicos dão suporte ao padrão de publicação/assinatura em que cada mensagem publicada é disponibilizada para uma assinatura registrada com o tópico. Cada assinatura mantém logicamente sua própria fila de mensagens. As assinaturas também podem ser configuradas com as regras de filtro que restringem o conjunto de mensagens passado para a fila de assinatura para aquelas que correspondem ao filtro. Este exemplo usa as filas do Barramento de Serviço.

![][1]

Esse mecanismo de comunicação oferece diversas vantagens sobre mensagens diretas, ou seja:

-   **Desacoplamento temporal.** Com o padrão de mensagens assíncrono, os produtores e consumidores não precisam estar online ao mesmo tempo. O ServiceBus armazena de forma confiável as mensagens até que a parte de consumo esteja prontapara recebê-las. Isso permite que os componentes do aplicativodistribuído sejam desconectados voluntariamente, por exemplo, paramanutenção ou devido a uma falha de componente, sem afetar osistema como um todo. Além disso, o aplicativo de consumo só precisaser colocado online durante determinadas horas do dia.

-   **Nivelamento de carga**. Em muitos aplicativos, a carga do sistema varia ao longo do tempo enquanto o tempo de processamento necessário para cada unidade de trabalho for normalmente constante. Intermediar produtores de mensagem e consumidorescom uma fila significa que o aplicativo de consumo (o função de trabalho) sóprecisa ser configurado para acomodar a carga média em vez de pico decarga. A profundidade da fila crescerá e contrairá conforme a carga de entradavariar. Isso economiza dinheiro diretamente em termos da quantidade deinfraestrutura necessária para atender à carga do aplicativo.

-   **Balanceamento de carga.** Conforme a carga aumenta, mais processos de função de trabalho podem seradicionados à leitura da fila. Cada mensagem é processada por apenas umdos processos de trabalho. Além disso, esse balanceamento de carga com base em recepçãopermite uma utilização ideal das máquinas de trabalho mesmo se asmáquinas de trabalho diferem em termos de capacidade de processamento, pois elasirão receber mensagens em sua própria taxa máxima. Esse padrão geralmente échamado de padrão de consumidor concorrente.

    ![][2]

As seções a seguir discutem o código que implementa essa arquitetura.

## Configurar o ambiente de desenvolvimento

Antes de iniciar o desenvolvimento de seu aplicativo do Azure, baixe as ferramentas e configure seu ambiente de desenvolvimento:

1.  Para instalar o SDK do Azure para .Net, clique no botão abaixo:

    [Obter as ferramentas e o SDK][]

2. 	Clique no link na versão do Visual Studio que você está usando. As etapas deste tutorial usam o Visual Studio 2013:

	![][32]

4. 	Quando for solicitado a executar ou salvar o arquivo de instalação, clique em **Executar**:

    ![][3]

5.  No Web Platform Installer, clique em **Instalar** e prossiga com a instalação:

    ![][33]

6.  Quando a instalação for concluída, você terá todo o necessário para iniciar o desenvolvimento. O SDK inclui ferramentas que permitem que você desenvolva os aplicativos do Azure no Visual Studio. Se vocênão tiver o Visual Studio instalado, ele também instala oVisual Studio Express gratuito para Web.

## Configurar o namespace do Barramento de Serviço

A próxima etapa é para criar um namespace de serviço e para obter uma chave de Assinatura de Acesso Compartilhado (SAS). Um namespace de serviço fornece um limite de aplicativo para cada aplicativo exposto por meio do Barramento de Serviço. A chave SAS será gerada pelo sistema quando um namespace de serviço for criado. A combinação do namespace de serviço e uma chave SAS oferece as credenciais para o Barramento de Serviço para autenticar o acesso a um aplicativo.

Você também pode gerenciar namespaces e entidades de mensagens do Barramento de Serviço usando o Gerenciador de Servidores do Visual Studio, mas só pode criar novos namespaces dentro do portal.

### Configurar o namespace usando o Portal de Gerenciamento

1.  Faça logon no [Portal de Gerenciamento do Azure][].

2.  No painel de navegação esquerdo do Portal de Gerenciamento, clique em**Service Bus**.

3.  No painel inferior do Portal de Gerenciamento, clique em **Criar**.

    ![][6]

4.  No diálogo **Adicionar um novo namespace**, digite um nome de namespace. O sistema imediatamente verifica para ver se o nome está disponível. ![][7]

5.  Depois de verificar se o nome do namespace está disponível, escolha o país ou a região em que o namespace deve ser hospedado (certifique-se de usar o mesmo país/região em que você está implantando seus recursos de computação). Além disso, certifique-se de selecionar **Sistema de mensagens** no campo do namespace **Tipo** e **Padrão** no campo **Camada de mensagens**.

    IMPORTANTE: selecione a **mesma região** que você pretende escolher para implantar seu aplicativo. Isso lhe dará o melhor desempenho.

6.  Clique na marca de seleção. Agora, o sistema cria o namespace de serviço e o habilita. Talvez você precise aguardar vários minutos, conforme o sistema fornece recursos para sua conta.

	![][27]

7.  Na janela principal, clique no nome do seu namespace de serviço.

	![][30]

8. Clique em **Informações de Conexão**.

	![][31]

9.  No painel **Acessar as informações de conexão**, encontre a cadeia de conexão que contém a chave SAS e o nome da chave.

    ![][35]

10.  Anote estas credenciais ou as copie para a área de transferência.

## Gerenciar namespaces e entidades de mensagens usando o Gerenciador de Servidores do Visual Studio

Para gerenciar um namespace e obter informações de conexão usando o Visual Studio em vez do Portal de Gerenciamento Azure, siga o procedimento descrito [aqui](http://msdn.microsoft.com/library/ff687127.aspx), na seção intitulada **Para conectar-se ao Azure no Visual Studio**. Quando você entra no Azure, o nó **Barramento de Serviço** na árvore **Microsoft Azure** no Gerenciador de Servidores é automaticamente populado com os namespaces que você criou. Clique com o botão direito do mouse em qualquer namespace e, em seguida, clique em **Propriedades** para ver a cadeia de conexão e outros metadados associados a esse namespace exibido no painel **Propriedades** do Visual Studio.

Anote o valor **SharedAccessKey** ou copie-o para a área de transferência:

![][34]

**Observação** também é possível usar o **Gerenciador de Servidores** para gerenciar um namespace do Barramento de Serviço em outra assinatura usando o seguinte procedimento:

1. Na barra de menus no Visual Studio, selecione **Exibir** e, em seguida, clique em **Gerenciador de Servidores**. Um nó **Barramento de Serviço** é exibido em **Azure** dentro da hierarquia do Gerenciador de Servidores, conforme mostrado na figura a seguir.

	![][21]

2. No Gerenciador de Servidores, expanda **Microsoft Azure**, clique com o botão direito do mouse em **Barramento de Serviço** e clique em **Adicionar Nova Conexão**.

3. No diálogo **Adicionar Conexão**, digite o nome do namespace de serviço, o nome e a chave do emissor ou cole na cadeia de conexão para o namespace. As permissões associadas com a chave do emissor determinam as operações que você pode executar nesse namespace. Clique em **OK** para conectar.

	![][22]

## Criar uma função Web

Nesta seção, você compilará o front-end de seu aplicativo. Primeiro, você criará várias páginas que seu aplicativo exibirá. Depois disso, você adicionará o código para enviar itens para uma fila do Barramento de Serviço e exibir informações de status sobre a fila.

### Criar o projeto

1.  Usando privilégios de administrador, inicie o Microsoft Visual Studio 2013 ou o Microsoft Visual Studio Express. Para iniciar o Visual Studio com privilégios de administrador, clique com o botão direito do mouse em **Microsoft Visual Studio 2013 (ou Microsoft Visual Studio Express)** e em **Executar como administrador**. O emulador de computação do Azure, abordado mais à frente neste guia, exige que o Visual Studio seja iniciado com privilégios de administrador.

    No Visual Studio, no menu **Arquivo**, clique em **Novo** e clique em **Projeto**.

    ![][8]


2.  Em **Modelos Instalados**, em **Visual C#**, clique em **Nuvem** e em **Serviço de Nuvem do Azure**. Nomeie o projeto **MultiTierApp**. Em seguida, clique em **OK**.

    ![][9]

3.  Em funções do **.NET Framework 4.5**, clique duas vezes em **Função Web do ASP.NET**.

    ![][10]

4.  Focalize **WebRole1** em **solução do Serviço de Nuvem do Azure**, clique no ícone de lápis e renomeie a função web para **FrontendWebRole**. Clique em **OK**. (Certifique-se de inserir "Frontend" com "e" minúsculo, não "FrontEnd".)

    ![][11]

5.  No diálogo **Novo Projeto ASP.NET**, na lista **Selecionar um modelo**, clique em **MVC** e **OK**.

    ![][12]

6.  No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Referências**, clique em **Gerenciar Pacotes NuGet...** ou **Adicionar Referência do Pacote de Biblioteca**.

7.  Selecione **Online** no lado esquerdo da caixa de diálogo. Pesquise "**Barramento de Serviço**" e selecione o item **Barramento de Serviço do Microsoft Azure**. Em seguida, conclua a instalação e feche essa caixa de diálogo.

    ![][13]

8.  Os assemblies de cliente obrigatórios agora são referenciados e alguns arquivos de código novos foram adicionados.

9.  Em **Gerenciador de Soluções**, clique com o botão direito do mouse em **Modelos**, **Adicionar** e **Classe**. Na caixa **Nome**, digite o nome **OnlineOrder.cs**. Clique em **Adicionar**.

### Escreva o código para a função Web

Nesta seção, você criará várias páginas que exibem seu aplicativo.

1.  No arquivo **OnlineOrder.cs** do Visual Studio, substitua a definição do namespace existente pelo seguinte código:

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  No **Gerenciador de Soluções**, clique duas vezes em **Controllers\\HomeController.cs**. Adicione as seguintes instruções **usando** na parte superior do arquivo para incluir os namespaces do modelo que você acabou de criar, bem como o Barramento de Serviço:

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  Também no arquivo **HomeController.cs** do Visual Studio, substitua a definição do namespace existente pelo código a seguir. Esse códigocontém métodos para lidar com o envio de itens na fila:

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // Simply redirect to Submit, since Submit will serve as the
                    // front page of this application
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit
                // Controller method for a view you will create for the submission
                // form
                public ActionResult Submit()
                {
                    // Will put code for displaying queue message count here.

                    return View();
                }

                // POST: /Home/Submit
                // Controller method for handling submissions from the submission
                // form
                [HttpPost]
				// Attribute to help prevent cross-site scripting attacks and
				// cross-site request forgery  
    			[ValidateAntiForgeryToken]
                public ActionResult Submit(OnlineOrder order)
                {
                    if (ModelState.IsValid)
                    {
                        // Will put code for submitting to queue here.

                        return RedirectToAction("Submit");
                    }
                    else
                    {
                        return View(order);
                    }
                }
            }
        }

4.  No menu **Compilar**, clique em **Solução de Compilação**.

5.  Agora, você criará a exibição do método **Submit()** criado acima. Clique com o botão direito do mouse no método Submit() e selecione **Adicionar Exibição**

    ![][14]

6.  Será exibida uma caixa de diálogo para criar o modo de exibição. Selecione a classe **OnlineOrder** no suspenso **Classe do modelo** e escolha **Criar** no suspenso **Modelo**.

    ![][15]

7.  Clique em **Adicionar**.

8.  Agora, você alterará o nome exibido de seu aplicativo. No **Gerenciador de Soluções**, clique duas vezes no arquivo **Views\\Shared\\\\\Layout.cshtml** para abri-lo no editor do Visual Studio.

9.  Altere todas as ocorrências de **Meu aplicativo ASP.NET** para **Produtos da LITWARE**.

10. Remova os links **Página Inicial**, **Sobre** e **Contato**. Exclua o código destacado:

	![][28]

11. Por fim, modifique a página de envio para incluir algumas informações sobre a fila. No **Gerenciador de Soluções**, clique duas vezes no arquivo **Views\\Home\\Submit.cshtml** para abri-lo no editor do Visual Studio. Adicione a linha a seguir depois de **&lt;h2>Enviar&lt;/h2>**. Por enquanto, o **ViewBag.MessageCount** permanece vazio. Você irá preenchê-lo mais tarde.

        <p>Current Number of Orders in Queue Waiting to be Processed: @ViewBag.MessageCount</p>


12. Você agora implementou a interface do usuário. Você pode pressionar **F5** para executar o aplicativo e confirmar que parece conforme o esperado.

    ![][17]

### Escreva o código para enviar itens para uma fila do Brramento de Serviço

Agora, você adicionará o código para enviar itens para uma fila. Primeiro, você criará uma classe que contém as informações de conexão de fila do Barramento de Serviço. Em seguida, você inicializará a conexão do **Global.aspx.cs**. Por fim, você atualizará o código de envio criado anteriormente em **HomeController.cs** para efetivamente enviar itens para uma fila do Barramento de Serviço.

1.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **FrontendWebRole** (clique com o botão direito no projeto, e não na função). Clique em **Adicionar** e depois em **Classe**.

2.  Nomeie a classe **QueueConnector.cs**. Clique em **Adicionar** para criar a classe.

3.  Agora, você adicionará o código que encapsula as informações da conexão e inicializará a conexão em uma fila do Barramento de Serviço. Em QueueConnector.cs, adicione o seguinte código e insira valores para **Namespace** (seu namespace de serviço) e **yourKey**, que é a chave de SAS obtida do [portal de gerenciamento do Azure][Azure Management Portal] anteriormente.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

        namespace FrontendWebRole
        {
            public static class QueueConnector
            {
                // Thread-safe. Recommended that you cache rather than recreating it
                // on every request.
                public static QueueClient OrdersQueueClient;

                // Obtain these values from the Management Portal
                public const string Namespace = "your service bus namespace";

                // The name of your queue
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey");
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Using Http to be friendly with outbound firewalls
                    ServiceBusEnvironment.SystemConnectivity.Mode =
                        ConnectivityMode.Http;

                    // Create the namespace manager which gives you access to
                    // management operations
                    var namespaceManager = CreateNamespaceManager();

                    // Create the queue if it does not exist already
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Get a client to the queue
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address,
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

    **Observação** mais tarde neste tutorial, você aprenderá como armazenar o nome de seu **Namespace** e o valor da chave SAS em um arquivo de configuração.

4.  Agora, você garantirá que o método **Initialize** seja chamado. No **Gerenciador de Soluções**, clique duas vezes em **Global.asax\\Global.asax.cs**.

5.  Adicione a linha a seguir na parte inferior do método **Application_Start**:

        FrontendWebRole.QueueConnector.Initialize();

6.  Por fim, você atualizará o código da Web criado anteriormente, para enviar itens para a fila. No **Gerenciador de Soluções**, clique duas vezes em **Controllers\\HomeController.cs** criado anteriormente.

7.  Atualize o método **Submit()** da seguinte maneira para obter a contagem de mensagens da fila:

        public ActionResult Submit()
        {
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus queues.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Get the queue, and obtain the message count.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  Atualize o método **Submit(OnlineOrder order)** da seguinte maneira para enviar informações do pedido para a fila:

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Create a message from the order
                var message = new BrokeredMessage(order);

                // Submit the order
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  Agora você pode executar o aplicativo novamente. Cada vez que você enviar umpedido, o número de mensagens aumenta.

    ![][18]

## Gerenciador de configuração de nuvem

O método **GetSettings** na classe **Microsoft.WindowsAzure.Configuration.CloudConfigurationManager** permite que você leia as definições de configuração do armazenamento de configuração para sua plataforma. Por exemplo, se seu código está sendo executado em uma função da Web ou de trabalho, o método **GetSettings** lerá o arquivo ServiceConfiguration.cscfg e se seu código estiver em execução em um aplicativo de console padrão o método **GetSettings** lerá o arquivo app. config.

Se você armazenar uma cadeia de conexão no seu namespace do Barramento de Serviço em um arquivo de configuração, você poderá usar o método **GetSettings** para ler uma cadeia de conexão que você pode usar para criar uma instância de um objeto **NamespaceMananger**. Você pode usar uma instância **NamespaceMananger** para configurar o Namespace do Barramento de Serviço por meio de programação. Você pode usar a mesma cadeia de conexão para criar uma instância de objetos do cliente (como objeto **QueueClient**, **TopicClient** e **EventHubClient**) que você pode usar para executar operações de tempo de execução como enviar e receber mensagens.

### Cadeia de conexão

Para criar uma instância de um cliente (por exemplo, um **QueueClient** do Barramento de Serviço), você pode representar as informações de configuração como uma cadeia de conexão. No lado do cliente, há um `CreateFromConnectionString()` método que cria uma instância desse tipo de cliente usando essa cadeia de conexão. Por exemplo, dada a seção de configuração a seguir:

	<ConfigurationSettings>
    ...
    	<Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=RootManageSharedAccessKey;SharedSecretValue=[yourKey]" />
	</ConfigurationSettings>

O código a seguir recupera a cadeia de conexão, cria uma fila e inicializa a conexão para a fila:

	QueueClient Client;

	string connectionString =
     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
     NamespaceManager.CreateFromConnectionString(connectionString);

	if (!namespaceManager.QueueExists(QueueName))
    {
        namespaceManager.CreateQueue(QueueName);
    }

	// Initialize the connection to Service Bus Queue
	Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

O código na seção a seguir usa a classe **CloudConfigurationManager**.

## Criar a função de trabalho

Agora você criará a função de trabalho que processa o envio de pedidos. Este exemplo usa o modelo de projeto do Visual Studio **Função de Trabalho com Fila do Brramento de Serviço**. Primeiro, use o Gerenciador de Servidores no Visual Studio para obter as credenciais necessárias.

1. Se você já conectou o Visual Studio à conta do Azure, conforme descrito na seção "Gerenciar namespaces e entidades do sistema de mensagens usando o Gerenciador de Servidores do Visual Studio."

2.  No Visual Studio, no **Gerenciador de Soluções**, clique com o botão direito na pasta **Funções** no projeto **MultiTierApp**.

3.  Clique em **Adicionar** e clique em **Novo Projeto da Função de Trabalho**. A caixa de diálogo **Adicionar Novo Projeto da Função** é exibida.

	![][26]

4.  Na caixa de diálogo **Adicionar Novo Projeto de Função**, clique em **Função de Trabalho com Fila do Service Bus**, como na figura a seguir:

	![][23]

5.  Na caixa **Nome**, nomeie o projeto **OrderProcessingRole**. Clique em **Adicionar**.

6.  No Gerenciador de Serviços, clique com botão direito no namespace de serviço e clique em **Propriedades**. No painel **Propriedades** do Visual Studio, a primeira entrada contém uma cadeia de conexão preenchida com o ponto de extremidade do namespace que contém as credenciais de autorização necessárias. Por exemplo, consulte a figura a seguir. Clique duas vezes em **ConnectionString** e pressione **Ctrl+C** para copiar essa cadeia de caracteres para a área de transferência.

	![][24]

7.  No Gerenciador de Soluções, clique com botão direito do mouse em **OrderProcessingRole** criado na etapa 5 (não se esqueça de clicar com o botão direito do mouse em **OrderProcessingRole** em **Funções** e não na classe). Clique em **Propriedades**.

8.  Na guia **Configurações** do diálogo **Propriedades**, clique dentro da caixa **Valor** para **Microsoft.ServiceBus.ConnectionString** e cole o valor do ponto de extremidade que você copiou na etapa 6.

	![][25]

9.  Crie uma classe **OnlineOrder** para representar os pedidos conforme você os processa na fila. Você pode reutilizar uma classe já criada. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **OrderProcessingRole** (clique com o botão direito do mouse no projeto, e não na função). Clique em **Adicionar** e clique em **Item Existente**.

10. Navegue até a subpasta de **FrontendWebRole\\Models** e clique duas vezes em **OnlineOrder.cs** para adicioná-la a esse projeto.

11. Em WorkerRole.cs, substitua o valor da variável **QueueName** em **WorkerRole.cs** de `"ProcessingQueue"` para `"OrdersQueue"`, como no seguinte código:

		// The name of your queue
		const string QueueName = "OrdersQueue";

12. Adicione a seguinte instrução using na parte superior do arquivo WorkerRole.cs:

		using FrontendWebRole.Models;

13. Na `Run()` função, dentro da `OnMessage` chamada, adicione o seguinte código dentro da `try` cláusula:

		Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
		// View the message as an OnlineOrder
		OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
		Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
		receivedMessage.Complete();

14. Você concluiu o aplicativo. Você pode testar o aplicativo completo clicando com o botão direito do mouse no projeto MultiTierApp no Gerenciador de Soluções, selecionando **Definir como Projeto de Inicialização** e pressionando F5. Observe que o número de mensagens não é incrementado porque a função de trabalho processa itens da fila e os marca como concluído. Você pode ver a saída do rastreamento da função de trabalho, exibindo a interface do usuário do emulador de computação do Azure. É possível fazer isso clicando com o botão direito do mouse no ícone do emulador na área de notificação da sua barra de tarefas e selecionando **Mostrar Interface do Usuário do Emulador de Computação**.

    ![][19]

    ![][20]

## Próximas etapas  

Para obter mais informações sobre o Barramento de Serviço, consulte os seguintes recursos:

* [Barramento de Serviço do Azure][sbmsdn]  
* [Página de serviço do Barramento de Serviço][sbwacom]  
* [Como usar as filas do Barramento de Serviço][sbwacomqhowto]  

Para obter mais informações sobre cenários de várias camadas ou para saber como implantar um aplicativo para um serviço de nuvem, consulte:

* [Aplicativo de várias camadas .NET usando tabelas de armazenamento, filas e blobs][mutitierstorage]  

Convém implementar o front-end de um aplicativo multicamadas em um site do Azure, em vez de um serviço de nuvem do Azure. Para saber mais sobre a diferença entre sites e serviços de nuvem, consulte [Modelos de execução do Azure][executionmodels].

Para implementar o aplicativo criado neste tutorial como um projeto Web padrão, em vez de uma função web do serviço de nuvem, siga as etapas neste tutorial com as seguintes diferenças:

1. Ao criar o projeto, escolha o modelo de projeto **Aplicativo Web do ASP.NET MVC** na categoria **Web** em vez do modelo **Serviço de Nuvem** na categoria **Nuvem**. Em seguida, siga as mesmas instruções para criar o aplicativo MVC até chegar a seção **Gerenciador de configuração de nuvem**.

2. Ao criar a função de trabalho, crie em uma solução nova e separada, semelhante às instruções originais para a função Web. No entanto, você está criando a função de trabalho no projeto de serviço de nuvem. Em seguida, siga as mesmas instruções para criar a função de trabalho.

3. Você pode testar o front-end e back-end separadamente ou executar simultaneamente em instâncias separadas do Visual Studio.

Para saber como implantar o front-end em um site do Azure, consulte [Implantando um aplicativo Web do ASP.NET em um site do Azure](http://azure.microsoft.com/develop/net/tutorials/get-started/). Para saber como implantar o back-end em um serviço de nuvem do Azure, consulte [Aplicativo multicamadas do .NET usando tabelas, filas e blobs de armazenamento][mutitierstorage].


[0]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
[1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
[sbqueuecomparison]: http://msdn.microsoft.com/library/hh767287.aspx
[2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
[Obter as ferramentas e o SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
[3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png



[Azure Management Portal]: http://manage.windowsazure.com
[Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
[6]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
[7]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
[8]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
[9]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
[10]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
[11]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
[12]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
[13]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
[14]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
[15]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
[16]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
[17]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
[18]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

[19]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
[20]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
[21]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorer.png
[22]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerAddConnect.png
[23]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
[24]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
[25]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
[26]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
[27]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
[28]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
[30]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
[31]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
[32]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
[33]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
[34]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/VSProperties.png
[35]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/multi-web-45.png
[sbmsdn]: http://msdn.microsoft.com/library/ee732537.aspx
[sbwacom]: /documentation/services/service-bus/
[sbwacomqhowto]: /develop/net/how-to-guides/service-bus-queues/
[mutitierstorage]: /develop/net/tutorials/multi-tier-web-site/1-overview/
[executionmodels]: http://azure.microsoft.com/develop/net/fundamentals/compute/

<!--HONumber=52-->
