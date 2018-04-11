---
title: Monitorar o uso e os custos estimados no Azure Monitor| Microsoft Docs
description: Visão geral do processo de utilização da página de custos estimados e uso do Azure Monitor
author: dalekoetke
manager: carmonmills
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: f87705ebbdd14b1bbf7cade481a7dbe7dd3d5131
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>Monitorar uso e custos estimados

No hub do Monitor do Portal do Azure, a página **Uso e custos estimados** foi criada para ajudar a reconhecer o uso dos principais recursos de monitoramento, como [alertas, métricas, notificações](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Para clientes nos planos de preços disponíveis antes de abril de 2018, isso também inclui o uso do Log Analytics adquirido por meio das ofertas do Insights e do Analytics.

Nessa página, os usuários podem visualizar o uso desses recursos nos últimos 31 dias, agregados por assinatura, com busca detalhada para ver a tendência de uso durante esse período. Há muitos dados necessários para efetuar pull em conjunto e fazer essa estimativa, portanto, seja paciente enquanto a página é carregada.
Veja um exemplo que mostra o monitoramento de uso e uma estimativa dos custos resultantes:

![Captura de tela do portal do uso e custos estimados](./media/monitoring-usage-and-estimated-costs/001.png)

Clicar no link na coluna de uso Mensal abrirá um gráfico mostrando as tendências de uso nos últimos 31 dias:

![Incluído por captura de tela do nó 671,47 GB](./media/monitoring-usage-and-estimated-costs/002.png)

Este é outro resumo de custos e uso semelhante, nesse caso, para uma assinatura no novo modelo de preços com base no consumo de abril de 2018. Observe a falta de cobrança baseada em nó e que a retenção e ingestão de dados para o Log Analytics e Application Insights agora são relatadas em um novo medidor comum.

![Captura de tela do portal do uso e custos estimados](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Novo modelo de preços

Em abril de 2018, um novo modelo de preços de monitoramento foi liberado.  Este apresenta preços com base no consumo e é amigável para nuvem. Você paga apenas pelo que usa, sem compromissos baseados em nó. Os detalhes do novo modelo de preços estão disponíveis para [alertas, métricas, notificações](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

Para clientes que participam do Log Analytics ou do Application Insights após 2 de abril de 2018, o novo modelo de preços é a única opção. Para clientes que já utilizaram esses serviços, a mudança para o novo modelo de preços é opcional.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Avaliar o impacto do novo modelo de preços

O novo modelo de preços terá impactos diferentes para cada cliente com base nos padrões de uso de monitoramento. Para clientes que já usavam o Log Analytics ou o Application Insights antes de 2 de abril de 2018, a página **Uso e custos estimados** no Azure Monitor fornece uma maneira de estimar qualquer alteração nos custos, caso o cliente passe para o novo modelo de preços e disponibiliza a maneira de mover uma assinatura para o novo modelo. Para a maioria dos clientes, o novo modelo de preços será vantajoso, mas especialmente para clientes com padrões de alto uso de dados ou em regiões de custo mais elevados, é possível que este não seja o caso.

Para ver uma estimativa dos custos para as assinaturas que você selecionou na página **Uso e custos estimados** clique na faixa azul perto da parte superior da página. É recomendável fazer essa assinatura por vez, pois esse é o nível no qual o novo modelo de preços pode ser adotado.

![Captura de tela do modelo de preços](./media/monitoring-usage-and-estimated-costs/004.png)

A seguir, é apresentada uma versão semelhante dessa página com uma faixa verde:

![Captura de tela do modelo de preços](./media/monitoring-usage-and-estimated-costs/005.png)

A seguir, será mostrado um conjunto diferente de medidores – os medidores que correspondem ao novo modelo de preços. Por exemplo, medidores de ingestão de dados como

1. Insight e Analytics\Excedente por Nó
2. Insight e Analytics\Incluído por Nó
3. Application Insights\Dados Excedentes Básicos
4. Application Insights\Dados Incluídos

são combinados em um novo medidor comum de ingestão de dados chamado **Serviços Compartilhados\Ingestão de Dados**, pois o novo modelo de preços não tem alocações de dados incluídas com base em nó.

Outra alteração que você verá é que os dados ingeridos no Log Analytics ou Application Insights em regiões com custos mais elevados serão mostrados com os novos medidores regionais para refletir adequadamente isso, por exemplo, **“Ingestão de Dados (Centro-oeste dos EUA)**.

> [!NOTE]
> Se você tiver uma assinatura do OMS (Microsoft Operations Management Suite), receberá as alocações de ingestão de dados do Log Analytics e do Application Insights para cada nó adquirido. Como isso é aplicado no nível da conta (não na assinatura), essa estimativa não pode mostrar o efeito dessas alocações. Neste caso, consulte seu representante de conta para uma discussão mais aprofundada sobre o novo modelo de preços.

## <a name="changes-when-moving-to-the-new-pricing-model"></a>Alterações ao mudar para o novo modelo de preços

Mover uma assinatura para o novo modelo de preços irá alterar a camada de preços de cada Log Analytics para uma nova camada Por GB e moverá todos (chamado “pergb2018” no Azure Resource Manager). Essa mudança também alterará todos os recursos do Application Insights no plano Enterprise para o plano Básico. Os efeitos destes são mostrados na estimativa de custo descrita acima. 

## <a name="moving-to-the-new-pricing-model"></a>Mover para o novo modelo de preços

Se você decidiu adotar o novo modelo de preços para uma assinatura, clique na opção **Seleção do modelo de preços** na parte superior da página **Uso e custos estimados**:

![Captura de tela do novo modelo de preços para monitorar Uso e custos estimados](./media/monitoring-usage-and-estimated-costs/006.png)

Isso abrirá a página **Seleção do modelo de preços** listando cada uma das assinaturas que você visualizou na página anterior:

![Captura de tela da seleção do modelo de preços](./media/monitoring-usage-and-estimated-costs/007.png)

Para mover uma assinatura para o novo modelo de preços, basta marcar a caixa e clicar em **Salvar**.  Você pode retornar para o modelo de preços mais antigo da mesma maneira. Lembre-se de que as permissões de colaborador ou proprietário da assinatura precisam alterar o modelo de preços.