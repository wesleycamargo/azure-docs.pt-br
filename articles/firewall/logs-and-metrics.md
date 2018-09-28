---
title: Visão geral dos logs do Firewall do Azure
description: Este artigo é uma visão geral dos logs de diagnóstico do Firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 6d8d0b2dc0f6baf48d2aacb9c7203937aef08d15
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957790"
---
# <a name="azure-firewall-logs"></a>Logs do Firewall do Azure

Você pode monitorar o Firewall do Azure usando os logs de firewall. Você também pode usar os logs de atividades para auditar operações nos recursos do Firewall do Azure.

Você pode acessar alguns desses logs por meio do portal. Os logs podem ser enviados para [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Armazenamento e Hubs de Eventos e analisados no Log Analytics ou por outras ferramentas, como Excel e Power BI.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

 Os seguintes logs de diagnóstico estão disponíveis para o Firewall do Azure:

* **Log de regra de aplicativo**

   O log de regra do aplicativo é salvo em uma conta de armazenamento, transmitido para hubs de eventos e/ou enviados ao Log Analytics somente se você tiver habilitado ele para cada Firewall do Azure. Cada nova conexão que corresponda a uma de suas regras de aplicativo configurado gera um log para a conexão aceita/negada. Os dados são registrados no formato JSON, conforme mostrado no seguinte exemplo:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Log de regra de rede**

   O log de regra de rede é salvo em uma conta de armazenamento, transmitido para hubs de eventos e/ou enviados ao Log Analytics somente se você tiver habilitado ele para cada Firewall do Azure. Cada nova conexão que corresponda a uma de suas regras de rede configuradas gera um log para a conexão aceita/negada. Os dados são registrados no formato JSON, conforme mostrado no seguinte exemplo:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Você tem três opções para armazenar os logs:

* **Conta de armazenamento**: as contas de armazenamento são mais adequadas para os logs quando eles são armazenados por mais tempo e examinados quando necessário.
* **Hubs de eventos**: os hubs de eventos são uma ótima opção para integração a outras ferramentas SEIM (informações de segurança e gerenciamento de evento) para receber alertas sobre os recursos.
* **Log Analytics**: o Log Analytics é mais adequado para o monitoramento geral em tempo real do aplicativo ou para a observação de tendências.

## <a name="activity-logs"></a>Logs de atividade

   As entradas do log de atividades são coletadas por padrão e podem ser exibidas no portal do Azure.

   Você pode usar os [logs de atividades do Azure](../azure-resource-manager/resource-group-audit.md) (anteriormente conhecidos como logs operacionais e logs de auditoria) para exibir todas as operações que estão sendo enviadas à sua assinatura do Azure.


## <a name="next-steps"></a>Próximas etapas

Para saber como monitorar as métricas e logs do Firewall do Azure, veja [Tutorial: Monitorar os logs de Firewall do Azure](tutorial-diagnostics.md).