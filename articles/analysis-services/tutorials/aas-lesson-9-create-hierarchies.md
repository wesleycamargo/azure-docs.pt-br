---
title: "Lição 9 do tutorial do Azure Analysis Services: criar hierarquias | Microsoft Docs"
description: 
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: d628dc621335acf231342a6d9186079de16e85f4
ms.contentlocale: pt-br
ms.lasthandoff: 06/03/2017

---
# <a name="lesson-9-create-hierarchies"></a>Lição 9: criar hierarquias

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, você cria hierarquias. As hierarquias são grupos de colunas organizados em níveis. Por exemplo, uma hierarquia de Geografia pode ter subníveis para País, Estado, Região e Cidade. As hierarquias podem aparecer separadas de outras colunas em uma lista de campos de aplicativo cliente de relatório, facilitando sua navegação e inclusão em um relatório pelos usuários do cliente. Para saber mais, confira [Hierarquias](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular)
  
Para criar hierarquias, use o designer de modelo em *Exibição de Diagrama*. Não há suporte para criar e gerenciar hierarquias na Exibição de Dados.  
  
Tempo estimado para conclusão desta lição: **20 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelagem tabular, que deve ser concluído na devida ordem. Antes de executar as tarefas nesta lição, você deve ter concluído a lição anterior: [Lição 8: criar perspectivas](../tutorials/aas-lesson-8-create-perspectives.md).  
  
## <a name="create-hierarchies"></a>Criar hierarquias  
  
#### <a name="to-create-a-category-hierarchy-in-the-dimproduct-table"></a>Para criar uma hierarquia Categoria na tabela DimProduct  
  
1.  No designer de modelo (exibição de diagrama), clique com o botão direito do mouse na tabela **DimProduct** > **Criar Hierarquia**. Uma nova hierarquia aparece na parte inferior da janela da tabela. Renomeie a hierarquia como **Categoria**.  
  
2.  Clique e arraste a coluna **ProductCategoryName** para a nova hierarquia **Categoria**.  
  
3.  Na hierarquia **Categoria**, clique com o botão direito do mouse em **ProductCategoryName** > **Renomear** e, em seguida, digite **Categoria**.  
  
    > [!NOTE]  
    > Renomear uma coluna em uma hierarquia não renomeia essa coluna na tabela. Uma coluna em uma hierarquia é apenas uma representação da coluna na tabela.  
  
4.  Clique e arraste a coluna **ProductSubcategoryName** para a hierarquia **Categoria**. Renomeie-a como **Subcategoria**. 
  
5.  Clique com o botão direito do mouse na coluna **ModelName** > **Adicionar à hierarquia** e, em seguida, selecione **Categoria**. Renomeie-a como **Modelo**.

6.  Finalmente, adicione **EnglishProductName** à hierarquia Categoria. Renomeie-a como **Produto**.  

    ![aas-lesson9-category](../tutorials/media/aas-lesson9-category.png)
  
#### <a name="to-create-hierarchies-in-the-dimdate-table"></a>Para criar hierarquias na tabela DimDate  
  
1.  Na tabela **DimDate**, crie uma hierarquia chamada **Calendar**.  
  
3.  Adicione as seguintes colunas em ordem:

    *  CalendarYear
    *  CalendarSemester
    *  CalendarQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
    
4.  Na tabela **DimDate**, crie uma hierarquia **Fiscal**. Adicione as seguintes colunas em ordem:  
  
    *  FiscalYear
    *  FiscalSemester
    *  FiscalQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
  
5.  Finalmente, na tabela **DimDate**, crie uma hierarquia **ProductionCalendar**. Adicione as seguintes colunas em ordem:  
    *  CalendarYear
    *  WeekNumberOfYear
    *  DayNumberOfWeek
  
 ## <a name="whats-next"></a>O que vem a seguir?
[Lição 10: criar partições](../tutorials/aas-lesson-10-create-partitions.md). 
  
  

