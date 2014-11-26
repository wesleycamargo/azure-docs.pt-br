<properties linkid="dev-net-e2e-multi-tier" urlDisplayName="Multi-Tier Application" pageTitle=".NET Multi-Tier Application - Azure Tutorial" metaKeywords="Azure Service Bus queue tutorial, Azure queue tutorial, Azure worker role tutorial, Azure .NET queue tutorial, Azure C# queue tutorial, Azure C# worker role tutorial" description="A tutorial that helps you develop a multi-tier app in Azure that uses Service Bus queues to communicate between tiers. Samples in .NET." metaCanonical="" services="cloud-services,service-bus" documentationCenter=".NET" title=".NET Multi-Tier Application Using Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/15/2014" ms.author="sethm" />

# Aplicativo multicamada .NET usando filas do Barramento de Serviço

O desenvolvimento para o Azure é fácil com o Visual Studio 2013 e o SDK do Azure gratuito para o .NET. Se ainda não tiver o Visual Studio 2013, o SDK instalará automaticamente o Visual Studio Express 2013, para que você possa começar a desenvolver para o Azure de forma totalmente gratuita. Este guia pressupõe que você não tem experiência anterior com o Windows Azure. Na conclusão deste guia, você terá um aplicativo que usa vários recursos do Azure em execução no ambiente local e que demonstram como funciona um aplicativo multicamadas.

Você aprenderá a:

-   Habilitar o computador para o desenvolvimento do Azure com um
    único download e instalar.
-   Usar o Visual Studio para desenvolver para o Azure.
-   Criar um aplicativo multicamadas no Azure usando
    funções web e de trabalho.
-   Comunicar-se entre camadas usando filas do Barramento de Serviço.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

Neste tutorial você irá compilar e executar o aplicativo multicamadas em um serviço de nuvem do Azure. O front-end será uma função web MVC do ASP.NET e o back-end será uma função de trabalho. Você poderia criar o mesmo aplicativo multicamadas com o front-end como um projeto Web que deveria ser implantado em um site do Azure, em vez de um serviço de nuvem. Para obter instruções sobre o que fazer de forma diferente no front-end de um site do Azure, consulte a seção [Próximas etapas][Próximas etapas].

A seguir está uma captura de tela do aplicativo concluído:

![][0]

**Observação** O Azure também oferece a funcionalidade da fila de armazenamento. Para obter mais informações sobre filas de armazenamento do Azure e o Barramento de Serviço, consulte [Filas do Azure e filas do Barramento de Serviço do Azure - comparadas e contrastadas][Filas do Azure e filas do Barramento de Serviço do Azure - comparadas e contrastadas].

## <span class="short-header">Comunicação entre funções</span>Visão geral do cenário: comunicação entre funções

Para enviar um pedido para processamento, o componente de UI de front-end, executar a função Web, é necessário interagir com a lógica de camada intermediária em execução na função de trabalho. Este exemplo usa a mensagem detalhada do Barramento de Serviço orientado para a comunicação entre as camadas de mensagens.

Usar mensagens de comunicação entre as camadas intermediária e da Web separa os dois componentes. Ao contrário com a mensagem direta (isto é, TCP ou HTTP), a camada da Web não se conecta com a camada intermediária diretamente; em vez disso, envia unidades de trabalho, como mensagens, para o Barramento de Serviço, que mantém confiável até a camada intermediária estar pronta para consumir e processar.

O Barramento de Serviço fornece duas entidades para dar suporte à comunicação de mensagens: filas e tópicos. Com filas, cada mensagem enviada para a fila é consumida por um único destinatário. Tópicos suportam o padrão de publicação/assinatura em que cada mensagem publicada é disponibilizada para cada assinatura registrada com o tópico. Cada assinatura mantém logicamente sua própria fila de mensagens. As assinaturas também podem ser configuradas com as regras de filtro que restringem o conjunto de mensagens passado para a fila de assinatura para aquelas que correspondem ao filtro. Este exemplo usa filas do Barramento de Serviço.

![][1]

Esse mecanismo de comunicação oferece diversas vantagens sobre mensagens diretas, ou seja:

-   **Desacoplamento temporal.** Com o padrão de mensagens assíncrono, os produtores e consumidores não precisam estar online ao mesmo tempo. O Barramento de Serviço armazena de forma confiável as mensagens até que a parte de consumo esteja pronta para recebê-las. Isso permite que os componentes do aplicativo distribuído sejam desconectados voluntariamente, por exemplo, para manutenção ou devido a uma falha de componente, sem afetar o sistema como um todo. Além disso, o aplicativo de consumo só precisa ser colocado online durante determinadas horas do dia.

-   **Nivelamento de carga**. Em muitos aplicativos, a carga do sistema varia ao longo do tempo enquanto o tempo de processamento necessário para cada unidade de trabalho é normalmente constante. Intermediar produtores de mensagem e consumidores com uma fila significa que o aplicativo de consumo (o trabalho) só precisa ser configurado para acomodar a carga média em vez de pico de carga. A profundidade da fila crescerá e contrairá conforme a carga de entrada variar. Isso economiza dinheiro diretamente em termos da quantidade de infraestrutura necessária para atender à carga do aplicativo.

-   **Balanceamento de carga.** Conforme a carga aumenta, mais processos de função de trabalho podem ser adicionados à leitura da fila. Cada mensagem é processada por apenas um dos processos de trabalho. Além disso, esse balanceamento de carga com base em recepção permite uma utilização ideal das máquinas de trabalho mesmo se as máquinas de trabalho diferem em termos de capacidade de processamento, pois elas irão receber mensagens em sua própria taxa máxima. Esse padrão geralmente é chamado de padrão de consumidor concorrente.

    ![][2]

As seções a seguir discutem o código que implementa essa arquitetura.

## <span class="short-header">Instalar ambiente</span>Instalar o ambiente de desenvolvimento

Antes de começar a desenvolver seu aplicativo do Azure, é necessário obter as ferramentas e configurar seu ambiente de desenvolvimento.

1.  Para instalar o SDK do Azure para .Net, clique no botão abaixo:

    [Obter as ferramentas e o SDK][Obter as ferramentas e o SDK]

2.  Clique em **Instalar o SDK**.

3.  Escolha o link para a versão do Visual Studio que você está usando. As etapas deste tutorial usam o Visual Studio 2013:

    ![][3]

4.  Quando for solicitado a executar ou salvar o arquivo de instalação, cliqueem **Executar**:

    ![][4]

5.  No Web Platform Installer, clique em **Instalar** e prossiga com a instalação:

    ![][5]

6.  Quando a instalação for concluída, você terá tudo o que é necessário para iniciar o desenvolvimento. O SDK inclui ferramentas que permitem que você desenvolva facilmente aplicativos do Azure no Visual Studio. Se você não tiver o Visual Studio instalado, ele também instala o Visual Studio Express gratuito para Web.

## <span class="short-header">Configurar o namespace</span>Configurar o namespace do Brramento de Serviço

A próxima etapa é criar um namespace do serviço, e obter uma chave secreta compartilhada. Um namespace de serviço fornece um limite de aplicativo para cada aplicativo exposto por meio do Barramento de Serviço. Uma chave secreta compartilhada é gerada automaticamente pelo sistema quando um namespace e serviço é criado. A combinação do namespace de serviço e uma chave secreta compartilhada oferece uma credencial para o Barramento de Serviço para autenticar o acesso a um aplicativo.

Você também pode gerenciar namespaces e entidades de mensagens do Barramento de Serviço usando o Gerenciador de Servidores do Visual Studio, mas só pode criar novos namespaces dentro do portal.

### Configurar o namespace usando o Portal de Gerenciamento

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  No painel de navegação esquerdo do Portal de Gerenciamento, clique em **Service Bus**.

3.  No painel inferior do Portal de Gerenciamento, clique em **Criar**.

    ![][6]

4.  No diálogo **Adicionar um novo namespace**, insira um nome de namespace. O sistema imediatamente verifica se o nome está disponível.

    ![][7]

5.  Depois de verificar se o nome do namespace está disponível, escolha o país ou a região em que o namespace deve ser hospedado (certifique-se de usar o mesmo país/região em que você está implantando seus recursos de computação).

    IMPORTANTE: Selecione a **mesma região** que você pretende escolher para implantar seu aplicativo. Isso lhe dará o melhor desempenho.

6.  Clique na marca de seleção. Agora, o sistema cria o seu namespace de serviço e o habilita. Talvez você precise aguardar vários minutos, enquanto o sistema provisiona recursos para sua conta.

    ![][8]

7.  Na janela principal, clique no nome do seu namespace de serviço.

    ![][9]

8.  Clique em **Informações de Conexão**.

    ![][10]

9.  No painel **Informações da conexão de acesso**, localize os valores **Emissor Padrão** e **Chave Padrão**.

10. Anote a chave ou copie-a na área de transferência.

### Gerenciar namespaces e entidades de mensagens usando o Gerenciador de Servidores do Visual Studio

Para gerenciar um namespace e obter informações de conexão usando o Visual Studio em vez do Portal de Gerenciamento, siga o procedimento descrito [aqui][aqui], na seção intitulada **Para conectar-se ao Azure no Visual Studio**. Quando você entra no Azure, o nó **Barramento de Serviço** na árvore **Microsoft Azure** no Gerenciador de Servidores é automaticamente populado com os namespaces que você criou. Clique com o botão direito do mouse em qualquer namespace e, em seguida, clique em **Propriedades** para ver a cadeia de conexão e outros metadados associados a esse namespace exibido no painel **Propriedades** do Visual Studio.

Anote o valor **SharedAccessKey** ou copie-o para a área de transferência:

![][11]

## <span class="short-header">Criar uma função web</span>Criar uma função web

Nesta seção, você criará o front-end de seu aplicativo. Você criará primeiro as várias páginas que o seu aplicativo exibe. Depois disso, você adicionará código para enviar itens para uma fila do Barramento de Serviço e exibir informações de status sobre a fila.

### Criar o projeto

1.  Usando privilégios de administrador, inicie o Microsoft Visual Studio 2013 ou o Microsoft Visual Studio Express. Para iniciar o Visual Studio com privilégios de administrador, clique com o botão direito do mouse em **Microsoft Visual Studio 2013 (ou Microsoft Visual Studio Express)** e em **Executar como administrador**. O emulador de computação do Azure, abordado mais à frente neste guia, exige que o Visual Studio seja iniciado com privilégios de administrador.

    No Visual Studio, no menu **Arquivo**, clique em **Novo** e, em seguida, clique em **Projeto**.

    ![][12]

2.  Em **Modelos Instalados**, em **Visual C#**, clique em **Nuvem** e em seguida, clique em **Serviço de Nuvem do Azure**. Nomeie o projeto **MultiTierApp**. Em seguida, clique em **OK**.

    ![][13]

3.  Em funções do **.NET Framework 4.5**, clique duas vezes em **Função Web do ASP.NET**.

    ![][14]

4.  Focalize **WebRole1** em **Solução do Serviço de Nuvem do Azure**, clique no ícone de lápis e renomeie a função web para **FrontendWebRole**. Clique em **OK**. (Certifique-se de inserir "Front-end" com "e" minúsculo , não "Front-End".)

    ![][15]

5.  Na caixa de diálogo **Novo Projeto ASP.NET**, na lista **Selecionar um modelo**, clique em **MVC**, e em **OK**.

    ![][16]

6.  No **Gerenciador de Soluções**, clique com o botão direito em **Referências** e em **Gerenciar Pacotes NuGet...** ou **Adicionar Referência do Pacote de Biblioteca**.

7.  Selecione **Online** no lado esquerdo da caixa de diálogo. Procure por " **WindowsAzure**" e selecione o item **Barramento de Serviço do Azure**. Conclua a instalação e feche a caixa de diálogo.

    ![][17]

8.  Os assemblies de cliente obrigatórios agora são referenciados e alguns arquivos de código novos foram adicionados.

9.  Em **Gerenciador de Soluções**, clique com o botão direito em **Modelos** e clique em **Adicionar** e, em seguida, clique em **Classe**. Na caixa Nome, digite o nome **OnlineOrder.cs**. Clique em **Adicionar**.

### Escreva o código para a função Web

Nesta seção, você irá criar várias páginas que exibe seu aplicativo.

1.  No arquivo **OnlineOrder.cs** do Visual Studio, substitua a definição do namespace existente pelo seguinte código:

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  No **Gerenciador de Soluções**, clique duas vezes em **Controllers\\HomeController.cs**. Adicione as seguintes instruções **using** na parte superior do arquivo para incluir os namespaces do modelo que você acabou de criar, bem como o Barramento de Serviço:

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  Também no arquivo **HomeController.cs** do Visual Studio, substitua a definição do namespace existente pelo código a seguir. Esse código contém métodos para lidar com o envio de itens na fila:

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

5.  Agora, você criará a exibição do método **Submit()** criado acima. Clique com o botão direito do mouse no método Submit() e selecione **Adicionar Exibição**.

    ![][18]

6.  Será exibida uma caixa de diálogo para criar o modo de exibição. Selecione a classe **OnlineOrder** no menu suspenso **Classe do modelo** e escolha **Criar** no menu suspenso **Modelo**.

    ![][19]

7.  Clique em **Adicionar**.

8.  Agora, você irá alterar o nome exibido do seu aplicativo. No **Gerenciador de Soluções**, clique duas vezes no arquivo **Views\Shared\\_Layout.cshtml** para abrir no editor do Visual Studio.

9.  Substitua todas as ocorrências de **Meu aplicativo ASP.NET MVC** com **Incríveis Produtos da LITWARE**.

10. Substitua **"seu logotipo aqui"** com **Produtos Incríveis da LITWARE**:

    ![][20]

11. Remova os links **Página Inicial**, **Sobre** e **Contato**. Exclua o código destacado:

    ![][21]

12. Por fim, modifique a página de envio para incluir algumas informações sobre a fila. No **Gerenciador de Soluções**, clique duas vezes no arquivo **Views\\Home\\Submit.cshtml** para abri-lo no editor do Visual Studio. Adicione a seguinte linha depois de **\<h2\>Submit\</h2\>**. Por enquanto, o **ViewBag.MessageCount** permanece vazio. Você irá preenchê-lo mais tarde.

        <p>Current Number of Orders in Queue Waiting to be Processed: @ViewBag.MessageCount</p>

13. Você agora implementou a interface do usuário. Você pode pressionar **F5** para executar o aplicativo e confirmar que parece conforme o esperado.

    ![][22]

### Escreva o código para enviar itens para uma fila do Brramento de Serviço

Agora, você adicionará o código para enviar itens para uma fila. Primeiro, você irá criar uma classe que contém as informações de conexão de fila do Barramento de Serviço. Em seguida, você irá inicializar a conexão do **Global.aspx.cs**. Por fim, você atualizará o código de envio criado anteriormente em **HomeController.cs** para efetivamente enviar itens para uma fila do Barramento de Serviço.

1.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **FrontendWebRole** (clique com o botão direito no projeto, e não na função). Clique em **Adicionar** e depois em **Classe**.

2.  Nomeie a classe **QueueConnector.cs**. Clique em **Adicionar** para criar a classe.

3.  Agora, você colará o código que encapsula as informações da conexão e contém métodos para inicializar a conexão em uma fila do Barramento de Serviço. Em QueueConnector.cs, cole o código a seguir e insira valores para **Namespace**, **IssuerName** e **IssuerKey**. Você pode obter esses valores no [Portal de Gerenciamento][Portal de Gerenciamento do Azure] ou no Gerenciador de Servidores do Visual Studio no **nó Barramento de Serviço**.

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
                public const string IssuerName = "issuer name";
                public const string IssuerKey = "issuer key";

                // The name of your queue
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedSecretTokenProvider(
                        IssuerName, IssuerKey);
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

4.  Agora, você garantirá que o método **Initialize** seja chamado. No **Gerenciador de Soluções**, clique duas vezes em **Global.asax\\Global.asax.cs**.

5.  Adicione a seguinte linha na parte inferior do método **Application\_Start**:

        FrontendWebRole.QueueConnector.Initialize();

6.  Por fim, você atualizará o código da Web criado anteriormente, para enviar itens para a fila. No **Gerenciador de Soluções**, clique duas vezes em **Controllers\\HomeController.cs** criado anteriormente.

7.  Atualize o método **Submit()** da seguinte maneira para obter a contagem de mensagens da fila:

        public ActionResult Submit()
        {            
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus Queues.
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

9.  Agora você pode executar o aplicativo novamente. Cada vez que você enviar um pedido, o número de mensagens aumenta.

    ![][23]

## <span class="short-header">Gerenciador de configuração</span>Gerenciador de configuração de nuvem

O Azure dá suporte a um conjunto de APIs gerenciadas que oferece uma maneira consistente de criar novas instâncias de clientes de serviço do Azure (por exemplo, o Barramento de Serviço) em serviços de nuvem da Microsoft. Essas APIs permitem criar instâncias desses clientes (por exemplo, **CloudBlobClient**, **QueueClient**, **TopicClient**), independentemente de onde o aplicativo esteja hospedado – no local, em um serviço de nuvem da Microsoft, em sites ou em uma função VM persistente. Você também pode usar essas APIs para recuperar as informações de configuração necessárias para instanciar esses clientes e alterar a configuração sem precisar reimplantar o aplicativo de chamada. As APIs estão localizadas na classe [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]. Também existem APIs no lado do cliente.

### Cadeia de conexão

Para criar uma instância de um cliente (por exemplo, um **QueueClient** do Barramento de Serviço), você pode representar as informações de configuração como uma cadeia de conexão. No lado do cliente, há um método **CreateFromConnectionString()** que cria uma instância desse tipo de cliente usando essa cadeia de conexão. Por exemplo, dada a seção de configuração a seguir:

    <ConfigurationSettings>
    ...
        <Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
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

O código na seção a seguir usa esses APIs de gerenciamento de configuração.

## <span class="short-header">Criar uma função de trabalho</span>Criar a função de trabalho

Agora você irá criar a função de trabalho que processa o envio de pedidos. Este exemplo usa o modelo de projeto do Visual Studio **Função de Trabalho com Fila do Brramento de Serviço**. Primeiro, você usará o Gerenciador de Servidores no Visual Studio para obter as credenciais necessárias.

1.  Se você já conectou o Visual Studio à conta do Azure, conforme descrito na seção **Configurar o namespace usando o Gerenciador de Servidores do Visual Studio**, passe à etapa 5.

2.  Na barra de menus no Visual Studio, selecione **Exibir** e, em seguida, clique em **Gerenciador de Servidores**. Um nó **Barramento de Serviço** é exibido em **Azure** dentro da hierarquia do Gerenciador de Servidores, conforme mostrado na figura a seguir.

    ![][24]

3.  No Gerenciador de Servidores, expanda **Azure**, clique com o botão direito do mouse em **Barramento de Serviço** e clique em **Adicionar Nova Conexão**.

4.  Na caixa de diálogo **Adicionar Conexão**, digite o nome do namespace de serviço, o nome do emissor e a chave do emissor. Clique em **OK** para conectar.

    ![][25]

5.  No Visual Studio, no **Gerenciador de Soluções**, clique com o botão direito na pasta **Funções** no projeto **MultiTierApp**.

6.  Clique em **Adicionar** e clique em **Novo Projeto da Função de Trabalho**. A caixa de diálogo **Adicionar Novo Projeto da Função** é exibida.

    ![][26]

7.  Na caixa de diálogo **Adicionar Novo Projeto de Função**, clique em **Função de Trabalho com Fila do Service Bus**, como na figura a seguir:

    ![][27]

8.  Na caixa **Nome**, nomeie o projeto **OrderProcessingRole**. Clique em **Adicionar**.

9.  No Gerenciador de Serviços, clique com botão direito no namespace de serviço e clique em **Propriedades**. No painel **Propriedades** do Visual Studio, a primeira entrada contém uma cadeia de conexão populada com o ponto de extremidade do namespace de serviço contendo as credenciais de autorização obrigatórias. Por exemplo, consulte a figura a seguir. Clique duas vezes em **ConnectionString** e pressione **Ctrl+C** para copiar essa cadeia de caracteres para a área de transferência.

    ![][28]

10. No Gerenciador de Soluções, clique com botão direito do mouse em **OrderProcessingRole** criado na etapa 7 (não se esqueça de clicar com o botão direito do mouse em **OrderProcessingRole** em **Funções**, e não na classe). Clique em **Propriedades**.

11. Na guia **Configurações** da caixa de diálogo **Propriedades**, clique na caixa **Valor** de **Microsoft.ServiceBus.ConnectionString** e cole o valor do ponto de extremidade copiado na etapa 8.

    ![][29]

12. Crie uma classe **OnlineOrder** para representar os pedidos conforme você os processa na fila. Você pode reutilizar uma classe já criada. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **OrderProcessingRole** (clique com o botão direito do mouse no projeto, e não na função). Clique em **Adicionar** e clique em **Item Existente**.

13. Navegue até a subpasta de **FrontendWebRole\\Models** e clique duas vezes em **OnlineOrder.cs** para adicioná-la a esse projeto.

14. Em WorkerRole.cs, substitua o valor da variável **QueueName** em **WorkerRole.cs** de `"ProcessingQueue"` para `"OrdersQueue"` como no seguinte código:

        // The name of your queue
        const string QueueName = "OrdersQueue";

15. Adicione a seguinte instrução using na parte superior do arquivo WorkerRole.cs:

        using FrontendWebRole.Models;

16. Na função `Run()`, dentro da chamada `OnMessage`, adicione o seguinte código dentro da cláusula `try`:

        Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
        // View the message as an OnlineOrder
        OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
        Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
        receivedMessage.Complete();

17. Você concluiu o aplicativo. Você pode testar o aplicativo completo como você fez anteriormente, pressionando F5. Observe que o número de mensagens não é incrementado porque a função de trabalho processa itens da fila e os marca como concluído. Você pode ver a saída do rastreamento da função de trabalho, exibindo a interface do usuário do emulador de computação do Azure. É possível fazer isso clicando com o botão direito do mouse no ícone do emulador na área de notificação da sua barra de tarefas e selecionando **Mostrar Interface do Usuário do Compute Emulator**.

    ![][30]

    ![][31]

## <a name="nextsteps"></a><span class="short-header">Próximas etapas</span>Próximas etapas

Para obter mais informações sobre o Barramento de Serviço, consulte os seguintes recursos:

-   [Service Bus do Azure][Service Bus do Azure]
-   [Como é o Barramento de Serviço][Como é o Barramento de Serviço]
-   [Como usar as filas do Service Bus][Como usar as filas do Service Bus]

Para obter mais informações sobre cenários de várias camadas ou para saber como implantar um aplicativo para um serviço de nuvem, consulte:

-   [Aplicativo de várias camadas .NET usando Tabelas de armazenamento, Filas e Blobs][Aplicativo de várias camadas .NET usando Tabelas de armazenamento, Filas e Blobs]

Convém implementar o front-end de um aplicativo multicamadas em um site do Azure, em vez de um serviço de nuvem do Azure. Para saber mais sobre a diferença entre sites e serviços de nuvem, consulte [Modelos de execução do Azure][Modelos de execução do Azure].

Para implementar o aplicativo criado neste tutorial como um projeto Web padrão, em vez de uma função web do serviço de nuvem, siga as etapas neste tutorial com as seguintes diferenças:

1.  Ao criar o projeto, selecione o modelo de projeto **Aplicativo da Web do ASP.NET MVC 4** na categoria **Web** em vez do modelo **Serviço de Nuvem** na categoria **Nuvem**. Em seguida, siga as mesmas instruções para criar o aplicativo MVC até chegar a seção **Gerenciador de configuração de nuvem**.

2.  Ao criar a função de trabalho, crie em uma solução nova e separada, semelhante às instruções originais para a função Web. No entanto, você está criando a função de trabalho no projeto de serviço de nuvem. Em seguida, siga as mesmas instruções para criar a função de trabalho.

3.  Você pode testar o front-end e back-end separadamente ou executar simultaneamente em instâncias separadas do Visual Studio.

Para saber como implantar o front-end em um site do Azure, consulte [Implantando um aplicativo Web do ASP.NET em um site do Azure][Implantando um aplicativo Web do ASP.NET em um site do Azure]. Para saber como implantar o back-end em um serviço de nuvem do Azure, consulte [Aplicativo multicamadas do .NET usando tabelas, filas e blobs de armazenamento][Aplicativo de várias camadas .NET usando Tabelas de armazenamento, Filas e Blobs].

  [Próximas etapas]: #nextsteps
  [0]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [Filas do Azure e filas do Barramento de Serviço do Azure - comparadas e contrastadas]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh767287.aspx
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Obter as ferramentas e o SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [4]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png
  [5]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [10]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [aqui]: http://http://msdn.microsoft.com/pt-br/library/windowsazure/ff687127.aspx
  [11]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/VSProperties.png
  [12]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [13]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.jpg
  [14]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [15]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [16]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [17]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [18]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [19]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [20]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [21]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [22]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [23]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png
  [24]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorer.png
  [25]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerAddConnect.png
  [26]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [28]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [29]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [30]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [31]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [Service Bus do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee732537.aspx
  [Como é o Barramento de Serviço]: /pt-br/documentation/services/service-bus/
  [Como usar as filas do Service Bus]: /pt-br/develop/net/how-to-guides/service-bus-queues/
  [Aplicativo de várias camadas .NET usando Tabelas de armazenamento, Filas e Blobs]: /pt-br/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Modelos de execução do Azure]: http://www.windowsazure.com/pt-br/develop/net/fundamentals/compute/
  [Implantando um aplicativo Web do ASP.NET em um site do Azure]: http://www.windowsazure.com/pt-br/develop/net/tutorials/get-started/
