---
title: Filtros em exibições do Azure Log Analytics | Microsoft Docs
description: Um filtro em uma exibição do Log Analytics permite que os usuários filtrem os dados na exibição pelo valor de uma propriedade específica sem modificar a exibição em si.  Este artigo descreve como usar um filtro e adicioná-lo a uma exibição personalizada.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 0ad22562bd1f36bba7c0ab99fe504e82645033d3
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131403"
---
# <a name="filters-in-log-analytics-views"></a>Filtros em exibições do Log Analytics
Um **filtro** em uma [exibição do Log Analytics](log-analytics-view-designer.md) permite que os usuários filtrem os dados na exibição pelo valor de uma propriedade específica sem modificar a exibição em si.  Por exemplo, você pode permitir que os usuários da sua exibição filtrem somente os dados de um computador ou conjunto de computadores específico.  É possível criar vários filtros em uma única exibição para permitir que os usuários filtrem por várias propriedades.  Este artigo descreve como usar um filtro e adicioná-lo a uma exibição personalizada.

## <a name="using-a-filter"></a>Como usar um filtro
Clique no intervalo de dados na parte superior de uma visualização para abrir a lista suspensa, onde você pode alterar o intervalo de dados da exibição.

![Exemplo de filtro](media/log-analytics-view-designer/filters-example-time.png)

Clique em **+** para adicionar um filtro usando filtros personalizados que são definidos para a visualização. Selecione um valor para o filtro no menu suspenso ou digite um valor. Continue a adicionar filtros clicando em **+**. 


![Exemplo de filtro](media/log-analytics-view-designer/filters-example-custom.png)

Se você remover todos os valores de um filtro, ele não será aplicado.


## <a name="creating-a-filter"></a>Como criar um filtro

Crie um filtro por meio da guia **Filtros** ao [editar uma exibição](log-analytics-view-designer.md).  O filtro é global para a exibição e se aplica a todas as partes da exibição.  

![Configurações de filtro](media/log-analytics-view-designer/filters-settings.png)

A tabela a seguir descreve as configurações de um filtro.

| Configuração | DESCRIÇÃO |
|:---|:---|
| Nome do campo | Nome do campo usado para filtragem.  Isso deve corresponder ao campo summarize na **Consulta de valores**. |
| Consulta de valores | Consulta que deve ser executada para popular a lista suspensa do filtro para o usuário.  É preciso que ela use [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) ou [distinct](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator) para fornecer valores exclusivos para determinado campo, o qual deve corresponder ao **Nome do campo**.  Você pode usar [sort](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator) para classificar os valores que são exibidos para o usuário. |
| Marca | Nome do campo que é usado em consultas de apoio do filtro e que também é exibido para o usuário. |

### <a name="examples"></a>Exemplos

A tabela a seguir inclui alguns exemplos de filtros comuns.  

| Nome do campo | Consulta de valores | Marca |
|:--|:--|:--|
| Computador   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | Computadores |
| EventLevelName | Event &#124; distinct EventLevelName | Severity |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | Severity |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Modificar as consultas de exibição

Para que um filtro tenha efeito, é necessário modificar todas as consultas na exibição para filtrar os valores selecionados.  Se você não modificar nenhuma consulta na exibição, os valores que o usuário selecionar não terá efeito.

A sintaxe para usar um valor de filtro em uma consulta é: 

    where ${filter name}  

Por exemplo, se a exibição tem uma consulta retorna eventos e usa um filtro chamado de Computadores, você pode usar o seguinte.

    Event | where ${Computers} | summarize count() by EventLevelName

Se você adicionar outro filtro chamado Gravidade, você poderá usar a consulta a seguir para usar ambos os filtros.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre as [Partes da Visualização](log-analytics-view-designer-parts.md) que você pode adicionar ao modo de exibição personalizado.