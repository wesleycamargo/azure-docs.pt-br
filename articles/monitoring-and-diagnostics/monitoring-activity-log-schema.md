---
title: Esquema de evento do Log de Atividades do Azure | Microsoft Docs
description: Compreender o esquema de evento para dados emitidos no Log de Atividades
author: johnkemnetz
manager: robb
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: johnkem
ms.openlocfilehash: a5c05466b21184a73d08190856e00ae95ee3727f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-activity-log-event-schema"></a>Esquema sobre eventos do Log de Atividades do Azure
O **Log de Atividades do Azure** é um log que fornece informações sobre eventos no nível da assinatura que ocorreram no Azure. Este artigo descreve o esquema de evento por categoria de dados.

## <a name="administrative"></a>Administrativo
Essa categoria contém o registro de todas as operações de criação, atualização, exclusão e ação executadas por meio do Resource Manager. Os exemplos dos tipos de eventos que você vê nessa categoria incluem "criar máquina virtual" e "excluir grupo de segurança". Cada ação tomada por um usuário ou um aplicativo usando o Resource Manager é modelada como uma operação em um tipo de recurso específico. Se o tipo de operação for Gravação, Exclusão ou Ação, os registros do início e do êxito ou falha da operação são registrados na categoria Administrativa. A categoria administrativa também inclui alterações de controle de acesso baseado em função em uma assinatura.

### <a name="sample-event"></a>Evento de exemplo
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/dd042f02-6b3e-4f79-939a-6a381ffed3c0/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/dd042f02-6b3e-4f79-939a-6a381ffed3c0/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/dd042f02-6b3e-4f79-939a-6a381ffed3c0/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "dd042f02-6b3e-4f79-939a-6a381ffed3c0",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Descrições de propriedade
| Nome do elemento | DESCRIÇÃO |
| --- | --- |
| autorização |Blob de propriedades RBAC do evento. Geralmente, inclui as propriedades "action", "role" e "scope". |
| chamador |Endereço de email do usuário que realizou a operação, declaração UPN ou declaração SPN com base na disponibilidade. |
| canais |Um dos seguintes valores: "Admin", "Operação" |
| declarações |O token JWT usado pelo Active Directory para autenticar o usuário ou aplicativo a fim de executar essa operação no Gerenciador de Recursos. |
| correlationId |Geralmente, um GUID no formato de cadeia de caracteres. Os eventos que compartilham um correlationId pertencem à mesma ação superior. |
| Descrição |Descrição de texto estático de um evento. |
| eventDataId |Identificador exclusivo de um evento. |
| httpRequest |Blob que descreve a solicitação Http. Geralmente inclui a “clientRequestId”, o “clientIpAddress” e o “método” (método HTTP. Por exemplo, PUT). |
| level |Nível do evento. Um dos seguintes valores: “Crítico”, “Erro”, “Aviso”, “Informativo” e “Detalhado” |
| resourceGroupName |Nome do grupo de recursos do recurso afetado. |
| resourceProviderName |Nome do provedor de recursos do recurso afetado |
| ResourceId |Id de recurso do recurso afetado. |
| operationId |Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Conjunto de pares de `<Key, Value>` (ou seja, um Dicionário) que descreve os detalhes do evento. |
| status |Cadeia de caracteres que descreve o status da operação. Alguns valores comuns são: Iniciado, Em Andamento, Êxito, Falha, Ativo, Resolvido. |
| subStatus |Geralmente, o código de status HTTP da chamada REST correspondente, mas também pode incluir outras cadeias de caracteres que descrevam um substatus, como estes valores comuns: OK (Código de Status HTTP: 200), Criado (Código de Status HTTP: 201), Aceito (Código de Status HTTP: 202), Sem Conteúdo (Código de Status HTTP: 204), Solicitação Incorreta (Código de Status HTTP: 400), Não Encontrado (Código de Status HTTP: 404), Conflito (Código de Status HTTP: 409), Erro de Servidor Interno (Código de Status HTTP: 500), Serviço Indisponível (Código de Status HTTP: 503), Tempo Limite do Gateway (Código de Status HTTP: 504). |
| eventTimestamp |Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento. |
| submissionTimestamp |Carimbo de hora quando o evento tornou-se disponível para consulta. |
| subscriptionId |ID de Assinatura do Azure. |

## <a name="service-health"></a>Integridade do serviço
Essa categoria contém o registro de qualquer incidente de integridade do serviço ocorrido no Azure. Um exemplo do tipo de evento que você vê nessa categoria é "SQL Azure no Leste dos EUA está passando por tempo de inatividade". Os eventos de serviço de integridade são fornecidos em cinco variedades: Ação Necessária, Recuperação Assistida, Incidente, Manutenção, Informações ou Segurança, e só aparecerão se você tiver um recurso na assinatura que seria afetada pelo evento.

### <a name="sample-event"></a>Evento de exemplo
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/mySubscriptionID/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/mySubscriptionID",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "mySubscriptionID",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```

### <a name="property-descriptions"></a>Descrições de propriedade
Nome do elemento | DESCRIÇÃO
-------- | -----------
canais | É um dos seguintes valores: “Admin” ou “Operation”
correlationId | Geralmente, é um GUID no formato de cadeia de caracteres. Eventos que pertencem à mesma ação superior geralmente compartilham a mesma correlationId.
Descrição | Descrição do alerta.
eventDataId | O identificador exclusivo de um evento.
eventName | O título do evento.
level | Nível do evento. Um dos seguintes valores: “Crítico”, “Erro”, “Aviso”, “Informativo” e “Detalhado”
resourceProviderName | Nome do provedor de recursos do recurso afetado. Se não for conhecido, será nulo.
resourceType| O tipo de recurso do recurso afetado. Se não for conhecido, será nulo.
subStatus | Geralmente é null para eventos de Integridade do Serviço.
eventTimestamp | Carimbo de data/hora de quando o log de eventos foi gerado e enviado para o Log de Atividades.
submissionTimestamp |   Carimbo de data/hora de quando o evento tornou-se disponível no Log de Eventos.
subscriptionId | A assinatura do Azure na qual esse evento foi registrado.
status | Cadeia de caracteres que descreve o status da operação. Alguns valores comuns são: Ativo, Resolvido.
operationName | Nome da operação. Normalmente Microsoft.ServiceHealth/incident/action.
categoria | “ServiceHealth”
ResourceId | ID de recurso do recurso afetado, se conhecido. Caso contrário, a ID da assinatura é fornecida.
Properties.title | O título localizado dessa comunicação. Inglês é o idioma padrão.
Properties.communication | Os detalhes localizados da comunicação com marcação HTML. Inglês é o padrão.
Properties.incidentType | Possíveis valores: AssistedRecovery, ActionRequired, Information, Incident, Maintenance, Security
Properties.trackingId | Identifica o incidente ao qual esse evento está associado. Use-a para correlacionar os eventos relacionados a um incidente.
Properties.impactedServices | Um blob JSON com escape que descreve as regiões e os serviços que são afetados pelo incidente. Uma lista de Services, que, individualmente, tem um ServiceName e uma lista de ImpactedRegions, que têm um RegionName.
Properties.defaultLanguageTitle | A comunicação em inglês
Properties.defaultLanguageContent | A comunicação em inglês como marcação HTML ou texto sem formatação
Properties.stage | Os possíveis valores para AssistedRecovery, ActionRequired, Information, Incident e Security são Active e Resolved. Para Maintenance, eles são: Active, Planned, InProgress, Canceled, Rescheduled, Resolved, Complete
Properties.communicationId | A comunicação à qual esse evento está associado.

## <a name="alert"></a>Alerta
Essa categoria contém o registro de todas as ativações de alertas do Azure. Um exemplo do tipo de evento que você vê nessa categoria é "% de CPU em myVM foi 80 nos últimos 5 minutos." Uma variedade de sistemas do Azure têm um conceito de alerta – você pode definir uma regra de algum tipo e receber uma notificação quando as condições corresponderem a essa regra. Cada vez que um tipo de alerta com suporte do Azure é 'ativado', ou as condições são atendidas para gerar uma notificação, um registro de ativação também é enviado para essa categoria de Log de Atividades.

### <a name="sample-event"></a>Evento de exemplo

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "mySubscriptionID"
}
```

### <a name="property-descriptions"></a>Descrições de propriedade
| Nome do elemento | DESCRIÇÃO |
| --- | --- |
| chamador | Always Microsoft.Insights/alertRules |
| canais | Sempre "Administrador, Operação" |
| declarações | Blob JSON com o SPN (nome da entidade de serviço) ou tipo de recurso do mecanismo de alerta. |
| correlationId | Um GUID no formato de cadeia de caracteres. |
| Descrição |Descrição de texto estático do evento de alerta. |
| eventDataId |Identificador exclusivo do alerta de evento. |
| level |Nível do evento. Um dos seguintes valores: “Crítico”, “Erro”, “Aviso”, “Informativo” e “Detalhado” |
| resourceGroupName |Nome do grupo de recursos para o recurso afetado, se for um alerta de métrica. Para outros tipos de alerta, será o nome do grupo de recursos que contém o alerta em si. |
| resourceProviderName |Nome do provedor de recursos para o recurso afetado, se for um alerta de métrica. Para outros tipos de alerta, será o nome do provedor de recursos do próprio alerta. |
| ResourceId | Nome da ID de recurso do recurso afetado, se for um alerta de métrica. Para outros tipos de alerta, essa é a ID de recurso do próprio recurso de alerta. |
| operationId |Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Conjunto de pares de `<Key, Value>` (ou seja, um Dicionário) que descreve os detalhes do evento. |
| status |Cadeia de caracteres que descreve o status da operação. Alguns valores comuns são: Iniciado, Em Andamento, Êxito, Falha, Ativo, Resolvido. |
| subStatus | Geralmente é null para alertas. |
| eventTimestamp |Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento. |
| submissionTimestamp |Carimbo de hora quando o evento tornou-se disponível para consulta. |
| subscriptionId |ID de Assinatura do Azure. |

### <a name="properties-field-per-alert-type"></a>Campo de propriedades por tipo de alerta
O campo de propriedades conterá valores diferentes dependendo da fonte do evento de alerta. Dois provedores de alerta de evento comuns são alertas do Log de Atividades e de métrica.

#### <a name="properties-for-activity-log-alerts"></a>Propriedades de alertas do Log de Atividades
| Nome do elemento | DESCRIÇÃO |
| --- | --- |
| properties.subscriptionId | A ID da assinatura do evento do log de atividades que ativou essa regra de alerta do log de atividades. |
| properties.eventDataId | A ID dos dados de evento do evento do log de atividades que ativou essa regra de alerta do log de atividades. |
| properties.resourceGroup | O grupo de recursos do evento do log de atividades que ativou essa regra de alerta do log de atividades. |
| properties.resourceId | A ID de recurso do evento do log de atividades que ativou essa regra de alerta do log de atividades. |
| properties.eventTimestamp | O carimbo de data/hora do evento do log de atividades que ativou essa regra de alerta do log de atividades. |
| properties.operationName | O nome da operação do evento do log de atividades que ativou essa regra de alerta do log de atividades. |
| properties.status | O status do evento do log de atividades que ativou essa regra de alerta do log de atividades.|

#### <a name="properties-for-metric-alerts"></a>Propriedades de alertas de métrica
| Nome do elemento | DESCRIÇÃO |
| --- | --- |
| properties.RuleUri | A ID de recurso da própria regra de alerta de métrica. |
| properties.RuleName | O nome da regra de alerta de métrica. |
| properties.RuleDescription | A descrição da regra de alerta de métrica (conforme definido na regra de alerta). |
| properties.Threshold | O valor limite usado na avaliação da regra de alerta de métrica. |
| properties.WindowSizeInMinutes | O tamanho da janela usada na avaliação da regra de alerta de métrica. |
| properties.Aggregation | O tipo de agregação definido na regra de alerta de métrica. |
| properties.Operator | O operador condicional usado na avaliação da regra de alerta de métrica. |
| properties.MetricName | O nome da métrica usada na avaliação da regra de alerta de métrica. |
| properties.MetricUnit | A unidade da métrica usada na avaliação da regra de alerta de métrica. |

## <a name="autoscale"></a>Autoscale
Essa categoria contém o registro de todos os eventos relacionados à operação do mecanismo de dimensionamento automático com base em quaisquer configurações de dimensionamento automático que você definiu na sua assinatura. Um exemplo do tipo de evento que você veria nessa categoria é "Falha na ação de escalar horizontalmente do Dimensionamento Automático". Usando o dimensionamento automático, você pode dimensionar o número de instâncias em um tipo de recurso com suporte com base na hora do dia e/ou dados de carga (métricas) usando uma configuração de dimensionamento automático. Quando as condições de redimensionamento forem atendidas, os eventos iniciados e bem-sucedidos ou com falha serão registrados nessa categoria.

### <a name="sample-event"></a>Evento de exemplo
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "mySubscriptionID"
}

```

### <a name="property-descriptions"></a>Descrições de propriedade
| Nome do elemento | DESCRIÇÃO |
| --- | --- |
| chamador | Always Microsoft.Insights/autoscaleSettings |
| canais | Sempre "Administrador, Operação" |
| declarações | Blob JSON com o SPN (nome da entidade de serviço) ou tipo de recurso do mecanismo de dimensionamento automático. |
| correlationId | Um GUID no formato de cadeia de caracteres. |
| Descrição |Descrição de texto estático do evento de dimensionamento automático. |
| eventDataId |Identificador exclusivo do evento de dimensionamento automático. |
| level |Nível do evento. Um dos seguintes valores: “Crítico”, “Erro”, “Aviso”, “Informativo” e “Detalhado” |
| resourceGroupName |Nome do grupo de recursos para a configuração de dimensionamento automático. |
| resourceProviderName |Nome do provedor de recursos para a configuração de dimensionamento automático. |
| ResourceId |ID de recurso da configuração de dimensionamento automático. |
| operationId |Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Conjunto de pares de `<Key, Value>` (ou seja, um Dicionário) que descreve os detalhes do evento. |
| properties.Description | Descrição detalhada do que o mecanismo de dimensionamento automático estava fazendo. |
| properties.ResourceName | ID do recurso do recurso afetado (o recurso no qual a ação de escala estava sendo executada) |
| properties.OldInstancesCount | O número de instâncias antes da ação de dimensionamento automático começar. |
| properties.NewInstancesCount | O número de instâncias depois da ação de dimensionamento automático começar. |
| properties.LastScaleActionTime | O carimbo de data/hora de quando ocorreu a ação de dimensionamento automático. |
| status |Cadeia de caracteres que descreve o status da operação. Alguns valores comuns são: Iniciado, Em Andamento, Êxito, Falha, Ativo, Resolvido. |
| subStatus | Geralmente é null para dimensionamento automático. |
| eventTimestamp |Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento. |
| submissionTimestamp |Carimbo de hora quando o evento tornou-se disponível para consulta. |
| subscriptionId |ID de Assinatura do Azure. |

## <a name="security"></a>Segurança
Esta categoria contém o registro de todos os alertas gerados pela Central de Segurança do Azure. Um exemplo do tipo de evento que você veria nessa categoria é "Arquivo de extensão dupla suspeito executado".

### <a name="sample-event"></a>Evento de exemplo
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/d4742bb8-c279-4903-9653-9858b17d0c2e/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/d4742bb8-c279-4903-9653-9858b17d0c2e/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "d4742bb8-c279-4903-9653-9858b17d0c2e",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Descrições de propriedade
| Nome do elemento | DESCRIÇÃO |
| --- | --- |
| canais | Sempre "Operação" |
| correlationId | Um GUID no formato de cadeia de caracteres. |
| Descrição |Descrição de texto estático do evento de segurança. |
| eventDataId |Identificador exclusivo do evento de segurança. |
| eventName |Nome amigável do evento de segurança. |
| ID |Identificador de recursos exclusivo do evento de segurança. |
| level |Nível do evento. Um dos seguintes valores: “Crítico”, “Erro”, “Aviso”, “Informativo” e “Detalhado” |
| resourceGroupName |Nome do grupo de recursos para o recurso. |
| resourceProviderName |Nome do provedor de recursos para a Central de Segurança do Azure. Sempre "Microsoft.Security". |
| resourceType |O tipo de recurso que gerou o evento de segurança, como "Microsoft.Security/locations/alerts" |
| ResourceId |ID do recurso do alerta de segurança. |
| operationId |Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Conjunto de pares de `<Key, Value>` (ou seja, um Dicionário) que descreve os detalhes do evento. Essas propriedades variam dependendo do tipo de alerta de segurança. Consulte [essa página](../security-center/security-center-alerts-type.md) para obter uma descrição dos tipos de alertas originados da Central de Segurança. |
| properties.Severity |O nível de gravidade. Os valores possíveis são "Alto", "Médio" ou "Baixo". |
| status |Cadeia de caracteres que descreve o status da operação. Alguns valores comuns são: Iniciado, Em Andamento, Êxito, Falha, Ativo, Resolvido. |
| subStatus | Geralmente null para eventos de segurança. |
| eventTimestamp |Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento. |
| submissionTimestamp |Carimbo de hora quando o evento tornou-se disponível para consulta. |
| subscriptionId |ID de Assinatura do Azure. |

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o Log de Atividades (anteriormente conhecido como Logs de Auditoria)](monitoring-overview-activity-logs.md)
* [Transmissão do Log de Atividades do Azure para os Hubs de Eventos](monitoring-stream-activity-logs-event-hubs.md)
