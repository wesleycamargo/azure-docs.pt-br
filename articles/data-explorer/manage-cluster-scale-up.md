---
title: Escala do cluster do Azure Data Explorer para acomodar as mudanças de demanda
description: Este artigo descreve as etapas de expansão e redução de um cluster do Azure Data Explorer conforme as mudanças de demanda.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: bc3f97c798f5e040908e8103c00d3f015f8c824d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415327"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Gerenciar o aumento do cluster para acomodar as mudanças de demanda

Dimensionar um cluster apropriadamente é essencial para o desempenho do Azure Data Explorer. Contudo, não é possível prever a demanda em um cluster com 100% de precisão. Um tamanho de cluster estático pode levar a subutilização ou superutilização, e nenhuma dessas condições é a ideal. Uma abordagem melhor é *dimensionar* um cluster, adicionando e removendo capacidade e CPU conforme as mudanças na demanda. Há dois fluxos de trabalho para o dimensionamento, escalar verticalmente e escalar horizontalmente. Este artigo mostra como gerenciar o aumento de um cluster.

1. Navegue para seu cluster e, em **Configurações**, selecione **Escalar verticalmente**.

    Você receberá uma lista de SKUs disponíveis. Por exemplo, na figura a seguir há apenas uma SKU disponível: D14_V2.

    ![Escalar verticalmente](media/manage-cluster-scale-up/scale-up.png)

    A D13_V2 está desabilitada já que é a SKU atual do cluster. L8 e L16 estão desabilitadas porque não estão disponíveis na região onde está o cluster.

1. Para alterar a SKU, selecione a que deseja e pressione o botão **Selecionar**.

> [!NOTE]
> O processo de expansão pode levar alguns minutos e, durante esse tempo, o cluster ficará suspenso. Observe que reduzir verticalmente pode prejudicar o desempenho do cluster.

Agora, você executou uma operação de expansão ou redução para o cluster do Azure Data Explorer. Você também pode fazer um [redimensionamento do cluster](manage-cluster-scale-out.md), também conhecido como dimensionamento automático, para mudar o tamanho dinamicamente com base nas métricas que especificar.

Se você precisar de auxílio com problemas de escala, abra uma solicitação de suporte no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
