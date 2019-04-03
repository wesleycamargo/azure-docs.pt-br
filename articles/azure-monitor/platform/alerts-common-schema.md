---
title: Esquema comum de alerta para alertas do Azure monitor
description: Noções básicas sobre o esquema de alerta comuns, por que você deve usar a ele e como habilitá-lo
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 8f8dcff0b72ea92e835c0702113a9cb6a7678e86
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851664"
---
# <a name="common-alert-schema"></a>Esquema de alerta comum

Este artigo descreve o que é o esquema de alerta comuns, os benefícios de usar a ele e como habilitá-lo.

## <a name="what-is-the-common-alert-schema"></a>O que é o esquema de alerta comuns?

O esquema de alerta comuns padroniza a experiência de consumo para notificações de alerta no Azure hoje mesmo. Historicamente, os três tipos de alertas no Azure hoje (métrica, log e log de atividades) tiveram seus próprios modelos de email, esquemas de webhook, etc. Com o esquema de alerta comuns, agora você pode receber notificações de alerta com um esquema consistente.

Qualquer instância de alerta descreve **o recurso que foi afetado** e **a causa do alerta**, e essas instâncias são descritas no esquema comum nas seções a seguir:
* **Essentials**: Um conjunto de **padronizado campos**, comuns em todos os tipos de alerta, que descrevem **quais recursos** o alerta está nos juntamente com metadados adicionais de comuns alerta (por exemplo, severidade ou descrição). 
* **Contexto de alerta**: Um conjunto de campos que descrevem os **causar do alerta**, com campos que variam de acordo **com base no tipo de alerta**. Por exemplo, um alerta de métrica teria os campos como o nome da métrica e o valor da métrica no contexto de alerta, enquanto que um alerta do log de atividades teria informações sobre o evento que gerou o alerta. 

Os cenários de integração comuns que ouvimos de nossos clientes envolvem o roteamento da instância de alerta para a equipe em questão com base em alguma dinâmica (por exemplo, grupo de recursos), após o qual a equipe responsável começa a trabalhar nele. Com o esquema de alerta comuns, você pode ter padronizado lógica de roteamento em tipos de alerta, aproveitando os campos essenciais, deixando os campos de contexto como está para as equipes em questão para investigar mais.

Isso significa que você pode potencialmente ter menos de integrações, tornando o processo de gerenciamento e manutenção uma _muito_ tarefa mais simples. Além disso, aprimoramentos de conteúdo de alerta futuras (por exemplo, personalização, enriquecimento de diagnóstico, etc.) serão apenas superficial para cima no esquema comum.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Que aprimoramentos colocar o esquema de alerta comuns?

O esquema de alerta comuns principalmente se manifestará em suas notificações de alerta. Os aprimoramentos que você verá estão listados abaixo:

| Ação | Melhorias|
|:---|:---|
| sms | Um modelo consistente de SMS para todos os tipos de alerta. |
| Email | Um modelo de email consistente e detalhadas, permitindo que você diagnosticar facilmente problemas rapidamente. Links profundo inseridos para a instância de alerta no portal e os recursos afetados Certifique-se de que você pode ir rapidamente para o processo de correção. |
| Função de aplicativo/Azure/lógica de Webhook | Uma JSON estrutura consistente para todos os tipos de alerta, que permite que você crie facilmente as integrações entre os diferentes tipos de alerta. |

O novo esquema também habilitará uma experiência mais rica de consumo de alerta no portal do Azure e o aplicativo móvel do Azure em um futuro imediato. 

[Saiba mais sobre as definições de esquema para Webhooks lógicos/aplicativos/Azure Functions.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> As seguintes ações não dão suporte o esquema comum de alerta: Conector ITSM, Runbook de automação.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Como habilitar o alerta esquema comum?

Você pode aceitar ou recusar no esquema comum do alerta por meio de grupos de ação sobre o portal e a API REST. A alternância para alternar para o novo esquema existe em um nível de ação. Por exemplo, você precisa aceitar separadamente para uma ação de email e uma ação de webhook.

> [!NOTE]
> 1. Os seguintes tipos de alertas dão suporte a esquema comum por padrão (sem aceitar necessário):
>     * Alertas de falha de anomalias
> 1. Os seguintes tipos de alerta atualmente não dão suporte a esquema comum:
>     * Alertas de integridade do serviço
>     * Log de atividades – alertas de segurança
>     * Alertas gerados por [Azure Monitor para VMs](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)

### <a name="through-the-azure-portal"></a>No portal do Azure

![Aceitar o esquema comum de alerta](media/alerts-common-schema/portal-opt-in.png)

1. Abra qualquer existente ou uma nova ação em um grupo de ação. 
1. Selecione 'Sim' para a alternância habilitar o esquema de alerta comuns, conforme mostrado.

### <a name="through-the-action-groups-rest-api"></a>Por meio de grupos de ação de API REST

Você também pode usar o [API de grupos de ação](https://docs.microsoft.com/rest/api/monitor/actiongroups) para aceitar o esquema de alerta comuns. Utilizando o [criar ou atualizar](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) chamada à API REST, você pode definir o sinalizador "useCommonAlertSchema" como 'true' (para aceitar) ou 'falso' (a recusa) para qualquer uma das seguintes ações - lógicos/email/webhook aplicativo do Azure/função.

Por exemplo, a solicitação a seguir corpo feito para o [criar ou atualizar](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) API REST fará o seguinte:

* Habilitar o esquema de alerta comuns para a ação de email "Email de John Doe"
* Desabilitar o esquema de alerta comuns para a ação de email "Email de Jane Smith"
* Habilitar o esquema de alerta comuns para a ação de webhook "Webhook de exemplo"

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>Próximas etapas

- [Definições de alerta de esquema comuns para Webhooks lógicos/aplicativos/Azure Functions.](https://aka.ms/commonAlertSchemaDefinitions)



