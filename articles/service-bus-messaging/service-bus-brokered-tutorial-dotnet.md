<properties 
    pageTitle="Tutorial .NET do sistema de mensagens agenciado do Barramento de Serviço | Microsoft Azure"
    description="Tutorial .NET do sistema de mensagens agenciado."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />


# <a name="service-bus-brokered-messaging-.net-tutorial"></a>Tutorial .NET do sistema de mensagens agenciado do Barramento de Serviço

O Barramento de Serviço do Azure oferece duas soluções de mensagens abrangentes – uma por meio de um serviço de “retransmissão” centralizado em execução na nuvem que oferece suporte a vários protocolos diferentes de transporte e padrões de serviços Web, incluindo SOAP, WS-* e REST. O cliente não precisa de uma conexão direta com o serviço local nem precisa saber onde reside o serviço, e o serviço local não precisa de portas de entrada abertas no firewall.

A segunda solução de mensagens habilita os recursos de mensagens “agenciados”. Eles podem ser considerados como assíncronos ou recursos do sistema de mensagens desacoplados que dão suporte a cenários de publicação-assinatura, de desacoplamento temporal e de balanceamento de carga usando a infraestrutura do sistema de mensagens do Barramento de Serviço. A comunicação desacoplada tem diversas vantagens; por exemplo, clientes e servidores podem se conectar como necessário e executar as operações deles de forma assíncrona.

O objetivo deste tutorial é oferecer uma visão geral e uma experiência prática com filas, um dos principais componentes do sistema de mensagens agenciado do Barramento de Serviço. Depois de trabalhar na sequência de tópicos deste tutorial, você terá um aplicativo que preenche uma lista de mensagens, cria uma fila e envia mensagens para essa fila. Por fim, o aplicativo recebe e exibe as mensagens da fila, limpa seus recursos e sai. Para obter um tutorial correspondente que descreve como compilar um aplicativo que use a Retransmissão do Barramento de Serviço, consulte o [tutorial do sistema de mensagens de retransmissão do Barramento de Serviço](../service-bus-relay/service-bus-relay-tutorial.md).

## <a name="introduction-and-prerequisites"></a>Introdução e pré-requisitos

Filas oferecem entrega de mensagem do tipo PEPS (primeiro a entrar, primeiro a sair) para um ou mais consumidores concorrentes. PEPS significa que geralmente se espera que as mensagens sejam recebidas e processadas pelos receptores na ordem cronológica em que foram adicionadas à fila, sendo que cada mensagem será recebida e processada por apenas um consumidor de mensagem. Um dos principais benefícios da utilização de filas é obter o *desacoplamento temporal* de componentes do aplicativo: em outras palavras, os produtores e os consumidores não precisam enviar e receber mensagens ao mesmo tempo, já que as mensagens são armazenadas de forma duradoura na fila. Um benefício relacionado é o *nivelamento de carga*, que permite que produtores e consumidores enviem e recebam mensagens em taxas diferentes.

A seguir, algumas etapas administrativas e de pré-requisito que você deve seguir antes de iniciar o tutorial. A primeira etapa é criar um namespace de serviço e obter uma chave de assinatura de acesso compartilhado (SAS). Um namespace fornece um limite de aplicativo para cada aplicativo exposto por meio do Barramento de Serviço. A chave SAS é automaticamente gerada pelo sistema quando um namespace de serviço é criado. A combinação do namespace de serviço e da chave SAS oferece uma credencial com a qual o Barramento de Serviço autentica o acesso a um aplicativo.

### <a name="create-a-service-namespace-and-obtain-a-sas-key"></a>Criar um namespace de serviço e obter uma chave SAS

A primeira etapa é criar um namespace de serviço e obter uma chave de [SAS](service-bus-sas-overview.md) (Assinatura de Acesso Compartilhado). Um namespace fornece um limite de aplicativo para cada aplicativo exposto por meio do Barramento de Serviço. A chave SAS é automaticamente gerada pelo sistema quando um namespace de serviço é criado. A combinação do namespace de serviço e a chave SAS fornece uma credencial para o Barramento de Serviço autenticar o acesso a um aplicativo.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

A próxima etapa será criar um projeto do Visual Studio e escrever duas funções auxiliares que carreguem uma lista delimitada por vírgulas de mensagens em um objeto [List](https://msdn.microsoft.com/library/6sh2ey19.aspx) .NET [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) fortemente tipado.

### <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. Abra o Visual Studio como administrador clicando com o botão direito do mouse no programa no menu Iniciar e clicando em **Executar como administrador**.

1. Crie um novo projeto de aplicativo de console. Clique no menu **Arquivo** e selecione **Novo**, então, clique em **Projeto**. Na caixa de diálogo **Novo Projeto**, clique em **Visual C#** (se **Visual C#** não aparecer, procure em **Outras Linguagens**), clique no modelo **Aplicativo de Console** e chame-o de **QueueSample**. Use o **Local** padrão. Clique em **OK** para criar o projeto.

1. Use o gerenciador de pacotes NuGet para adicionar as bibliotecas do Barramento de Serviço ao seu projeto:
    1. No Gerenciador de Soluções, clique com o botão direito no projeto **QueueSample** e em **Gerenciar Pacotes NuGet**.
    2. Na caixa de diálogo** Gerenciar Pacotes Nuget**, clique na guia **Procurar** e procure o **Barramento de Serviço do Azure**, em seguida, clique em **Instalar**.
<br />
1. No Gerenciador de Soluções, clique duas vezes no arquivo Program.cs para abri-lo no editor do Visual Studio. Altere o nome do namespace de seu padrão `QueueSample` para `Microsoft.ServiceBus.Samples`.

    ```
    Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Modifique as instruções `using` como mostrado no código a seguir.

    ```
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
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

    Salve e feche o arquivo Data.csv e lembre-se do local onde o salvou.

1. No Gerenciador de Soluções, clique com o botão direito do mouse no nome do seu projeto (neste exemplo, **QueueSample**), clique em **Adicionar** e clique em **Item Existente**.

1. Navegue até o arquivo Data.csv criado na etapa 6. Clique no arquivo e clique em **Adicionar**. Verifique se **Todos os Arquivos (*.*)** está selecionado na lista de tipos de arquivo.

### <a name="create-a-method-that-parses-a-list-of-messages"></a>Criar um método que analisa uma lista de mensagens

1. Na classe `Program` antes do método `Main()` declare duas variáveis: uma do tipo **DataTable**, para conter a lista de mensagens no Data.csv. A outra deve ser do tipo de objeto de Lista, fortemente tipada para [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). A última é a lista de mensagens agenciadas que serão usadas pelas etapas subsequentes do tutorial.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList;
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

### <a name="create-a-method-that-loads-the-list-of-messages"></a>Criar um método que carrega a lista de mensagens

1. Fora de `Main()`, defina um método `GenerateMessages()` que obtenha o objeto **DataTable** retornado por `ParseCSVFile()` e carregue a tabela em uma lista fortemente tipada de mensagens agenciadas. O método retorna o objeto **List**, como no exemplo a seguir. 

    ```
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
    ```

1. Em `Main()`, diretamente depois da chamada para `ParseCSVFile()`, adicione uma instrução que chama o método `GenerateMessages()` com o valor de retorno a partir de `ParseCSVFile()` como um argumento:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
    }
    ```

### <a name="obtain-user-credentials"></a>Obter credenciais de usuário

1. Primeiro, crie três variáveis de cadeia de caracteres global para armazenar esses valores. Declare essas variáveis diretamente após as declarações variáveis anteriores; por exemplo:

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        public class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList; 

            // Add these variables
            private static string ServiceNamespace;
            private static string sasKeyName = "RootManageSharedAccessKey";
            private static string sasKeyValue;
            …
    ```

1. Em seguida, crie uma função que aceite e armazene o namespace de serviço e a chave SAS. Adicione esse método fora de `Main()`. Por exemplo: 

    ```
    static void CollectUserInput()
    {
        // User service namespace
        Console.Write("Please enter the namespace to use: ");
        ServiceNamespace = Console.ReadLine();
    
        // Issuer key
        Console.Write("Enter the SAS key to use: ");
        sasKeyValue = Console.ReadLine();
    }
    ```

1. Em `Main()`, diretamente depois da chamada para `GenerateMessages()`, adicione uma instrução que chama o método `CollectUserInput()`:

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

### <a name="build-the-solution"></a>Compilar a solução

No menu **Compilar** no Visual Studio, você pode clicar em **Compilar Solução** ou pressionar **Ctrl+Shift+B** para confirmar a precisão de seu trabalho até o momento.

## <a name="create-management-credentials"></a>Criar credenciais de gerenciamento

Nesta etapa, você define as operações de gerenciamento que usará para criar credenciais da assinatura de acesso compartilhado (SAS) com as quais o seu aplicativo será autorizado.

1. Para maior clareza, este tutorial coloca todas as operações de fila em um método separado. Crie um método assíncrono `Queue()` na classe `Program`, após o método `Main()`. Por exemplo:
 
    ```
    public static void Main(string[] args)
    {
    …
    }
    static async Task Queue()
    {
    }
    ```

1. A próxima etapa é criar uma credencial SAS usando um objeto [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx). O método de criação obtém o nome da chave SAS e o valor obtido no método `CollectUserInput()`. Adicione o seguinte código ao método `Queue()`:

    ```
    static async Task Queue()
    {
        // Create management credentials
        TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
    }
    ```

2. Crie um novo objeto de gerenciamento de namespaces com um URI que contenha o nome do namespace e as credenciais de gerenciamento obtidas na etapa anterior como argumentos. Adicione este código diretamente depois do código adicionado na etapa anterior. Substitua `<yourNamespace>` pelo nome do namespace de seu serviço:
    
    ```
    NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

### <a name="example"></a>Exemplo

Neste ponto, seu código deverá semelhante ao seguinte:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
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
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
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

## <a name="send-messages-to-the-queue"></a>Enviar mensagens para a fila

Nesta etapa, você criará uma fila e enviará as mensagens contidas na lista de mensagens agenciadas para essa fila.

### <a name="create-queue-and-send-messages-to-the-queue"></a>Criar a fila e enviar mensagens para a fila

1. Primeiro, crie a fila. Por exemplo, chame-a de `myQueue` e declare-a diretamente após as operações de gerenciamento adicionadas no método `Queue()` na última etapa:

    ```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
    ```

1. No método `Queue()`, crie um objeto de fábrica de sistema de mensagens com um URI do Barramento de Serviço recém-criado como um argumento. Adicione o código a seguir diretamente após as operações de gerenciamento adicionadas na última etapa. Substitua `<yourNamespace>` pelo nome do namespace de seu serviço:

    ```
    MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
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
        await myQueueClient.SendAsync(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
    }
    ```

## <a name="receive-messages-from-the-queue"></a>Receber mensagens da fila

Nesta etapa, você obterá a lista de mensagens da fila criada na etapa anterior.

### <a name="create-a-receiver-and-receive-messages-from-the-queue"></a>Criar um receptor e receber mensagens da fila

No método `Queue()`, itere a fila e receba as mensagens usando o método [QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx), imprimindo cada mensagem no console. Adicione o código a seguir diretamente após o código adicionado na etapa anterior:

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();
    
        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Observe que `Thread.Sleep` só é usado para simular o processamento da mensagem e provavelmente não seria necessário em um aplicativo real de mensagens.

### <a name="end-the-queue-method-and-clean-up-resources"></a>Encerrar o método Queue e limpar os recursos

Diretamente após o código anterior, adicione o seguinte código para limpar os recursos de fábrica de mensagens e de fila:

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### <a name="call-the-queue-method"></a>Chame o método Queue

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

### <a name="example"></a>Exemplo

O código a seguir contém o aplicativo **QueueSample** completo.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
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

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }
            
            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
            
            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
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

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
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
    }
}
```

## <a name="build-and-run-the-queuesample-application"></a>Compilar e executar o aplicativo QueueSample

Agora que você concluiu as etapas anteriores, poderá compilar e executar o aplicativo **QueueSample**.

### <a name="build-the-queuesample-application"></a>Compilar o aplicativo QueueSample

No Visual Studio, no menu **Compilar**, clique em **Compilar Solução** ou pressione **Ctrl+Shift+B**. Se você encontrar erros, verifique se seu código está correto com base no exemplo completo apresentado no final da etapa anterior.

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como compilar um serviço e um aplicativo cliente simples do Barramento de Serviço usando os recursos de sistema de mensagens agenciado do Barramento de Serviços. Para obter um tutorial semelhante que use a [Retransmissão](service-bus-messaging-overview.md#Relayed-messaging) do Barramento de Serviço, consulte o [tutorial do sistema de mensagens de retransmissão do Barramento de Serviço](../service-bus-relay/service-bus-relay-tutorial.md).

Para saber mais sobre o [Barramento de Serviço](https://azure.microsoft.com/services/service-bus/), veja os tópicos a seguir.

- [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
- [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
- [Arquitetura do Barramento de Serviço](service-bus-architecture.md)




<!--HONumber=Oct16_HO2-->


