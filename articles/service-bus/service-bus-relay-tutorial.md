<properties 
   pageTitle="Tutorial de sistema de mensagens de retransmissão do Barramento de Serviço | Microsoft Azure"
   description="Criar um serviço e um aplicativo cliente do Barramento de Serviço usando o sistema de mensagens de retransmissão do Barramento de Serviço."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="sethm" />

# Tutorial de sistema de mensagens de retransmissão do Barramento de Serviço

Este tutorial descreve como compilar um serviço e aplicativo simples de cliente do Barramento de Serviço usando as funcionalidades de "retransmissão" do barramento de serviço. Para obter um tutorial semelhante que use o [sistema de mensagens agenciado](service-bus-messaging-overview.md#Brokered-messaging) do Barramento de Serviço, confira o [Tutorial do Sistema de Mensagens Agenciado .NET do Barramento de Serviço](service-bus-brokered-tutorial-dotnet.md).

Trabalhar com este tutorial fornece uma compreensão das etapas necessárias para criar um aplicativo de serviço e cliente do Barramento de Serviço. Assim como suas contrapartes WCF, um serviço é um constructo que expõe um ou mais pontos de extremidade, cada um dos quais expõe uma ou mais operações de serviço. O ponto de extremidade de serviço especifica um endereço em que o serviço pode ser encontrado, uma associação que contém as informações que um cliente deve comunicar com o serviço e um contrato que define a funcionalidade fornecida pelo serviço a seus clientes. A principal diferença entre um WCF e um serviço de Barramento de Serviço é que o ponto de extremidade é exposto na nuvem, em vez de localmente em seu computador.

Depois de trabalhar passando pela sequência de tópicos neste tutorial, você terá um serviço em execução e um cliente que pode invocar as operações do serviço. O primeiro tópico descreve como configurar uma conta. As próximas etapas descrevem como definir um serviço que usa um contrato, como implementar o serviço e como configurar o serviço no código. Eles também descrevem como hospedar e executar o serviço. O serviço é criado é auto-hospedado e o cliente e o serviço são executados no mesmo computador. Você pode configurar o serviço usando código ou um arquivo de configuração.

As três etapas finais descrevem como criar um aplicativo cliente, configurá-lo e usar um cliente que pode acessar a funcionalidade do host.

Todos os tópicos desta seção supõem que você está usando o Visual Studio como o ambiente de desenvolvimento.

## Inscreva-se para uma Conta

A primeira etapa é criar um namespace de serviço do Barramento de Serviço e obter uma chave de SAS (Assinatura de Acesso Compartilhado). Um namespace de serviço fornece um limite de aplicativo para cada aplicativo exposto por meio do Barramento de Serviço. A combinação do namespace de serviço e a chave SAS fornece uma credencial para o Barramento de Serviço autenticar o acesso a um aplicativo.

1. Para criar um namespace de serviço, acesse o [portal clássico do Azure][]. Clique em **Barramento de Serviço** no lado esquerdo e clique em **Criar**. Digite um nome para o namespace e clique na marca de seleção.

	>[AZURE.NOTE] Você não precisa usar o mesmo namespace para aplicativos cliente e de serviço.

1. Na janela principal do portal, clique no nome do namespace de serviço criado na etapa anterior.

2. Clique em **Configurar** para exibir as políticas de acesso compartilhado padrão para seu namespace.

3. Anote a chave primária para a política **RootManageSharedAccessKey** ou copie-a na área de transferência. Você usará este valor posteriormente neste tutorial.

## Definir um contrato de serviço WCF para usar com o Barramento de Serviço

O contrato de serviço especifica a quais operações (a terminologia do serviço Web para funções ou métodos) o serviço dá suporte. Os contratos são criados pela definição de uma interface C++, C# ou Visual Basic. Cada método na interface corresponde a uma operação de serviço específica. O atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) deve ser aplicado a cada interface e o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) deve ser aplicado a cada operação. Se um método em uma interface que tem o atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) não tiver o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), tal método não será exposto. O código para essas tarefas é fornecido no exemplo logo após o procedimento. Para uma discussão mais ampla de contratos e serviços, consulte [Projetar e Implementar Serviços](https://msdn.microsoft.com/library/ms729746.aspx) na documentação do WCF.

### Para criar um contrato de Barramento de Serviço com uma interface

1. Abra o Visual Studio como administrador clicando com o botão direito no programa no menu **Iniciar** e, em seguida, selecionando **Executar como administrador**.

1. Crie um novo projeto de aplicativo de console. Clique no menu **Arquivo** e selecione **Novo**, então, clique em **Projeto**. Na caixa de diálogo **Novo Projeto**, clique em **Visual C#** (se **Visual C#** não aparecer, procure em **Outras Linguagens**). Clique no modelo **Aplicativo de Console** e nomeie-o **EchoService**. Use o **Local** padrão. Clique em **OK** para criar o projeto.

1. Adicione uma referência a `System.ServiceModel.dll` ao projeto: no Gerenciador de Soluções, clique com o botão direito do mouse na pasta **Referências**, na pasta do projeto e clique em **Adicionar Referência**. Selecione a guia **.NET** na caixa de diálogo **Adicionar Referência** e role para baixo até ver **System.ServiceModel**. Selecione-o e, em seguida, clique em **OK**.

1. No Gerenciador de Soluções, clique duas vezes no arquivo Program.cs para abri-lo no editor.

1. Adicione uma declaração `using` para o namespace System.ServiceModel.

	```
	using System.ServiceModel;
	```

	[System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) é o namespace que permite o acesso programático aos recursos básicos do WCF. O Barramento de Serviço usa vários dos objetos e atributos do WCF para definir contratos de serviço.

1. Altere o nome do namespace, de seu nome padrão **EchoService** para **Microsoft.ServiceBus.Samples**.

	>[AZURE.IMPORTANT] Este tutorial usa o namespace **Microsoft.ServiceBus.Samples** do C#, que é o namespace do tipo gerenciado de contrato que é usado no arquivo de configuração na etapa [Configurar o cliente WCF](#configure-the-wcf-client). Você pode especificar qualquer namespace desejado ao compilar esta amostra; no entanto, o tutorial não funcionará a menos que você modifique os namespaces do contrato e do serviço de modo correspondente, no arquivo de configuração de aplicativo. O namespace especificado no arquivo App.config deve ser o mesmo que o namespace especificado em seus arquivos C#.

1. Imediatamente após a declaração do namespace `Microsoft.ServiceBus.Samples`, mas ainda dentro do namesapace, defina uma nova interface chamada `IEchoContract` e aplique o atributo `ServiceContractAttribute` à interface com um valor de ****http://samples.microsoft.com/ServiceModel/Relay/**. O valor do namespace é diferente do namespace que você usa em todo o escopo do seu código. Em vez disso, o valor do namespace é usado como um identificador exclusivo para este contrato. Especificar o namespace de forma explícita impede a adição do valor de namespace padrão ao nome do contrato.

	```
	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	publicinterface IEchoContract
	{
	}
	```

	>[AZURE.NOTE] Normalmente, o namespace de contrato de serviço contém um esquema de nomenclatura que inclui informações de versão. A inclusão de informações de versão no namespace de contrato de serviço permite que os serviços isolem as alterações principais, definindo um novo contrato de serviço com um novo namespace e expondo-o em um novo ponto de extremidade. Dessa maneira, os clientes podem continuar a usar o antigo contrato de serviço sem que ele precise ser atualizado. Informações de versão podem consistir de uma data ou um número da versão. Para saber mais, veja [Controle de Versão do Serviço](http://go.microsoft.com/fwlink/?LinkID=180498). Para os fins deste tutorial, o esquema de nomenclatura do namespace do contrato de serviço não contém informações de versão.

1. Dentro da interface `IEchoContract`, declare um método para a operação individual exposta pelo contrato `IEchoContract` na interface e aplique o atributo `OperationContractAttribute` ao método que você deseja expor como parte do contrato de Barramento de Serviço público.

	```
	[OperationContract]
	string Echo(string text);
	```

1. Fora do contrato, declare um canal que herde de ambas as `IEchoChannel` e também para a interface `IClientChannel`, conforme mostrado aqui:

	```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    publicinterface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    publicinterface IEchoChannel : IEchoContract, IClientChannel { }
	```

	Um canal é o objeto WCF por meio do qual o host e o cliente trocam informações. Posteriormente, você vai escrever código contra o canal para repercutir informações entre os dois aplicativos.

1. No menu **Build**, clique em **Solução de Build** ou pressione F6 para confirmar a precisão de seu trabalho até o momento.

### Exemplo

O código a seguir mostra uma interface básica que define um contrato de Barramento de Serviço.

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Agora que a interface está criada, você pode implementar a interface.

## Implementar o contrato WCF para usar o Barramento de Serviço

A criação de um serviço de Barramento de Serviço no exige primeiro a criação do contrato, que é definido por meio de uma interface. Para obter mais informações sobre como criar a interface, consulte a etapa anterior. A próxima etapa é implementar a interface. Isso envolve a criação de uma classe chamada `EchoService` que implementa a interface `IEchoContract` definida pelo usuário. Depois de implementar a interface, você a configura usando um arquivo App.config. O arquivo de configuração contém as informações necessárias para o aplicativo, como o nome do serviço, o nome do contrato e o tipo de protocolo usado para se comunicar com o Barramento de Serviço. O código usado para essas tarefas é fornecido no exemplo logo após o procedimento. Para obter uma discussão mais geral sobre como implementar um contrato de serviço, consulte [Implementar Contratos de Serviço](https://msdn.microsoft.com/library/ms733764.aspx) na documentação do WCF (Windows Communication Foundation).

1. Crie uma nova classe chamada `EchoService` diretamente após a definição da interface `IEchoContract`. A classe `EchoService` implementa a interface `IEchoContract`. 

	```
	class EchoService : IEchoContract
	{
	}
	```
	
	Assim como outras implementações de interface, você pode implementar a definição em um arquivo diferente. No entanto, para este tutorial, a implementação está localizada no mesmo arquivo que a definição de interface e o método `Main`.

1. Aplique o atributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx), que indica o nome do serviço e o namespace.

	```[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	class EchoService : IEchoContract
	{
	}
	```

1. Implemente o método `Echo` definido na interface `IEchoContract` na classe `EchoService`.

	```
	public string Echo(string text)
	{
    	Console.WriteLine("Echoing: {0}", text);
    	return text;
	}
	```

1. Clique em **Compilar**, depois em **Compilar Solução** para confirmar a precisão do seu trabalho.

### Para definir a configuração do host de serviço

1. O arquivo de configuração é muito semelhante a um arquivo de configuração do WCF. Ele inclui o nome do serviço, o ponto de extremidade (ou seja, o local que o Barramento de Serviço expõe para os clientes e hosts se comunicarem) e a associação (o tipo de protocolo usado para comunicação). A principal diferença é que esse ponto de extremidade de serviço configurado se refere a uma associação [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx), que não faz parte do .NET Framework. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) é uma das associações definidas pelo Barramento de Serviço.

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo App.config, que atualmente contém os seguintes elementos XML:

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	</configuration>
	```

1. Adicione um elemento XML `<system.serviceModel>` ao arquivo App.config. Este é um elemento do WCF que define um ou mais serviços. Ele é usado neste exemplo para definir o nome do serviço e o ponto de extremidade.

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
  
	  </system.serviceModel>
	</configuration>
	```

1. Dentro das marcas `<system.serviceModel>`, adicione um elemento `<services>`. Assim como nas associações, você pode definir vários aplicativos de Barramento de Serviço em um único arquivo de configuração. Este tutorial, porém, define apenas um.
 
	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	    <services>

	    </services>
	  </system.serviceModel>
	</configuration>
	```

1. Dentro do elemento `<services>`, adicione um elemento `<service>` para definir o nome do serviço.

	```
	<servicename="Microsoft.ServiceBus.Samples.EchoService">
	</service>
	```

1. Dentro do elemento `<service>`, defina o local do contrato do ponto de extremidade e também o tipo de associação para o ponto de extremidade.

	```
	<endpointcontract="Microsoft.ServiceBus.Samples.IEchoContract"binding="netTcpRelayBinding"/>
	```

	O ponto de extremidade define no qual o cliente procurará o aplicativo host. Posteriormente, o tutorial usa esta etapa para criar um URI que expõe totalmente o host através do Barramento de Serviço. A associação declara que estamos usando o TCP como protocolo para comunicação com o Barramento de Serviço.


1. Imediatamente após o elemento `<services>`, adicione a seguinte extensão de associação:
 
	```
	<extensions>
	  <bindingExtensions>
	    <addname="netTcpRelayBinding"type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
	  </bindingExtensions>
	</extensions>
	```

1. No menu **Compilar**, clique em **Compilar Solução** para confirmar a precisão do seu trabalho.

### Exemplo

O código a seguir mostra a implementação do contrato de serviço.

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

O código a seguir mostra o formato básico do arquivo App.config associado ao host de serviço.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## Hospedar e executar um serviço Web básico para efetuar o registro com o Barramento de Serviço

Esta etapa descreve como executar um serviço básico do Barramento de Serviço.

### Para criar as credenciais do Barramento de Serviço

1. Instale o [pacote do NuGet do Barramento de Serviço](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

1. Em `Main()`, crie duas variáveis nas quais armazenar o namespace e a chave de SAS que são lidos da janela do console.

	```
	Console.Write("Your Service Namespace: ");
	string serviceNamespace = Console.ReadLine();
	Console.Write("Your SAS key: ");
	string sasKey = Console.ReadLine();
	```

	A chave de SAS será usada posteriormente para acessar seu projeto de Barramento de Serviço. O namespace é passado como um parâmetro para o `CreateServiceUri` para criar um URI de serviço.

4. Usando um objeto [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx), declare que você usará uma chave de SAS como o tipo de credencial. Adicione o código a seguir diretamente após o código adicionado na última etapa.

	```
	TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
	sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
	```

### Para criar um endereço base para o serviço

1. Seguindo o código adicionado na última etapa, crie uma instância de `Uri` para o endereço básico do serviço. Esse URI especifica o esquema de Barramento de Serviço, o namespace e o caminho da interface do serviço.

	```
	Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
	```

	"sb" é uma abreviação para o esquema de Barramento de Serviço e indica que estamos usando TCP como o protocolo. Isso também foi indicado anteriormente no arquivo de configuração, quando [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) foi especificado como a associação.
	
	Para este tutorial, o URI é `sb://putServiceNamespaceHere.windows.net/EchoService`.

### Para criar e configurar o host de serviço

1. Defina o modo de conectividade para `AutoDetect`.

	```
	ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
	```

	O modo de conectividade descreve o protocolo que o serviço usa para comunicar-se com o Barramento de Serviço; HTTP ou TCP. Usando a configuração padrão `AutoDetect`, o serviço tenta conectar-se ao Barramento de Serviço por TCP se estiver disponível e por HTTP se o TCP não estiver disponível. Observe que isso difere do protocolo que o serviço especifica para comunicação de cliente. Esse protocolo é determinado pela associação usada. Por exemplo, um serviço pode usar a associação [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx), que especifica que o ponto de extremidade (exposto no Barramento de Serviço) comunica-se com clientes por HTTP. Esse mesmo serviço pôde especificar **ConnectivityMode.AutoDetect** para que o serviço se comunicasse com o Barramento de Serviço por TCP.

1. Crie o host de serviço, usando o endereço URI criado anteriormente nesta seção.

	```
	ServiceHost host = new ServiceHost(typeof(EchoService), address);
	```

	O host de serviço é o objeto WCF que instancia o serviço. Aqui, você informa a ele o tipo de serviço que você deseja criar (um tipo `EchoService`) e também o endereço no qual você deseja expor o serviço.

1. Na parte superior do arquivo Program.cs, adicione referências a [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) e [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx).

	```
	using System.ServiceModel.Description;
	using Microsoft.ServiceBus.Description;
	```

1. De volta em `Main()`, configure o ponto de extremidade para habilitar o acesso público.

	```
	IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
	```

	Essa etapa informa ao feed do barramento de serviço que seu aplicativo pode ser encontrado publicamente ao examinar o feed ATOM do Barramento de Serviço para o seu projeto. Se você definir **DiscoveryType** como **private**, um cliente ainda poderá acessar o serviço. No entanto, o serviço não seria exibido ao procurar o namespace do Barramento de Serviço. Em vez disso, o cliente precisaria saber o caminho do ponto de extremidade com antecedência.

1. Aplique as credenciais de serviço aos pontos de extremidade de serviço definidos no arquivo App.config:

	```
	foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
	{
	    endpoint.Behaviors.Add(serviceRegistrySettings);
	    endpoint.Behaviors.Add(sasCredential);
	}
	```

	Conforme mencionado na etapa anterior, você poderia ter declarado vários serviços e pontos de extremidade no arquivo de configuração. Se você tivesse feito isso, esse código percorreria o arquivo de configuração e procuraria por cada ponto de extremidade ao qual ele devesse aplicar suas credenciais. No entanto, para este tutorial, o arquivo de configuração tem apenas um ponto de extremidade.

### Para abrir o host de serviço

1. Abra o arquivo serviço.

	```
	host.Open();
	```

1. Informe ao usuário que o serviço está em execução e explique como desligá-lo.

	```
	Console.WriteLine("Service address: " + address);
	Console.WriteLine("Press [Enter] to exit");
	Console.ReadLine();
	```

1. Quando terminar, feche o host do serviço.

	```
	host.Close();
	```

1. Pressione F6 para compilar o projeto.

### Exemplo

O exemplo a seguir inclui o contrato e a implementação do serviço das etapas anteriores no tutorial e hospeda o serviço em um aplicativo de console. Compile o que é mostrado a seguir em um arquivo executável chamado EchoService.exe.

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## Criar um cliente WCF para o contrato de serviço

A próxima etapa é criar um aplicativo cliente do Barramento de Serviço básico e definir o contrato de serviço que você implementará em etapas posteriores. Observe que muitas dessas etapas se assemelham àquelas usadas para criar um serviço: definir um contrato, editar um arquivo App.config, usar as credenciais para se conectar ao Barramento de Serviço e assim por diante. O código usado para essas tarefas é fornecido no exemplo logo após o procedimento.

1. Crie um novo projeto na solução atual do Visual Studio para o cliente fazendo o seguinte:
	1. No Gerenciador de Soluções, na mesma solução que contém o serviço, clique com o botão direito do mouse na solução atual (não no projeto) e clique em **Adicionar**. Em seguida, clique em **Novo Projeto**.
	2. Na caixa de diálogo **Adicionar Novo Projeto**, clique em **Visual C#** (se **Visual C#** não aparecer, procure em **Outras Linguagens**), selecione o modelo **Aplicativo de Console** e nomeie-o como **EchoClient**.
	3. Clique em **OK**. <br />

1. No Gerenciador de Soluções, clique duas vezes no arquivo Program.cs no projeto **EchoClient** para abri-lo no editor.

1. Altere o nome do namespace de seu padrão `EchoClient` para `Microsoft.ServiceBus.Samples`.

1. Adicione uma referência a System.ServiceModel.dll ao projeto:
	1. Clique com o botão direito do mouse em **Referências** sob o projeto **EchoClient** no Gerenciador de Soluções. Em seguida, clique em **Adicionar Referência**.
	2. Como você já adicionou uma referência a esse assembly na primeira etapa deste tutorial, ela é listada na guia **Recente**. Clique em **Recente** e, em seguida, selecione **System.ServiceModel.dll** na lista. Em seguida, clique em **OK**. Se você não vir **System.ServiceModel.dll** na guia **Recente**, clique na guia **Procurar** e vá para **C:\\Windows\\Microsoft.NET\\Framework\\v3.0\\Windows Communication Foundation**. Em seguida, selecione o assembly aí. <br />

1. Adicione uma declaração `using` ao namespace [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) no arquivo Program.cs.

	```
	using System.ServiceModel;
	```

1. Repita as etapas anteriores para adicionar uma referência a Microsoft.ServiceBus.dll e ao namespace [Microsoft.ServiceBus](https://msdn.microsoft.com/library/microsoft.servicebus.aspx) ao seu projeto.

1. Adicione a definição de contrato de serviço ao namespace, conforme mostrado no exemplo a seguir. Observe que essa definição é idêntica à definição usada no projeto **Service**. Você deve adicionar esse código à parte superior do namespace `Microsoft.ServiceBus.Samples`.

	```
	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	publicinterface IEchoContract
	{
	    [OperationContract]
	    string Echo(string text);
	}

	publicinterface IEchoChannel : IEchoContract, IClientChannel { }
	```

1. Pressione F6 para compilar o cliente.

### Exemplo

O código a seguir mostra o status atual do arquivo Program.cs no projeto EchoClient.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## Configurar o cliente WCF

Nesta etapa, você cria um arquivo App.config para um aplicativo cliente básico que acessa o serviço criado anteriormente neste tutorial. Esse arquivo App.config define o contrato, a associação e o nome do ponto de extremidade. O código usado para essas tarefas é fornecido no exemplo logo após o procedimento.

1. No Gerenciador de Soluções, no projeto cliente, clique duas vezes em **App.config** para abrir o arquivo, que atualmente contém os seguintes elementos XML:

	```
	<?xmlversion="1.0"?>
	<configuration>
	  <startup>
	    <supportedRuntimeversion="v4.0"sku=".NETFramework,Version=v4.0"/>
	  </startup>
	</configuration>
	```

1. Adicione um elemento XML ao arquivo App.config para `system.serviceModel`.

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	
	  </system.serviceModel>
	</configuration>
	```
	
	Esse elemento declara que o aplicativo usa pontos de extremidade no estilo WCF. Conforme mencionado anteriormente, grande parte da configuração de um aplicativo do barramento de serviço é idêntico à de um aplicativo WCF; a principal diferença é o local para o qual o arquivo de configuração aponta.

1. Dentro do elemento system.serviceModel, adicione um elemento `<client>`.

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	    <client>
	    </client>
	  </system.serviceModel>
	</configuration>
	```

	Essa etapa declara que você está definindo um aplicativo de cliente no estilo WCF.

1. Dentro do elemento `client`, defina o nome, o contrato e o tipo de associação para o ponto de extremidade.

	```
	<endpointname="RelayEndpoint"
					contract="Microsoft.ServiceBus.Samples.IEchoContract"
					binding="netTcpRelayBinding"/>
	```

	Essa etapa define o nome do ponto de extremidade, o contrato definido no serviço e o fato de que o aplicativo cliente usa TCP para se comunicar com o Barramento de Serviço. O nome do ponto de extremidade é usado na próxima etapa para vincular esta configuração de ponto de extremidade com o URI do serviço.

1. Imediatamente após o elemento <client>, adicione a extensão de associação a seguir.
 
	```
	<extensions>
	  <bindingExtensions>
	    <addname="netTcpRelayBinding"type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
	  </bindingExtensions>
	</extensions>
	```

1. Clique em **Arquivo**, depois em **Salvar Tudo**.

## Exemplo

O código a seguir mostra o arquivo App.config para o cliente Echo.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## Implementar o cliente WCF para chamar o Barramento de Serviço

Nesta etapa, você implementa um aplicativo cliente básico que acessa o serviço que você criou anteriormente neste tutorial. Semelhante ao serviço, o cliente executa muitas das mesmas operações para acessar o Barramento de Serviço:

1. Define o modo de conectividade.
1. Cria o URI que localiza o serviço de host.
1. Define as credenciais de segurança.
1. Aplica as credenciais à conexão.
1. Abre a conexão.
1. Executa as tarefas específicas do aplicativo.
1. Encerra a conexão.

No entanto, uma das principais diferenças é que o aplicativo cliente usa um canal para conectar-se ao barramento de serviço, enquanto o serviço usa uma chamada para **ServiceHost**. O código usado para essas tarefas é fornecido no exemplo logo após o procedimento.

### Para implementar um aplicativo cliente

1. Defina o modo de conectividade como **AutoDetect**. Adicione o código a seguir dentro do método `Main()` do aplicativo cliente.

	```
	ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
	```

1. Defina variáveis para conter os valores para o namespace de serviço e a chave SAS que são lidos do console.

	```
	Console.Write("Your Service Namespace: ");
	string serviceNamespace = Console.ReadLine();
	Console.Write("Your SAS Key: ");
	string sasKey = Console.ReadLine();
	```

1. Crie o URI que define o local do host em seu projeto de Barramento de Serviço.

	```
	Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
	```

1. Crie o objeto de credencial para o ponto de extremidade do namespace de serviço.

	```
	TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
	sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
	```

1. Crie a fábrica de canais que carrega a configuração descrita no arquivo App.config.

	```
	ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
	```

	Uma fábrica de canais é um objeto WCF que cria um canal por meio do qual o os aplicativos cliente e de serviço se comunicam.

1. Aplique as credenciais do Barramento de Serviço.

	```
	channelFactory.Endpoint.Behaviors.Add(sasCredential);
	```

1. Crie e abra o canal para o serviço.

	```
	IEchoChannel channel = channelFactory.CreateChannel();
	channel.Open();
	```

1. Escreva a interface do usuário básico e a funcionalidade para o eco.

	```
	Console.WriteLine("Enter text to echo (or [Enter] to exit):");
	string input = Console.ReadLine();
	while (input != String.Empty)
	{
	    try
	    {
	        Console.WriteLine("Server echoed: {0}", channel.Echo(input));
	    }
	    catch (Exception e)
	    {
	        Console.WriteLine("Error: " + e.Message);
	    }
	    input = Console.ReadLine();
	}
	```

	Observe que o código usa a instância do objeto do canal como um proxy para o serviço.

1. Feche o canal e a fábrica.

	```
	channel.Close();
	channelFactory.Close();
	```

## Para executar seu aplicativo cliente

1. Pressione F6 para compilar a solução. Isso compila tanto o projeto cliente quanto o projeto de serviço que você criou na etapa anterior deste tutorial e cria um arquivo executável para cada um deles.

1. Antes de executar o aplicativo cliente, certifique-se que o aplicativo de serviço está em execução.

	Agora você deve ter um arquivo executável para o aplicativo de serviço Echo chamado EchoService.exe, localizado em sua pasta de projeto de serviço em \\bin\\Debug\\EchoService.exe (para a configuração de depuração) ou \\bin\\Release\\EchoService.exe (para a configuração de versão). Clique duas vezes nesse arquivo para iniciar o aplicativo de serviço.

1. Uma janela do console será aberta e solicita que você informe o namespace. Na janela do console, digite o namespace de serviço e pressione Enter.

1. Em seguida, será solicitado que você informe a sua chave de SAS. Insira a chave de SAS e pressione ENTER.

	Eis aqui um exemplo de saída da janela do console. Observe que os valores fornecidos aqui são apenas para fins de exemplificação.

	`Your Service Namespace: myNamespace` `Your SAS Key: <SAS key value>`

	O aplicativo de serviço é iniciado e imprime na janela do console o endereço no qual ele está escutando, como visto no exemplo a seguir.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/` `Press [Enter] to exit`
    
1. Execute o aplicativo cliente. Agora você deve ter um executável para o aplicativo cliente Echo chamado EchoClient.exe, localizado no diretório do projeto do cliente em \\bin\\Debug\\EchoClient.exe (para a configuração de depuração) ou \\bin\\Release\\EchoClient.exe (para a configuração de versão). Clique duas vezes nesse arquivo para iniciar o aplicativo cliente.

1. Uma janela do console será aberta e solicitará as mesmas informações que você inseriu anteriormente para o aplicativo de serviço. Siga as etapas anteriores para inserir os mesmos valores para o aplicativo cliente para o namespace de serviço, o nome do emissor e o segredo do emissor.

1. Depois de inserir esses valores, o cliente abre um canal para o serviço e solicita que você digite algum texto, como visto no exemplo de saída do console a seguir.

	`Enter text to echo (or [Enter] to exit):`

	Digite algum texto para enviar ao aplicativo de serviço e pressione Enter. Esse texto é enviado para o serviço por meio da operação de serviço Echo e aparece na janela do console de serviço como a saída de exemplo a seguir.

	`Echoing: My sample text`

	O aplicativo cliente recebe o valor retornado da operação `Echo`, que é o texto original, e o exibe na janela do console. A seguir, um exemplo de saída da janela do console do cliente.

	`Server echoed: My sample text`

1. Você pode continuar a enviar mensagens de texto do cliente para o serviço dessa maneira. Quando terminar, pressione Enter nas janelas do console de cliente e serviço para encerrar ambos os aplicativos.

## Exemplo

O exemplo a seguir mostra como criar um aplicativo cliente, como chamar as operações do serviço e como fechar o cliente após a conclusão da chamada da operação.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

Certifique-se de que o serviço está em execução antes de iniciar o cliente.

## Próximas etapas

Este tutorial mostrou como compilar um serviço e um aplicativo cliente simples do Barramento de Serviço usando os recursos de “retransmissão” do Barramento de Serviços. Para obter um tutorial semelhante que usa o barramento de serviço [sistema de mensagens agenciado](service-bus-messaging-overview.md#Brokered-messaging), consulte o [Tutorial do Sistema de Mensagens Agenciado .NET do Barramento de Serviço](service-bus-brokered-tutorial-dotnet.md).

Para saber mais sobre o Barramento de Serviço, consulte os tópicos a seguir.

- [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
- [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
- [Arquitetura do Barramento de Serviço](service-bus-architecture.md)

[portal clássico do Azure]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0218_2016-->