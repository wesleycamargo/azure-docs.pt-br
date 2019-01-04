---
title: Enviar eventos usando o .NET Framework – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo apresenta instruções passo a passo para a criação de um aplicativo .NET Fraemwork que envia eventos para os Hubs de Eventos do Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 062dc707dea99ed6e5e04905a13572c234f0c172
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091146"
---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>Enviar eventos para Hubs de Eventos do Azure usando o .NET Framework
Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Este tutorial mostra como enviar eventos a um hub de eventos usando um aplicativo de console escrito em C# usando o .NET Framework. 

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* [Microsoft Visual Studio 2017 ou superior](https://visualstudio.com).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um namespace de Hubs de Eventos e um hub de eventos
A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [neste artigo](event-hubs-create.md) e então continue com as etapas a seguir neste tutorial.

Obtenha a cadeia de conexão para o namespace do hub de eventos seguindo as instruções do artigo: [Obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Você usa a cadeia de conexão posteriormente no tutorial.

## <a name="create-a-console-application"></a>Criar um aplicativo de console

No Visual Studio, crie um novo projeto de aplicativo de área de trabalho do Visual C# usando o modelo de projeto de **Aplicativo de Console** . Nomeie o projeto como **Remetente**.
   
![Criar um aplicativo de console](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

## <a name="add-the-event-hubs-nuget-package"></a>Adicione o pacote NuGet de Hubs de Eventos

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **Remetente** clique em **Gerenciar Pacotes NuGet para Solução**. 
2. Clique na guia **Procurar** e procure `WindowsAzure.ServiceBus`. Clique em **Instalar**e aceite os termos de uso. 
   
    ![Instale o pacote do NuGet do Barramento de Serviço](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    O Visual Studio faz o download, instala e adiciona uma referência ao [pacote NuGet de biblioteca do Barramento de Serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

## <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever código para enviar mensagens ao hub de eventos

1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
2. Adicione os seguintes campos à classe **Programa**, substituindo os valores do espaço reservado pelo nome do hub de eventos criado na seção anterior e pela cadeia de conexão no nível do namespace que você salvou anteriormente. Você pode copiar a cadeia de conexão do hub de eventos da chave **Primária da cadeia de conexão** em **RootManageSharedAccessKey** na página do Hub de Eventos no portal do Azure. Para obter as etapas detalhadas, confira [Obter cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal).
   
    ```csharp
    static string eventHubName = "Your Event Hub name";
    static string connectionString = "namespace connection string";
    ```
3. Adicione o seguinte método à classe **Programa** :
   
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
   
      Esse método envia continuamente os eventos para seu hub de eventos com um atraso de 200 ms.
4. Por fim, adicione as seguintes linhas ao método **Principal** :
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. Execute o programa e certifique-se de que não existem erros.
  
Parabéns! Agora você enviou mensagens para um hub de eventos.

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você enviou mensagens para um hub de eventos usando o .NET Framework. Para saber como receber eventos de um hub de eventos usando o .NET Framework, consulte [Receber eventos de hub de eventos – .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md).

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

