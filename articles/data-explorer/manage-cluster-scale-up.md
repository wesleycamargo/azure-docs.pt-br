---
title: Escalar verticalmente um cluster do Data Explorer do Azure para acomodar a demanda de alteração
description: Este artigo descreve as etapas para escalar verticalmente e reduzir verticalmente um cluster do Gerenciador de dados do Azure com base na demanda de alteração.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 565953c8e0c6f9765d5eeb16a9fa18c3e79b8370
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044922"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Gerenciar o aumento do cluster para acomodar as mudanças de demanda

Dimensionar um cluster apropriadamente é essencial para o desempenho do Azure Data Explorer. Mas por demanda em um cluster não pode ser prevista com exatidão absoluta. Um tamanho de cluster estático pode levar a subutilização ou superutilização, nenhum deles é ideal.

Uma abordagem melhor é *escala* um cluster, adicionando e removendo a capacidade e os recursos de CPU com a demanda de alteração. Há dois fluxos de trabalho para o dimensionamento: ampliação e expansão. Este artigo mostra como gerenciar o aumento de escala do cluster.

1. Vá para seu cluster. Sob **as configurações**, selecione **escalar verticalmente**.

    Você vê uma lista de SKUs disponíveis. Por exemplo, na figura a seguir, apenas quatro SKUs estão disponíveis.

    ![Escalar verticalmente](media/manage-cluster-scale-up/scale-up.png)

    SKUs estão desabilitados porque eles são o SKU atual, ou eles não estão disponíveis na região em que o cluster está localizado.

1. Para alterar a SKU, selecione a SKU desejado e escolha o **selecionar** botão.

> [!NOTE]
> O processo de expansão pode levar alguns minutos e, durante esse tempo o cluster será suspenso. Observe que reduzir verticalmente pode prejudicar o desempenho do cluster.

Agora, você fez uma operação de ampliação ou redução de escala para o cluster do Gerenciador de dados do Azure. Você também pode [gerenciar o cluster de escala horizontal](manage-cluster-scale-out.md) que dinamicamente escalar horizontalmente a contagem de instâncias com base nas métricas que você especificar.

Se precisar de ajuda com problemas de dimensionamento de cluster [abrir uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) no portal do Azure.
