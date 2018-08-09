---
title: Barramento de Serviço do Azure para exemplos de integração da Grade de Eventos | Microsoft Docs
description: Este artigo oferece exemplos do sistema de mensagens do Barramento de Serviço e integração da Grade de Eventos.
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 72a9cc905adda5146cf943d8f0ed2789c3088422
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447383"
---
# <a name="azure-service-bus-to-azure-event-grid-integration-examples"></a>Exemplos do Barramento de Serviço do Azure para a integração da Grade de Eventos do Azure

Neste artigo, você aprenderá a configurar uma função do Azure e um aplicativo lógico que recebe mensagens baseadas no recebimento de um evento da Grade de Eventos do Azure. Você fará o seguinte:
 
* Crie um [teste de função do Azure](#test-function-setup) simples para depurar e ver o fluxo inicial de eventos da Grade de Eventos. Execute esta etapa, independentemente se você executar as outras.
* Crie uma [função do Azure para receber e processar mensagens do Barramento de Serviço do Azure](#receive-messages-using-azure-function) com base em eventos da Grade de Eventos.
* Utilize o [recurso Aplicativos Lógicos do Serviço de Aplicativo do Azure](#receive-messages-using-azure-logic-app).

O exemplo que você criar pressupõe que o tópico do Barramento de Serviço tem duas assinaturas. O exemplo também pressupõe que a assinatura da Grade de Eventos foi criada para enviar eventos para apenas uma assinatura do Barramento de Serviço. 

No exemplo, envie mensagens para o tópico do Barramento de Serviço e, em seguida, verifique se o evento foi gerado para esta assinatura do Barramento de Serviço. O aplicativo de função ou lógica recebe as mensagens da assinatura do Barramento de Serviço e, em seguida, o conclui.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, verifique se você concluiu as etapas nas próximas duas seções.

### <a name="create-a-service-bus-namespace"></a>Criar um namespace do Barramento de Serviço

Crie um namespace do Barramento de Serviço Premium e crie um tópico do Barramento de Serviço que tem duas assinaturas.

### <a name="send-a-message-to-the-service-bus-topic"></a>Enviar uma mensagem para o Tópico do Barramento de Serviço

Você pode usar qualquer método para enviar uma mensagem para o tópico do Barramento de Serviço. O código de exemplo no fim deste procedimento pressupõe que você está usando o Visual Studio 2017.

1. Clone [o repositório do barramento de serviço do azure GitHub](https://github.com/Azure/azure-service-bus/).

1. No Visual Studio, vá para a pasta *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* e, em seguida, abra o arquivo *SBEventGridIntegration.sln*.

1. Vá para o projeto **MessageSender** e, em seguida, selecione **Program.cs**.

   ![8][]

1. Preencha o nome do tópico e a cadeia de conexão e, em seguida, execute o código do aplicativo de console a seguir:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```

## <a name="set-up-a-test-function"></a>Configurar uma função de teste

Antes de trabalhar com todo o cenário, configure pelo menos uma função de teste pequena, que pode ser usada para depurar e observar quais eventos estão fluindo.

1. No portal do Azure, crie um novo aplicativo do Azure Functions. Para aprender os conceitos básicos do Azure Functions, confira a [documentação do Azure Functions](https://docs.microsoft.com/azure/azure-functions/).

1. Em sua função recém-criada, selecione o sinal de adição (+) para adicionar uma função de gatilho de HTTP:

    ![2][]
    
    A janela **Comece rapidamente com uma função preexistente** é aberta.

    ![3][]

1. Clique no botão **Webhook + API**, selecione **CSharp** e, em seguida, selecione **Criar esta função**.
 
1. Na função, cole o código a seguir:

    ```CSharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
    IEnumerable<string> headerValues;
    if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
    {
    var validationHeaderValue = headerValues.FirstOrDefault();
    if(validationHeaderValue == "SubscriptionValidation")
    {
    var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
         var code = events[0].Data["validationCode"];
         return req.CreateResponse(HttpStatusCode.OK,
         new { validationResponse = code });
    }
    }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```

1. Selecione **Salvar e executar**.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Conecte a função e o namespace através da Grade de Eventos

Nesta seção, você une a função e o namespace do Barramento de Serviço. Para este exemplo, use o portal do Azure. Para entender como usar o PowerShell ou a CLI do Azure para executar este procedimento, confira [Visão Geral da integração do Barramento de Serviço do Azure para a Grade de Eventos do Azure](service-bus-to-event-grid-integration-concept.md).

Para criar uma assinatura da Grade de Eventos do Azure, faça o seguinte:
1. No portal do Azure, vá para seu namespace e, em seguida, no painel esquerdo, selecione **Grade de Eventos**.  
    A janela do namespace é aberta, com duas assinaturas da Grade de Eventos exibidas no painel direito.

    ![20][]

1. Selecione **Assinatura do Evento**.  
    A janela **Assinatura do Evento** será aberta. A imagem a seguir exibe um formulário para inscrever-se em uma função do Azure ou em um webhook sem a aplicação de filtros.

    ![21][]

1. Preencha o formulário, como mostrado e, na caixa **Filtro do Sufixo**, lembre-se de inserir o filtro relevante.

1. Selecione **Criar**.

1. Envie uma mensagem para o tópico do Barramento de Serviço, conforme mencionado na seção “Pré-requisitos”, e verifique se os eventos estão fluindo por meio do recurso de Monitoramento do Azure Functions.

A próxima etapa é unir a função e o namespace do Barramento de Serviço. Para este exemplo, use o portal do Azure. Para entender como usar o PowerShell ou a CLI do Azure para executar esta etapa, confira [Visão Geral da integração do Barramento de Serviço do Azure para a Grade de Eventos do Azure](service-bus-to-event-grid-integration-concept.md).

![9][]

### <a name="receive-messages-by-using-azure-functions"></a>Receber mensagens usando o Azure Functions

Na seção anterior, você examinou um teste simples e o cenário de depuração e verificou se os eventos estão fluindo. 

Nesta seção, você aprenderá como receber e processar mensagens depois de receber um evento.

Você adicionará uma função do Azure, conforme exibido no exemplo a seguir, porque as funções do Barramento de Serviço dentro do Azure Functions ainda não oferecem suporte nativo à nova integração da Grade de Eventos.

1. Na mesma Solução do Visual Studio aberta nos pré-requisitos, selecione **ReceiveMessagesOnEvent.cs**. 

    ![10][]

1. Insira sua cadeia de conexão no código a seguir:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```

1. No portal do Azure, baixe o perfil de publicação para a função do Azure que você criou na seção "Configurar uma função de teste".

    ![11][]

1. No Visual Studio, clique com o botão direito do mouse em **SBEventGridIntegration** e, em seguida, selecione **Publicar**. 

1. No painel **Publicar** para o perfil de publicação que você baixou anteriormente, selecione **Importar perfil** e, em seguida, selecione **Publicar**.

    ![12][]

1. Depois de publicar a nova função do Azure, crie uma nova assinatura da Grade de Eventos do Azure que aponta para a nova função do Azure.  
    Na caixa **Termina com**, aplique o filtro correto que deve ser o nome da assinatura do Barramento de Serviço.

1. Envie uma mensagem para o tópico do Barramento de Serviço do Azure criado anteriormente e, em seguida, monitore o log do Azure Functions no portal do Azure para ver se os eventos estão fluindo e se as mensagens estão sendo recebidas.

    ![12-1][]

### <a name="receive-messages-by-using-logic-apps"></a>Receber mensagens usando os Aplicativos Lógicos

Conecte um aplicativo lógico com o Barramento de Serviço do Azure e a Grade de Eventos do Azure fazendo o seguinte:

1. Primeiro, crie um novo aplicativo lógico no portal do Azure e selecione a **Grade de Eventos**.

    ![13][]

    A janela do designer de Aplicativos Lógicos será aberta.

    ![14][]

1. Adicione suas informações fazendo o seguinte:

    a. Na caixa **Nome do Recurso**, digite seu próprio nome de namespace. 

    b. Em **Opções avançadas**, na caixa **Filtro do Sufixo**, insira o filtro para sua assinatura.

1. Adicione uma ação de recebimento do Barramento de Serviço para receber mensagens a partir de uma assinatura de tópico.  
    A ação final é mostrada na imagem a seguir:

    ![15][]

1. Adicione um evento concluído, conforme mostra a imagem a seguir:

    ![16][]

1. Salve o aplicativo lógico e envie uma mensagem para o tópico do Barramento de Serviço, conforme mencionado na seção “Pré-requisitos”.  
    Observe a execução do aplicativo lógico. Para exibir mais dados para a execução, selecione **Visão geral** e, em seguida, exiba os dados em **Histórico de execuções**.

    ![17][]

    ![18][]

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Grade de Eventos do Azure](https://docs.microsoft.com/azure/event-grid/).
* Saiba mais sobre o [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Saiba mais sobre o [recurso de Aplicativos Lógicos do Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/logic-apps/).
* Saiba mais sobre o [Barramento de Serviço do Azure](https://docs.microsoft.com/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
