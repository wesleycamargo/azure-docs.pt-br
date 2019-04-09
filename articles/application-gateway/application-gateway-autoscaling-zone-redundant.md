---
title: Dimensionamento Automático e Gateway de Aplicativo com Redundância de Zona no Azure (Versão Prévia Pública)
description: Este artigo apresenta o SKU do Aplicativo Azure v2, que inclui os recursos de Dimensionamento automático e com redundância de zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/6/2019
ms.author: victorh
ms.openlocfilehash: 4410dd9e61fe5b585ca5b245dbf33dbf8c38e701
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59010202"
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

> [!NOTE]
> O dimensionamento automático e o gateway de aplicativo com redundância de zona SKU agora dá suporte a [investigação de integridade padrão](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe) para monitorar a integridade de todos os recursos em seu pool de back-end e remover qualquer recurso considerado não íntegro do pool automaticamente. O colocará de investigação de integridade padrão configurado automaticamente para todos os back-ends para o qual você não configurou nenhuma configuração de investigação personalizada. Para obter mais informações, consulte [investigações de integridade no gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Comparação de recursos entre o SKU de v1 e v2 SKU

A tabela a seguir compara os recursos disponíveis com cada SKU.

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Dimensionamento automático                                       |          | &#x2713; |
| Redundância de zona                                   |          | &#x2713; |
| &nbsp;VIP estático&nbsp;&nbsp;                      |          | &#x2713; |
| Roteamento baseado em URL                                 | &#x2713; | &#x2713; |
| Hospedagem de vários sites                             | &#x2713; | &#x2713; |
| Redirecionamento do tráfego                               | &#x2713; | &#x2713; |
| Firewall do aplicativo Web (WAF)                    | &#x2713; | &#x2713; |
| Encerramento do protocolo SSL            | &#x2713; | &#x2713; |
| Criptografia SSL de ponta a ponta                         | &#x2713; | &#x2713; |
| Afinidade de sessão                                  | &#x2713; | &#x2713; |
| Páginas de erro personalizadas                                | &#x2713; | &#x2713; |
| Reescreva cabeçalhos HTTP (S)                           |          | &#x2713; |
| Suporte para WebSocket                                 | &#x2713; | &#x2713; |
| Suporte do HTTP/2                                    | &#x2713; | &#x2713; |
| Descarregamento de conexão                               | &#x2713; | &#x2713; |
| Controlador de entrada de serviço do Kubernetes (AKS) do Azure |          | &#x2713; |

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
|Modo FIPS|Essas não atualmente têm suporte.|
|Modo somente de ILB|Não há suporte para esse recurso no momento. Público e o modo ILB juntos tem suporte.|
|Integração do Netwatcher|Sem suporte na versão prévia pública.|

## <a name="next-steps"></a>Próximas etapas
- [Criar um dimensionamento automático, o gateway de aplicativo com redundância de zona com um endereço IP virtual reservado usando o Azure PowerShell](tutorial-autoscale-ps.md)
- Saiba mais sobre [Gateway de aplicativo](overview.md).
- Saiba mais sobre [Firewall do Azure](../firewall/overview.md).
