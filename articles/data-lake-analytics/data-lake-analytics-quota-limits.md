---
title: Limites de Cota do Azure Data Lake Analytics | Microsoft Docs
description: "Saiba como ajustar e aumentar os limites de cota na conta do ADLA (Azure Data Lake Analytics). Conhecer esses limites pode ajudar você a entender o comportamento do seu trabalho U-SQL. Todos esses limites são flexíveis e você sempre pode aumentar os limites máximos entrando em contato conosco."
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
ms.date: 11/16/2016
ms.author: omidm
translationtype: Human Translation
ms.sourcegitcommit: ce76fb1feaa38ff20ccc873114541a015126a1ad
ms.openlocfilehash: cffafbc20294c235060b03a75f6d1bee712115d1


---
# <a name="azure-data-lake-analytics-quota-limits"></a>Limites de Cota do Azure Data Lake Analytics
Saiba como ajustar e aumentar os limites de cota na conta do ADLA (Azure Data Lake Analytics). Conhecer esses limites pode ajudar você a entender o comportamento do seu trabalho U-SQL. Todos esses limites são flexíveis e você sempre pode aumentar os limites máximos entrando em contato conosco.

**Pré-requisitos**

Antes de começar este tutorial, você deve ter os seguintes itens:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta da Análise Data Lake**. Confira [Introdução ao Azure Data Lake Analytics usando o Portal do Azure](https://azure.microsoft.com/en-us/documentation/articles/data-lake-analytics-get-started-portal/#create-adl-analytics-account).
* **Conhecimento básico do processo de trabalho da Análise Data Lake**. Confira [Introdução ao Azure Data Lake Analytics usando o Portal do Azure](https://azure.microsoft.com/en-us/documentation/articles/data-lake-analytics-get-started-portal/).

<!-- ################################ -->
<!-- ################################ -->
## <a name="quota-limits"></a>Limites de cota:
A lista a seguir mostra os limites de cota atual do sistema:

**Limites de Assinaturas do Azure:** o limite a seguir é aplicável às assinaturas do Azure:
* **Número máximo de contas do ADLA por assinatura:** 5. Esse é o número máximo de contas do ADLA que você pode criar por assinatura. Você recebe o erro "Você atingiu o número máximo de contas do Data Lake Analytics permitidas (5) na região sob o nome da assinatura" ao tentar criar a sexta conta do ADLA. Você pode corrigir esse problema facilmente ao excluir as contas do ADLA usadas em sua assinatura ou ao entrar em contato conosco para abrir um tíquete de suporte.

**Limites da conta do ADLA:**
* **Número máximo de AUs (Unidades de Análise) por conta:** 250. Esse é o número máximo de AUs que podem ser executadas simultaneamente em sua conta. A quantidade total de AUs em execução em todos os trabalhos não pode ultrapassar esse número. Exceder esse valor faz com que os trabalhos mais recentes sejam enfileirados automaticamente. Por exemplo:
    * Você pode ter somente um trabalho em execução com 250 AUs. Ao enviar o segundo trabalho, esse permanecerá na fila de trabalhos até que o primeiro seja concluído.
    * Você pode já ter cinco trabalhos em execução e cada um deles enviado com 50 AUs. No entanto, ao enviar o sexto trabalho com, por exemplo, 20 AUs, ele aguardará na fila de trabalhos e começará a ser executado quando 20 AUs estiverem disponíveis.
* **Número máximo de trabalhos U-SQL simultâneos por conta:** 20. Esse é o número máximo de trabalhos que podem ser executados simultaneamente em sua conta. Exceder esse valor faz com que os trabalhos mais recentes sejam enfileirados automaticamente.

**Para ajustar os limites de cota do ADLA por conta:**
1. Entre no [portal do Azure](https://portal.azure.com).
2. Escolha a conta ADLA que você já criou
3. Clique em **Propriedades**
4. Ajuste **Paralelismo** e **Trabalhos simultâneos** para atender às suas necessidades.

    ![Folha do portal da Análise Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

**Para aumentar os limites máximos de cota:**
1. Abra uma solicitação de suporte no Portal do Azure.

    ![Folha do portal da Análise Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Folha do portal da Análise Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Selecione o tipo de problema como **Cota**
3. Selecione sua **Assinatura** (Verifique se ela não é uma assinatura de "teste").
4. Selecione o tipo de cota como **Data Lake Analytics**

    ![Folha do portal da Análise Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5.  Na folha do problema, explique seu limite de aumento solicitado e os **Detalhes** sobre o porquê você precisa de capacidade extra.

    ![Folha do portal da Análise Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6.  Verifique suas informações de contato e crie a solicitação de suporte.

Examinaremos sua solicitação e tentaremos atender às suas necessidades de negócios o mais rápido possível.

## <a name="see-also"></a>Confira também
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Introdução à Análise do Data Lake usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerenciar a Análise Azure Data Lake usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorar e solucionar problemas em trabalhos do Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)



<!--HONumber=Nov16_HO3-->


