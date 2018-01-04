---
title: "Saídas e destinos de dados com suporte disponíveis com a preparação de dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece uma lista completa dos destinos e saídas disponíveis para a preparação de dados do Azure Machine Learning"
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
ms.date: 09/07/2017
ms.openlocfilehash: 50d2d481b91199630bbfbf3cfdd21a1bf3062ff0
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2017
---
# <a name="supported-data-exports-for-this-preview"></a>Exportações de dados com suporte para essa versão prévia 
É possível exportar para diversos formatos diferentes. Você pode usar esses formatos para manter os resultados intermediários de preparação de dados antes de integrar os resultados ao restante do fluxo de trabalho de Machine Learning.

## <a name="types"></a>Tipos 
### <a name="csv-file"></a>Arquivo CSV 
Grave um arquivo de valores separados por vírgula no armazenamento.

#### <a name="options"></a>Opções
- Terminações de linha
- Substituir valores nulos por
- Substituir erros por 
- Separador


### <a name="parquet"></a>Parquet 
Grave um conjunto de dados no armazenamento como Parquet.

O Parquet como um formato pode assumir vários formatos no armazenamento. Para conjuntos de dados menores, um único arquivo .parquet pode ser usado. Várias bibliotecas de Python dão suporte à leitura e gravação em um único arquivo .parquet. 

No momento, o Azure Machine Learning Workbench se baseia na biblioteca do PyArrow Python para gravar o Parquet durante o uso interativo local. Isso significa que único arquivo Parquet é atualmente o único formato de saída com suporte do Parquet durante o uso interativo local.

Durante execuções de escalabilidade horizontal (no Spark), o Azure Machine Learning Workbench dependerá dos recursos de leitura e de gravação do Parquet do Spark. O formato de saída padrão do Spark para Parquet (atualmente o único com suporte) é semelhante em termos de estrutura a um conjunto de dados Hive. Isso significa uma pasta que contém muitos arquivos .parquet que são uma partição menor de um conjunto de dados maior. 

#### <a name="caveats"></a>Limitações 
O Parquet como um formato é relativamente novo e tem algumas inconsistências de implementação entre as diferentes bibliotecas. Por exemplo, Spark impõe restrições em quais caracteres são válidos em nomes de colunas ao gravar no Parquet. O PyArrow não faz isso. Os seguintes caracteres não podem ser usados em um nome de coluna: 
- ,
- ;
- {}
- ()
- \\n
- \\t
- =

>[!NOTE]
>- Para garantir a compatibilidade com o Spark, sempre que dados forem gravados no Parquet, as ocorrências desses caracteres nomes de coluna são substituídos por ‘_’ (sublinhado).
>- Para garantir a consistência entre as execuções locais e com escala horizontal, todos os dados gravados no Parquet, por meio do aplicativo, Python ou Spark, têm os nomes de coluna corrigidos para garantir a compatibilidade com o Spark. Para garantir os nomes de coluna esperados ao gravar caracteres do Parquet no Sparks, remova o conjunto inválido das colunas antes da gravação.



## <a name="locations"></a>Locais 
### <a name="local"></a>Local 
Disco rígido local ou local de armazenamento de rede mapeado.

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure
O armazenamento de Blobs do Azure requer uma assinatura do Azure.

