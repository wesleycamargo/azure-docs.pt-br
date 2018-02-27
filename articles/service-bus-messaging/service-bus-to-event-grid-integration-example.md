---
title: "Barramento de Serviço do Azure para exemplos de integração da Grade de Eventos | Microsoft Docs"
description: "Exemplos do sistema de mensagens do Barramento de Serviço e integração da Grade de Eventos"
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 8aff62dd32395c1ef292942e977467779f945931
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-service-bus-to-azure-event-grid-examples"></a>Barramento de Serviço do Azure para exemplos da Grade de Eventos do Azure

Neste documento, você aprenderá a configurar o Azure Functions e um aplicativo lógico que recebe mensagens baseadas no recebimento de um evento da Grade de Eventos. O exemplo supõe um tópico do Barramento de Serviço com duas assinaturas e que a assinatura da Grade de Eventos seja criada de modo a enviar eventos apenas para uma assinatura do Barramento de Serviço. Depois, envie mensagens para o tópico do Barramento de Serviço e verifique se o evento é gerado para essa assinatura do Barramento de Serviço e, em seguida, o aplicativo de função ou lógico recebe as mensagens daquela assinatura do Barramento de Serviço e a completa.

* Primeiro, verifique se você tem todos os [Pré-requisitos](#prerequisites) em vigor.
* Crie um [teste simples do Azure Function](#test-function-setup) para depurar e ver o fluxo inicial de eventos da Grade de Eventos.  **Esta etapa deve ser feita independentemente de executar 3. ou 4.**
* Crie um [Azure Function para receber e processar mensagens do barramento de serviço](#receive-messages-using-azure-function) com base em eventos da grade de eventos.
* Utilize [Aplicativos Lógicos](#receive-messages-using-azure-logic-app).

## <a name="prerequisites"></a>pré-requisitos

### <a name="service-bus-namespace"></a>Namespace do Barramento de Serviço

Crie um namespace premium do Barramento de Serviço. Crie um tópico do Barramento de Serviço com duas assinaturas.

### <a name="code-to-send-message-to-the-service-bus-topic"></a>Código para enviar mensagens para o tópico do Barramento de Serviço

Você pode usar qualquer meio para enviar uma mensagem para o tópico do Barramento de Serviço. Como alternativa, use o exemplo abaixo. O código de exemplo pressupõe que você está usando o Visual Studio 2017.

Clone [este repositório GitHub](https://github.com/Azure/azure-service-bus/).

Navegue até a pasta a seguir:

\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration e abra o arquivo: SBEventGridIntegration.sln.

Depois, navegue até o projeto MessageSender e abra Program.cs.

![8][]

Preencha o nome do tópico e também a cadeia de conexão e execute o aplicativo de console:

```CSharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
const string TopicName = "YOUR TOPIC NAME";
```

## <a name="test-function-setup"></a>Configuração da função de teste

Antes de trabalhar com todo o cenário de ponta a ponta, é preciso ter pelo menos uma função de teste pequena, que pode ser usada para depurar e ver quais eventos estão fluindo.

No portal, crie um novo Aplicativo do Azure Function. Siga este [link](https://docs.microsoft.com/en-us/azure/azure-functions/) para aprender os conceitos básicos do Azure Functions.

Em sua função recém-criada, clique no pequeno sinal de mais para adicionar uma função de gatilho de http:

![2][]

![3][]

Depois, copie o seguinte código na função:

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

Clique em salvar e executar.

## <a name="connect-function-and-namespace-via-event-grid"></a>Conecte a função e o namespace através da Grade de Eventos

A próxima etapa é unir a função e o namespace do barramento de serviço. Para este exemplo, use o portal do Azure. Confira a página [conceitos](service-bus-to-event-grid-integration-concept.md) para entender como usar o PowerShell ou a CLI do Azure para obter o mesmo.

Para criar uma nova assinatura da Grade de Eventos do Azure, navegue até o namespace no portal do Azure e selecione a folha da Grade de Eventos. Clique em “+ Assinatura de Evento”.

A captura de tela abaixo mostra um namespace que já tem algumas assinaturas da Grade de Eventos.

![20][]

A captura de tela a seguir mostra como assinar um Azure Function ou um Gancho da Web sem qualquer filtragem específica. Lembre-se de adicionar o filtro à sua assinatura do Barramento de Serviço como "Filtro de Sufixo":

![21][]

Envie uma mensagem para o tópico do Barramento de Serviço, conforme mencionado nos pré-requisitos e verifique se os eventos estão fluindo por meio do recurso de Monitoramento do Azure Function.

![9][]

### <a name="receive-messages-using-azure-function"></a>Enviar e receber mensagens usando o Azure Function

Na seção anterior, você examinou um teste simples e o cenário de depuração e verificou se os eventos estão fluindo. Nesta parte da documentação, você examinará como receber e processar mensagens ao receber um evento.

O motivo para adicionar um Azure Function da seguinte maneira é porque as funções do Barramento de Serviço dentro do Azure Functions em si ainda não oferecem suporte nativo à nova integração da Grade de Eventos.

Na mesma Solução do Visual Studio aberta nos pré-requisitos, selecione ReceiveMessagesOnEvent.cs. Insira sua cadeia de conexão no código:

![10][]

```Csharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
```

Depois, acesse o portal do Azure e baixe o perfil de publicação para o Azure Function criado antes, em 2[. Configuração da função de teste](#2-test-function-setup).

![11][]

Em seguida, no Visual Studio, clique com o botão direito do mouse em SBEventGridIntegration e selecione Publicar. Use o perfil de publicação baixado antes, selecione Importar perfil e clique em Publicar.

![12][]

Depois de publicar o novo Azure Function, crie uma nova assinatura da Grade de Eventos do Azure, apontando para o novo Azure Function. Verifique se você aplicou o filtro correto "Termina com", que deve ser o nome da sua assinatura do Barramento de Serviço.

Em seguida, envie uma mensagem para o tópico do Barramento de Serviço do Azure criado anteriormente e examine o log do Azure Function no portal para ver se os eventos estão fluindo e se as mensagens são recebidas.

![12-1][]

### <a name="receive-messages-using-azure-logic-app"></a>Receber mensagens usando o Aplicativo Lógico do Azure

As instruções a seguir mostram como se conectar a um Aplicativo Lógico do Azure junto com o Barramento de Serviço do Azure e a Grade de Eventos do Azure:

Primeiro, crie um novo Aplicativo Lógico no portal do Azure e selecione a Grade de Eventos como ação de início.

![13][]

A exibição inicial no designer de Aplicativos Lógicos deve parecer com a seguinte captura de tela, enquanto você precisa substituir "Nome do Recurso" com seu próprio nome de namespace. Além disso, verifique se você expandiu as opções avançadas e adicionou o filtro de sufixo de sua assinatura:

![14][]

Em seguida, adicione uma ação de recebimento do barramento de serviço para receber a partir de uma assinatura de tópico. A ação final deve ter aparência semelhante à captura de tela a seguir.

![15][]

Depois, adicione um evento de conclusão que deve ter esta aparência.

![16][]

Salve o aplicativo lógico e envie uma mensagem para o tópico do Barramento de Serviço, conforme mencionado nos pré-requisitos. Depois, observe a execução dos Aplicativos lógicos. Se você clicar em "Visão geral" e depois em "Histórico de execuções", também é possível obter mais dados sobre a execução.

![17][]

![18][]

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Grade de Eventos do Azure](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Saiba mais sobre o [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Saiba mais sobre [Aplicativos Lógicos do Azure](https://docs.microsoft.com/en-us/azure/logic-apps/).
* Saiba mais sobre o [Barramento de Serviço do Azure](https://docs.microsoft.com/en-us/azure/azure-functions/).

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