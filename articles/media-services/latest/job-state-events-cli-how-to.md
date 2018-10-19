---
title: Rotear eventos de Serviços de Mídia do Azure para um ponto de extremidade da Web personalizado  | Microsoft Docs
description: Use a Grade de Eventos do Azure para assinar o evento de alteração de estado do trabalho dos Serviços de Mídia.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/20/2018
ms.author: juliako
ms.openlocfilehash: e7268a066acf41c454de0c66aa21603199d85a60
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034834"
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>Rotear eventos de Serviços de Mídia do Azure para um ponto de extremidade da Web personalizado usando a CLI

A Grade de Eventos do Azure é um serviço de eventos para a nuvem. Neste artigo, você usa a CLI do Azure para assinar eventos de alteração de estado de trabalho dos Serviços de Mídia do Azure e para acionar o evento para exibir o resultado. 

Normalmente, você envia eventos para um ponto de extremidade que responde ao evento, como um webhook ou uma Função do Azure. Este tutorial mostra como criar e definir um webhook.

Quando você concluir as etapas descritas neste artigo, verá que os dados do evento foi enviados para um ponto de extremidade.

## <a name="prerequisites"></a>Pré-requisitos

- Ter uma assinatura ativa do Azure.
- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).

    Lembre-se dos valores que você usou para o nome do grupo de recursos e o nome da conta de Serviços de Mídia.

- Instale a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Este artigo requer a CLI do Azure versão 2.0 ou posterior. Execute `az --version` descobrir a versão que você tem. Você também pode usar o [Azure Cloud Shell](https://shell.azure.com/bash).

## <a name="enable-event-grid-resource-provider"></a>Habilitar provedor de recursos da Grade de Eventos

A primeira coisa que você precisa fazer é garantir que você tenha o provedor de recursos da Grade de Eventos habilitado em sua assinatura. 

No portal do **Azure**, faça o seguinte:

1. Vá para Assinaturas.
2. Selecione sua assinatura.
3. Em Configurações, selecione Provedores de Recursos.
4. Pesquise por "EventGrid".
5. Verifique se a Grade de Eventos está registrada. Se não estiver, clique no botão **Registrar**.  

## <a name="create-a-generic-azure-function-webhook"></a>Criar um webhook genérico do Azure Functions 

### <a name="create-a-message-endpoint"></a>Criar um ponto de extremidade de mensagem

Antes de se inscrever no artigo da Grade de Eventos, crie um ponto de extremidade que coleta as mensagens, para que você possa exibi-las.

Crie uma função disparada por um webhook genérico, conforme descrito no artigo [webhook genérico](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function). Neste tutorial, o código **C#** é usado.

Quando o webhook for criado, copie a URL. Basta clicar no link *Obter URL de função* na parte superior da janela do Portal do **Azure**. Não é necessário a última parte da URL (*&clientID=default*).

![Criar um webhook](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>Validar o webhook

Quando você registra seu próprio ponto de extremidade de webhook com a Grade de Eventos, ele envia uma solicitação POST com um código de validação simples para comprovar a propriedade do ponto de extremidade. Seu aplicativo precisa responder retornando o código de validação como eco. A Grade de Eventos não entrega eventos para pontos de extremidade do webHook que não passaram na validação. Para saber mais, confira [Event Grid security and authentication](https://docs.microsoft.com/azure/event-grid/security-authentication) (Segurança e autenticação da Grade de Eventos). Esta seção define duas partes que devem ser definidas para a validação passar.

#### <a name="update-the-source-code"></a>Atualizar o código-fonte

Depois de criar o webhook o arquivo **run.csx** aparece no navegador. Substitua o código padrão pelo código a seguir. 

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"Webhook was triggered!");

    string jsonContent = await req.Content.ReadAsStringAsync();
    string eventGridValidation = 
        req.Headers.FirstOrDefault( x => x.Key == "Aeg-Event-Type" ).Value?.FirstOrDefault();

    dynamic eventData = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"event: {eventData}");

    if (eventGridValidation != String.Empty)
    {
        if (eventData[0].data.validationCode !=String.Empty && eventData[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent")
        {
            return req.CreateResponse(HttpStatusCode.OK, new 
            {
                validationResponse = eventData[0].data.validationCode
            });
        }
    }
    
    log.Info(jsonContent);

    return req.CreateResponse(HttpStatusCode.OK);
}
```

#### <a name="update-test-request-body"></a>Atualize o corpo da solicitação de teste

À direita da janla do Portal do **Azure**, você verá duas guias: **Exibir arquivos** e **Teste**. Selecione a guia **Testar**. No **Corpo da solicitação**, cole o json a seguir. Você pode colá-lo como ele está. Não é necessário alterar nenhum valor.

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}
]
```

Clique em **Salvar e executar** na parte superior da janela.

![Corpo da solicitação](./media/job-state-events-cli-how-to/generic_webhook_test.png)

## <a name="register-for-the-event-grid-subscription"></a>Registrar-se na assinatura da Grade de Eventos 

Você se inscreve em um artigo para indicar à Grade de Eventos quais eventos você deseja acompanhar. O exemplo a seguir assina a conta dos Serviços de Mídia que você criou e transmite a URL do webhook do Azure Functions que você criou como o ponto de extremidade para a notificação de eventos. 

Substitua `<event_subscription_name>` por um nome exclusivo para a assinatura de evento. Para `<resource_group_name>` e `<ams_account_name>`, use os valores que você escolheu ao criar a conta de Serviços de Mídia. Para `<endpoint_URL>`, cole a URL do ponto de extremidade. Remova *&clientID=default* da URL. A especificação de um ponto de extremidade durante a assinatura faz com que a Grade de Eventos manipule o roteamento de eventos para esse ponto de extremidade. 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

O valor da ID de recurso da conta de Serviços de Mídia é semelhante a este:

```
/subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amstestaccount
```

## <a name="test-the-events"></a>Teste os eventos

Crie um trabalho de codificação. Por exemplo, conforme descrito no início rápido [Transmissão de arquivos de vídeo](stream-files-dotnet-quickstart.md).

Você disparou o evento e a Grade de Eventos enviou a mensagem para o ponto de extremidade configurado durante a assinatura. Navegue até o webhook que você criou anteriormente. Clique em **Monitorar** e **Atualizar**. Consulte os eventos de alterações de estado do trabalho: "Em fila", "Agendado", "Processando", "Concluído", "Erro", "Cancelado", "Cancelando".  Para obter mais informações, confira [Esquemas de eventos dos Serviços de Mídia](media-services-event-schemas.md).

O exemplo a seguir mostra o esquema do evento JobStateChange:

```json
[{
  "topic": "/subscriptions/<subscription id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job id>",
  "eventType": "Microsoft.Media.JobStateChange",
  "eventTime": "2018-04-20T21:17:26.2534881",
  "id": "<id>",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

![Eventos de teste](./media/job-state-events-cli-how-to/test_events.png)

## <a name="next-steps"></a>Próximas etapas

[Reagindo a eventos](reacting-to-media-services-events.md)

## <a name="see-also"></a>Consulte também

[CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
