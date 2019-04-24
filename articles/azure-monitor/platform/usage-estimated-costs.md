---
title: Monitorar o uso e os custos estimados no Azure Monitor
description: Visão geral do processo de utilização da página de custos estimados e uso do Azure Monitor
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 7117e7287f601b306893cb02dc5d7599d7c6224d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60453739"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitorar o uso e os custos estimados no Azure Monitor

> [!NOTE]
> Este artigo descreve como exibir o uso e os custos estimados nos vários recursos de monitoramento do Azure para diferentes modelos de preços.  Consulte os seguintes artigos para informações relacionadas.
> - [Gerenciar o custo controlando a retenção e o volume de dados no Log Analytics](manage-cost-storage.md) descreve como controlar os custos, alterando seu período de retenção de dados.
> - [Analisar o uso de dados no Log Analytics](../../azure-monitor/platform/data-usage.md) descreve como analisar e alertar sobre o uso de dados.
> - [Gerenciar preços e volume de dados no Application Insights](../../azure-monitor/app/pricing.md) descreve como analisar o uso de dados no Application Insights.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

No hub do Monitor do Portal do Azure, a página **Uso e custos estimados** explica o uso dos principais recursos de monitoramento, como [alertas, métricas, notificações](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Para clientes nos planos de preços disponíveis antes de abril de 2018, isso também inclui o uso do Log Analytics adquirido por meio das ofertas do Insights e do Analytics.

Nesta página, os usuários podem exibir o uso de recursos para os últimos 31 dias, agregados por assinatura. Os drill-ins mostram tendências de uso durante o período de 31 dias. São necessários vários dados para se juntar e fazer essa estimativa, portanto, seja paciente enquanto a página é carregada.

Este exemplo mostra o uso de monitoramento e uma estimativa dos custos resultantes:

![Captura de tela do portal do uso e custos estimados](./media/usage-estimated-costs/001.png)

Selecione o link na coluna de uso mensal para abrir um gráfico que mostra as tendências de uso nos últimos 31 dias:

![Incluído por captura de tela do gráfico de barras do nó](./media/usage-estimated-costs/002.png)

Aqui está outro uso semelhante e resumo de custo. Este exemplo mostra uma assinatura no novo modelo de preços com base no consumo de abril de 2018. Observe a falta de cobrança com base no nó. A ingestão de dados e a retenção para o Log Analytics e Application Insights agora são relatadas em um novo medidor comum.

![Captura de tela do portal do uso e custos estimados - Preços de abril de 2018](./media/usage-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Novo modelo de preços

Em abril de 2018, um [novo modelo de preços de monitoramento foi liberado](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Este apresenta preços com base no consumo e é amigável para nuvem. Você paga apenas pelo que usa, sem compromissos baseados em nó. Os detalhes do novo modelo de preços estão disponíveis para [alertas, métricas, notificações](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Para clientes que participam do Log Analytics ou do Application Insights após 2 de abril de 2018, o novo modelo de preços é a única opção. Para clientes que já utilizam esses serviços, a mudança para o novo modelo de preços é opcional.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Avaliar o impacto do novo modelo de preços
O novo modelo de preços terá impactos diferentes para cada cliente com base nos padrões de uso de monitoramento. Para os clientes que estavam usando o Log Analytics ou o Application Insights antes de 2 de abril de 2018, a página **Uso e custo estimados** no Azure Monitor calcula qualquer alteração nos custos se elas são movidas para o novo modelo de preços. Ele fornece a maneira de mover uma assinatura para o novo modelo. Para a maioria dos clientes, o novo modelo de preços será vantajoso. Para clientes com padrões de alto uso de dados ou em regiões de custo mais elevados, é possível que este não seja o caso.

Para ver uma estimativa dos custos para as assinaturas que você selecionou na página **Uso e custos estimados**, selecione a faixa azul perto da parte superior da página. É recomendável fazer uma assinatura por vez, pois esse é o nível no qual o novo modelo de preços pode ser adotado.

![Monitorar o uso e os custos estimados no novo modelo de preços - captura de tela](./media/usage-estimated-costs/004.png)

A nova página mostra uma versão semelhante da página anterior com uma faixa verde:

![Monitorar o uso e os custos estimados no modelo de preços atual - captura de tela](./media/usage-estimated-costs/005.png)

A página mostra também um conjunto diferente de medidores que correspondem ao novo modelo de preços. Essa lista é um exemplo:

- Insight e Análise\Excedente por Nó
- Insight e Análise\Incluído por Nó
- Application Insights\Dados Excedentes Básicos
- Application Insights\Dados Incluídos

O novo modelo de preços não tem as alocações de dados incluídas com base no nó. Portanto, esses medidores de ingestão de dados são combinados em um novo medidor de ingestão de dados comuns chamado **Serviços Compartilhados\Ingestão de Dados**. 

Há outra alteração nos dados ingeridos no Log Analytics ou no Application Insights em regiões com custos mais altos. Os dados para essas regiões de alto custo serão mostrados com os novos medidores regionais. Um exemplo é **Ingestão de Dados (Centro-oeste dos EUA)**.

> [!NOTE]
> Os custos estimados por assinatura não influenciam os direitos por nó de nível de conta da assinatura Operations Management Suite (OMS). Neste caso, consulte seu representante de conta para uma discussão mais aprofundada sobre o novo modelo de preços.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Novo modelo de preços e direitos de inscrição do Operations Management Suite

Clientes que adquiriram o Microsoft Operations Management Suite E1 e E2 estão qualificados para direitos de ingestão de dados por nó para [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) e [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Para receber esses direitos para workspaces Log Analytics ou recursos Application Insights em uma determinada assinatura: 

- O modelo de preços da assinatura deve permanecer no modelo de pré-abril 2018.
- Workspaces do Log Analytics devem usar o tipo de preço "por nó (OMS)".
- Recursos do Application Insights devem usar o plano de preços "Enterprise".

Dependendo do número de nós do conjunto que sua organização adquiriu, mover algumas assinaturas para o novo modelo de preços poderia ser vantajoso, mas isso requer uma consideração cuidadosa. Em geral, é aconselhável simplesmente permanecer no modelo pré-abril 2018, conforme descrito acima.

> [!WARNING]
> Se sua organização adquiriu o Microsoft Operations Management Suite E1 e E2, é melhor manter suas assinaturas no modelo de preços de pré-abril 2018. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Alterações ao mudar para o novo modelo de preços

O novo modelo de preços simplifica as opções de preço do Log Analytics e Application Insights em um único tipo (ou plano). Mover uma assinatura para o novo de modelo de preços:

- Alterará o tipo de preços para cada Log Analytics para um novo tipo por GB (chamado de "pergb2018" no Azure Resource Manager)
- Todos os recursos do Application Insights no plano Enterprise são alterados para o plano Básico.

A estimativa de custo mostra os efeitos dessas alterações.

> [!WARNING]
> Aqui uma observação importante se você usa o Azure Resource Manager ou o PowerShell para implantar [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) ou [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) em uma assinatura que você alterou para o novo modelo de preços. Se você especificar um tipo/plano de preço diferente de “pergb2018” para o Log Analytics ou “Básico” para o Application Insights, em vez de a implantação falhar devido à especificação de um tipo/plano de preço inválido, ela terá êxito, **mas usará apenas o tipo/plano de preço válido**. (Isso não se aplica à Camada gratuita do Log Analytics, na qual é gerada uma mensagem de tipo de preço inválido).
>

## <a name="moving-to-the-new-pricing-model"></a>Mover para o novo modelo de preços

Se você decidiu adotar o novo modelo de preços para uma determinada assinatura, vá para cada recurso do Application Insights, abra o **uso e custos estimados** e certifique-se de que é o tipo de preço básico e vá para cada Log Analytics espaço de trabalho, abra o **tipo de preço** da página e altere para o **por GB (2018)** tipo de preço. 

> [!NOTE]
> O requisito de que todos os recursos do Application Insights e espaços de trabalho do Log Analytics dentro de uma determinada assinatura adotem o modelo de preços mais recente agora foi removido, o que permite maior flexibilidade e a configuração mais fácil. 

## <a name="automate-moving-to-the-new-pricing-model"></a>Mudança automatizada para o novo modelo de preços

Conforme observado acima, a não é mais um requisito para mover todos os recursos de monitoramento em uma assinatura para o novo modelo de preços, ao mesmo tempo e, portanto, o ``migratetonewpricingmodel`` ação não terá nenhum efeito. Agora você pode mover recursos do Application Insights e espaços de trabalho do Log Analytics separadamente para os tipos de preço mais recentes.  

Automatizar essa alteração está documentado para o Application Insights usando [Set-AzureRmApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/set-azurermapplicationinsightspricingplan) com ``-PricingPlan "Basic"`` e o Log Analytics usando [Set-AzureRmOperationalInsightsWorkspace](https://docs.microsoft.com/powershell/module/AzureRM.OperationalInsights/Set-AzureRmOperationalInsightsWorkspace) com ``-sku "PerGB2018"``. 

