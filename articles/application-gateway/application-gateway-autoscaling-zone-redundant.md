---
title: Escalonamento Automático e Gateway de Aplicativo redundante de Zona no Azure (Visualização Pública) | Microsoft Docs
description: Este artigo fornece informações sobre limites de tamanho de solicitações de firewall de aplicativo da Web e listas de exclusão no Application Gateway com o portal do Azure.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 09/26/2018
ms.author: victorh
ms.openlocfilehash: ab1c9405042de02183b8742fa940a3a5a482923a
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165222"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Escalonamento automático e gateway de aplicativos redundante de zona (visualização pública)

O Application Gateway e o Web Application Firewall (WAF) agora estão disponíveis no Public Preview em um novo SKU que oferece aprimoramentos de desempenho e adiciona suporte para novos recursos críticos como escalonamento automático, redundância de zona e suporte para VIPs estáticos. Os recursos existentes sob o SKU geralmente disponível continuam sendo suportados no novo SKU, com poucas exceções listadas na seção de limitações conhecidas. Os novos SKUs incluem os seguintes aprimoramentos:

- **Escalonamento automático**: implantações de Gateway de Aplicativo ou WAF sob o SKU de escalonamento automático podem ser ampliados ou reduzidos com base na mudança de padrões de carga de tráfego. O escalonamento automático também remove o requisito de escolher um tamanho de implantação ou contagem de instâncias durante o provisionamento. Portanto, a SKU oferece a verdadeira elasticidade. No novo SKU, o Application Gateway pode operar tanto em capacidade fixa (escalonamento automático desativado) quanto em modo ativado em escalonamento automático. O modo de capacidade fixa é útil para cenários com cargas de trabalho consistentes e previsíveis. O modo de escalonamento automático é benéfico em aplicativos que veem muita variação no tráfego do aplicativo.
   
   > [!NOTE]
   > Dimensionamento automático não está disponível atualmente para o SKU do WAF. Configure o WAF com o modo de capacidade fixo, em vez do modo de dimensionamento automático.
- **Redundância de zona**: uma implantação do Application Gateway ou do WAF pode abranger várias Zonas de disponibilidade, eliminando a necessidade de provisionar e girar instâncias separadas do Application Gateway em cada zona com um Gerenciador de Tráfego. Você pode escolher uma única zona ou várias zonas nas quais as instâncias do Application Gateway são implantadas, garantindo assim a resiliência de falha de zona. O pool de back-end para aplicativos pode ser distribuído de maneira semelhante em Zonas de Disponibilidade.
- **Melhorias no desempenho**: O SKU de escalonamento automático oferece um desempenho de descarga de SSL até 5 vezes melhor em comparação com o SKU geralmente disponível.
- **Implementação mais rápida e tempo de atualização** O SKU de escalonamento automático fornece um tempo de implantação e atualização mais rápido em comparação com o SKU geralmente disponível.
- **VIP estático**: O gateway de aplicativo VIP agora suporta exclusivamente o tipo VIP estático. Isso garante que o VIP associado ao gateway de aplicativo não seja alterado mesmo após uma reinicialização.

> [!IMPORTANT]
> O dimensionamento automático e o gateway de aplicativo com redundância de zona SKU está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regiões com suporte
AutoScaling SKU está disponível no Leste dos EUA 2, centro dos EUA, oeste dos EUA 2, França Central, Europa Ocidental e sudeste asiático.

## <a name="pricing"></a>Preços
Durante a versão prévia, não há nenhum encargo. Você será cobrado por recursos, além de gateway de aplicativo, como o Key Vault, as máquinas virtuais, etc. 

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

|Problema|Detalhes|
|--|--|
|Cobrança|Não há faturamento atualmente.|
|Modo FIPS, WebSocket|Essas não atualmente têm suporte.|
|Modo somente de ILB|Não há suporte para esse recurso no momento. Público e o modo ILB juntos tem suporte.|
|Dimensionamento automático de firewall de aplicativo Web|WAF não oferece suporte ao modo de dimensionamento automático. Modo de capacidade fixo é suportado.|

## <a name="next-steps"></a>Próximas etapas
- [Criar um gateway de aplicativo com redundância de zona e dimensionamento automático com um endereço IP virtual reservado usando o Azure PowerShell](tutorial-autoscale-ps.md)
- Saiba mais sobre [Gateway de aplicativo](overview.md).
- Saiba mais sobre [Firewall do Azure](../firewall/overview.md). 

