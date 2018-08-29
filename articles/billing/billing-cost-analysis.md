---
title: Explore os custos do Azure com a análise de custos | Microsoft Docs
description: Este artigo ajuda você a usar a análise de custo para explorar e analisar seus custos organizacionais do Azure.
services: billing
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/16/2018
ms.topic: conceptual
ms.service: billing
manager: dougeby
ms.custom: ''
ms.openlocfilehash: eeaf02853f8ffe9ca67dbf31afc687afb7dee242
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180391"
---
# <a name="explore-and-analyze-costs-with-cost-analysis"></a>Explorar e analisar os custos com análise de custo

Antes de poder controlar e otimizar adequadamente seus custos do Azure, você precisa entender onde os custos se originaram em sua organização. Também é útil saber quanto dinheiro seus serviços custam e em apoio a quais ambientes e sistemas. A visibilidade em todo o espectro de custos é fundamental para entender com precisão os padrões de gastos organizacionais, que podem ser usados para impor mecanismos de controle de custos, como orçamentos.

Neste artigo, você usa a análise de custo para explorar e analisar seus custos organizacionais. Você visualiza os custos agregados de sua organização para entender onde os custos são acumulados e identificar tendências de gastos. Você visualiza os custos acumulados ao longo do tempo para estimar tendências de custo mensais, trimestrais ou mesmo anuais em comparação a um orçamento. Um orçamento ajuda a aderir a restrições financeiras ou a ver custos diários ou mensais para isolar irregularidades de gastos. E, então, você pode baixar dados do relatório atual para análise posterior ou para usar em um sistema externo.

## <a name="requirements"></a>Requisitos

A análise de custos está disponível para todos os clientes do Enterprise Agreement (EA). Você deve ter acesso de leitura a pelo menos um dos seguintes escopos para visualizar os dados de custo.

- EA do Azure (registro) da conta de cobrança
- Assinatura de EA do Azure
- Grupo de recursos de assinatura de EA do Azure

## <a name="review-costs-in-cost-analysis"></a>Examine os custos de análise de custo

Para analisar seus custos com a análise de custos, abra o Portal do Azure e navegue para **Gerenciamento de Custos + Faturamento** &gt; **Contas de faturamento** &gt; selecione sua conta de faturamento do EA &gt; em Gerenciamento de Custos , selecione **Análise de custo**.

A visão inicial da análise de custos inclui as seguintes áreas:

**Total** - Mostra o custo total do mês atual.

**Orçamento** - Mostra o limite de gastos planejados para o escopo selecionado, se disponível.

**Custo acumulado**: mostra o total de gastos diários acumulados, a partir do início do mês. Se você [criou um orçamento](billing-cost-management-budget-scenario.md#create-the-azure-budget) para sua conta de faturamento ou assinatura, poderá ver rapidamente sua tendência de gastos em relação ao orçamento. Passe o mouse sobre uma data para o custo acumulado até aquele dia.

**Gráficos de pivô (donut)** - Fornecem pivôs dinâmicos. Eles dividem o custo total por um conjunto comum de propriedades padrão. Os gráficos mostram o menor custo acumulado para o mês atual. Você pode alterar gráficos dinâmicos a qualquer momento selecionando um pivô diferente. Por padrão, os custos são divididos por serviço (categoria do medidor), local (região) e escopo filho (por exemplo, contas de inscrição em contas de faturamento, grupos de recursos em assinaturas e recursos em grupos de recursos).

![Visão inicial da análise de custo](./media/billing-cost-analysis/cost-analysis-01.png)



## <a name="customizing-cost-views"></a>Personalizando exibições de custo

A visualização padrão fornece respostas rápidas para perguntas comuns, como:

- Quanto eu gasta?
- Será permanecer dentro do meu orçamento?

No entanto, existem muitos casos em que você precisa de uma análise mais profunda. A personalização começa no topo da página, com a seleção da data.

Por padrão, a análise de custo mostra dados para o mês atual. Use o seletor de datas para alternar rapidamente para o último mês, este mês, este trimestre, este ano ou um período personalizado de sua escolha. A seleção do último mês é a maneira mais rápida de analisar sua última fatura do Azure e reconciliar as cobranças com facilidade. As opções atuais de trimestre e ano ajudam a acompanhar os custos em relação aos orçamentos de longo prazo. Se preferir, você pode selecionar um intervalo de datas mais refinado ou mais amplo, de um único dia até os últimos sete dias ou qualquer coisa em um ano antes do mês atual.

![Seletor de data](./media/billing-cost-analysis/date-selector.png)

Também por padrão, a análise de custo mostra **custos acumulados**. Os custos acumulados incluem todos os custos para cada dia, além dos dias anteriores, para uma visão em constante crescimento de seus custos diários acumulados. Essa visualização é otimizada para mostrar como você está tendendo contra um orçamento para o período selecionado.

Há também o **diária** modo de exibição. Ele mostra custos para cada dia e não mostra uma tendência de crescimento. A visualização diária é otimizada para mostrar irregularidades, pois os custos aumentam ou diminuem de um dia para o outro. Quando você seleciona um orçamento, a visualização diária também mostra uma estimativa de como seu orçamento diário pode ser. Se os seus custos diários estiverem consistentemente acima do orçamento diário estimado, poderá exceder o seu orçamento mensal. O orçamento diário estimado é simplesmente um meio de ajudar você a visualizar seu orçamento em um nível inferior. Quando você tem flutuações nos custos diários, a comparação do orçamento diário estimado com o orçamento mensal é menos precisa.

![Exibição diária](./media/billing-cost-analysis/daily-view.png)

Você pode **Agrupar por** para selecionar uma categoria de grupo para alterar os dados exibidos no gráfico da área total superior. Agrupamento permite ver rapidamente como seus gastos estão categorizados por tipo de recurso. Aqui está uma exibição dos custos de serviço do Azure para uma exibição do último mês.

![Vista acumulada diária agrupada](./media/billing-cost-analysis/grouped-daily-accum-view.png)

Gráficos dinâmicos estão sob o modo de exibição superior Total. Use-os para ver as visualizações de diferentes categorias de agrupamento e filtragem. Quando você seleciona qualquer categoria de grupo, o conjunto completo de dados para a visualização total fica na parte inferior da exibição. Aqui está um exemplo de grupos de recursos.

![Dados completos para o modo de exibição atual](./media/billing-cost-analysis/full-data-set.png)

### <a name="download-cost-analysis-data"></a>Baixar dados de análise de custo

Quando você faz o **download** de informações da análise de custos, um arquivo CSV é gerado para todos os dados mostrados atualmente no portal do Azure. Se você tiver aplicado algum filtro ou agrupamento, eles serão incluídos no arquivo. Alguns dados subjacentes para o topo O gráfico total que não é exibido ativamente no portal também está incluído no arquivo CSV.

## <a name="next-steps"></a>Próximas etapas

Veja outra documentação do [Azure billing and cost management](billing-cost-management-budget-scenario.md).
