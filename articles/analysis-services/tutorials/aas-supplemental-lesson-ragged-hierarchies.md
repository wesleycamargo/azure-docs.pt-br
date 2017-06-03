---
title: "Lição suplementar de tutorial do Azure Analysis Services: hierarquias desbalanceadas | Microsoft Docs"
description: Descreve como corrigir hierarquias desbalanceadas no tutorial do Azure Analysis Services.
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
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 36acf2b20a4c3acab8050eb9c5489c8ee53e4d4e
ms.contentlocale: pt-br
ms.lasthandoff: 05/05/2017

---
# <a name="supplemental-lesson---ragged-hierarchies"></a>Lição suplementar – hierarquias desbalanceadas

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição suplementar, você resolver um problema comum ao dinamizar hierarquias que contêm valores em branco (membros) em diferentes níveis. Por exemplo, uma organização em que um gerente de alto nível tem tanto gerentes departamentais quanto não gerentes como subordinados diretos. Ou então, hierarquias geográficas compostas por país-região-cidade, em que algumas cidades não têm um estado ou província pai, por exemplo, Washington D.C. e Cidade do Vaticano. Quando uma hierarquia tem membros em branco, ela geralmente desce a níveis diferentes ou desbalanceados.

![aas-lesson-detail-ragged-hierarchies-table](../tutorials/media/aas-lesson-detail-ragged-hierarchies-table.png)

Modelos tabulares no nível de compatibilidade 1400 têm uma propriedade **Ocultar Membros** adicional para hierarquias. A configuração **Padrão** pressupõe que não existem membros em branco em nenhum nível. A configuração **Ocultar membros em branco** exclui membros em branco da hierarquia quando adicionada a uma tabela dinâmica ou relatório.  
  
Tempo estimado para conclusão desta lição: **20 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico de lição suplementar faz parte de um tutorial de modelagem Tabular. Antes de executar as tarefas desta lição suplementar, você deve ter concluído todas as lições anteriores ou ter um projeto de modelo de amostra de Vendas pela Internet da Adventure Works concluído. 

Se você criou o projeto de vendas pela Internet da AW como parte do tutorial, o modelo ainda não contém nenhum dado ou hierarquias desbalanceadas. Para concluir esta lição suplementar, você precisa primeiro criar o problema adicionando algumas tabelas adicionais, criar relações, colunas calculadas, uma medida e uma nova hierarquia de Organização. Essa parte leva cerca de 15 minutos. Em seguida, você pode resolvê-la em apenas alguns minutos.  

## <a name="add-tables-and-objects"></a>Adicionar tabelas e objetos
  
### <a name="to-add-new-tables-to-your-model"></a>Para adicionar novas tabelas ao seu modelo
  
1.  No Gerenciador de Modelos tabulares, expanda **Fontes de Dados**, clique com o botão direito do mouse em sua conexão > **Importar Novas Tabelas**.
  
2.  No navegador, selecione **DimEmployee** e **FactResellerSales** e, em seguida, clique em **OK**.

3.  No Editor de Consultas, clique em **Importar**

4.  Crie as seguintes [relações](../tutorials/aas-lesson-4-create-relationships.md):

    | Tabela 1           | Coluna       | Direção do Filtro   | Tabela 2     | Coluna      | Ativo |
    |-------------------|--------------|--------------------|-------------|-------------|--------|
    | FactResellerSales | OrderDateKey | Padrão            | DimDate     | Data        | Sim    |
    | FactResellerSales | DueDate      | Padrão            | DimDate     | Data        | Não     |
    | FactResellerSales | ShipDateKey  | Padrão            | DimDate     | Data        | Não     |
    | FactResellerSales | ProductKey   | Padrão            | DimProduct  | ProductKey  | Sim    |
    | FactResellerSales | EmployeeKey  | Para Ambas as Tabelas | DimEmployee | EmployeeKey | Sim    |

5. Na tabela **DimEmployee**, crie as seguintes [colunas calculadas](../tutorials/aas-lesson-5-create-calculated-columns.md): 

    **Caminho** 
    ```
    =PATH([EmployeeKey],[ParentEmployeeKey])
    ```

    **FullName** 
    ```
    =[FirstName] & " " & [MiddleName] & " " & [LastName]
    ```

    **Level1** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,1)) 
    ```

    **Level2** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,2)) 
    ```

    **Level3** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,3)) 
    ```

    **Level4** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,4)) 
    ```

    **Level5** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,5)) 
    ```

6.  Na tabela **DimEmployee**, crie uma [hierarquia](../tutorials/aas-lesson-9-create-hierarchies.md) chamada **Organização**. Adicione a seguintes colunas, em ordem: **Level1**, **Level2**, **Level3**, **Level4**, **Level5**.

7.  Na tabela **FactResellerSales**, crie a seguinte [medida](../tutorials/aas-lesson-6-create-measures.md):

    ```
    ResellerTotalSales:=SUM([SalesAmount])
    ```

8.  Use [Analisar no Excel](../tutorials/aas-lesson-12-analyze-in-excel.md) para abrir o Excel e criar uma tabela dinâmica automaticamente.

9.  Em **Campos de Tabela Dinâmica**, adicione a hierarquia **Organização** da tabela **DimEmployee** a **Linhas** e a medida **ResellerTotalSales** da tabela **FactResellerSales** a **valores**.

    ![aas-lesson-detail-ragged-hierarchies-pivottable](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable.png)

    Como você pode ver na tabela dinâmica, a hierarquia exibe linhas que são irregulares. Há muitas linhas em que os membros em branco são mostrados.

## <a name="to-fix-the-ragged-hierarchy-by-setting-the-hide-members-property"></a>Para corrigir a hierarquia desbalanceada definindo a propriedade Ocultar membros

1.  Em **Gerenciador de Modelos tabulares**, expanda **Tabelas** > **DimEmployee** > **Hierarquias** > **Organização**.

2.  Em **Propriedades** > **Ocultar Membros**, selecione **Ocultar membros em branco**. 

    ![aas-lesson-detail-ragged-hierarchies-hidemembers](../tutorials/media/aas-lesson-detail-ragged-hierarchies-hidemembers.png)

3.  De volta ao Excel, atualize a tabela dinâmica. 

    ![aas-lesson-detail-ragged-hierarchies-pivottable-refresh](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable-refresh.png)

    Agora isso tem uma aparência muito melhor!

## <a name="see-also"></a>Consulte também   
[Lição 9: criar hierarquias](../tutorials/aas-lesson-9-create-hierarchies.md)  
[Lição Suplementar – Segurança Dinâmica](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Lição suplementar – linhas de detalhes](../tutorials/aas-supplemental-lesson-detail-rows.md)  
