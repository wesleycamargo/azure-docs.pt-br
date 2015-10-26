<properties 
   pageTitle="Tutorial .NET do sistema de mensagens agenciado do Barramento de Serviço | Microsoft Azure"
   description="Tutorial .NET do sistema de mensagens agenciado"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/14/2015"
   ms.author="sethm" />

# Tutorial .NET do sistema de mensagens agenciado do Barramento de Serviço

O Barramento de Serviço do Azure oferece duas soluções de sistema de mensagens abrangentes – uma por meio de um serviço de “retransmissão” centralizada em execução na nuvem que oferece suporte a uma variedade de diferentes protocolos de transporte e de padrões de serviços Web, incluindo SOAP, WS-* e REST. O cliente não precisa de uma conexão direta com o serviço local nem precisa saber onde reside o serviço, e o serviço local não precisa de portas de entrada abertas no firewall.

A segunda solução de sistema de mensagens habilita recursos do sistema de mensagens “agenciado”. Eles podem ser considerados como assíncronos ou recursos do sistema de mensagens desacoplados que dão suporte a cenários de publicação-assinatura, de desacoplamento temporal e de balanceamento de carga usando a infraestrutura do sistema de mensagens do Barramento de Serviço. A comunicação desacoplada tem diversas vantagens; por exemplo, clientes e servidores podem se conectar como necessário e executar as operações deles de forma assíncrona.

O objetivo deste tutorial é oferecer uma visão geral e uma experiência prática com filas, um dos principais componentes do sistema de mensagens agenciado do Barramento de Serviço. Depois de trabalhar na sequência de tópicos deste tutorial, você terá um aplicativo que preenche uma lista de mensagens, cria uma fila e envia mensagens para essa fila. Por fim, o aplicativo recebe e exibe as mensagens da fila, limpa seus recursos e sai. Para obter um tutorial correspondente que descreva como compilar um aplicativo que use os recursos do sistema de mensagens “retransmitidos” do Barramento de Serviço, consulte o [tutorial do sistema de mensagens de retransmissão do Barramento de Serviço](service-bus-relay-tutorial.md).

## Introdução e pré-requisitos

Filas oferecem entrega de mensagem do tipo PEPS (primeiro a entrar, primeiro a sair) para um ou mais consumidores concorrentes. PEPS significa que geralmente se espera que as mensagens sejam recebidas e processadas pelos receptores na ordem cronológica em que foram adicionadas à fila, sendo que cada mensagem será recebida e processada por apenas um consumidor de mensagem. Um dos principais benefícios da utilização de filas é obter o *desacoplamento temporal* de componentes do aplicativo: em outras palavras, os produtores e os consumidores não precisam enviar e receber mensagens ao mesmo tempo, já que as mensagens são armazenadas de forma duradoura na fila. Um benefício relacionado é o *nivelamento de carga*, que permite que produtores e consumidores enviem e recebam mensagens em taxas diferentes.

A seguir, algumas etapas administrativas e de pré-requisito que você deve seguir antes de iniciar o tutorial. A primeira etapa é criar um namespace de serviço e obter uma chave de assinatura de acesso compartilhado (SAS). Um namespace de serviço fornece um limite de aplicativo para cada aplicativo exposto por meio do Barramento de Serviço. A chave SAS é automaticamente gerada pelo sistema quando um namespace de serviço é criado. A combinação do namespace de serviço e da chave SAS oferece uma credencial com a qual o Barramento de Serviço autentica o acesso a um aplicativo.

### Criar um namespace de serviço e obter uma chave SAS

1. Para criar um namespace de serviço, execute as etapas descritas em [Como criar ou modificar um namespace de serviço do Barramento de Serviço](https://msdn.microsoft.com/library/azure/hh690931.aspx).

1. Na janela principal do portal do Azure, clique no nome do namespace de serviço criado na etapa anterior.

1. Clique em **Configurar**.

1. Na seção **gerador de assinatura de acesso compartilhado**, anote a chave primária associada à política **RootManagerSharedAccessKey** ou copie-a para a área de transferência. Você usará esse valor posteriormente neste tutorial.

A próxima etapa será criar um projeto do Visual Studio e escrever duas funções auxiliares que carreguem uma lista delimitada por vírgulas de mensagens em um objeto [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) .NET [lista](https://msdn.microsoft.com/library/6sh2ey19.aspx) fortemente tipado.

### Criar um projeto do Visual Studio

1. Abra o Visual Studio como administrador clicando com o botão direito do mouse no programa no menu Iniciar e clicando em **Executar como administrador**.

1. Crie um novo projeto de aplicativo de console. Clique no menu **Arquivo** e selecione **Novo**, então clique em **Projeto**. Na caixa de diálogo **Novo Projeto**, clique em **Visual C#** (se **Visual C#** não aparecer, procure em **Outras Linguagens**), clique no modelo **Aplicativo de Console** e chame-o de **QueueSample**. Use o **Local** padrão. Clique em **OK** para criar o projeto.

1. Use o gerenciador de pacotes NuGet para adicionar as bibliotecas do Barramento de Serviço ao seu projeto:
	1. No Gerenciador de Soluções, clique com o botão direito do mouse na pasta do projeto e clique em **Gerenciar Pacotes NuGet**.
	2. Na caixa de diálogo **Gerenciar Pacotes Nuget**, pesquise online **Barramento de Serviço** e clique em **Instalar**. <br />
1. No Gerenciador de Soluções, clique duas vezes no arquivo Program.cs para abri-lo no editor do Visual Studio. Altere o nome do namespace de seu padrão `QueueSample` para `Microsoft.ServiceBus.Samples`.

	```
	Microsoft.ServiceBus.Samples
	{
	    …
	```

1. Modifique as instruções `using` como mostrado no código a seguir.

	```
	using System;
	using System.Collections.Generic;
	using System.Data;
	using System.IO;
	using System.Threading;
	using Microsoft.ServiceBus.Messaging;
	```

1. Crie um arquivo de texto chamado Data.csv e copie o texto delimitado por vírgula a seguir.

	```
	IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
	1,Package lost,1,1,Basic,5,1,FALSE
	2,Package damaged,1,1,Basic,5,1,FALSE
	3,Product defective,1,2,Premium,5,2,FALSE
	4,Product damaged,2,2,Premium,5,2,FALSE
	5,Package lost,2,2,Basic,5,2,TRUE
	6,Package lost,3,2,Basic,5,2,FALSE
	7,Package damaged,3,7,Premium,5,3,FALSE
	8,Product defective,3,2,Premium,5,3,FALSE
	9,Product damaged,4,6,Premium,5,3,TRUE
	10,Package lost,4,8,Basic,5,3,FALSE
	11,Package damaged,5,4,Basic,5,4,FALSE
	12,Product defective,5,4,Basic,5,4,FALSE
	13,Package lost,6,8,Basic,5,4,FALSE
	14,Package damaged,6,7,Premium,5,5,FALSE
	15,Product defective,6,2,Premium,5,5,FALSE
	```

	Salve e feche o arquivo Data.csv e lembre-se do local em que você o salvou.

1. No Gerenciador de Soluções, clique com o botão direito do mouse no nome do seu projeto (neste exemplo, **QueueSample**), clique em **Adicionar** e clique em **Item Existente**.

1. Navegue até o arquivo Data.csv criado na etapa 6. Clique no arquivo e clique em **Adicionar**. Verifique se **Todos os Arquivos (*.*)** está selecionado na lista de tipos de arquivo.

### Criar uma função que analise uma lista de mensagens

1. Antes do método `Main()`, declare duas variáveis: uma do tipo **DataTable**, para conter a lista de mensagens em Data.csv. A outra deve ser do tipo de objeto de Lista, fortemente tipada para [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). A última é a lista de mensagens agenciadas que serão usadas pelas etapas subsequentes do tutorial.

	```
	namespace Microsoft.ServiceBus.Samples
	{
	    publicclass Program
	    {
	
	        privatestatic DataTable issues;
	        privatestatic List<BrokeredMessage> MessageList;
	```

1. Fora de `Main()`, defina um método `ParseCSV()` que analise a lista de mensagens em Data.csv e que carregue as mensagens em uma tabela [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx), como mostrado aqui. O método retorna um objeto **DataTable**.

	```
	static DataTable ParseCSVFile()
	{
	    DataTable tableIssues = new DataTable("Issues");
	    string path = @"..\..\data.csv";
	    try
	    {
	        using (StreamReader readFile = new StreamReader(path))
	        {
	            string line;
	            string[] row;
	
	            // create the columns
	            line = readFile.ReadLine();
	            foreach (string columnTitle in line.Split(','))
	            {
	                tableIssues.Columns.Add(columnTitle);
	            }
	
	            while ((line = readFile.ReadLine()) != null)
	            {
	                row = line.Split(',');
	                tableIssues.Rows.Add(row);
	            }
	        }
	    }
	    catch (Exception e)
	    {
	        Console.WriteLine("Error:" + e.ToString());
	    }
	
	    return tableIssues;
	}
	```

1. No método `Main()`, adicione uma instrução que chame o método `ParseCSVFile()`:

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	
	}
	```

### Criar uma função que carregue a lista de mensagens

1. Fora de `Main()`, defina um método `GenerateMessages()` que obtenha o objeto **DataTable** retornado por `ParseCSVFile()` e carregue a tabela em uma lista fortemente tipada de mensagens agenciadas. O método retorna o objeto **List**, como no exemplo a seguir. 

	```
	static List<BrokeredMessage> GenerateMessages(DataTable issues)
	{
	    // Instantiate the brokered list object
	    List<BrokeredMessage> result = new List<BrokeredMessage>();
	
	    // Iterate through the table and create a brokered message for each rowforeach (DataRow item in issues.Rows)
	    {
	        BrokeredMessage message = new BrokeredMessage();
	        foreach (DataColumn property in issues.Columns)
	        {
	            message.Properties.Add(property.ColumnName, item[property]);
	        }
	        result.Add(message);
	    }
	    return result;
	}
	```

1. Em `Main()`, diretamente abaixo da chamada a `ParseCSVFile()`, adicione uma instrução que chame o método `GenerateMessages()` com o valor de retorno de `ParseCSVFile()` como um argumento:

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	}
	```

### Obter credenciais de usuário

1. Primeiro, crie três variáveis de cadeia de caracteres global para armazenar esses valores. Declare essas variáveis diretamente após as declarações variáveis anteriores; por exemplo:

	```
	namespace Microsoft.ServiceBus.Samples
	{
	    publicclass Program
	    {
	
	        privatestatic DataTable issues;
	        privatestatic List<BrokeredMessage> MessageList; 
	        // Add these variablesprivatestaticstring ServiceNamespace;
	        privatestaticstring sasKeyName = "RootManageSharedAccessKey";
	        privatestaticstring sasKeyValue;
	        …
	```

1. Em seguida, crie uma função que aceite e armazene o namespace de serviço e a chave SAS. Adicione esse método fora de `Main()`. Por exemplo:

	```
	static void CollectUserInput()
	{
	    // User service namespace
	    Console.Write("Please enter the service namespace to use: ");
	    ServiceNamespace = Console.ReadLine();
	
	    // Issuer key
	    Console.Write("Please enter the SAS key to use: ");
	    sasKeyValue = Console.ReadLine();
	}
	```

1. Em `Main()`, diretamente abaixo da chamada a `GenerateMessages()`, adicione uma instrução que chame o método `CollectUserInput()`:

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	    
	    // Collect user input
	    CollectUserInput();
	}
	```

### Compilar a solução

No menu **Compilar** do Visual Studio, você pode clicar em **Compilar Solução** ou pressione F6 para confirmar a precisão de seu trabalho até esse momento.

Criar Credenciais de Gerenciamento

Esta é a segunda etapa do tutorial de recursos do sistema de mensagens do Barramento de Serviço. Nesta etapa, você define as operações de gerenciamento que usará para criar credenciais da assinatura de acesso compartilhado (SAS) com as quais o seu aplicativo será autorizado.

## Criar credenciais de gerenciamento

Nesta etapa, você define as operações de gerenciamento que usará para criar credenciais da assinatura de acesso compartilhado (SAS) com as quais o seu aplicativo será autorizado.

1. Para maior clareza, este tutorial coloca todas as operações de fila em um método separado. Crie um método `Queue()` na classe `Program`, abaixo do método `Main()`. Por exemplo:
 
	```
	public static void Main(string[] args)
	{
	…
	}
	staticvoid Queue()
	{
	}
	```

1. A próxima etapa é criar uma credencial SAS usando um objeto [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx). O método de criação obtém o nome da chave SAS e o valor obtido no método `CollectUserInput()`. Adicione o seguinte código ao método `Queue()`:

	```
	staticvoid Queue()
	{
	    // Create management credentials
	    TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
	}
	```
### Criar o gerenciador de namespaces

1. Crie um novo objeto de gerenciamento de namespaces com um URI que contenha o nome do namespace e as credenciais de gerenciamento obtidas na última etapa como argumentos. Adicione este código diretamente abaixo do código adicionado na etapa anterior:
	
	```
	NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", <namespaceName>, string.Empty), credentials);
	```

### Exemplo

Neste ponto, seu código deverá semelhante ao seguinte:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Queue();
    }

    static void Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

Na próxima etapa, você criará a fila para a qual enviará as mensagens.

## Enviar mensagens para a fila

Nesta etapa, você criará uma fila e enviará as mensagens contidas na lista de mensagens agenciadas para essa fila.

### Criar a fila e enviar mensagens para a fila

1. Primeiro, crie a fila. Por exemplo, chame-a de `myQueue` e declare-a diretamente após as operações de gerenciamento adicionadas na última etapa:

	```
	QueueDescription myQueue;
	myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
	```

1. No método `Queue()`, crie um objeto de fábrica de sistema de mensagens com um URI do Barramento de Serviço recém-criado como um argumento. Adicione o código a seguir diretamente após as operações de gerenciamento adicionadas na última etapa:

	```
	MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
	```

1. Em seguida, crie o objeto de fila usando a classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx). Adicione o código a seguir diretamente após o código adicionado na última etapa:

	```
	QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
	```

1. Em seguida, adicione o código que percorra a lista de mensagens agenciadas criada anteriormente, enviando cada uma delas para a fila. Adicione o código a seguir diretamente após a instrução `CreateQueueClient()` adicionada na etapa anterior:
	
	```
	// Send messages
	Console.WriteLine("Now sending messages to the queue.");
	for (int count = 0; count < 6; count++)
	{
	    var issue = MessageList[count];
	    issue.Label = issue.Properties["IssueTitle"].ToString();
	    myQueueClient.Send(issue);
	    Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
	}
	```

## Receber mensagens da fila

Nesta etapa, você obterá a lista de mensagens da fila criada na etapa anterior.

### Criar um receptor e receber mensagens da fila

No método `Queue()`, itere pela fila e receba as mensagens usando o método [Microsoft.ServiceBus.Messaging.QueueClient.Receive](https://msdn.microsoft.com/library/azure/hh322678.aspx), mostrando cada mensagem no console. Adicione o código a seguir diretamente após o código adicionado na etapa anterior:

	```
	Console.WriteLine("Now receiving messages from Queue.");
	BrokeredMessage message;
	while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
	    {
	        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
	        message.Complete();
	
	        Console.WriteLine("Processing message (sleeping...)");
	        Thread.Sleep(1000);
	    }
	```

### Encerrar o método `Queue()` e limpar os recursos

Diretamente abaixo do código anterior, adicione o seguinte código para limpar os recursos de fábrica de mensagens e de fila:

	```
	factory.Close();
	myQueueClient.Close();
	namespaceClient.DeleteQueue("IssueTrackingQueue");
	```

### Chamar o método `Queue()`

A última etapa será adicionar uma instrução que chame o método `Queue()` de `Main()`. Adicione a seguinte linha de código realçada ao final de Main():
	
	```
	public static void Main(string[] args)
	{
	    // Collect user input
	    CollectUserInput();
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	
	    // Add this call
	    Queue();
	}
	```

### Exemplo

O código a seguir contém o aplicativo **QueueSample** completo.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Queue();
    }

    static void Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);

      QueueDescription myQueue;
      myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");

      MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
      QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

      // Send messages
      Console.WriteLine("Now sending messages to the Queue.");
      for (int count = 0; count < 6; count++)
      {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        myQueueClient.Send(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
      }

      Console.WriteLine("Now receiving messages from Queue.");
      BrokeredMessage message;
      while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
      {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
      }

      factory.Close();
      myQueueClient.Close();
      namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## Compilar e executar o aplicativo QueueSample

Agora que você concluiu as etapas anteriores, poderá compilar e executar o aplicativo **QueueSample**.

### Compilar o aplicativo QueueSample

No Visual Studio, no menu **Compilar**, clique em **Compilar Solução** ou pressione F6. Se você encontrar erros, verifique se seu código está correto com base no exemplo completo apresentado no final da etapa anterior.

### Executar o aplicativo QueueSample

1. Antes de executar o aplicativo, você deverá verificar se criou um namespace de serviço e se obteve uma chave SAS, como descrito em [Introdução e pré-requisitos](#introduction-and-prerequisites).

1. Abra seu navegador e vá ate o [portal do Azure](http://manage.windowsazure.com).

1. Clique em **Barramento de Serviço** na árvore à esquerda.

1. Clique no nome do namespace que você deseja usar. Na parte inferior da página, clique em **Informações da Conexão**. Anote a cadeia de conexão com a chave SAS ou a copie para a área de transferência.

1. No Visual Studio, no menu **Depurar**, clique em **Iniciar Depuração** ou pressione F5. Quando solicitado, insira o nome do namespace de serviço e a chave obtida na etapa anterior.

## Próximas etapas

Este tutorial mostrou como compilar um serviço e um aplicativo cliente simples do Barramento de Serviço usando os recursos de sistema de mensagens agenciado do Barramento de Serviços. Para obter um tutorial semelhante que use o [sistema de mensagens de retransmissão](service-bus-messaging-overview.md/#Relayed-messaging) do Barramento de Serviço, consulte o [tutorial do sistema de mensagens de transmissão do Barramento de Serviço](service-bus-relay-tutorial.md).

Para saber mais sobre o Barramento de Serviço, consulte os tópicos a seguir.

- [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
- [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
- [Arquitetura do Barramento de Serviço](service-bus-architecture.md)

<!---HONumber=Oct15_HO3-->