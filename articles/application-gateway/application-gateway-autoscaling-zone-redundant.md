---
title: Dimensionamento Automático e Gateway de Aplicativo com Redundância de Zona no Azure (Versão Prévia Pública)
description: Este artigo fornece informações sobre limites de tamanho de solicitações de firewall de aplicativo da Web e listas de exclusão no Application Gateway com o portal do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 1/10/2019
ms.author: victorh
ms.openlocfilehash: f5885fd2ac76550990c9a56a1d200bbe11555918
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213749"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Escalonamento automático e gateway de aplicativos com redundância de zona (visualização pública)

O Gateway de Aplicativo e o Web Application Firewall (WAF) agora estão disponíveis no Public Preview em um novo SKU v2 que oferece aprimoramentos de desempenho e adiciona suporte para novos recursos críticos como escalonamento automático, redundância de zona e suporte para VIPs estáticos. Os recursos existentes sob o SKU geralmente disponível continuam sendo suportados no novo SKU v2, com poucas exceções listadas na seção de limitações conhecidas. Os novos SKUs v2 incluem os seguintes aprimoramentos:

- **Dimensionamento automático**: implantações do Gateway de Aplicativo ou do WAF com o SKU de dimensionamento automático podem ser expandidas ou reduzidas com base na mudança dos padrões de carga de tráfego. O escalonamento automático também remove o requisito de escolher um tamanho de implantação ou contagem de instâncias durante o provisionamento. Portanto, a SKU oferece a verdadeira elasticidade. No novo SKU, o Gateway de Aplicativo pode operar tanto em capacidade fixa (dimensionamento automático desabilitado) quanto em modo habilitado em dimensionamento automático. O modo de capacidade fixa é útil para cenários com cargas de trabalho consistentes e previsíveis. O modo de escalonamento automático é benéfico em aplicativos que veem muita variação no tráfego do aplicativo.
   
   > [!NOTE]
   > Dimensionamento automático não está disponível atualmente para o SKU do WAF. Configure o WAF com o modo de capacidade fixo, em vez do modo de dimensionamento automático.
- **Redundância de Zona**: uma implantação do Gateway de Aplicativo ou do WAF pode abranger várias Zonas de Disponibilidade, eliminando a necessidade de provisionar e girar instâncias separadas do Gateway de Aplicativo em cada zona com um Gerenciador de Tráfego. Você pode escolher uma única zona ou várias zonas nas quais as instâncias do Application Gateway são implantadas, garantindo assim a resiliência de falha de zona. O pool de back-end para aplicativos pode ser distribuído de maneira semelhante em Zonas de Disponibilidade.
- **Aprimoramentos de desempenho**: o SKU de dimensionamento automático oferece um desempenho de descarga de SSL até 5 vezes melhor em comparação com o SKU geralmente disponível.
- **Implementação mais rápida e tempo de atualização** O SKU de escalonamento automático fornece um tempo de implantação e atualização mais rápido em comparação com o SKU geralmente disponível.
- **VIP estático**: o gateway de aplicativo VIP agora é compatível exclusivamente com o tipo VIP estático. Isso garante que o VIP associado ao gateway de aplicativo não seja alterado mesmo após uma reinicialização.

> [!IMPORTANT]
> O dimensionamento automático e o gateway de aplicativo com redundância de zona SKU está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regiões com suporte
O SKU de Dimensionamento Automático está disponível no Leste dos EUA 2, no Centro dos EUA, no Oeste dos EUA 2, no Centro-Norte dos EUA, no Oeste dos EUA, no Centro-Sul dos EUA, na França Central, na Europa Ocidental, na Europa Setentrional, no Oeste do Reino Unido, no Sudeste Asiático e no Leste do Japão.

## <a name="pricing"></a>Preços
Durante a versão prévia, não há nenhum encargo. Você é cobrado por recursos que não sejam o gateway de aplicativo, como Key Vault, máquinas virtuais etc. 

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

|Problema|Detalhes|
|--|--|
|Certificado de autenticação|Sem suporte.<br>Para saber mais, confira [Visão geral de SSL de ponta a ponta com o Gateway de Aplicativo](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Combinando Standard_v2 e o Gateway de Aplicativo Standard na mesma sub-rede|Sem suporte.<br>Além disso, se o dimensionamento automático estiver habilitado, uma sub-rede pode ter apenas um gateway de aplicativo.|
|Rota Definida pelo Usuário (UDR) na sub-rede de Gateway de Aplicativo|Sem suporte|
|NSG para o intervalo de porta de entrada| -65200 a 65535 para Standard_v2 SKU<br>-65503 to 65534 para Standard SKU.<br>Consulte mais informações em [Perguntas Frequentes](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Logs de desempenho no diagnóstico do Azure|Sem suporte.<br>As métricas do Azure devem ser usadas.|
|Cobrança|Não há faturamento atualmente.|
|Modo FIPS, WebSocket|Essas não atualmente têm suporte.|
|Modo somente de ILB|Não há suporte para esse recurso no momento. Público e o modo ILB juntos tem suporte.|
|Dimensionamento automático de firewall de aplicativo Web|WAF não oferece suporte ao modo de dimensionamento automático. Modo de capacidade fixo é suportado.|

## <a name="next-steps"></a>Próximas etapas
- [Criar um gateway de aplicativo com redundância de zona e dimensionamento automático com um endereço IP virtual reservado usando o Azure PowerShell](tutorial-autoscale-ps.md)
- Saiba mais sobre [Gateway de aplicativo](overview.md).
- Saiba mais sobre [Firewall do Azure](../firewall/overview.md). 

