---
title: Filtragem baseada em inteligência de ameaças do Azure do Firewall
description: Saiba mais sobre a filtragem de inteligência de ameaça do Firewall do Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 4ef9089c94d9e806cc519c4f8243cdcb7e73953a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60194014"
---
# <a name="azure-firewall-threat-intelligence-based-filtering---public-preview"></a>A filtragem baseada em inteligência contra ameaças de Firewall do Azure - visualização pública

A filtragem contra ameaças baseada em inteligência pode ser habilitada para o seu firewall de forma a alertar e rejeitar o tráfego de/para endereços IP e domínios mal-intencionados. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. [Gráfico de segurança inteligente](https://www.microsoft.com/en-us/security/operations/intelligence) impulsiona a inteligência contra ameaças da Microsoft e é usado por vários serviços, incluindo a Central de segurança do Azure.

![Inteligência contra ameaças de firewall](media/threat-intel/firewall-threat.png)

> [!IMPORTANT]
> Filtragem com base em inteligência de ameaças estão atualmente em visualização pública e é fornecida com um contrato de nível de serviço de visualização. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.  Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

Se ameaças a filtragem baseada em inteligência estiver habilitada, as regras associadas são processadas antes de qualquer uma das regras NAT, regras de rede ou regras de aplicativo. Durante a visualização, somente os registros de confiança mais altos são incluídos.

Você pode optar por registrar apenas um alerta quando uma regra é disparada, ou você pode escolher o alerta e negar modo.

Por padrão, a ameaça a filtragem baseada em inteligência está habilitado no modo de alerta. Você não pode desativar esse recurso ou alterar o modo de até que a interface do portal está disponível em sua região.

![Inteligência contra ameaças com base em filtragem de interface de portal](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Logs

O seguinte trecho do log mostra uma regra de disparada:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testando

- **Saída de teste** -alertas de tráfego de saída devem ser uma ocorrência rara, pois significa que seu ambiente tiver sido comprometido. Para ajudar a alertas de saída de teste estão funcionando, um teste FQDN tiver sido criado, o que dispara um alerta. Use **testmaliciousdomain.eastus.cloudapp.azure.com** para seus testes de saída.

- **Teste de entrada** -você pode esperar ver alertas no tráfego de entrada, se as regras DNAT são configuradas no firewall. Isso é verdadeiro mesmo que apenas fontes específicas são permitidas na regra DNAT e o tráfego é negado caso contrário. Firewall do Azure não emite alertas sobre todos os scanners de porta conhecida. somente em scanners que são conhecidas também se envolvam em atividades mal-intencionadas.

## <a name="next-steps"></a>Próximos passos

- Consulte [amostras de análise de Log de Firewall do Azure](log-analytics-samples.md)
- Saiba como [implantar e configurar um Firewall do Azure](tutorial-firewall-deploy-portal.md)
- Examine o [relatório de inteligência do Microsoft Security](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)