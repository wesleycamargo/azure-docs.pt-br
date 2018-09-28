---
title: Início rápido - Explorar os custos do Azure com análise de custos | Microsoft Docs
description: Este início rápido ajuda você a usar a análise de custo para explorar e analisar os custos organizacionais do Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/14/2018
ms.topic: quickstart
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 0e94392b882d111d925c864feb9aaeb54bae4e2a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980399"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Início rápido: explore e analise custos com análise de custos

Antes de poder controlar e otimizar adequadamente seus custos do Azure, você precisa entender onde os custos se originaram em sua organização. Também é útil saber quanto dinheiro seus serviços custam e em apoio a quais ambientes e sistemas. A visibilidade em todo o espectro de custos é fundamental para entender com precisão os padrões de gastos da organização. Os padrões de gastos podem ser usados para impor mecanismos de controle de custos, como orçamentos.

Neste início rápido, você usa a análise de custo para explorar e analisar seus custos organizacionais. Você pode visualizar os custos agregados por organização para entender onde os custos ocorrem ao longo do tempo e identificar tendências de gastos. Você pode visualizar os custos acumulados ao longo do tempo para estimar as tendências de custo mensais, trimestrais ou mesmo anuais em comparação a um orçamento. Um orçamento ajuda o provedor a aderir às restrições financeiras. E um orçamento é usado para visualizar os custos diários ou mensais para isolar as irregularidades de gastos. E, então, você pode baixar dados do relatório atual para análise posterior ou para usar em um sistema externo.

Neste início rápido, você aprenderá como

- Examine os custos de análise de custo
- Personalizar as exibições de custo
- Baixar dados de análise de custo


## <a name="prerequisites"></a>Pré-requisitos

A análise de custos está disponível para todos os clientes do Enterprise Agreement (EA). Você deve ter acesso de leitura a pelo menos um dos seguintes escopos para visualizar os dados de custo.

- EA do Azure (registro) da conta de cobrança
- Assinatura de EA do Azure
- Grupo de recursos de assinatura de EA do Azure

## <a name="sign-in-to-azure"></a>Entrar no Azure

- Entre no Portal do Azure em http://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Examine os custos de análise de custo

Para analisar seus custos com a análise de custos, abra o Portal do Azure e navegue para **Gerenciamento de Custos + Faturamento** &gt; **Contas de faturamento** &gt; selecione sua conta de faturamento do EA &gt; em Gerenciamento de Custos , selecione **Análise de custo**.

A visão inicial da análise de custos inclui as seguintes áreas:

**Total** - Mostra o custo total do mês atual.

**Orçamento** - Mostra o limite de gastos planejados para o escopo selecionado, se disponível.

**Custo acumulado**: mostra o total de gastos diários acumulados, a partir do início do mês. Depois que você [criar um orçamento](tutorial-acm-create-budgets.md) para sua conta de faturamento ou assinatura, poderá ver rapidamente sua tendência de gastos em relação ao orçamento. Passe o mouse sobre uma data para visualizar o custo acumulado desse dia.

**Gráficos dinâmicos (donut)** - Fornecem dinâmicos pivots, dividindo o custo total por um conjunto comum de propriedades padrão. Eles mostram o menor custo acumulado para o mês atual. Você pode alterar gráficos dinâmicos a qualquer momento selecionando um pivô diferente. Os custos são categorizados por: serviço (categoria do medidor), localização (região) e escopo filho por padrão. Por exemplo, contas de inscrição em contas de faturamento, grupos de recursos em assinaturas e recursos em grupos de recursos.

![Visão inicial da análise de custo](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Personalizar as exibições de custo

A visualização padrão fornece respostas rápidas para perguntas comuns, como:

- Quanto eu gasta?
- Será permanecer dentro do meu orçamento?

No entanto, existem muitos casos em que você precisa de uma análise mais profunda. A personalização começa no topo da página, com a seleção da data.

Análise de custo mostra os dados para o mês atual por padrão. Use o seletor de data para alternar rapidamente para: o último mês, este mês, este trimestre do calendário, neste ano do calendário ou um intervalo de datas personalizado de sua escolha. A seleção do último mês é a maneira mais rápida de analisar sua última fatura do Azure e reconciliar as cobranças com facilidade. As opções atuais de trimestre e ano ajudam a acompanhar os custos em relação aos orçamentos de longo prazo. Você também pode selecionar um intervalo de datas diferentes. Por exemplo, você pode selecionar um único dia, os últimos sete dias ou qualquer coisa até um ano antes do mês atual.

![Seletor de data](./media/quick-acm-cost-analysis/date-selector.png)

A análise de custo mostra **custos acumulados** por padrão. Os custos acumulados incluem todos os custos para cada dia mais os dias anteriores, para uma visão em constante crescimento de seus custos diários acumulados. Essa visualização é otimizada para mostrar como você está tendendo contra um orçamento para o período selecionado.

Há também a visualização **diária** mostrando os custos para cada dia. O modo de exibição de diário não mostra uma tendência de crescimento. A exibição é projetada para mostrar irregularidades como picos de custos ou quedas diárias. Se você tiver selecionado um orçamento, a visualização diária também mostrará uma estimativa do orçamento diário. Quando seus custos diários estão consistentemente acima do orçamento diário estimado, você pode esperar que ultrapasse seu orçamento mensal. O orçamento diário estimado é simplesmente um meio de ajudar você a visualizar seu orçamento em um nível inferior. Quando você tem flutuações nos custos diários, a comparação do orçamento diário estimado com o orçamento mensal é menos precisa.

![Exibição diária](./media/quick-acm-cost-analysis/daily-view.png)

Você pode **Agrupar por** para selecionar uma categoria de grupo para alterar os dados exibidos no gráfico da área total superior. Agrupamento permite ver rapidamente como seus gastos estão categorizados por tipo de recurso. Aqui está uma exibição dos custos de serviço do Azure para uma exibição do último mês.

![Vista acumulada diária agrupada](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Gráficos dinâmicos sob a visualização Total total mostram exibições para diferentes categorias de agrupamento e filtragem. Quando você seleciona qualquer categoria de grupo, o conjunto completo de dados para a visualização total fica na parte inferior da exibição. Aqui está um exemplo de grupos de recursos.

![Dados completos para o modo de exibição atual](./media/quick-acm-cost-analysis/full-data-set.png)

A imagem anterior mostra nomes de grupos de recursos. As tags de visualização de recursos não estão disponíveis em nenhuma das visualizações de análise de custos, filtros ou agrupamentos.

## <a name="download-cost-analysis-data"></a>Baixar dados de análise de custo

Você pode **Fazer o download** de informações da análise de custo para gerar um arquivo CSV para todos os dados mostrados atualmente no portal do Azure. Quaisquer filtros ou agrupamentos aplicados são incluídos no arquivo. Dados subjacentes para o topo O gráfico total que não é exibido ativamente está incluído no arquivo CSV.

## <a name="next-steps"></a>Próximas etapas

Avance para o primeiro tutorial para aprender como criar e gerenciar orçamentos.

> [!div class="nextstepaction"]
> [Crie e gerencie orçamentos](tutorial-acm-create-budgets.md)
