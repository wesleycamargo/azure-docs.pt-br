---
title: Chamar um webhook em um alerta de log de atividades do Azure | Microsoft Docs
description: "Saiba como rotear eventos de log de atividades para outros serviços para ações personalizadas. Por exemplo, você pode enviar mensagens SMS, registrar bugs ou notificar uma equipe por meio do serviço de chat/mensagens."
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
ms.openlocfilehash: 9872c30d123f0a7443e28dc58ee0d4e16572a390
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
# <a name="call-a-webhook-on-an-azure-activity-log-alert"></a>Chamar um webhook em alertas de log de atividades do Azure
É possível usar webhooks para encaminhar uma notificação de alerta do Azure para outros sistemas para pós-processamento ou notificações personalizadas. Você pode usar um webhook em um alerta para encaminhá-lo para serviços que enviam mensagens de SMS, para registrar bugs, notificar uma equipe por meio de serviços de chat ou envio de mensagens ou qualquer outra ação. Você também pode configurar um alerta de log de atividades para enviar um email quando um alerta for ativado.

Este artigo descreve como configurar um webhook para ser chamado quando um alerta do log de atividades do Azure é disparado. Também mostra a aparência do conteúdo do HTTP POST para um webhook. Para obter informações sobre a instalação e o esquema de um alerta de métrica do Azure, consulte [Configurar um webhook em um alerta do métrica do Azure](insights-webhooks-alerts.md). 

> [!NOTE]
> No momento, o recurso que permite chamar um webhook em um alerta do log de atividades do Azure está em versão prévia.
>
>

Você pode configurar um alerta do log de atividades usando [cmdlets do Azure PowerShell](insights-powershell-samples.md#create-metric-alerts), uma [CLI entre plataformas](insights-cli-samples.md#work-with-alerts) ou [APIs REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx). No momento, você não pode usar o portal do Azure para configurar um alerta de log de atividades.

## <a name="authenticate-the-webhook"></a>Autenticar o webhook
O webhook pode autenticar usando um destes métodos:

* **Autorização baseada em token**. O URI do webhook é salvo com uma ID de token. Por exemplo: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
* **Autorização básica**. O URI do webhook é salvo com um nome de usuário e senha. Por exemplo: `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Esquema de conteúdo
A operação POST contém o seguinte esquema e carga JSON para todos os alertas baseados em log de atividades. Esse esquema é semelhante àquele usado para alertas baseados em métrica.

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
| status |Usado para alertas de métrica. Para alertas de log de atividades, sempre defina como Ativado.|
| context |O contexto do evento. |
| log de atividades | As propriedades de log do evento.|
| autorização |As propriedades de RBAC (Controle de Acesso Baseado em Função) do evento. Essas propriedades geralmente incluem **ação**, **função** e **escopo**. |
| ação | A ação capturada pelo alerta. |
| scope | O escopo do alerta (ou seja, o recurso).|
| canais | A operação. |
| declarações | Uma coleção de informações que se relaciona com as declarações. |
| chamador |O GUID ou nome de usuário do usuário que realizou a operação, a declaração UPN ou a declaração SPN com base na disponibilidade. Pode ser um valor nulo para determinadas chamadas do sistema. |
| correlationId |Geralmente um GUID no formato de cadeia de caracteres. Eventos com **correlationId** pertencem à mesma ação maior. Normalmente, eles têm o mesmo valor de **correlationId**. |
| Descrição |A descrição do alerta que foi definida quando o alerta foi criado. |
| eventSource |O nome do serviço ou infraestrutura do Azure que gerou o evento. |
| eventTimestamp |A hora em que o evento ocorreu. |
| eventDataId |O identificador exclusivo do evento. |
| level |Um dos seguintes valores: Crítico, Erro, Aviso, Informativo e Detalhado. |
| operationName |O nome da operação. |
| operationId |Geralmente, um GUID que é compartilhado entre os eventos. O GUID normalmente corresponde a uma única operação. |
| ResourceId |A ID do recurso afetado. |
| resourceGroupName |O nome do grupo de recursos do recurso afetado. |
| resourceProviderName |O provedor de recursos do recurso afetado. |
| status |Um valor de cadeia de caracteres que indica o status da operação. Os valores comuns incluem: Iniciado, Em Andamento, Êxito, Falha, Ativo, Resolvido. |
| subStatus |Geralmente inclui o código de status HTTP da chamada REST correspondente. Também pode incluir outras cadeias de caracteres que descrevam um substatus. Os valores de substatus comuns incluem: OK (Código de Status HTTP: 200), Criado (Código de Status HTTP: 201), Aceito (Código de Status HTTP: 202), Sem Conteúdo (Código de Status HTTP: 204), Solicitação Incorreta (Código de Status HTTP: 400), Não Encontrado (Código de Status HTTP: 404), Conflito (Código de Status HTTP: 409), Erro Interno do Servidor (Código de Status HTTP: 500), Serviço Indisponível (Código de Status HTTP: 503), Tempo Limite do Gateway (Código de Status HTTP: 504). |
| subscriptionId |A ID de assinatura do Azure. |
| submissionTimestamp |A hora em que o evento foi gerado pelo serviço do Azure que processou a solicitação. |
| resourceType | O tipo de recurso que gerou o evento.|
| propriedades |Um conjunto de pares chave/valor que tem detalhes sobre o evento. Por exemplo, `Dictionary<String, String>`. |

## <a name="next-steps"></a>Próximas etapas
* Leia mais sobre o [log de atividades](monitoring-overview-activity-logs.md).
* Saiba como [Executar exemplos de scripts da Automação do Azure (runbooks) em alertas do Azure](http://go.microsoft.com/fwlink/?LinkId=627081).
* Saiba como [usar um aplicativo lógico para enviar uma mensagem de SMS por meio do Twilio de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Este exemplo serve para alertas de métrica, mas pode ser modificado para funcionar com um alerta do log de atividades.
* Saiba como [usar um aplicativo lógico para enviar uma mensagem do Slack de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Este exemplo serve para alertas de métrica, mas pode ser modificado para funcionar com um alerta do log de atividades.
* Saiba como [usar um aplicativo lógico para enviar uma mensagem a uma fila do Azure de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Este exemplo serve para alertas de métrica, mas pode ser modificado para funcionar com um alerta do log de atividades.
