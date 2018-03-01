---
title: Escalar a capacidade do Power BI Embedded | Microsoft Docs
description: Este artigo explica como escalar uma capacidade do Power BI Embedded no Microsoft Azure.
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/19/2018
ms.author: asaxton
ms.openlocfilehash: 9b7d0bc31946d6022e9bfae463f4a22eb12c2a58
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="scale-your-power-bi-embedded-capacity"></a>Escalar a capacidade do Power BI Embedded

Este artigo explica como escalar uma capacidade do Power BI Embedded no Microsoft Azure. O dimensionamento permite aumentar ou diminuir o tamanho de sua capacidade.

Isso pressupõe que você tenha criado uma capacidade do Power BI Embedded. Se não tiver criado, consulte [Criar uma capacidade do Power BI Embedded no Portal do Azure](create-capacity.md) para começar.

> [!NOTE]
> Uma operação de dimensionamento pode levar cerca de um minuto. Durante esse tempo, a capacidade não estará disponível. O conteúdo incorporado pode falhar ao carregar.

## <a name="scale-a-capacity"></a>Escalar uma capacidade

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

2. Selecione **Todos os serviços** > **Power BI Embedded** para ver suas capacidades.

    ![Todos os serviços no Portal do Azure](media/scale-capacity/azure-portal-more-services.png)

3. Selecione a capacidade que deseja escalar.

    ![Lista de capacidade do Power BI Embedded no Portal do Azure](media/scale-capacity/azure-portal-capacity-list.png)

4. Selecione **Tipo de preço** em **Escalar** na capacidade.

    ![Opção Tipo de preço em Escalar](media/scale-capacity/azure-portal-scale-pricing-tier.png)

    O tipo de preço atual está contornado em azul.

    ![Tipo de preço atual contornado em azul](media/scale-capacity/azure-portal-current-tier.png)

5. Para escalar verticalmente ou reduzir verticalmente, selecione o novo tipo para o qual mover. Selecionar uma nova camada coloca um contorno pontilhado azul ao redor da seleção. Selecione **Selecionar** para escalar o novo tipo.

    ![Selecionar o novo tipo](media/scale-capacity/azure-portal-select-new-tier.png)

    A escala da capacidade pode demorar um minuto ou dois para ser concluída.

6. Confirme o tipo exibindo a guia de visão geral. O tipo de preço atual é listado.

    ![Confirmar o tipo atual](media/scale-capacity/azure-portal-confirm-tier.png)

## <a name="next-steps"></a>Próximas etapas

Para pausar ou iniciar sua capacidade, consulte [Pausar e iniciar a capacidade do Power BI Embedded no Portal do Azure](pause-start.md).

Para começar a inserir conteúdo do Power BI em seu aplicativo, consulte [Como inserir seus painéis, relatórios e blocos do Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Mais perguntas? [Experimente perguntar para a Comunidade do Power BI](http://community.powerbi.com/)
