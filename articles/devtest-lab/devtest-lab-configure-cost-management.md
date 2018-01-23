---
title: "Exibir a tendência de custo estimado mensal do laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba mais sobre o gráfico de tendência de custo estimado mensal do Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: v-craic
ms.openlocfilehash: 660180fac4f4743bc543b1babf7dbe921bf8c26b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Exibir a tendência de custo estimado mensal do laboratório no Azure DevTest Labs
O recurso de gerenciamento de custos dos Laboratórios de Desenvolvimento/Teste ajuda a rastrear o custo de laboratório. Este artigo ilustra como usar o gráfico **Tendência de custo estimado mensal** para exibir o custo estimado até a data do mês do calendário atual e o custo projetado do final do mês para o mês do calendário atual. Este artigo mostra como gerenciar melhor os custos de laboratório definindo metas e limites de gastos que, quando alcançadas, fazem com que os DevTest Labs relatem os resultados para você.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Visualizando o gráfico Tendência de custo estimado mensal
Para exibir o gráfico Tendência de custo estimado mensal, siga estas etapas: 

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **Todos os Serviços** e selecione **DevTest Labs** na lista. (O laboratório já pode ser exibido no painel, em **Todos os Recursos**).
1. Na lista de laboratórios, selecione o laboratório desejado.  
1. Na área **Visão geral** do laboratório, selecione **Configuração e políticas**.   
1. À esquerda em **CONTROLE DE CUSTOS**, selecione **Tendência de custos**.

   A captura de tela a seguir mostra um exemplo de um gráfico de custo. 
   
    ![Gráfico de custo](./media/devtest-lab-configure-cost-management/graph.png)

O valor **Custo estimado** é o custo estimado até a data do mês do calendário atual. O **Custo projetado** é o custo estimado para todo o mês do calendário atual, calculado usando o custo do laboratório nos cinco dias anteriores.

Observe que os valores do custo são arredondados para o próximo número inteiro. Por exemplo:  

* 5.01 arredonda até 6 
* 5.50 arredonda até 6
* 5.99 arredonda até 6

Como é indicado acima do gráfico, os custos vistos por padrão no gráfico são custos *estimados* usando as tarifas da oferta [Pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Você também pode definir duas metas de gastos que são exibidas nos gráficos [gerenciando as metas de custos de seu laboratório.](#managing-cost-targets-for-your-lab)

Além disso, o seguinte *não* está incluído no cálculo de custo:

* As assinaturas do CSP e do Dreamspark não têm suporte atualmente, pois o Azure DevTest Labs usa as [APIs de cobrança do Azure](../billing/billing-usage-rate-card-overview.md) para calcular o custo do laboratório e essas APIs não dão suporte a assinaturas do Dreamspark ou do CSP.
* Suas tarifas da oferta. No momento, não é possível usar as tarifas de oferta (mostradas em sua assinatura) que você negociou com a Microsoft ou parceiros Microsoft. Somente as tarifas pagas conforme o uso são usadas.
* Seus impostos
* Seus descontos
* Sua moeda de cobrança. No momento, o custo do laboratório é exibido apenas na moeda USD.

## <a name="managing-cost-targets-for-your-lab"></a>Gerenciando as metas de custos para seu laboratório
O DevTest Labs lhe permite gerenciar melhor os custos em seu laboratório definindo uma meta de gasto que você pode exibir no gráfico Tendência de custo estimada por mês. O DevTest Labs também pode enviar uma notificação quando a meta ou o limite de gasto especificado é atingido. 

1. No painel **Visão geral** do seu laboratório, selecione **Configuração e políticas**.
1. À esquerda em **CONTROLE DE CUSTOS**, selecione **Tendência de custos**.

    ![Botão Gerenciar meta](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. No painel **Tendência de custos**, selecione **Gerenciar meta**.

    ![Botão Gerenciar meta](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. No painel Gerenciar meta, especifique a meta e os limites de gastos desejados. Você também pode definir se cada limite selecionado é relatado no gráfico de tendência de custos ou por meio de uma notificação de webhook.

    ![Painel Gerenciar meta](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selecione um período durante o qual você deseja que as metas de custo sejam controladas.
      - **Mensal**: as metas de custo são controladas por mês.
      - **Fixo**: as metas de custo são controladas para o intervalo de datas que você especificar nos campos de data inicial e final. Normalmente, isso pode corresponder ao período durante o qual projeto está agendado para ser executado.
   - Especifique uma **Meta de custo**. Por exemplo, pode ser quanto você planeja gastar neste laboratório no período que definiu.
   - Selecione para habilitar ou desabilitar qualquer limite que você deseja que seja relatado – em incrementos de 25% –, até 125% de sua **Meta de custo** especificada.
      - **Notificar**: quando esse limite for atingido, você será notificado por uma URL de webhook que você especificar.
      - **Plotar no gráfico**: quando esse limite for atingido, os resultados serão plotados no gráfico de tendência de custo que você pode ver, conforme descrito em [Visualizando o gráfico Tendência de custo estimado mensal](#viewing-the-monthly-estimated-cost-trend-chart).
   - Se optar por **Notificar** quando o limite for atingido, você precisará especificar uma URL de webhook. Na área de integração de custos, selecione **Clique aqui para adicionar uma integração**.

      Insira uma URL de webhook no painel Configurar notificação e, em seguida, selecione **OK**.

       ![Painel Configurar notificação](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Se especificar **Notificar**, você precisará definir uma URL de webhook.
      - Da mesma forma, se definir uma URL de webhook, você precisará definir **Notificação** como **Ativado** no painel de limite de custos.
      - Você deve criar um webhook antes de inseri-lo aqui.  

      Para saber mais sobre os webhooks, veja [Criar um webhook ou uma função da API do Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas
* [Mais duas coisas para manter o custo sob controle em laboratórios de DevTest](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Por que os limites de custo?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Próximas etapas
Eis aqui algumas coisas para experimentar a seguir:

* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) - saiba como definir as várias políticas usadas para controlar como seu laboratório e suas VMs são usados. 
* [Criar imagem personalizada](devtest-lab-create-template.md) – durante a criação de uma VM, você especifica uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um arquivo VHD.
* [Configurar imagens do Marketplace](devtest-lab-configure-marketplace-images.md) – os DevTest Labs dão suporte à criação de VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais imagens (caso haja alguma) do Azure Marketplace podem ser usadas durante a criação de VMs em um laboratório.
* [Criar uma VM em um laboratório](devtest-lab-add-vm.md) – ilustra como criar uma VM de uma imagem base (personalizada ou do Marketplace) e como trabalhar com artefatos na VM.

