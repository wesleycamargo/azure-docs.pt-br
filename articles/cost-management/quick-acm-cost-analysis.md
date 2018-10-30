---
title: Início rápido - Explorar os custos do Azure com análise de custos | Microsoft Docs
description: Este início rápido ajuda você a usar a análise de custo para explorar e analisar os custos organizacionais do Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/19/2018
ms.topic: quickstart
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 6b935322c9d892793f3695e0922d15f5886c7e25
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471281"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Início rápido: explore e analise custos com análise de custos

Antes de poder controlar e otimizar adequadamente seus custos do Azure, você precisa entender onde os custos se originaram em sua organização. Também é útil saber quanto dinheiro seus serviços custam e em apoio a quais ambientes e sistemas. A visibilidade em todo o espectro de custos é fundamental para entender com precisão os padrões de gastos da organização. Os padrões de gastos podem ser usados para impor mecanismos de controle de custos, como orçamentos.

Neste início rápido, você usa a análise de custo para explorar e analisar seus custos organizacionais. Você pode visualizar os custos agregados por organização para entender onde os custos ocorrem ao longo do tempo e identificar tendências de gastos. Você pode visualizar os custos acumulados ao longo do tempo para estimar as tendências de custo mensais, trimestrais ou mesmo anuais em comparação a um orçamento. Um orçamento ajuda a fornecer aderência às restrições financeiras. E um orçamento é usado para visualizar os custos diários ou mensais para isolar as irregularidades de gastos. E, então, você pode baixar dados do relatório atual para análise posterior ou para usar em um sistema externo.

Neste guia de início rápido, você aprende a:

- Examine os custos de análise de custo
- Personalizar as exibições de custo
- Baixar dados de análise de custo


## <a name="prerequisites"></a>Pré-requisitos

A análise de custo está disponível para todos os clientes do [EA (Contrato Enterprise)](https://azure.microsoft.com/pricing/enterprise-agreement/). Você precisa ter acesso de leitura a pelo menos um ou mais dos seguintes escopos para exibir os dados de custo.


|**Escopo**|**Definido em**|**Acesso necessário para analisar os custos do escopo**|**Configuração de pré-requisito de EA**|**Consolida os dados de cobrança a**|
|---                |---                  |---                   |---            |---           |
|Conta de cobrança<sup>1</sup>|[https://ea.azure.com ](https://ea.azure.com )|Administrador Corporativo|Nenhum|Todas as assinaturas do contrato empresarial|
|department|[https://ea.azure.com ](https://ea.azure.com )|Administrador de departamento|Encargos de exibição do administrador de departamento habilitados|Todas as assinaturas que pertencem a uma conta de registro que esteja vinculada ao departamento|
|Conta de registro<sup>2</sup2>|[https://ea.azure.com ](https://ea.azure.com )|Proprietário da conta|Encargos de exibição do proprietário da conta habilitados|Todas as assinaturas da conta de registro|
|Grupo de gerenciamento|[https://portal.azure.com ](https://portal.azure.com )|Leitor do Gerenciamento de Custos (ou Leitor)|Encargos de exibição do proprietário da conta habilitados|Todas as assinaturas abaixo do grupo de gerenciamento|
|Assinatura|[https://portal.azure.com ](https://portal.azure.com )|Leitor do Gerenciamento de Custos (ou Leitor)|Encargos de exibição do proprietário da conta habilitados|Todos os recursos/grupos de recursos na assinatura|
|Grupo de recursos|[https://portal.azure.com ](https://portal.azure.com )|Leitor do Gerenciamento de Custos (ou Leitor)|Encargos de exibição do proprietário da conta habilitados|Todos os recursos no grupo de recursos|

<sup>1</sup>A conta de cobrança é frequentemente chamada de Registro ou Contrato Enterprise.

<sup>2</sup>A conta de registro é frequentemente chamada de proprietário da conta.

Para obter mais informações sobre como configurar os **encargos da exibição de DA** e os **encargos da exibição de AO**, confira [Habilitando o acesso a custos](../billing/billing-enterprise-mgmt-grp-troubleshoot-cost-view.md#enabling-access-to-costs).





## <a name="sign-in-to-azure"></a>Entrar no Azure

- Entre no Portal do Azure em http://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Examine os custos de análise de custo

Para examinar os custos com análise de custo, no portal do Azure, navegue até **Gerenciamento de Custos + Cobrança** &gt; **Gerenciamento de Custos** &gt; **Alterar escopo**, escolha um escopo e, em seguida, clique em **Selecionar**.

O escopo que você seleciona é usado em todo o Gerenciamento de Custos para fornecer a consolidação de dados e para controlar o acesso a informações de custo. Ao usar escopos, você não faz multisseleção. Você seleciona um escopo mais amplo com o acúmulo de outros e, em seguida, filtra o que deseja. Isso é importante para entender porque algumas pessoas não devem ter acesso a um escopo pai acumulado por escopos filho.

Clique em **Abrir análise de custo**.

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

Ao agrupar os custos segundo um atributo específico, os dez principais colaboradores de custo são mostrados, do mais alto para o mais baixo. Se houver mais de dez grupos, os nove principais colaboradores de custo serão mostrados, bem como um grupo **Outros**, que abrange todos os grupos restantes juntos.

Máquinas virtuais *clássicas* (Gerenciamento de Serviços do Azure ou ASM), redes e recursos de armazenamento não compartilham dados detalhados de cobrança. Eles são mesclados como **Serviços clássicos** ao agrupar os custos.


## <a name="download-cost-analysis-data"></a>Baixar dados de análise de custo

Você pode **Fazer o download** de informações da análise de custo para gerar um arquivo CSV para todos os dados mostrados atualmente no portal do Azure. Quaisquer filtros ou agrupamentos aplicados são incluídos no arquivo. Dados subjacentes para o topo O gráfico total que não é exibido ativamente está incluído no arquivo CSV.

## <a name="next-steps"></a>Próximas etapas

Avance para o primeiro tutorial para aprender como criar e gerenciar orçamentos.

> [!div class="nextstepaction"]
> [Crie e gerencie orçamentos](tutorial-acm-create-budgets.md)
