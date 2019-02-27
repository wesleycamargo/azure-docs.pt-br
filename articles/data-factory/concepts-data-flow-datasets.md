---
title: Conjuntos de Dados do Fluxo de Dados do Mapeamento do Azure Data Factory
description: O Fluxo de Dados do Mapeamento do Azure Data Factory tem uma compatibilidade de conjunto de dados específico
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 36ca5e07adf79de77ac4ab4149ff8e96a1dece8d
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408741"
---
# <a name="mapping-data-flow-datasets"></a>Conjuntos de Dados de Fluxo de Dados de Mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Conjuntos de Dados são um constructo de Data Factory que define a forma dos dados com os quais você está trabalhando no seu pipeline. No Fluxo de Dados, os dados de nível de linha e coluna requerem uma definição de conjunto de dados refinada. Os conjuntos de dados usados em pipelines de fluxo de controle não exigem a mesma profundidade da compreensão dos dados.

Os conjuntos de dados na Fonte de Fluxo de Dados e Transformações do Coletor são usados para definir o esquema de dados básico. Se você não tiver o esquema em seus dados, poderá definir o Descompasso do Esquema para a Fonte e o Coletor. Com o esquema definido no conjunto de dados, você terá os tipos de dados relacionados, formatos de dados, localização do arquivo e informações de conexão do Serviço Vinculado associado.

## <a name="dataset-types"></a>Tipos de conjunto de dados

No momento no fluxo de dados, você encontrará quatro tipos de conjunto de dados:

* BD SQL do Azure
* Data Warehouse SQL do Azure
* Parquet
* Texto delimitado

Os conjuntos de dados de fluxo de dados separam o *tipo* de fonte do tipo de conexão de Serviço Vinculado. Normalmente no Data Factory, você escolhe o tipo de conexão (Blob, ADLS etc.) e, em seguida, define o tipo de arquivo no conjunto de dados. Dentro do Fluxo de Dados, você selecionará os tipos de fonte, que podem ser associados a diferentes tipos de conexão de Serviço Vinculado.

![Opções de Transformação de Fonte](media/data-flow/dataset1.png "fontes")

## <a name="data-flow-compatible-datasets"></a>Conjuntos de dados compatíveis do Fluxo de Dados

Ao criar um novo Conjunto de Dados, há uma caixa de seleção rotulada como "Fluxo de Dados Compatível" no canto superior direito do painel. Clicar nesse botão, você filtrará apenas os conjuntos de dados que podem ser usados com Fluxos de Dados. 

## <a name="import-schemas"></a>Importar esquemas

Ao importar o esquema de conjuntos de dados de Fluxo de Dados, você verá um botão Importar Esquema. Clicar nesse botão apresentará duas opções: Importar da fonte ou importar de um arquivo local. Na maioria dos casos, você importará o esquema diretamente da fonte. No entanto, se você tiver um arquivo de esquema existente (arquivo Parquet ou CSV com cabeçalhos), poderá apontar para o arquivo local e o Data Factory definirá o esquema com base no arquivo de esquema.

