---
title: Dimensionamento Automático e Gateway de Aplicativo com Redundância de Zona no Azure (Versão Prévia Pública)
description: Este artigo apresenta o SKU do Aplicativo Azure v2, que inclui os recursos de Dimensionamento automático e com redundância de zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/6/2019
ms.author: victorh
ms.openlocfilehash: 9929b09280cea56a5fadcd4d0d9aba5b851f326e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544028"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Escalonamento automático e gateway de aplicativos com redundância de zona (visualização pública)

O Gateway de Aplicativo e o Web Application Firewall (WAF) agora estão disponíveis no Public Preview em um novo SKU v2 que oferece aprimoramentos de desempenho e adiciona suporte para novos recursos críticos como escalonamento automático, redundância de zona e suporte para VIPs estáticos. Os recursos existentes sob o SKU geralmente disponível continuam sendo suportados no novo SKU v2, com poucas exceções listadas na seção de limitações conhecidas. Os novos SKUs v2 incluem os seguintes aprimoramentos:

- **Dimensionamento automático**: implantações do Gateway de Aplicativo ou do WAF com o SKU de dimensionamento automático podem ser expandidas ou reduzidas com base na mudança dos padrões de carga de tráfego. O escalonamento automático também remove o requisito de escolher um tamanho de implantação ou contagem de instâncias durante o provisionamento. Essa SKU oferece a verdadeira elasticidade. No novo SKU, o Gateway de Aplicativo pode operar tanto em capacidade fixa (dimensionamento automático desabilitado) quanto em modo habilitado em dimensionamento automático. O modo de capacidade fixa é útil para cenários com cargas de trabalho consistentes e previsíveis. O modo de escalonamento automático é benéfico em aplicativos que veem muita variação no tráfego do aplicativo.

- **Redundância de Zona**: uma implantação do Gateway de Aplicativo ou do WAF pode abranger várias Zonas de Disponibilidade, eliminando a necessidade de provisionar e girar instâncias separadas do Gateway de Aplicativo em cada zona com um Gerenciador de Tráfego. Você pode escolher uma única zona ou várias zonas nas quais as instâncias do Application Gateway são implantadas, garantindo assim a resiliência de falha de zona. O pool de back-end para aplicativos pode ser distribuído de maneira semelhante em Zonas de Disponibilidade.
- **Aprimoramentos de desempenho**: o SKU de dimensionamento automático oferece um desempenho de descarga de SSL até 5 vezes melhor em comparação com o SKU geralmente disponível.
- **Implementação mais rápida e tempo de atualização** O SKU de escalonamento automático fornece um tempo de implantação e atualização mais rápido em comparação com o SKU geralmente disponível.
- **VIP estático**: o gateway de aplicativo VIP agora é compatível exclusivamente com o tipo VIP estático. Isso garante que o VIP associado ao gateway de aplicativo não seja alterado, mesmo após uma reinicialização.

> [!IMPORTANT]
> O dimensionamento automático e o gateway de aplicativo com redundância de zona SKU está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regiões com suporte

O SKU do dimensionamento automático está disponível nas seguintes regiões: Centro-Norte dos EUA, Centro-Sul dos EUA, Oeste dos EUA, Oeste dos EUA 2, Leste dos EUA, Leste dos EUA 2, Centro dos EUA, Europa Setentrional, Europa Ocidental, Sudeste Asiático, França Central, Oeste do Reino Unido, Leste do Japão, Oeste do Japão.

## <a name="pricing"></a>Preços

Durante a visualização, não há nenhum custo. Você será cobrado por recursos, além de gateway de aplicativo, como o Key Vault, as máquinas virtuais e assim por diante.

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

|Problema|Detalhes|
|--|--|
|Certificado de autenticação|Sem suporte.<br>Para saber mais, confira [Visão geral de SSL de ponta a ponta com o Gateway de Aplicativo](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Combinando Standard_v2 e o Gateway de Aplicativo Standard na mesma sub-rede|Sem suporte|
|Rota Definida pelo Usuário (UDR) na sub-rede de Gateway de Aplicativo|Sem suporte|
|NSG para o intervalo de porta de entrada| -65200 a 65535 para Standard_v2 SKU<br>-65503 to 65534 para Standard SKU.<br>Consulte mais informações em [Perguntas Frequentes](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Logs de desempenho no diagnóstico do Azure|Sem suporte.<br>As métricas do Azure devem ser usadas.|
|Cobrança|Não há faturamento atualmente.|
|Modo FIPS, WebSocket|Essas não atualmente têm suporte.|
|Modo somente de ILB|Não há suporte para esse recurso no momento. Público e o modo ILB juntos tem suporte.|
|Integração do Netwatcher|Sem suporte na versão prévia pública.|

## <a name="next-steps"></a>Próximas etapas
- [Criar um gateway de aplicativo com redundância de zona e dimensionamento automático com um endereço IP virtual reservado usando o Azure PowerShell](tutorial-autoscale-ps.md)
- Saiba mais sobre [Gateway de aplicativo](overview.md).
- Saiba mais sobre [Firewall do Azure](../firewall/overview.md).
