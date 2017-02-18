---
title: Enviar eventos para Hubs de Eventos do Azure usando o .NET Framework | Microsoft Docs
description: "Introdução ao envio de eventos para Hubs de Eventos usando o .NET Framework"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/30/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: c52f7055897ba8e851add431e5ab9c0defdb5bfc
ms.openlocfilehash: 29523e308e038904773582c73c1688f57e3c31e3


---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>Enviar eventos para Hubs de Eventos do Azure usando o .NET Framework

## <a name="introduction"></a>Introdução
Os Hubs de Eventos são um serviço que processa grandes quantidades de dados de eventos (telemetria) a partir de aplicativos e dispositivos conectados. Depois de coletar dados para Hubs de Eventos, você pode armazenar os dados usando um cluster de armazenamento ou transformá-los usando um provedor de análise em tempo real. Essa funcionalidade de coleta e processamento de eventos em grande escala é um componente fundamental de arquiteturas de aplicativos modernas, incluindo a IoT (Internet das Coisas).

Este tutorial mostra como usar o [portal do Azure](https://portal.azure.com) para criar um Hub de Eventos. Ele também mostra como enviar eventos para um Hub de eventos usando um aplicativo de console escrito em c# usando o .NET Framework. Para receber eventos usando o .NET Framework, veja o artigo [Receber eventos usando o .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md) artigo, ou clique no idioma apropriado de recebimento no sumário à esquerda.

Para concluir esse tutorial, você precisará do seguinte:

* [Microsoft Visual Studio](http://visualstudio.com)
* Uma conta ativa do Azure. Se não tiver uma, você poderá criar uma conta gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="send-messages-to-event-hubs"></a>Enviar mensagens ao Hub de Eventos
Nesta seção, você escreverá um aplicativo de console do Windows para enviar eventos para o hub de eventos.

1. No Visual Studio, crie um novo projeto de aplicativo de área de trabalho do Visual C# usando o modelo de projeto de **Aplicativo de Console** . Nomeie o projeto como **Remetente**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)
2. No Gerenciador de Soluções, clique com o botão direito na solução e clique em **Gerenciar Pacotes NuGet para Solução**. 
3. Clique na guia **Procurar** e procure `Microsoft Azure Service Bus`. Verifique se o nome do projeto (**Remetente**) está especificado na caixa **Versão(ões)**. Clique em **Instalar**e aceite os termos de uso. 
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    O Visual Studio faz o download, instala e adiciona uma referência ao [pacote NuGet de biblioteca do Barramento de Serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus).
4. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
5. Adicione os seguintes campos à classe **Programa** , substituindo os valores do espaço reservado pelo nome do Hub de Eventos criado na seção anterior e a cadeia de conexão no nível do namespace que você salvou anteriormente.
   
    ```csharp
    static string eventHubName = "{Event Hub name}";
    static string connectionString = "{send connection string}";
    ```
6. Adicione o seguinte método à classe **Programa** :
   
    ```csharp
    static void SendingRandomMessages()
    {
        var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
        while (true)
        {
            try
            {
                var message = Guid.NewGuid().ToString();
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                Console.ResetColor();
            }
   
            Thread.Sleep(200);
        }
    }
    ```
   
    Este método envia continuamente os eventos ao seu hub de eventos com um atraso de 200 ms.
7. Por fim, adicione as seguintes linhas ao método **Main** :
   
    ```csharp
    Console.WriteLine("Press Ctrl-C to stop the sender process");
    Console.WriteLine("Press Enter to start now");
    Console.ReadLine();
    SendingRandomMessages();
    ```

## <a name="next-steps"></a>Próximas etapas
Agora que você criou um aplicativo funcional que cria um Hub de Eventos e envia dados, poderá passar para os seguintes cenários:

* [Receber eventos usando o Host de Processador de Eventos](event-hubs-dotnet-framework-getstarted-receive-eph.md)
* [Referência do host de processador de eventos](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Visão Geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md




<!--HONumber=Feb17_HO1-->


