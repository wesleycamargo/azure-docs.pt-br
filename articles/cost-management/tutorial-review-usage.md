---
title: 'Tutorial: Examinar o uso e os custos do Gerenciamento de Custos do Azure | Microsoft Docs'
description: Neste tutorial, você analisa o uso e os custos para acompanhar tendências, detectar ineficiências e criar alertas.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: 79857f05505a59de94d7a6926afe38cceeac34f3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>Tutorial: Examinar o uso e os custos

O Gerenciamento de Custos do Azure mostra o uso e os custos para que seja possível acompanhar tendências, detectar ineficiências e criar alertas. Todos os dados de uso e de custo são exibidos em relatórios e painéis da Cloudyn. Os exemplos neste tutorial demonstram como examinar o uso e os custos usando painéis e relatórios. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Acompanhar as tendências de uso e custo
> * Detectar ineficiências de uso
> * Criar alertas para gastos incomuns ou excesso de gastos

Se você não tem uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>pré-requisitos

- Você deve ter uma conta do Azure.
- Você deve ter um registro de avaliação ou uma assinatura paga do Gerenciamento de Custos do Azure.

## <a name="open-the-cloudyn-portal"></a>Abra o portal da Cloudyn

O uso e os custos são examinados no portal da Cloudyn. Abra o portal da Cloudyn no portal do Azure ou navegue até https://azure.cloudyn.com e faça login.

## <a name="track-usage-and-cost-trends"></a>Acompanhar as tendências de uso e custo

Acompanhe a quantia de dinheiro real gasta com uso e custos usando relatórios Ao Longo do Tempo para identificar tendências. Para começar a verificar tendências, use o relatório de Custo Real ao Longo do Tempo. No menu na parte superior do portal, clique em **Custos** > **Análise de Custo** > **Custo Real ao Longo do Tempo**. Quando você abre o relatório pela primeira vez, não há nenhum grupo ou filtro aplicado.

Aqui está um exemplo de relatório:

![relatório de exemplo](./media/tutorial-review-usage/actual-cost01.png)

O relatório mostra todos os gastos nos últimos 30 dias. Para exibir apenas os gastos para serviços do Azure, aplique o grupo Serviço e, em seguida, filtre todos os serviços do Azure. A imagem a seguir mostra os serviços filtrados.

![serviços filtrados](./media/tutorial-review-usage/actual-cost02.png)

No exemplo anterior, menos dinheiro foi gasto a partir de 31/08/2017 do que antes. Essa tendência de custo continua para vários serviços por cerca de nove dias. Em seguida, as despesas adicionais continuam como antes. No entanto, muitas colunas podem confundir uma tendência óbvia. Você pode alterar o modo de exibição do relatório para um gráfico de linha ou de área para ver os dados exibidos em outros modos de exibição. A imagem a seguir mostra a tendência mais claramente.

![tendência no relatório](./media/tutorial-review-usage/actual-cost03.png)

No exemplo, você vê claramente que o custo de armazenamento do Azure caiu a partir de 31-08-2017 enquanto as despesas com outros serviços do Azure permaneceram no mesmo nível. Portanto, o que causou essa redução de gastos? Neste exemplo, alguns funcionários estavam de férias do trabalho em não usaram o serviço de armazenamento.

Para assistir a um vídeo de tutorial sobre o acompanhamento de tendências de custo e uso, consulte [Analisar seus dados de cobrança da nuvem vs. tempo com Gerenciamento de Custos do Azure](https://youtu.be/7LsVPHglM0g).

## <a name="detect-usage-inefficiencies"></a>Detectar ineficiências de uso

Os relatórios do otimizador melhoram a eficiência, otimizam o uso e identificam maneiras de economizar o dinheiro gasto com recursos de nuvem. Eles são muito úteis para recomendações de dimensionamento econômico cujo objetivo é ajudar a reduzir as VMs ociosas ou caras.

Um problema comum que afeta as organizações quando elas começam a mover recursos para a nuvem é a estratégia de virtualização. Geralmente, elas usam uma abordagem semelhante à que já era usada para a criação de máquinas virtuais para o ambiente de virtualização local. E assumem que os custos serão reduzidos ao mover suas VMs locais para a nuvem, no estado em que se encontram. No entanto, essa abordagem provavelmente não reduz os custos.

O problema é que a infraestrutura existente já foi paga. Os usuários podiam criar e manter grandes VMs em execução caso quisessem, mesmo sendo VMs ociosas, sem muita consequência. Mover VMs grandes ou ociosas para a nuvem provavelmente *aumenta* os custos. A alocação de custo para recursos é muito importante ao firmar contratos com provedores de serviços de nuvem. Você deverá pagar por aquilo que confirmar mesmo se não usar o recurso completamente.

O relatório Recomendações de Dimensionamento Econômico identifica a possível economia anual comparando a capacidade do tipo de instância VM com os dados históricos de uso de CPU e de memória.  

No menu na parte superior do portal, clique em **Otimizador** > **Otimização de Dimensionamento** > **Recomendações de Dimensionamento Econômico**. Filtre o provedor para o Azure para ver apenas as VMs do Azure. Aqui está uma imagem de exemplo.

![VMs do Azure](./media/tutorial-review-usage/sizing01.png)

Neste exemplo, é possível economizar $3.114 ao seguir as recomendações para alterar os tipos de instância VM. Clique no símbolo de adição (+) em **Detalhes** da primeira recomendação. Aqui estão os detalhes sobre a primeira recomendação.

![detalhes de recomendação](./media/tutorial-review-usage/sizing02.png)

Exiba as IDs da instância VM clicando no símbolo de mais ao lado de **Lista de Candidatos**.

![Lista de candidatos](./media/tutorial-review-usage/sizing03.png)

Para assistir a um vídeo de tutorial sobre como detectar ineficiências de uso, consulte [Otimizando o tamanho da VM no Gerenciamento de Custos do Azure](https://youtu.be/1xaZBNmV704).

## <a name="create-alerts-for-unusual-spending"></a>Criar alertas para gastos incomuns

Você pode alertar os stakeholders automaticamente sobre riscos de anomalia de gastos e de excesso de gastos. Você pode criar alertas com rapidez e facilidade usando relatórios que dão suporte para alertas com base no orçamento e nos limites de custo.

Crie um alerta para qualquer gasto usando qualquer relatório de custo. Neste exemplo, use o relatório Custo Real ao Longo do Tempo para ser notificado quando os gastos da VM do Azure se aproximarem do seu orçamento total. Todas as etapas abaixo são necessárias para criar o alerta. No menu na parte superior do portal, clique em **Custos** > **Análise de Custo** > **Custo Real ao Longo do Tempo**. Defina **Grupos** para **Serviço** e defina **Filtrar no serviço** para **Azure/VM**. Na parte superior direita do relatório, clique em **Ações** e, em seguida, selecione **Agendar relatório**.

Use a guia **Agendamento** para enviar o relatório para o seu email usando a frequência desejada. Selecione **Enviar por email**. As marcas, o agrupamento e a filtragem que você usar serão incluídos no relatório por email. Clique na guia **Limite** e selecione **Custo Real versus Limite**. Se você tiver um orçamento total de $500.000 e desejar ser notificado quando os custos se aproximarem da metade, crie um **Alerta vermelho** em $250.000 e um **Alerta amarelo** em $240.000. Não inclua vírgulas nos valores inseridos. Em seguida, escolha o número de alertas consecutivos. Quando você receber o número total de alertas especificado, não será enviado mais nenhum alerta. Salve o relatório agendado.

![relatório de exemplo](./media/tutorial-review-usage/schedule-alert01.png)

Você também pode escolher a métrica de limite Percentual de Custo versus Limite para criar alertas. Com essa métrica, você pode usar porcentagens do orçamento, em vez de valores de moeda.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Acompanhar as tendências de uso e custo
> * Detectar ineficiências de uso
> * Criar alertas para gastos incomuns ou excesso de gastos


Avance para o próximo tutorial para aprender a prever despesas usando dados históricos.

> [!div class="nextstepaction"]
> [Previsão de despesas futuras](tutorial-forecast-spending.md)
