---
title: Limites de Cota do Azure Data Lake Analytics | Microsoft Docs
description: Saiba como ajustar e aumentar os limites de cota na conta do ADLA (Azure Data Lake Analytics).
services: data-lake-analytics
keywords: "Análise Azure Data Lake"
documentationcenter: 
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: omidm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b68138f04eea1a21731118803d14698320e00be0
ms.contentlocale: pt-br
ms.lasthandoff: 07/01/2017


---
# <a name="azure-data-lake-analytics-quota-limits"></a>Limites de Cota do Azure Data Lake Analytics
Saiba como ajustar e aumentar os limites de cota na conta do ADLA (Azure Data Lake Analytics). Conhecer esses limites pode ajudar você a entender o comportamento do seu trabalho U-SQL. Todos esses limites são flexíveis e você sempre pode aumentar os limites máximos entrando em contato conosco.

## <a name="azure-subscriptions-limits"></a>Limites das Assinaturas do Azure

**Número máximo de contas do ADLA por assinatura:** 5. Esse é o número máximo de contas do ADLA que você pode criar por assinatura. Você recebe o erro "Você atingiu o número máximo de contas do Data Lake Analytics permitidas (5) na região sob o nome da assinatura" ao tentar criar a sexta conta do ADLA. Você pode corrigir esse problema facilmente ao excluir as contas do ADLA usadas em sua assinatura ou ao entrar em contato conosco para abrir um tíquete de suporte.

## <a name="adla-account-limits"></a>Limites da conta do ADLA

**Número máximo de AUs (Unidades de Análise) por conta:** 250. 

Esse é o número máximo de AUs que podem ser executadas simultaneamente em sua conta. A quantidade total de AUs em execução em todos os trabalhos não pode ultrapassar esse número. Exceder esse valor faz com que os trabalhos mais recentes sejam enfileirados automaticamente. Por exemplo:

  * Você pode ter somente um trabalho em execução com 250 AUs. Ao enviar o segundo trabalho, esse permanecerá na fila de trabalhos até que o primeiro seja concluído.
  * Você pode já ter cinco trabalhos em execução e cada um deles enviado com 50 AUs. No entanto, ao enviar o sexto trabalho com, por exemplo, 20 AUs, ele aguardará na fila de trabalhos e começará a ser executado quando 20 AUs estiverem disponíveis.


**Número máximo de trabalhos U-SQL simultâneos por conta:** 20. 

Esse é o número máximo de trabalhos que podem ser executados simultaneamente em sua conta. Exceder esse valor faz com que os trabalhos mais recentes sejam enfileirados automaticamente.

## <a name="adjust-the-adla-quota-limits-per-account"></a>Ajustar os limites de cota do ADLA por conta

1. Entre no [portal do Azure](https://portal.azure.com).
2. Escolha a conta ADLA que você já criou
3. Clique em **Propriedades**
4. Ajuste **Paralelismo** e **Trabalhos simultâneos** para atender às suas necessidades.

    ![Folha do portal da Análise Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="to-increase-the-max-quota-limits"></a>Para aumentar os limites máximos de cota

1. Abra uma solicitação de suporte no Portal do Azure.

    ![Folha do portal da Análise Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Folha do portal da Análise Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Selecione o tipo de problema como **Cota**
3. Selecione sua **Assinatura** (Verifique se ela não é uma assinatura de "teste").
4. Selecione o tipo de cota como **Data Lake Analytics**

    ![Folha do portal da Análise Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Na folha do problema, explique seu limite de aumento solicitado e os **Detalhes** sobre o porquê você precisa de capacidade extra.

    ![Folha do portal da Análise Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Verifique suas informações de contato e crie a solicitação de suporte.

A Microsoft examina sua solicitação e tenta acomodar suas necessidades de negócios assim que possível.

## <a name="next-steps"></a>Próximas etapas
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Gerenciar a Análise Azure Data Lake usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorar e solucionar problemas em trabalhos do Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

