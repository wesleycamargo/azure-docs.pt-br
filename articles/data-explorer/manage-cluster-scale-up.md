---
title: Escala do cluster do Azure Data Explorer para acomodar as mudanças de demanda
description: Este artigo descreve as etapas de expansão e redução de um cluster do Azure Data Explorer conforme as mudanças de demanda.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 213a49d87d5e9f801bb17604a322c231a4e3dee2
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735309"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Gerenciar a escala vertical do cluster para acomodar as mudanças de demanda

Dimensionar um cluster apropriadamente é essencial para o desempenho do Azure Data Explorer. Contudo, não é possível prever a demanda em um cluster com 100% de precisão. Um tamanho de cluster estático pode levar a subutilização ou superutilização, e nenhuma dessas condições é a ideal. Uma abordagem melhor é *dimensionar* um cluster, adicionando e removendo capacidade e CPU conforme as mudanças na demanda. Este artigo mostra como gerenciar a escala vertical de um cluster.

Navegue para seu cluster e, em **Configurações**, selecione **Escalar verticalmente**.

Aqui, você deve receber a lista de SKUs que estão disponíveis. Você pode escolher na lista de cartões habilitados. Por exemplo, na figura abaixo, há apenas uma SKU que pode ser escolhida em D14_vs.

![Escalar verticalmente](media/manage-cluster-scale-up/scale-up.png)

A D13_v2 está desabilitada porque é a SKU atual do cluster. L8 e L16 estão desabilitadas porque não estão disponíveis na região onde está o cluster.

Para alterar uma SKU clique na SKU que você gostaria de usar e clique no botão **Selecionar**.

[!NOTE] O processo de expansão pode levar alguns minutos e, durante esse tempo, o cluster ficará suspenso. Observe que reduzir verticalmente pode prejudicar o desempenho do cluster.

Se você precisar de auxílio com problemas de escala, abra uma solicitação de suporte no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).