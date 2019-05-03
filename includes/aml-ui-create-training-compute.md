---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/06/2019
ms.openlocfilehash: cf35651f7dd839e8792029851b9bfe278036624c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028509"
---
Um teste é executado em um destino de computação, um recurso de computação que é anexado ao seu espaço de trabalho.  Depois de criar um destino de computação, você pode reutilizá-lo para execuções futuras.

1. Selecione **executar** na parte inferior para executar o experimento.

     ![Executar o experimento](./media/aml-ui-create-training-compute/run-experiment.png)

1. Quando o **destinos de computação de instalação** caixa de diálogo aparece se o seu espaço de trabalho já tem um recurso de computação, você pode selecioná-lo agora.  Caso contrário, selecione **criar novo**.

    > [!NOTE]
    > A interface visual só pode executar experimentos em destinos de computação do Machine Learning. Outros destinos de computação não serão mostrados.

1. Forneça um nome para o recurso de computação.

1. Selecione **Executar**.

    ![Destino de computação do programa de instalação](./media/aml-ui-create-training-compute/set-compute.png)

    O recurso de computação agora será criado. Exiba o status no canto superior direito do experimento. 

    > [!NOTE]
    > Ele leva aproximadamente 5 minutos para criar um recurso de computação. Depois que o recurso for criado, você pode reutilizá-lo e ignorar esse tempo de espera para execuções futuras.
    >
    > O recurso de computação será o dimensionamento automático para 0 nós quando ele estiver ocioso para economizar o custo.  Quando você usá-lo novamente após um atraso, você pode enfrentar aproximadamente 5 minutos de tempo de espera enquanto ele é dimensionado back.
