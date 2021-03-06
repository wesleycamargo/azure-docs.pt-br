---
title: 'Lição 8 do tutorial do Azure Analysis Services: criar perspectivas | Microsoft Docs'
description: Descreve como criar perspectivas no projeto de tutorial do Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c26da5d047a1e904510f96b44d81632b6e98689e
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190962"
---
# <a name="create-perspectives"></a>Criar perspectivas

Nesta lição, você criará uma perspectiva de Vendas pela Internet. Uma perspectiva define um subconjunto exibível de um modelo que fornece pontos de vista concentrados, específicos da empresa ou específicos do aplicativo. Quando um usuário se conecta a um modelo usando uma perspectiva, ele vê apenas os objetos de modelo (tabelas, colunas, medidas, hierarquias e KPIs) como campos definidos nessa perspectiva. Para saber mais, confira [Perspectivas](https://docs.microsoft.com/sql/analysis-services/tabular-models/perspectives-ssas-tabular).
  
A perspectiva de vendas pela Internet que você criou nesta lição excluirá o objeto de tabela DimCustomer. Quando você cria uma perspectiva que exclui determinados objetos de exibição, esse objeto ainda existe no modelo. No entanto, não é visível em uma lista de campos de cliente de relatório. Colunas calculadas e medidas incluídas ou não em uma perspectiva ainda podem calcular com base em de dados do objeto excluído.  
  
O objetivo desta lição é descrever como criar perspectivas e se familiarizar com ferramentas de criação de modelo tabular. Se você expandir este modelo para incluir tabelas adicionais posteriormente, você poderá criar perspectivas adicionais para definir diferentes pontos de vista do modelo, por exemplo, Vendas e Inventário.  
  
Tempo estimado para concluir esta lição: **Cinco minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelagem tabular, que deve ser concluído na devida ordem. Antes de executar as tarefas nesta lição, você deve ter concluído a lição anterior: [Lição 7: Criar Indicadores de Desempenho Principais](../tutorials/aas-lesson-7-create-key-performance-indicators.md).  
  
## <a name="create-perspectives"></a>Criar perspectivas  
  
#### <a name="to-create-an-internet-sales-perspective"></a>Para criar uma perspectiva de Vendas pela Internet  
  
1.  Clique no menu **Modelo** > **Perspectivas** > **Criar e Gerenciar**.  
  
2.  Na caixa de diálogo **Perspectivas**, clique em **Nova Perspectiva**.  
  
3.  Clique duas vezes no título de coluna **Nova Perspectiva** e renomeie-o para **Vendas pela Internet**.  
  
4.  Selecione todas as tabelas, *exceto* **DimCustomer**.  
  
    ![aas-lesson8-perspectives](../tutorials/media/aas-lesson8-perspectives.png)
  
    Em uma lição posterior, você usará o recurso Analisar no Excel para testar essa perspectiva. A Lista de Campos de Tabela Dinâmica do Excel incluirá cada tabela, com exceção da tabela DimCustomer.  

## <a name="whats-next"></a>O que vem a seguir?
[Lição 9: Criar hierarquias](../tutorials/aas-lesson-9-create-hierarchies.md).
  
  
  
  
