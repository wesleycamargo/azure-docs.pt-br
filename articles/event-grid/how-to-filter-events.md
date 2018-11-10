---
title: Como filtrar eventos para o Azure Event Grid
description: Mostra como criar assinaturas do Azure Event Grid que filtram eventos.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: tomfitz
ms.openlocfilehash: 8bf7ac9daf928c35a3d6efcac528d3372fa87c8a
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50252038"
---
# <a name="filter-events-for-event-grid"></a>Filtrar eventos de grade de eventos

Este artigo mostra como filtrar eventos ao criar uma assinatura de grade de eventos. Para saber mais sobre as opções de filtragem de eventos, consulte [Entender a filtragem de eventos para assinaturas de grade de eventos](event-filtering.md).

## <a name="filter-by-event-type"></a>Filtrar por tipo de evento

Ao criar uma assinatura da Grade de Eventos, você pode especificar quais [tipos de eventos](event-schema.md) devem enviar para o terminal. Os exemplos nesta seção criam assinaturas de eventos para um grupo de recursos, mas limitam os eventos que são enviados para `Microsoft.Resources.ResourceWriteFailure` e `Microsoft.Resources.ResourceWriteSuccess`. Se você precisar de mais flexibilidade ao filtrar eventos por tipos de eventos, consulte [Filtrar por operadores avançados e campos de dados](#filter-by-advanced-operators-and-data-fields).

Para o PowerShell, use o parâmetro `-IncludedEventType` ao criar a assinatura.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzureRmEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

CLI do Azure, use o `--included-event-types` parâmetro. O exemplo a seguir usa a CLI do Azure em um shell Bash:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Para um modelo do Resource Manager, use a propriedade `includedEventTypes`.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Filtrar por assunto

Você pode filtrar eventos pelo assunto nos dados do evento. Você pode especificar um valor para corresponder ao início ou fim do assunto. Se você precisar de mais flexibilidade ao filtrar eventos por assunto, consulte [Filtrar por operadores avançados e campos de dados](#filter-by-advanced-operators-and-data-fields).

No seguinte exemplo do PowerShell, você cria uma inscrição de evento que filtra no início do assunto. Você usa o parâmetro `-SubjectBeginsWith` para limitar eventos aos de um recurso específico. Você passa o ID do recurso de um grupo de segurança de rede.

```powershell
$resourceId = (Get-AzureRmResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzureRmEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

O próximo exemplo do PowerShell cria uma assinatura para um armazenamento de blobs. Ele limita eventos para aqueles com um assunto que termina em `.jpg`.

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzureRmEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

No seguinte exemplo de CLI do Azure, você cria uma inscrição de evento que filtra no início do assunto. Você usa o parâmetro `--subject-begins-with` para limitar eventos aos de um recurso específico. Você passa o ID do recurso de um grupo de segurança de rede.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

O próximo exemplo de CLI do Azure cria uma inscrição para um armazenamento de blobs. Ele limita eventos para aqueles com um assunto que termina em `.jpg`.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

No seguinte exemplo de modelo do Resource Manager, você cria uma inscrição de evento que filtra no início do assunto. Você usa a propriedade `subjectBeginsWith` para limitar eventos aos de um recurso específico. Você passa o ID do recurso de um grupo de segurança de rede.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

O próximo exemplo de modelo do Resource Manager cria uma assinatura para um armazenamento de blobs. Ele limita eventos para aqueles com um assunto que termina em `.jpg`.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Filtrar por dados e operadores

Para usar a filtragem avançada, você deve instalar uma extensão de visualização para a CLI do Azure. Você pode usar [CloudShell](/azure/cloud-shell/quickstart) ou instalar a CLI do Azure localmente.

### <a name="install-extension"></a>Instalar a extensão

No CloudShell:

* Se você instalou a extensão anteriormente, atualize-a `az extension update -n eventgrid`
* Se você não instalou a extensão anteriormente, instale-a `az extension add -n eventgrid`

Para uma instalação local:

1. Desinstale o CLI do Azure localmente.
1. Instale a [versão mais recente](/cli/azure/install-azure-cli) do Azure CLI.
1. Iniciar janela de comando.
1. Desinstalar as versões anteriores da extensão `az extension remove -n eventgrid`
1. Instalar a extensão `az extension add -n eventgrid`

Agora você está pronto para usar a filtragem avançada.

### <a name="subscribe-with-advanced-filters"></a>Inscreva-se com filtros avançados

Para saber mais sobre os operadores e chaves que você pode usar para filtragem avançada, consulte [Filtragem avançada](event-filtering.md#advanced-filtering).

O exemplo a seguir cria um tópico personalizado. Assina o tópico personalizado e os filtros por um valor no objeto de dados. Os eventos que possuem a propriedade color definida como azul, vermelha ou verde são enviados para a assinatura.

```azurecli-interactive
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "2018-11-30"
```

Observe que uma data de expiração está definida para a assinatura. A assinatura do evento é expirada automaticamente após essa data. Defina uma expiração para assinaturas de eventos que são necessárias apenas por um tempo limitado.

### <a name="test-filter"></a>Filtro de teste

Para testar o filtro, envie um evento com o campo de cores definido como verde.

```azurecli-interactive
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

O evento é enviado para o seu endpoint.

Para testar um cenário em que o evento não é enviado, envie um evento com o campo de cores definido como amarelo.

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Amarelo não é um dos valores especificados na assinatura, portanto, o evento não é entregue à sua assinatura.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre o monitoramento de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Para saber mais sobre a chave de autenticação, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
