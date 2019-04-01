---
title: Escala do cluster do Azure Data Explorer para acomodar as mudanças de demanda
description: Este artigo descreve as etapas para escalar verticalmente e reduzir verticalmente um cluster do Gerenciador de dados do Azure com base na demanda de alteração.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 71f1d197b4a3fb437607f876ea524e582e62c0fd
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756337"
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
