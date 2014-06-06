<properties linkid="dev-net-how-to-service-bus-relay" urlDisplayName="Retransmissão de Service Bus" pageTitle="Como usar a Retransmissão de Service Bus (.NET) - Azure" metaKeywords="Introdução à Retransmissão de Service Bus C# do Azure " description="Saiba como usar o serviço de Retransmissão de Service Bus do Azure para conectar dois aplicativos hospedados em locais diferentes." metaCanonical="" services="service-bus" documentationCenter=".NET" title="Como usar o serviço de Retransmissão de Service Bus" authors=""  solutions="" writer="sethm" manager="dwrede" editor="mattshel"  />







# Como usar o serviço de Retransmissão de Service Bus

Este guia mostra como usar o serviço de Retransmissão de Service Bus.
As amostras são escritas em C# e usam a API do Windows Communication Foundation com extensões contidas no assemby do Service Bus que faz parte das bibliotecas do .NET para Azure. Para obter mais informações sobre a Retransmissão de Service Bus, consulte a seção [Próximas etapas][]
.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

<h2><span class="short-header">O que é a Retransmissão de Service Bus</span>O que é a Retransmissão de Service Bus</h2>

O serviço de **Retransmissão** do Service Bus permite criar **aplicativos híbridos** que são executados em um datacenter do Azure e em seu próprio ambiente corporativo local. A Retransmissão de Service Bus facilita isso permitindo que você exponha com segurança os serviços do Windows Communication Foundation (WCF) que residem em uma rede corporativa para a nuvem pública, sem precisar abrir uma conexão de firewall ou exigir mudanças intrusivas em uma infraestrutura de rede corporativa.

![Conceitos de retransmissão](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

A Retransmissão de Service Bus permite que você hospede serviços WCF em seu ambiente corporativo existente. Você pode delegar a escuta de sessões e solicitações de entrada para esses serviços WCF ao Service Bus em execução no Azure. Isso permite que você exponha esses serviços para o código do aplicativo em execução no Azure ou para usuários móveis ou ambientes de parceiros na extranet. O Service Bus permite que você controle com segurança quem pode acessar esses serviços em um nível granular refinado. Ele fornece uma maneira poderosa e segura de expor a funcionalidade do aplicativo e os dados de suas soluções corporativas existentes e tira proveito disso na nuvem.

Este guia demonstra como usar a Retransmissão de Service Bus para criar um serviço Web do WCF exposto usando uma associação de canal TCP, que implementa uma conversa segura entre duas partes.

<h2><span class="short-header">Criar um namespace de serviço</span>Criar um namespace de serviço</h2>

Para começar a usar a Retransmissão de Service Bus do Azure, você deve primeiro criar um namespace de serviço. Um namespace de serviço fornece um contêiner de escopo para lidar com os recursos do Service Bus no aplicativo.

Para criar um namespace de serviço:

1.  Faça logon no [Portal de Gerenciamento do Azure][].

2.  No painel de navegação esquerdo do Portal de Gerenciamento, clique em **Service Bus**.

3.  No painel inferior do Portal de Gerenciamento, clique em **Criar**.   

	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)

4.  Na caixa de diálogo **Adicionar um novo namespace**, digite um nome de namespace.
    O sistema imediatamente verifica se o nome está disponível.   

	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-04.png)


5.  Depois de verificar se o nome do namespace está disponível, escolha o país ou a região em que o namespace deve ser hospedado (certifique-se de usar o mesmo país/região em que você está implantando os recursos de computação).

	IMPORTANTE: selecione a **mesma região** que você pretende escolher para implantar seu aplicativo. Isso propicia melhor desempenho.

6.	Clique na marca de seleção. O sistema agora cria o namespace de serviço e o habilita. Talvez você precise aguardar vários minutos enquanto o sistema provisiona recursos para sua conta.

	![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-27.png)

	O namespace que você criou aparece no Portal de Gerenciamento e demora algum tempo para ser ativado. Aguarde até que o status seja **Ativo** para continuar.

<h2><span class="short-header">Obter credenciais de gerenciamento</span>Obter as credenciais de gerenciamento padrão do namespace</h2>

Para executar operações de gerenciamento, como a criação de uma conexão de retransmissão, no novo namespace, você deve obter as credenciais de gerenciamento para o namespace.

1.  No painel de navegação esquerdo, clique no nó **Service Bus** para exibir a lista de namespaces disponíveis:   
	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)


2.  Selecione o namespace que você acabou de criar na lista abaixo:   
	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-09.png)


3.  Clique em **Informações de Conexão**.   
	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-06.png)
 
4.  Na caixa de diálogo **Informações de conexão de acesso**, localize as entradas de **Emissor Padrão** e **Chave Padrão**. Tome nota desses valores, pois você usará essas informações abaixo para executar operações com o namespace.

<h2><span class="short-header">Obter o pacote do NuGet</span>Obter o pacote do NuGet do Service Bus</h2>

O pacote do **NuGet** do Service Bus é a maneira mais fácil de obter a API do Service Bus e configurar seu aplicativo com todas as dependências do Service Bus. A extensão do Visual Studio do NuGet facilita a instalação e a atualização de bibliotecas e ferramentas do Visual Studio e do Visual Studio Express 2012 para Web. O pacote do NuGet do Service Bus é a maneira mais fácil de obter a API do Service Bus e configurar seu aplicativo com todas as dependências do Service Bus.

Para instalar o pacote do NuGet em seu aplicativo, proceda da seguinte maneira:

1.  No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** e clique em **Gerenciar Pacotes do NuGet**.
2.  Pesquise por WindowsAzure"e selecione o item **Service Bus do Azure**. Clique em **Instalar** para concluir a instalação e fechar essa caixa de diálogo.

	![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)
  

<h2><span class="short-header">Expor e consumir um serviço Web SOAP</span>Como usar o Service Bus para a expor e consumir um serviço Web SOAP com TCP</h2>

Para expor um serviço Web SOAP WCF existente para consumo externo, você deve alterar as associações e endereços do serviço. Isso pode exigir alterações em seu arquivo de configuração ou exigir alterações de código, dependendo de como você instalou e configurou seus serviços WCF. Observe que o WCF permite ter vários pontos de extremidade de rede sobre o mesmo serviço, portanto, você pode manter os pontos de extremidade internos existentes ao adicionar pontos de extremidade do Service Bus para acesso externo ao mesmo tempo.

Nesta tarefa, você criará um serviço WCF simples e adicionará um ouvinte do Service Bus a esse serviço. Este exercício pressupõe alguma familiaridade com o Visual Studio 2012 e, portanto, não explica todos os detalhes da criação de um projeto. Em vez disso, ele se concentra no código.

Antes de iniciar as etapas a seguir, conclua o seguinte procedimento para configurar seu ambiente:

1.  No Visual Studio, crie um aplicativo de console que contenha dois projetos: "Cliente" e "Serviço" na solução.
2.  Defina a estrutura de destino dos dois projetos como o .NET Framework 4.
3.  Adicione o pacote **NuGet do Service Bus do Azure** para os dois projetos.
    Isso adiciona todas as referências ao assembly necessárias para seus projetos.

### Como criar o serviço

Primeiro, crie o serviço. Qualquer serviço WCF consiste em pelo menos três partes distintas:

-   Definição de um contrato que descreve quais mensagens são trocadas e quais operações devem ser chamadas. 
-   Implementação do contrato mencionado.
-   Host que hospeda esse serviço e expõe vários pontos de extremidade.

Os exemplos de código desta seção abordam cada um desses componentes.

O contrato define uma única operação, **AddNumbers**, que adiciona dois números e retorna o resultado. A interface **IProblemSolverChannel** permite que o cliente gerencie mais facilmente o ciclo de vida do proxy. A criação dessa interface é considerada uma prática recomendada. É uma boa ideia colocar essa definição de contrato em um arquivo separado para que você possa fazer referência a ele nos dois projetos, "Cliente" e "Serviço", mas você também pode copiar o código nos dois projetos:

        using System.ServiceModel;
     
        [ServiceContract(Namespace = "urn:ps")]
        interface IProblemSolver
        {
            [OperationContract]
            int AddNumbers(int a, int b);
        }
     
        interface IProblemSolverChannel : IProblemSolver, IClientChannel {}

Com o contrato estabelecido, a implementação é trivial:

        class ProblemSolver : IProblemSolver
        {
            public int AddNumbers(int a, int b)
            {
                return a + b;
            }
        }

**Como configurar um host de serviço de forma programática**

Com o contrato e a implementação estabelecidos, você pode hospedar o serviço. A hospedagem ocorre dentro de um objeto **System.ServiceModel.ServiceHost**, que cuida do gerenciamento de instâncias do serviço e hospeda os pontos de extremidade que escutam mensagens. O código a seguir configura o serviço com um ponto de extremidade local normal e um ponto de extremidade do Service Bus para ilustrar a aparência, lado a lado, dos pontos de extremidade internos e externos. Substitua a cadeia de caracteres "\*\*namespace\*\*" pelo nome de seu namespace e "\*\*key\*\*" pela chave do emissor obtida na etapa de configuração acima. 

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

    sh.AddServiceEndpoint(
       typeof (IProblemSolver), new NetTcpBinding(), 
       "net.tcp://localhost:9358/solver");

    sh.AddServiceEndpoint(
       typeof(IProblemSolver), new NetTcpRelayBinding(), 
       ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver"))
        .Behaviors.Add(new TransportClientEndpointBehavior {
              TokenProvider = TokenProvider.CreateSharedSecretTokenProvider( "owner", "**key**")});

    sh.Open();

    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();

    sh.Close();

No exemplo, você cria dois pontos de extremidade que estão na mesma implementação de contrato. Um é local e o outro é projetado por meio do Service Bus. As principais diferenças entre eles são as associações. **NetTcpBinding** para o local e **NetTcpRelayBinding** para o ponto de extremidade e os endereços do Service Bus. O ponto de extremidade local tem um endereço de rede local com uma porta distinta. O ponto de extremidade do Service Bus tem um endereço de ponto de extremidade composto da cadeia de caracteres "sb", o nome do seu namespace e o "solver" de caminho. Isso resulta no URI "sb://[serviceNamespace].servicebus.windows.net/solver" identificando o ponto de extremidade de serviço como um ponto de extremidade TCP do Service Bus com um nome DNS externo totalmente qualificado. Se colocar o código substituindo os espaços reservados, conforme explicado acima, na função **Principal** do aplicativo "Serviço", você terá um serviço funcional. Se desejar que o serviço escute exclusivamente o Service Bus, remova a declaração de ponto de extremidade local.

**Como configurar um host de serviço no arquivo App.config**

Você também pode configurar o host usando o arquivo App.config. O código de hospedagem do serviço nesse caso é o seguinte:

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
    sh.Open();
    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();
    sh.Close();

As definições de ponto de extremidade são movidas para o arquivo App.config. Observe que o pacote do **NuGet** já adicionou uma série de definições ao arquivo App.config que são as extensões da configuração necessárias para o Service Bus. O seguinte trecho de código que é o equivalente exato do código listado acima, deve aparecer diretamente sob o elemento **system.serviceModel**. Esse trecho pressupõe que o nome do namespace de seu projeto C\# é "Service".
Substitua os espaços reservados pelo namespace e a chave do Service Bus.

    <services>
        <service name="Service.ProblemSolver">
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpBinding"
                      address="net.tcp://localhost:9358/solver"/>
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpRelayBinding"
                      address="sb://**namespace**.servicebus.windows.net/solver"
                      behaviorConfiguration="sbTokenProvider"/>
        </service>
    </services>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

Depois de fazer essas alterações, o serviço é iniciado como antes, mas com dois pontos de extremidade ativos: um local e um escutando na nuvem.

### Como criar o cliente

**Como configurar um cliente de forma programática**

Para consumir o serviço, você pode construir um cliente WCF usando um objeto **ChannelFactory**. O Service Bus usa um modelo de segurança baseado em declarações implementado com o ACS (Access Control Service). A classe **TokenProvider** representa um provedor de token de segurança com métodos de fábrica internos que retornam alguns provedores de token conhecidos. O exemplo abaixo usa **SharedSecretTokenProvider** para armazenar as credenciais secretas compartilhadas e lida com a aquisição dos tokens apropriados do Access Control Service. O nome e a chave são os obtidos no portal, conforme descrito na seção anterior.

Primeiro, faça referência ou copie o código do contrato **IProblemSolver** do serviço no projeto do cliente.

Em seguida, substitua o código do método **Main** do cliente novamente substituindo o texto do espaço reservado pelo namespace e a chave do serviço Service Bus:

    var cf = new ChannelFactory<IProblemSolverChannel>(
        new NetTcpRelayBinding(), 
        new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver")));

    cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
                { TokenProvider = TokenProvider.CreateSharedSecretTokenProvider("owner","**key**") });
     
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

Agora você pode compilar o cliente e o serviço, executá-los (execute o serviço primeiro) e o cliente irá chamar o serviço e imprimir "9". Você pode executar o cliente e o servidor em máquinas diferentes, até entre redes, e a comunicação ainda funcionará. O código do cliente também pode ser executado na nuvem ou localmente.

**Como configurar um cliente no arquivo App.config**

Você também pode configurar o cliente usando o arquivo App.config. O código do cliente para isso é o seguinte:

    var cf = new ChannelFactory<IProblemSolverChannel>("solver");
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

As definições de ponto de extremidade são movidas para o arquivo App.config. O seguinte trecho de código que é o mesmo código listado acima, deve aparecer diretamente sob o elemento **system.serviceModel**. Aqui, como antes, você deve substituir os espaços reservados pelo namespace e a chave do Service Bus.

    <client>
        <endpoint name="solver" contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://**namespace**.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </client>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

<h2><span class="short-header">Próximas etapas</span>Próximas etapas</h2>

Agora que você já aprendeu os conceitos básicos do serviço de **Retransmissão** de Service Bus, siga estes links para obter mais informações.

-   Criando um serviço: [Criando um serviço para o Service Bus][].
-   Criando o cliente: [Criando um aplicativo cliente do Service Bus][].
-   Exemplos de Service Bus: baixe em [Exemplos do Azure][].

  [Próximas etapas]: #next_steps
  [O que é a Retransmissão de Service Bus]: #what-is
  [Criar um namespace de serviço]: #create_namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain_credentials
  [Obtenha o pacote do NuGet do Service Bus]: #get_nuget_package
  [Como usar o Service Bus para expor e consumir um serviço Web SOAP com TCP]: #how_soap
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
   [Criando um serviço para o Service Bus]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee173564.aspx
  [Criando um aplicativo cliente do Service Bus]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee173543.aspx
  [Exemplos do Azure]: http://code.msdn.microsoft.com/windowsazure

