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
ms.date: 04/09/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: f25c39b602449be3ab9d1cd7e67d6fcfc78afb17
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>Monitorar uso e custos estimados

No hub do Monitor do Portal do Azure, a página **Uso e custos estimados** explica o uso dos principais recursos de monitoramento, como [alertas, métricas, notificações](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Para clientes nos planos de preços disponíveis antes de abril de 2018, isso também inclui o uso do Log Analytics adquirido por meio das ofertas do Insights e do Analytics.

Nesta página, os usuários podem exibir o uso de recursos para os últimos 31 dias, agregados por assinatura. Os drill-ins mostram tendências de uso durante o período de 31 dias. São necessários vários dados para se juntar e fazer essa estimativa, portanto, seja paciente enquanto a página é carregada.

Este exemplo mostra o uso de monitoramento e uma estimativa dos custos resultantes:

![Captura de tela do portal do uso e custos estimados](./media/monitoring-usage-and-estimated-costs/001.png)

Selecione o link na coluna de uso mensal para abrir um gráfico que mostra as tendências de uso nos últimos 31 dias:

![Incluído por captura de tela do gráfico de barras do nó](./media/monitoring-usage-and-estimated-costs/002.png)

Aqui está outro uso semelhante e resumo de custo. Este exemplo mostra uma assinatura no novo modelo de preços com base no consumo de abril de 2018. Observe a falta de cobrança com base no nó. A ingestão de dados e a retenção para o Log Analytics e Application Insights agora são relatadas em um novo medidor comum.

![Captura de tela do portal do uso e custos estimados - Preços de abril de 2018](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Novo modelo de preços

Em abril de 2018, um novo modelo de preços de monitoramento foi liberado. Apresenta preços com base no consumo e é amigável para a nuvem. Você paga apenas pelo que usa, sem compromissos baseados em nó. Os detalhes do novo modelo de preços estão disponíveis para [alertas, métricas, notificações](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

Para clientes que começaram a usar o Log Analytics ou o Application Insights após 2 de abril de 2018, o novo modelo de preços é a única opção. Para clientes que já utilizam esses serviços, a mudança para o novo modelo de preços é opcional.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Avaliar o impacto do novo modelo de preços

O novo modelo de preços terá impactos diferentes para cada cliente com base nos padrões de uso de monitoramento. Para os clientes que estavam usando o Log Analytics ou o Application Insights antes de 2 de abril de 2018, a página **Uso e custo estimados** no Azure Monitor calcula qualquer alteração nos custos se elas são movidas para o novo modelo de preços. Ele fornece a maneira de mover uma assinatura para o novo modelo. Para a maioria dos clientes, o novo modelo de preços será vantajoso. Para clientes com padrões de alto uso de dados ou em regiões de custo mais elevados, é possível que este não seja o caso.

Para ver uma estimativa dos custos para as assinaturas que você selecionou na página **Uso e custos estimados**, selecione a faixa azul perto da parte superior da página. É recomendável fazer uma assinatura por vez, pois esse é o nível no qual o novo modelo de preços pode ser adotado.

![Monitorar o uso e os custos estimados no novo modelo de preços - captura de tela](./media/monitoring-usage-and-estimated-costs/004.png)

A nova página mostra uma versão semelhante da página anterior com uma faixa verde:

![Monitorar o uso e os custos estimados no modelo de preços atual - captura de tela](./media/monitoring-usage-and-estimated-costs/005.png)

A página mostra também um conjunto diferente de medidores que correspondem ao novo modelo de preços. Essa lista é um exemplo:

- Insight e Analytics\Excedente por Nó
- Insight e Analytics\Incluído por Nó
- Application Insights\Dados Excedentes Básicos
- Application Insights\Dados Incluídos

O novo modelo de preços não tem as alocações de dados incluídas com base no nó. Portanto, esses medidores de ingestão de dados são combinados em um novo medidor de ingestão de dados comuns chamado **Serviços Compartilhados\Ingestão de Dados**. 

Há outra alteração nos dados ingeridos no Log Analytics ou no Application Insights em regiões com custos mais altos. Os dados para essas regiões de alto custo serão mostrados com os novos medidores regionais. Um exemplo é **Ingestão de Dados (Centro-oeste dos EUA)**.

> [!NOTE]
> Os custos estimados por assinatura não influenciam os direitos por nó de nível de conta da assinatura Operations Management Suite (OMS). Neste caso, consulte seu representante de conta para uma discussão mais aprofundada sobre o novo modelo de preços.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Novo modelo de preços e direitos de inscrição do Operations Management Suite

Clientes que adquiriram o Microsoft Operations Management Suite E1 e E2 estão qualificados para direitos de ingestão de dados por nó para [Log Analytics](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite) e [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-pricing#the-price-plans). Para receber esses direitos para espaços de trabalho Log Analytics ou recursos Application Insights em uma determinada assinatura, o modelo de preços de assinatura deve permanecer no modelo de preços antes de abril de 2018. Que é onde o modelo de preços do Log Analytics "Por nó (OMS)" e o modelo de preços do Application Insights "Empresarial" estão disponíveis. Dependendo do número de nós do conjunto que sua organização adquiriu, mover algumas assinaturas para o novo modelo de preços ainda pode ser vantajoso. Mas isso requer uma consideração cuidadosa.

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Alterações ao mudar para o novo modelo de preços

Mover uma assinatura para o novo modelo de preços irá alterar a camada de preços de cada Log Analytics para uma nova camada Por GB e moverá todos (chamado “pergb2018” no Azure Resource Manager). Essa mudança também alterará todos os recursos do Application Insights no plano Enterprise para o plano Básico. A estimativa de custo mostra os efeitos dessas alterações.

## <a name="moving-to-the-new-pricing-model"></a>Mover para o novo modelo de preços

Se você decidiu adotar o novo modelo de preços para uma assinatura, selecione a opção **Seleção do modelo de preços** na parte superior da página **Uso e custos estimados**:

![Monitorar o uso e os custos estimados no novo modelo de preços - captura de tela](./media/monitoring-usage-and-estimated-costs/006.png)

A página **Seleção de modelo de preços** será aberta. Mostra uma lista de cada uma das assinaturas que você exibiu na página anterior:

![Captura de tela da seleção do modelo de preços](./media/monitoring-usage-and-estimated-costs/007.png)

Para mover uma assinatura para o novo modelo de preços, basta marcar a caixa de seleção e clicar em **Salvar**. Você pode retornar para o modelo de preços mais antigo da mesma maneira. Lembre-se de que as permissões de colaborador ou proprietário da assinatura são necessárias para alterar o modelo de preços.
