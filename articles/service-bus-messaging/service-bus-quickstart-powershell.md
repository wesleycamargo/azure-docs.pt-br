---
title: Início Rápido – Usar o Azure PowerShell para criar uma fila do Barramento de Serviço | Microsoft Docs
description: Neste início rápido, você aprenderá a usar o Azure PowerShell para criar uma fila do Barramento de Serviço. Em seguida, você usará um aplicativo de exemplo para enviar e receber mensagens da fila.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/12/2019
ms.author: spelluru
ms.openlocfilehash: 36f1b39ca4cef33a8cfcdb295b5c4d59e1128a0c
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426838"
---
# <a name="quickstart-use-azure-powershell-to-create-a-service-bus-queue"></a>Início Rápido: Usar o Azure PowerShell para criar uma fila do Barramento de Serviço
O Barramento de Serviço do Microsoft Azure é um agente de mensagem de integração empresarial que fornece mensagens seguras e confiabilidade absoluta. Um cenário típico de Barramento de Serviço geralmente envolve o desacoplamento de dois ou mais aplicativos, serviços ou processos e a transferência de alterações de dados ou estado. Esses cenários podem envolver agendamento de vários trabalhos em lotes em outro aplicativo ou serviços, ou cumprimento da ordem de disparo. Por exemplo, uma empresa de varejo pode enviar os dados do ponto de vendas a um back office ou centro de distribuição regional para atualizações de estoque e reabastecimento. Nesse cenário, o aplicativo cliente envia e recebe mensagens de uma fila do Barramento de Serviço.

![fila](./media/service-bus-quickstart-powershell/quick-start-queue.png)

Este início rápido descreve como enviar e receber mensagens de e para uma fila do Barramento de Serviço, usando o PowerShell para criar uma fila e um namespace de mensagens nesse namespace e obter as credenciais de autorização nesse namespace. O procedimento mostra como enviar e receber mensagens dessa fila usando a [	Biblioteca do .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][] antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você tem o seguinte instalado:

- [Atualização 3 do Visual Studio 2017 (versão 15.3, 26730.01)](https://www.visualstudio.com/vs) ou posterior.
- [NET Core SDK](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

Este início rápido requer que você esteja executando a versão mais recente do Azure PowerShell. Se precisar instalar ou atualizar, confira [Instalar e configurar o Azure PowerShell][].

## <a name="log-in-to-azure"></a>Fazer logon no Azure

1. Primeiro, instale o módulo do PowerShell do Barramento de Serviço caso ainda não tenha feito isso:

   ```azurepowershell-interactive
   Install-Module AzureRM.ServiceBus
   ```

2. Execute o comando a seguir para fazer logon no Azure:

   ```azurepowershell-interactive
   Login-AzureRmAccount
   ```

3. Emita os comandos a seguir para definir o contexto da assinatura atual ou veja a assinatura ativa no momento:

   ```azurepowershell-interactive
   Select-AzureRmSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzureRmContext
   ```

## <a name="provision-resources"></a>Provisionar recursos

No prompt do PowerShell, emita os comandos a seguir para provisionar os recursos do Barramento de Serviço. Verifique se todos os espaços reservados foram substituídos pelos valores apropriados:

```azurepowershell-interactive
# Create a resource group 
New-AzureRmResourceGroup –Name my-resourcegroup –Location eastus

# Create a Messaging namespace
New-AzureRmServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location eastus

# Create a queue 
New-AzureRmServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzureRmServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

Depois que o cmdlet `Get-AzureRmServiceBusKey` é executado, copie e cole a cadeia de conexão e o nome da fila selecionados em um local temporário, como o Bloco de Notas. Isso será necessário na próxima etapa.

## <a name="send-and-receive-messages"></a>Enviar e receber mensagens

Depois que o namespace e a fila forem criados e você tiver as credenciais necessárias, estará pronto para enviar e receber mensagens. É possível examinar o código [nesta pasta de exemplo do GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart).

Para executar o código, faça o seguinte:

1. Clone o [repositório GitHub do Barramento de Serviço](https://github.com/Azure/azure-service-bus/), emitindo o comando a seguir:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

3. Navegue até a pasta de exemplo `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Se você ainda não fez isso, obtenha a cadeia de conexão usando o cmdlet a seguir do PowerShell. Certifique-se de substituir `my-resourcegroup` e `namespace-name` pelos valores específicos: 

   ```azurepowershell-interactive
   Get-AzureRmServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```

5.  No prompt do PowerShell, digite o comando a seguir:

   ```shell
   dotnet build
   ```

6.  Navegue até a pasta `bin\Debug\netcoreapp2.0`.

7.  Digite o comando a seguir para executar o programa. Verifique se você substituiu `myConnectionString` pelo valor obtido anteriormente e `myQueueName` pelo nome da fila que você criou:

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 

8. Observe 10 mensagens sendo enviadas para a fila e, subsequentemente, recebidas da fila:

   ![saída do programa](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Limpar recursos

Execute o comando a seguir para remover o grupo de recursos, o namespace e todos os recursos relacionados:

```powershell-interactive
Remove-AzureRmResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Esta seção contém mais detalhes sobre o que o código de exemplo faz. 

### <a name="get-connection-string-and-queue"></a>Obter cadeia de conexão e fila

A cadeia de conexão e o nome da fila são transmitidos para o método `Main()` como argumentos de linha de comando. `Main()` declara duas variáveis de cadeia de caracteres para manter esses valores:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
Em seguida, o método `Main()` inicia o loop de mensagem assíncrona, `MainAsync()`.

### <a name="message-loop"></a>Loop de mensagem

O método MainAsync () cria um cliente de fila com os argumentos de linha de comando, chama um manipulador de mensagens de recebimento nomeado `RegisterOnMessageHandlerAndReceiveMessages()` e envia o conjunto de mensagens:

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

O método `RegisterOnMessageHandlerAndReceiveMessages()` simplesmente define algumas opções de manipulador de mensagens e, em seguida, chama o métodos `RegisterMessageHandler()` do cliente de fila, que inicia o recebimento:

```csharp
static void RegisterOnMessageHandlerAndReceiveMessages()
{
    // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
    var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
    {
        // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
        // Set it according to how many messages the application wants to process in parallel.
        MaxConcurrentCalls = 1,

        // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
        // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
        AutoComplete = false
    };

    // Register the function that will process messages
    queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
} 
```

### <a name="send-messages"></a>Enviar mensagens

As operações de criação e envio de mensagens ocorrem no método `SendMessagesAsync()`:

```csharp
static async Task SendMessagesAsync(int numberOfMessagesToSend)
{
    try
    {
        for (var i = 0; i < numberOfMessagesToSend; i++)
        {
            // Create a new message to send to the queue
            string messageBody = $"Message {i}";
            var message = new Message(Encoding.UTF8.GetBytes(messageBody));

            // Write the body of the message to the console
            Console.WriteLine($"Sending message: {messageBody}");

            // Send the message to the queue
            await queueClient.SendAsync(message);
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
    }
}
```

### <a name="process-messages"></a>Processar mensagens

O método `ProcessMessagesAsync()` reconhece, processa e conclui o recebimento das mensagens:

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um namespace de Barramento de Serviço e outros recursos necessários para enviar e receber mensagens de uma fila. Para saber mais sobre como gravar código para enviar e receber mensagens, continue no tutorial para Barramento de Serviço a seguir:

> [!div class="nextstepaction"]
> [Atualizar inventário usando o portal do Azure PowerShell](./service-bus-tutorial-topics-subscriptions-powershell.md)

[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Instalar e configurar o Azure PowerShell]: /powershell/azure/azurerm/install-azurerm-ps