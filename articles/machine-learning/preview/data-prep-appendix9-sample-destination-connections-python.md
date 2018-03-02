---
title: "Saídas/destinos de exemplo possíveis com a preparação de dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece um conjunto de exemplos de saídas/destinos de dados personalizados com a preparação de dados do Azure Machine Learning"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 203c8399153d2bc2d855fc2602b01ed074852687
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="sample-of-destination-connections-python"></a>Exemplo de conexões de destino (Python) 
Antes de ler este apêndice, leia [Visão geral da extensibilidade do Python](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Gravar no Excel 


A gravação no Excel requer uma biblioteca adicional. A adição de novas bibliotecas está documentada na visão geral de extensibilidade. `openpyxl`é a biblioteca que você precisa adicionar.

Antes de gravar no Excel, algumas outras alterações podem ser necessárias. Não há suporte para alguns tipos de dados usados na preparação de dados em alguns formatos de destino. Por exemplo, se existirem objetos “Error”, eles não serão serializados corretamente no Excel. Assim, antes de tentar gravar no Excel, você precisará de uma transformação "Substituir Valores de Erro", o que elimina os erros de todas as colunas.

Se todo o trabalho anterior for concluído, as linhas a seguir gravarão a tabela de dados em uma única planilha em um documento Excel. Adicione uma transformação Transformar Fluxo de Dados (Script). Então insira o código a seguir em uma seção de expressão.


### <a name="on-windows"></a>No Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>No macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
