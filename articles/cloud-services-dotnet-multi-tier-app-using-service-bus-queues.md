<properties linkid="dev-net-e2e-multi-tier" urlDisplayName="Aplicativo multicamadas" pageTitle="Aplicativo multicamadas do .NET - Tutorial do Azure" metaKeywords="Tutorial de fila do Barramento de Serviço do Azure, tutorial de fila do Azure, tutorial de função de trabalho do Azure, tutorial de fila do .NET do Azure, tutorial de fila do C# do Azure, tutorial de função de trabalho do C# do Azure" description="Um tutorial que ajuda a desenvolver um aplicativo multicamadas no Azure que usa filas do Barramento de Serviço para se comunicar entre camadas. Amostras no .NET." metaCanonical="" services="cloud-services,service-bus" documentationCenter=".NET" title="Aplicativo multicamadas do .NET usando filas do Barramento de Serviço" authors="sethm" solutions="" manager="dwrede" editor="mattshel" />





#Aplicativo multicamada .NET usando filas do Brramento de Serviço

O desenvolvimento para o Azure é fácil com o Visual Studio 2013 e o
SDK do Azure gratuito para o .NET. Se ainda não tiver o Visual
Studio 2013, o SDK instalará automaticamente o Visual Studio Express 2013, para que você possa começar a desenvolver para o Azure de forma
totalmente gratuita. Este guia pressupõe que você não tem experiência anterior com o Windows
Azure. Na conclusão deste guia, você terá um aplicativo que usa
vários recursos do Azure em execução no ambiente local e
que demonstram como funciona um aplicativo multicamadas.

Você aprenderá a:

-   Habilitar o computador para o desenvolvimento do Azure com um
    único download e instalar.
-   Usar o Visual Studio para desenvolver para o Azure.
-   Criar um aplicativo multicamadas no Azure usando
    funções web e de trabalho.
-   Comunicar-se entre camadas usando filas do Barramento de Serviço.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

Neste tutorial você irá compilar e executar o aplicativo multicamadas em um serviço de nuvem do Azure. O front-end será uma função web MVC do ASP.NET e o back-end será uma função de trabalho. Você poderia criar o mesmo aplicativo multicamadas com o front-end como um projeto Web que deveria ser implantado em um site do Azure, em vez de um serviço de nuvem. Para obter instruções sobre o que fazer de forma diferente no front-end de um site do Azure, consulte a seção [Próximas etapas](#nextsteps).

A seguir está uma captura de tela do aplicativo concluído:

![][0]

**Observação** O Azure também oferece a funcionalidade da fila de armazenamento. Para obter mais informações sobre filas de armazenamento do Azure e o Barramento de Serviço, consulte [Filas do Azure e filas do Barramento de Serviço do Azure - comparadas e contrastadas][sbqueuecomparison].  

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

<h2><span class="short-header">Comunicação entre funções</span>Visão geral do cenário: comunicação entre funções</h2>

Para enviar um pedido para processamento, o componente de UI de front-end,
executar a função Web, é necessário interagir com a lógica de camada intermediária em execução
na função de trabalho. Este exemplo usa a mensagem detalhada do Brramento de Serviço orientado para
a comunicação entre as camadas de mensagens.

Usar mensagens de comunicação entre as camadas intermediária e da Web
separa os dois componentes. Ao contrário com a mensagem direta (isto é, TCP ou HTTP),
a camada da Web não se conecta com a camada intermediária diretamente; em vez disso,
envia unidades de trabalho, como mensagens, para o Brramento de Serviço, que mantém confiável
até a camada intermediária estar pronta para consumir e processar.

O Brramento de Serviço fornece duas entidades para dar suporte à comunicação de mensagens: Com filas, cada mensagem enviada para a fila é
consumida por um único destinatário. Tópicos suportam o padrão de publicação/assinatura
em que cada mensagem publicada é disponibilizada para cada
assinatura registrada com o tópico. Cada assinatura mantém
logicamente sua própria fila de mensagens. As assinaturas também podem ser
configuradas com as regras de filtro que restringem o conjunto de mensagens passado para
a fila de assinatura para aquelas que correspondem ao filtro. Este exemplo usa
filas do Brramento de Serviço.

![][1]

Esse mecanismo de comunicação oferece diversas vantagens sobre
mensagens diretas, ou seja:

-   **Desacoplamento temporal.** Com o padrão de mensagens assíncrono,
    os produtores e consumidores não precisam estar online ao mesmo tempo. O Service
Bus armazena de forma confiável as mensagens até que a parte de consumo esteja pronta
para recebê-las. Isso permite que os componentes do aplicativo
distribuído sejam desconectados voluntariamente, por exemplo, para
manutenção ou devido a uma falha de componente, sem afetar o
sistema como um todo. Além disso, o aplicativo de consumo só precisa
ser colocado online durante determinadas horas do dia.

-   **Nivelamento de carga**. Em muitos aplicativos, a carga do sistema varia ao
longo do tempo enquanto o tempo de processamento necessário para cada unidade de trabalho é
normalmente constante. Intermediar produtores de mensagem e consumidores
com uma fila significa que o aplicativo de consumo (o função de trabalho) só
precisa ser configurado para acomodar a carga média em vez de pico de
carga. A profundidade da fila crescerá e contrairá conforme a carga de entrada
variar. Isso economiza dinheiro diretamente em termos da quantidade de
infraestrutura necessária para atender à carga do aplicativo.

-   **Balanceamento de carga.** Conforme a carga aumenta, mais processos de função de trabalho podem ser
adicionados à leitura da fila. Cada mensagem é processada por apenas um
dos processos de trabalho. Além disso, esse balanceamento de carga com base em recepção
permite uma utilização ideal das máquinas de trabalho mesmo se as
máquinas de trabalho diferem em termos de capacidade de processamento, pois elas
irão receber mensagens em sua própria taxa máxima. Esse padrão geralmente é
chamado de padrão de consumidor concorrente.

    ![][2]

As seções a seguir discutem o código que implementa essa
arquitetura.

<h2><span class="short-header">Definir ambiente</span>Definir o ambiente de desenvolvimento</h2>

Antes de começar a desenvolver seu aplicativo do Azure, você precisa
obter as ferramentas e configurar seu ambiente de desenvolvimento.

1.  Para instalar o SDK do Azure para .NET, clique no botão abaixo:

    [Obter as ferramentas e o SDK][]

2. 	Clique em **Instalar o SDK**.

3. 	Escolha o link para a versão do Visual Studio que você está usando. As etapas deste tutorial usam o Visual Studio 2013:

	![][32]

4. 	Quando for solicitado a executar ou salvar o arquivo de instalação, clique
em **Executar**:

    ![][3]

5.  No Web Platform Installer, clique em **Instalar** e continue a instalação:

    ![][33]

6.  Quando a instalação for concluída, você terá todo
    o necessário para iniciar o desenvolvimento. O SDK inclui ferramentas que permitem
    desenvolver facilmente aplicativos do Azure no Visual Studio. Se você
não tiver o Visual Studio instalado, ele também instala o
Visual Studio Express gratuito para Web.

<h2><span class="short-header">Configurar o namespace</span>Configurar o namespace do Brramento de Serviço</h2>

A próxima etapa é para criar um namespace de serviço e para obter uma
chave secreta compartilhada. Um namespace de serviço fornece um limite de aplicativo para
cada aplicativo exposto por meio do Brramento de Serviço. Uma chave secreta compartilhada é
gerada automaticamente pelo sistema quando um namespace de serviço é
criado. A combinação do namespace de serviço e uma chave secreta compartilhada
oferece uma credencial para o Brramento de Serviço para autenticar o acesso a um
aplicativo.

Você também pode gerenciar namespaces e entidades de mensagens do Barramento de Serviço usando o Gerenciador de Servidores do Visual Studio, mas só pode criar novos namespaces dentro do portal. 

###Configurar o namespace usando o Portal de Gerenciamento

1. Faça logon no [Portal de Gerenciamento do Azure][].

2.  No painel de navegação à esquerda do Portal de Gerenciamento, clique em
    **Barramento de Serviço**.

3.  No painel inferior do Portal de Gerenciamento, clique em **Criar**.

    ![][6]

4.  Na caixa de diálogo **Adicionar um novo namespace**, digite um nome de namespace.
    O sistema imediatamente verifica para ver se o nome está disponível.   
    ![][7]

5.  Depois de verificar se o nome do namespace está disponível, escolha o
    país ou a região em que o namespace deve ser hospedado (não se esqueça
     de usar o mesmo país/região em que você está implantando os
    recursos de computação).

    IMPORTANTE: selecione a **mesma região** que você pretende escolher para implantar seu aplicativo. Isso lhe dará o melhor desempenho.

6.  Clique na marca de seleção. Agora, o sistema cria o namespace de serviço e o habilita. Talvez você precise aguardar vários minutos, conforme o sistema fornece recursos para sua conta.

	![][27]

7.  Na janela principal, clique no nome do namespace de serviço.

	![][30]

8. Clique em **Informações da Conexão**.

	![][31]

9.  No painel **Informações da conexão de acesso**, localize os valores **Emissor Padrão** e **Chave Padrão**.

10.  Anote a chave ou a copie para a área de transferência.

###Gerenciar namespaces e entidades de mensagens usando o Gerenciador de Servidores do Visual Studio

Para gerenciar um namespace e obter informações de conexão usando o Visual Studio em vez do Portal de Gerenciamento, siga o procedimento descrito [aqui](http://http://msdn.microsoft.com/pt-br/library/windowsazure/ff687127.aspx), na seção intitulada **Para se conectar ao Azure no Visual Studio**. Quando você entra no Azure, o nó **Barramento de Serviço** na árvore **Microsoft Azure** do Gerenciador de Servidores é populado automaticamente com todos os namespaces criados. Clique com o botão direito do mouse em qualquer namespace e clique em **Propriedades** para ver a cadeia de conexão e outros metadados associados a esse namespace exibido no painel **Propriedades** do Visual Studio. 

Anote o valor **SharedAccessKey** ou copie-o para a área de transferência:

![][34]

<h2><span class="short-header">Criar uma função web</span>Criar uma função web</h2>

Nesta seção, você criará o front-end de seu aplicativo. Primeiro,
você irá criar várias páginas que exibe seu aplicativo.
Depois disso, você adicionará código para enviar itens para uma fila do Brramento de Serviço
e exibir informações de status sobre a fila.

### Criar o projeto

1.  Usando privilégios de administrador, inicie o Microsoft Visual Studio
    2013 ou o Microsoft Visual Studio Express. Para iniciar o Visual
    Studio com privilégios de administrador, clique com o botão direito do mouse em **Microsoft Visual
    Studio 2013 (ou Microsoft Visual Studio Express)** e
    em **Executar como administrador**. O emulador de computação do Azure,
    abordado mais à frente neste guia, exige que o Visual Studio seja
    iniciado com privilégios de administrador.

    No Visual Studio, no menu **Arquivo**, clique em **Novo** e
    em **Projeto**.

    ![][8]


2.  Em **Modelos Instalados**, em **Visual C#**, clique em **Nuvem** e
    em **Serviço de Nuvem do Azure**. Nomeie o projeto
    **MultiTierApp**. Clique em **OK**.

    ![][9]

3.  Em funções do **.NET Framework 4.5**, clique duas vezes em **Função Web
    do ASP.NET**.

    ![][10]

4.  Focalize **WebRole1** em **Solução do Serviço de Nuvem do Azure**, clique
    no ícone de lápis e renomeie a função web para **FrontendWebRole**. Clique em **OK**. (Certifique-se de inserir "Front-end" com "e" minúsculo , não "Front-End".)

    ![][11]

5.  Na caixa de diálogo **Novo Projeto ASP.NET**, na lista **Selecionar um modelo**, clique em **MVC**,
    e em **OK**.

    ![][12]

6.  No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Referências**, clique em
    **Gerenciar Pacotes NuGet...** ou **Adicionar Referência do Pacote de Biblioteca**.

7.  Selecione **Online** no lado esquerdo da caixa de diálogo. Procure
    "**WindowsAzure**" e selecione o item **Barramento de Serviço
    do Azure**. Conclua a instalação e feche a caixa de diálogo.

    ![][13]

8.  Os assemblies de cliente obrigatórios agora são referenciados e alguns
    arquivos de código novos foram adicionados.

9.  No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Modelos**, clique em **Adicionar**
    e em **Classe**. Na caixa Nome, digite o nome
    **OnlineOrder.cs**. Clique em **Adicionar**.

### Escreva o código para a função Web

Nesta seção, você irá criar várias páginas que exibe seu
aplicativo.

1.  No arquivo **OnlineOrder.cs** do Visual Studio, substitua a
    definição do namespace existente pelo seguinte código:

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  No **Gerenciador de Soluções**, clique duas vezes em
    **Controllers\HomeController.cs**. Adicione as seguintes instruções **using**
    na parte superior do arquivo para incluir os namespaces do modelo
    que você acabou de criar, bem como o Barramento de Serviço:

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  Também no arquivo **HomeController.cs** do Visual Studio, substitua a
    definição do namespace existente pelo código a seguir. Esse código
contém métodos para lidar com o envio de itens na fila:

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

4.  No menu **Compilar**, clique em **Compilar Solução**.

5.  Agora, você criará a exibição do método **Submit()**
    criado acima. Clique com o botão direito do mouse no método Submit() e selecione
**Adicionar Exibição**.

    ![][14]

6.  Uma caixa de diálogo é exibida para criar a exibição. Selecione a classe
    **OnlineOrder** no menu suspenso **Classe do modelo** e escolha
    **Criar** no menu suspenso **Modelo**.

    ![][15]

7.  Clique em **Adicionar**.

8.  Agora, você alterará o nome exibido do aplicativo. No
    **Gerenciador de Soluções**, clique duas vezes no arquivo
    **Views\Shared\\_Layout.cshtml** para abri-lo no editor do Visual
    Studio.

9.  Substitua todas as ocorrências de **Meu Aplicativo MVC do ASP.NET** por
    **Produtos Incríveis da LITWARE**.

10.	Substitua **"seu logotipo aqui"** com **Produtos Incríveis da LITWARE**:

	![][16]

11. Remova os links **Página Inicial**, **Sobre** e **Contato**. Exclua o código destacado:

	![][28]
  

12. Por fim, modifique a página de envio para incluir algumas informações sobre
    a fila. No **Gerenciador de Soluções**, clique duas vezes no arquivo
    **Views\Home\Submit.cshtml** para abri-lo no editor do Visual
    Studio. Adicione a linha a seguir depois de **&lt;h2>Enviar&lt;/h2>**. Por enquanto,
    **ViewBag.MessageCount** permanece vazio. Você irá preenchê-lo mais tarde.

        <p>Número atual de pedidos na fila aguardando para serem processados: @ViewBag.MessageCount</p>
             

13. Você já implementou a interface do usuário. Você pode pressionar **F5** para executar
o aplicativo e confirmar que parece conforme o esperado.

    ![][17]

### Escreva o código para enviar itens para uma fila do Brramento de Serviço

Agora, você adicionará o código para enviar itens para uma fila. Primeiro, você irá
criar uma classe que contém as informações de conexão de fila do
Brramento de Serviço. Em seguida, você irá inicializar a conexão do
**Global.aspx.cs**. Por fim, você atualizará o código de envio
criado anteriormente em **HomeController.cs** para efetivamente enviar itens para uma
fila do Barramento de Serviço.

1.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **FrontendWebRole** (clique com o botão direito no projeto, e não na função). Clique em **Adicionar** e depois em **Classe**.

2.  Nomeie a classe **QueueConnector.cs**. Clique em **Adicionar** para criar a classe.

3.  Agora, você colará o código que encapsula as informações da
    conexão e contém métodos para inicializar a conexão em
    uma fila do Barramento de Serviço. Em QueueConnector.cs, cole o código a seguir e insira
    valores para **Namespace**, **IssuerName** e **IssuerKey**. Você pode
    obter esses valores no [Portal de Gerenciamento][Portal de Gerenciamento do Azure] ou no Gerenciador de Servidores do Visual Studio no nó **Barramento de Serviço**.

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

4.  Agora, você garantirá que o método **Initialize** seja chamado. No **Gerenciador de Soluções**, clique duas vezes em **Global.asax\Global.asax.cs**.

5.  Adicione a seguinte linha na parte inferior do método **Application_Start**
    :

        FrontendWebRole.QueueConnector.Initialize();

6.  Por fim, você atualizará o código da Web criado anteriormente, para
    enviar itens para a fila. No **Gerenciador de Soluções**,
    clique duas vezes em **Controllers\HomeController.cs** criado
    anteriormente.

7.  Atualize o método **Submit()** da seguinte maneira para obter a contagem de mensagens
    da fila:

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

8.  Atualize o método **Submit(OnlineOrder order)** da seguinte maneira para enviar
    informações do pedido para a fila:

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

9.  Agora você pode reexecutar o aplicativo. Cada vez que você enviar um
pedido, o número de mensagens aumenta.

    ![][18]

<h2><span class="short-header">Gerenciador de configuração</span>Gerenciador de configuração de nuvem</h2>

O Azure dá suporte a um conjunto de APIs gerenciadas que oferece uma maneira consistente de criar novas instâncias de clientes de serviço do Azure (por exemplo, o Barramento de Serviço) em serviços de nuvem da Microsoft. Essas APIs permitem criar instâncias desses clientes (por exemplo, **CloudBlobClient**, **QueueClient**, **TopicClient**), independentemente de onde o aplicativo esteja hospedado – no local, em um serviço de nuvem da Microsoft, em sites ou em uma função VM persistente. Você também pode usar essas APIs para recuperar as informações de configuração necessárias para instanciar esses clientes e alterar a configuração sem precisar reimplantar o aplicativo de chamada. As APIs estão localizadas na classe [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][]. Também existem APIs no lado do cliente.

### Connection string

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

<h2><span class="short-header">Criar uma função de trabalho</span>Criar a função de trabalho</h2>

Agora você irá criar a função de trabalho que processa o
envio de pedidos. Este exemplo usa o modelo de projeto do Visual Studio **Função de Trabalho com Fila do Brramento de Serviço**. Primeiro, você usará o Gerenciador de Servidores no Visual Studio para obter as credenciais necessárias.

1. Se você já conectou o Visual Studio à conta do Azure, conforme descrito na seção **Configurar o namespace usando o Gerenciador de Servidores do Visual Studio**, passe à etapa 5. 

3. Na barra de menus do Visual Studio, escolha **Exibir** e clique em **Gerenciador de Servidores**. Um nó **Barramento de Serviço** é exibido em **Azure** dentro da hierarquia do Gerenciador de Servidores, conforme mostrado na figura a seguir.

	![][21]

2. No Gerenciador de Servidores, expanda **Azure**, clique com o botão direito do mouse em **Barramento de Serviço** e clique em **Adicionar Nova Conexão**.

3. Na caixa de diálogo **Adicionar Conexão**, digite o nome do namespace de serviço, o nome do emissor e a chave do emissor. Clique em **OK** para conectar.

	![][22]

4.  No Visual Studio, no **Gerenciador de Soluções**, clique com o botão direito na pasta
    **Funções** no projeto **MultiTierApp**.

5.  Clique em **Adicionar** e em **Novo Projeto da Função de Trabalho**. A caixa de diálogo **Adicionar Novo Projeto da Função** é exibida.

	![][26]

6.  Na caixa de diálogo **Adicionar Novo Projeto de Função**, clique em **Função de Trabalho com Fila do Barramento de Serviço**, como na seguinte figura:

	![][23]

7.  Na caixa **Nome**, nomeie o projeto **OrderProcessingRole**. Clique em **Adicionar**.

8.  No Gerenciador de Servidores, clique com botão direito do mouse no namespace de serviço e clique em **Propriedades**. No painel **Propriedades** do Visual Studio, a primeira entrada contém uma cadeia de conexão populada com o ponto de extremidade do namespace de serviço contendo as credenciais de autorização obrigatórias. Por exemplo, consulte a figura a seguir. Clique duas vezes em **ConnectionString** e pressione **Ctrl+C** para copiar essa cadeia de caracteres para a área de transferência.

	![][24]

9.  No Gerenciador de Soluções, clique com botão direito do mouse em **OrderProcessingRole** criado na etapa 7 (não se esqueça de clicar com o botão direito do mouse em **OrderProcessingRole** em **Funções**, e não na classe). Clique em **Propriedades**.

10.  Na guia **Configurações** da caixa de diálogo **Propriedades**, clique na caixa **Valor** de **Microsoft.ServiceBus.ConnectionString** e cole o valor do ponto de extremidade copiado na etapa 8.

	![][25]

11.  Crie uma classe **OnlineOrder** para representar os pedidos conforme você os processa na fila. Você pode reutilizar uma classe já criada. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **OrderProcessingRole** (clique com o botão direito do mouse no projeto, e não na função). Clique em **Adicionar** e clique em **Item Existente**.

12. Navegue até a subpasta de **FrontendWebRole\Models** e clique duas vezes em **OnlineOrder.cs** para adicioná-la a esse projeto.

13. Em WorkerRole.cs, substitua o valor da variável **QueueName** em **WorkerRole.cs** de `"ProcessingQueue"` para `"OrdersQueue"` como no seguinte código:

		// The name of your queue
		const string QueueName = "OrdersQueue";

14. Adicione a seguinte instrução using na parte superior do arquivo WorkerRole.cs:

		using FrontendWebRole.Models;

15. Na função `Run()` dentro da chamada `OnMessage`, adicione o seguinte código dentro da cláusula `try`:

		Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
		// View the message as an OnlineOrder
		OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
		Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
		receivedMessage.Complete();

16.  Você concluiu o aplicativo. Você pode testar todo o
    aplicativo como fez anteriormente, pressionando F5. A contagem de mensagens não é incrementada porque a função de trabalho processa itens da fila e os marca como concluídos. Você pode ver a saída do rastreamento da
    função de trabalho, exibindo a interface do usuário do emulador de computação do Azure. É
possível fazer isso clicando com o botão direito do mouse no ícone do emulador na área de
notificação da sua barra de tarefas e selecionando **Mostrar Interface do Usuário do Compute Emulator**.

    ![][19]

    ![][20]

<h2><a name="nextsteps"></a><span class="short-header">Próximas etapas</span>Próximas etapas</h2>  

Para obter mais informações sobre o Brramento de Serviço, consulte os seguintes recursos:  
  
* [Barramento de Serviço do Azure][sbmsdn]  
* [Tutoriais do Barramento de Serviço][sbwacom]  
* [Como usar filas do Barramento de Serviço][sbwacomqhowto]  

Para obter mais informações sobre cenários de várias camadas ou para saber como implantar um aplicativo para um serviço de nuvem, consulte:  

* [Aplicativo de várias camadas .NET usando Tabelas de armazenamento, Filas e Blobs][mutitierstorage]  

Convém implementar o front-end de um aplicativo multicamadas em um site do Azure, em vez de um serviço de nuvem do Azure. Para saber mais sobre a diferença entre sites e serviços de nuvem, consulte [Modelos de execução do Azure][executionmodels].

Para implementar o aplicativo criado neste tutorial como um projeto Web padrão, em vez de uma função web do serviço de nuvem, siga as etapas neste tutorial com as seguintes diferenças:

1. Ao criar o projeto, escolha o modelo de projeto **Aplicativo Web MVC 4 do ASP.NET** na categoria **Web** em vez do modelo **Serviço de Nuvem** na categoria **Nuvem**. Em seguida, siga as mesmas instruções para criar o aplicativo MVC até chegar a seção **Gerenciador de configuração de nuvem**.

2. Ao criar a função de trabalho, crie-a em uma solução nova e separada, semelhante às instruções originais para a função web. No entanto, você está criando a função de trabalho no projeto de serviço de nuvem. Em seguida, siga as mesmas instruções para criar a função de trabalho.

3. Você pode testar o front-end e o back-end separadamente ou executá-los simultaneamente em instâncias separadas do Visual Studio.

Para saber como implantar o front-end em um site do Azure, consulte [Implantando um aplicativo Web do ASP.NET em um site do Azure](http://www.windowsazure.com/pt-br/develop/net/tutorials/get-started/). Para saber como implantar o back-end em um serviço de nuvem do Azure, consulte [Aplicativo multicamadas do .NET usando tabelas, filas e blobs de armazenamento][mutitierstorage].


  [0]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh767287.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Obter as ferramentas e o SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png
  
  
  
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.jpg
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
  [sbmsdn]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee732537.aspx  
  [sbwacom]: /pt-br/manage/services/service-bus/  
  [sbwacomqhowto]: /pt-br/develop/net/how-to-guides/service-bus-queues/  
  [mutitierstorage]: /pt-br/develop/net/tutorials/multi-tier-web-site/1-overview/ 
  [executionmodels]: http://www.windowsazure.com/pt-br/develop/net/fundamentals/compute/

