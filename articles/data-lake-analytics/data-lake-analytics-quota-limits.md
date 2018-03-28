---
title: Limites de Cota do Azure Data Lake Analytics
description: Saiba como ajustar e aumentar os limites de cota nas contas do ADLA (Azure Data Lake Analytics).
services: data-lake-analytics
keywords: Análise Azure Data Lake
documentationcenter: ''
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.topic: article
ms.workload: big-data
ms.date: 03/15/2018
ms.author: omidm
ms.openlocfilehash: 22774511720173915207da80a6ca33d5dbc83e19
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="azure-data-lake-analytics-quota-limits"></a>Limites de Cota do Azure Data Lake Analytics

Saiba como ajustar e aumentar os limites de cota nas contas do ADLA (Azure Data Lake Analytics). Conhecer esses limites pode ajudar você a entender o comportamento do seu trabalho U-SQL. Todos os limites de cota são flexíveis, portanto, é possível aumentar os limites máximos, contatando o suporte do Azure.

## <a name="azure-subscriptions-limits"></a>Limites das assinaturas do Azure

**Número máximo de contas do ADLA por assinatura:**5

Esse é o número máximo de contas ADLA que você pode criar por assinatura, por região. Você recebe o erro "Você atingiu o número máximo de contas do Data Lake Analytics permitidas (5) na região sob o nome da assinatura" ao tentar criar a sexta conta do ADLA. Nessa situação, é possível escolher outra região, se adequado, ou excluir qualquer conta ADLA não usada na mesma região ou contatar o suporte do Azure [abrindo um tíquete de suporte](#increase-maximum-quota-limits) para solicitar um aumento de cota.

## <a name="adla-account-limits"></a>Limites da conta do ADLA

**Número máximo de AUs (Unidades de Análise) por conta:** 250

Esse é o número máximo de AUs que podem ser executadas simultaneamente em sua conta. Se o número total de AUs em execução em todos os trabalhos exceder esse limite, novos trabalhos serão colocados na fila automaticamente. Por exemplo: 

* Caso você possua apenas um trabalho em execução com 250 AUs, ao adicionar um segundo trabalho ele permanecerá na fila até que o primeiro trabalho esteja concluído.
* Caso você já possua cinco trabalhos em execução e cada um estiver utilizando 50 AUs, ao adicionar um sexto trabalho que necessite de 20 AUs ele permanecerá na fila até que 20 AUs se tornem disponíveis.

**Número máximo de trabalhos U-SQL simultâneos por conta:**  20

Esse é o número máximo de trabalhos que podem ser executados simultaneamente em sua conta. Exceder esse valor faz com que os trabalhos mais recentes sejam enfileirados automaticamente.

## <a name="adjust-adla-quota-limits-per-account"></a>Ajustar os limites de cota do ADLA por conta

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Escolha uma conta ADLA que você já criou.
3. Clique em **Propriedades**.
4. Ajuste **Paralelismo** e **Trabalhos simultâneos** para atender às suas necessidades.

    ![Página do portal do Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="increase-maximum-quota-limits"></a>Aumente os limites máximos de cota

1. Abra uma solicitação de suporte no Portal do Azure.

    ![Página do portal do Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Página do portal do Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Selecione o tipo de problema **Cota**.
3. Selecione sua **Assinatura**(Verifique se ela não é uma assinatura de "teste").
4. Selecione o tipo de cota **Data Lake Analytics**.

    ![Página do portal do Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Na página do problema, explique seu limite de aumento solicitado com **Detalhes**, informando o motivo da necessidade dessa capacidade extra.

    ![Página do portal do Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Verifique suas informações de contato e crie a solicitação de suporte.

A Microsoft examina sua solicitação e tenta acomodar suas necessidades de negócios assim que possível.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Gerenciar a Análise Azure Data Lake usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorar e solucionar problemas em trabalhos do Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
