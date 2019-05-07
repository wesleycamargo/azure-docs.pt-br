---
title: Obter alterações de recurso
description: Entender como encontrar quando um recurso foi alterado e obter uma lista das propriedades que foi alterada.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/20/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 81cb5e62c8abc93aa5defb690628c7178fd2a869
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142532"
---
# <a name="get-resource-changes"></a>Obter alterações de recurso

Recursos sejam alterados durante o curso de uso diário, reconfiguração e até mesma reimplantação.
Alterações podem vir de um indivíduo ou por um processo automatizado. A maioria das alterações ocorre por design, mas às vezes não é. Com os últimos 14 dias de histórico de alterações, o gráfico de recursos do Azure permite que você:

- Localize quando as alterações foram detectadas em uma propriedade do Azure Resource Manager.
- Veja quais propriedades foram alteradas como parte do evento de alterações.

Detecção de alteração e os detalhes são importantes para os cenários de exemplo a seguir:

- Durante o gerenciamento de incidentes para entender _potencialmente_ alterações relacionadas. Consultar eventos de alteração durante uma janela específica de tempo e avaliar os detalhes de alteração.
- Mantendo um banco de dados de gerenciamento de configuração, conhecido como um CMDB, atualizado. Em vez de atualizar todos os recursos e seus conjuntos de propriedades completo com a frequência agendada, obter apenas o que foi alterado.
- Noções básicas sobre quais outras propriedades podem ter sido alteradas quando um recurso alterado o estado de conformidade. Avaliação dessas propriedades adicionais pode fornecer informações sobre outras propriedades que precisam ser gerenciados por meio de uma definição de política do Azure.

Este artigo mostra como coletar essas informações por meio do SDK do gráfico de recursos. Para ver essas informações no portal do Azure, consulte o Azure Policy [histórico de alterações](../../policy/how-to/determine-non-compliance.md#change-history-preview) ou o Log de atividades do Azure [histórico de alterações](../../../azure-monitor/platform/activity-logs-overview.md#view-change-history).

> [!NOTE]
> Detalhes de alteração no gráfico de recursos são para as propriedades do Gerenciador de recursos. Para controlar alterações em uma máquina virtual, consulte pela automação do Azure [controle de alterações](../../../automation/automation-change-tracking.md) ou o Azure Policy [configuração de convidado para VMs](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Histórico de alterações no gráfico de recursos do Azure está em visualização pública.

## <a name="find-when-changes-were-detected"></a>Encontrar quando as alterações foram detectadas

A primeira etapa em ver o que mudou em um recurso é encontrar os eventos de alteração relacionados a esse recurso dentro de uma janela de tempo. Esta etapa é feita por meio de **resourceChanges** ponto de extremidade REST.

O **resourceChanges** ponto de extremidade requer dois parâmetros no corpo da solicitação:

- **resourceId**: O recurso do Azure para procurar alterações.
- **interval**: Uma propriedade com _inicie_ e _final_ datas para quando verificar se há um evento de alteração usando o **Zulu fuso horário (Z)**.

Exemplo de corpo de solicitação:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

Com o corpo da solicitação acima, o URI da API REST para **resourceChanges** é:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

A resposta é semelhante a este exemplo:

```json
{
    "changes": [{
            "changeId": "2db0ad2d-f6f0-4f46-b529-5c4e8c494648",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

Cada detectado que o evento de alteração para o **resourceId** tem um **changeId** que é exclusiva para esse recurso. Enquanto o **changeId** cadeia de caracteres, às vezes, pode conter outras propriedades, tem apenas garantido que ele seja exclusivo. O registro de alteração inclui os tempos de que o antes e depois os instantâneos foram tirados.
O evento de alteração ocorreu em algum ponto nessa janela de tempo.

## <a name="see-what-properties-changed"></a>Consulte as propriedades alteradas

Com o **changeId** da **resourceChanges** ponto de extremidade, o **resourceChangeDetails** ponto de extremidade REST, em seguida, é usado para obter informações específicas de evento de alteração.

O **resourceChangeDetails** ponto de extremidade requer dois parâmetros no corpo da solicitação:

- **resourceId**: O recurso do Azure para procurar alterações.
- **changeId**: O evento de alteração exclusivo para o **resourceId** obtidas **resourceChanges**.

Exemplo de corpo de solicitação:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "53dc0515-b86b-4bc2-979b-e4694ab4a556"
}
```

Com o corpo da solicitação acima, o URI da API REST para **resourceChangeDetails** é:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

A resposta é semelhante a este exemplo:

```json
{
    "changeId": "53dc0515-b86b-4bc2-979b-e4694ab4a556",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** e **afterSnapshot** cada dar as propriedades e a hora em que o instantâneo foi tirado nesse momento. A alteração ocorreu em algum ponto entre esses instantâneos. Examinando o exemplo acima, podemos ver que a propriedade que mudou foi **supportsHttpsTrafficOnly**.

Para comparar os resultados por meio de programação, comparar os **conteúdo** parte de cada um para determinar a diferença. Se você comparar todo o instantâneo, o **carimbo de hora** sempre mostra como uma diferença, apesar de que está sendo esperada.

## <a name="next-steps"></a>Próximas etapas

- Consulte o idioma em uso no [consultas Starter](../samples/starter.md).
- Consulte avançada usa na [consultas avançadas](../samples/advanced.md).
- Saiba como [explore recursos](../concepts/explore-resources.md).
