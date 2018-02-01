---
title: Chamar um webhook em alertas do Log de Atividades do Azure | Microsoft Docs
description: "Rotear eventos de log de atividade para outros serviços para ações personalizadas. Por exemplo, envie SMS, registre bugs ou notifique uma equipe por meio do serviço de bate-papo/mensagens."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 64d333d1-7f37-4a00-9d16-dda6e69a113b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: johnkem
ms.openlocfilehash: 08467aed4e1601b32598fc42515d9c38b601a9d4
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="call-a-webhook-on-azure-activity-log-alerts"></a>Chamar um webhook em alertas do Log de Atividades do Azure
Os webhooks permitem rotear uma notificação de alerta do Azure para outros sistemas para pós-processamento ou notificações personalizadas. Você pode usar um webhook em um alerta para roteá-lo aos serviços que enviam SMS, registrar bugs, notificar uma equipe por meio de serviços de bate-papo/mensagens ou qualquer outra ação. Este artigo descreve como configurar um webhook para ser chamado quando um alerta do Log de Atividades do Azure é disparado. Também mostra a aparência do conteúdo do HTTP POST para um webhook. Para obter informações sobre a configuração e o esquema de um alerta de métrica do Azure, [consulte esta página](insights-webhooks-alerts.md). Você também pode configurar um alerta do Log de Atividades para enviar um email quando estiver ativado.

> [!NOTE]
> Atualmente, esse recurso está em visualização e será removido em algum momento no futuro.
>
>

Você pode configurar um alerta do Log de Atividades usando os [Cmdlets do Azure PowerShell](insights-powershell-samples.md#create-metric-alerts), [CLI entre plataformas](insights-cli-samples.md#work-with-alerts) ou [API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx). No momento, você não pode definir um usando o portal do Azure.

## <a name="authenticating-the-webhook"></a>Autenticação do webhook
O webhook pode autenticar usando um destes métodos:

1. **Autorização baseada em token** - O URI do webhook é salvo com uma ID de token, por exemplo, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2. **Autorização básica** - O URI do webhook é salvo com um nome de usuário e senha, por exemplo, `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Esquema de conteúdo
A operação POST contém o seguinte esquema e carga JSON para todos os alertas baseados em Log de Atividade. Esse esquema é semelhante àquele usado por alertas baseados em métrica.

```json
{
    "WebhookName": "Alert1515526229589",
    "RequestBody": {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "authorization": {
                        "action": "Microsoft.Compute/virtualMachines/deallocate/action",
                        "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1"
                    },
                    "channels": "Operation",
                    "claims": {
                        "aud": "https://management.core.windows.net/",
                        "iss": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "iat": "1234567890",
                        "nbf": "1234567890",
                        "exp": "1234567890",
                        "aio": "Y2NgYBD8ZLlhu27JU6WZsXemMIvVAAA=",
                        "appid": "00000000-0000-0000-0000-000000000000",
                        "appidacr": "2",
                        "e_exp": "262800",
                        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "http://schemas.microsoft.com/identity/claims/objectidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                        "uti": "XnCk46TrDkOQXwo49Y8fAA",
                        "ver": "1.0"
                    },
                    "caller": "00000000-0000-0000-0000-000000000000",
                    "correlationId": "00000000-0000-0000-0000-000000000000",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-01-09T20:11:25.8410967+00:00",
                    "eventDataId": "00000000-0000-0000-0000-000000000000",
                    "level": "Informational",
                    "operationName": "Microsoft.Compute/virtualMachines/deallocate/action",
                    "operationId": "00000000-0000-0000-0000-000000000000",
                    "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
                    "resourceGroupName": "ContosoVM",
                    "resourceProviderName": "Microsoft.Compute",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "submissionTimestamp": "2018-01-09T20:11:40.2986126+00:00",
                    "resourceType": "Microsoft.Compute/virtualMachines"
                }
            },
            "properties": {}
        }
    },
    "RequestHeader": {
        "Expect": "100-continue",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "IcMBroadcaster/1.0",
        "X-CorrelationContext": "RkkKACgAAAACAAAAEADlBbM7x86VTrHdQ2JlmlxoAQAQALwazYvJ/INPskb8S5QzgDk=",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

| Nome do elemento | DESCRIÇÃO |
| --- | --- |
| status |Usado para alertas de métrica. Sempre definido como "ativado" para alertas do Log de Atividades. |
| context |Contexto do evento. |
| log de atividades | As propriedades de log do evento.|
| autorização |Propriedades RBAC do evento. Isso geralmente inclui a “ação”, a “função” e o “escopo”. |
| ação | Ação capturada pelo alerta. |
| scope | Estado do alerta (ex. recurso).|
| canais | Operação |
| declarações | Uma coleção de informações que se relaciona com as declarações. |
| chamador |GUID ou nome de usuário do usuário que realizou a operação, declaração UPN ou declaração SPN com base na disponibilidade. Pode ser nulo para determinadas chamadas do sistema. |
| correlationId |Geralmente um GUID no formato de cadeia de caracteres. Eventos com correlationId pertencem à mesma ação maior e geralmente compartilham uma correlationId. |
| Descrição |Descrição do alerta conforme definido durante a criação do alerta. |
| eventSource |Nome do serviço ou infraestrutura do Azure que gerou o evento. |
| eventTimestamp |Hora em que o evento ocorreu. |
| eventDataId |Identificador exclusivo do evento. |
| level |Um dos seguintes valores: “Crítico”, “Erro”, “Aviso”, “Informativo” e “Detalhado”. |
| operationName |Nome da operação. |
| operationId |Geralmente um GUID compartilhado entre os eventos correspondentes a uma única operação. |
| ResourceId |ID de recurso do recurso afetado. |
| resourceGroupName |Nome do grupo de recursos do recurso afetado |
| resourceProviderName |O provedor de recursos do recurso afetado. |
| status |Cadeia de caracteres. Status da operação. Os valores comuns incluem: "Iniciado", "Em Andamento", "Êxito", "Falha", "Ativo", "Resolvido". |
| subStatus |Geralmente inclui o código de status HTTP da chamada REST correspondente. Também pode incluir outras cadeias de caracteres que descrevam um substatus. Os valores de substatus comuns incluem: OK (Código de Status HTTP: 200), Criado (Código de Status HTTP: 201), Aceito (Código de Status HTTP: 202), Sem Conteúdo (Código de Status HTTP: 204), Solicitação Incorreta (Código de Status HTTP: 400), Não Encontrado (Código de Status HTTP: 404), Conflito (Código de Status HTTP: 409), Erro Interno do Servidor (Código de Status HTTP: 500), Serviço Indisponível (Código de Status HTTP: 503), Tempo Limite do Gateway (Código de Status HTTP: 504) |
| subscriptionId |ID de assinatura do Azure. |
| submissionTimestamp |Hora quando o evento foi gerado pelo serviço do Azure que processou a solicitação. |
| resourceType | O tipo de recurso que gerou o evento.|
| propriedades |Conjunto de pares `<Key, Value>` (ou seja, `Dictionary<String, String>`) que inclui detalhes sobre o evento. |

## <a name="next-steps"></a>Próximas etapas
* [Leia mais sobre o Log de Atividades](monitoring-overview-activity-logs.md)
* [Exemplos de scripts da Automação do Azure (Runbooks) em alertas do Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Usar aplicativo lógico para enviar um SMS por meio de Twilio de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Este exemplo serve para alertas de métrica, mas pode ser modificado para funcionar com um alerta do Log de Atividades.
* [Usar aplicativo lógico para enviar uma mensagem do Slack de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Este exemplo serve para alertas de métrica, mas pode ser modificado para funcionar com um alerta do Log de Atividades.
* [Usar aplicativo lógico para enviar uma mensagem a uma Fila do Azure de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Este exemplo serve para alertas de métrica, mas pode ser modificado para funcionar com um alerta do Log de Atividades.
