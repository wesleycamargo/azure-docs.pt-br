---
title: Conjuntos de Dados do Fluxo de Dados do Mapeamento do Azure Data Factory
description: Azure Data Factory de mapeamento de fluxo de dados tem compatibilidade de conjunto de dados específico
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: efb82c57a5620ef3eace8b39f6f27f2286202f84
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521832"
---
# <a name="mapping-data-flow-datasets"></a>Conjuntos de Dados de Fluxo de Dados de Mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Conjuntos de Dados são um constructo de Data Factory que define a forma dos dados com os quais você está trabalhando no seu pipeline. No Fluxo de Dados, os dados de nível de linha e coluna requerem uma definição de conjunto de dados refinada. Os conjuntos de dados usados em pipelines de fluxo de controle não exigem a mesma profundidade da compreensão dos dados.

Conjuntos de dados no fluxo de dados são usados nas transformações do código-fonte e coletor. Elas são usadas para definir os esquemas de dados básicos. Se você não tiver o esquema em seus dados, poderá definir o Descompasso do Esquema para a Fonte e o Coletor. Com o esquema definido no conjunto de dados, você terá os tipos de dados relacionados, formatos de dados, localização do arquivo e informações de conexão do Serviço Vinculado associado. Os metadados dos conjuntos de dados aparecerá na sua transformação de origem como a origem "Projeção". O esquema do conjunto de dados representa o tipo de dados físico e a forma, enquanto a projeção na transformação de origem representa a representação de fluxo de dados dos dados com tipos e nomes definidos.

## <a name="dataset-types"></a>Tipos de conjunto de dados

No momento no fluxo de dados, você encontrará quatro tipos de conjunto de dados:

* BD SQL do Azure
* Data Warehouse SQL do Azure
* Parquet (do ADLS e Blob)
* Texto delimitado (do ADLS e Blob)

Conjuntos de fluxo de dados separado a *tipo de fonte* da *tipo de conexão de serviço vinculado*. Normalmente no Data Factory, você escolhe o tipo de conexão (Blob, ADLS etc.) e, em seguida, define o tipo de arquivo no conjunto de dados. Dentro do Fluxo de Dados, você selecionará os tipos de fonte, que podem ser associados a diferentes tipos de conexão de Serviço Vinculado.

![Opções de Transformação de Fonte](media/data-flow/dataset1.png "fontes")

## <a name="data-flow-compatible-datasets"></a>Conjuntos de dados compatíveis do Fluxo de Dados

Ao criar um novo Conjunto de Dados, há uma caixa de seleção rotulada como "Fluxo de Dados Compatível" no canto superior direito do painel. Clicar nesse botão, você filtrará apenas os conjuntos de dados que podem ser usados com Fluxos de Dados. 

## <a name="import-schemas"></a>Importar esquemas

Ao importar o esquema de conjuntos de dados de Fluxo de Dados, você verá um botão Importar Esquema. Clicar nesse botão apresentará duas opções: Importar da fonte ou importar de um arquivo local. Na maioria dos casos, você importará o esquema diretamente da fonte. No entanto, se você tiver um arquivo de esquema existente (arquivo Parquet ou CSV com cabeçalhos), poderá apontar para o arquivo local e o Data Factory definirá o esquema com base no arquivo de esquema.

## <a name="create-new-table"></a>Criar nova tabela

No fluxo de dados, você pode pedir o ADF para criar uma nova definição de tabela no banco de dados de destino, definindo um conjunto de dados na transformação coletor que tem um novo nome de tabela. No conjunto de dados SQL, clique em "Editar", abaixo do nome da tabela e insira um novo nome de tabela. Em seguida, na transformação de coletor, ative "Permitir que o descompasso do esquema". Seth a configuração "Importar esquema" como None.

![Esquema de fonte de transformação](media/data-flow/dataset2.png "esquema SQL")

## <a name="choose-your-type-of-data-first"></a>Escolha seu tipo de dados pela primeira vez

### <a name="delimited-text"></a>Texto delimitado

O conjunto de dados de texto delimitado, você definirá o delimitador para lidar com qualquer um dos delimitadores únicos ('\t 'para TSV,',' para o CSV, ' |'...) ou usar vários caracteres de delimitador. Defina a alternância de linha de cabeçalho e, em seguida, vá para a transformação de origem para detectar automaticamente tipos de dados. Se você estiver usando um conjunto de dados de texto delimitados para descarregar dados em um coletor, basta selecione uma pasta de destino. Nas configurações do coletor, você pode definir o nome dos arquivos de saída.

### <a name="parquet"></a>Parquet

Use o Parquet como o tipo preferencial de conjunto de dados de preparo em fluxos de dados do ADF. Parquet armazena metadados sofisticados esquema juntamente com os dados.

### <a name="database-types"></a>Tipos de banco de dados

Você pode selecionar o BD SQL do Azure ou SQL DW do Azure.

Para os outros tipos de conjunto de dados do ADF, use a atividade de cópia para preparar seus dados. Há um modelo do ADF na Galeria de modelos para ajudá-lo a criar este padrão.

![cópia de preparo](media/data-flow/templatedf.png "copiar preparo")

## <a name="choose-your-connection-type"></a>Escolha seu tipo de conexão

Se você estiver usando o Parquet ou texto delimitado por conjuntos de dados, você pode, em seguida, selecione o local para seus dados: ADLS ou Blob.

## <a name="next-steps"></a>Próximas etapas

Comece [criando um novo fluxo de dados](data-flow-create.md) e adicione uma transformação de origem. Em seguida, configure o conjunto de dados para sua fonte.

Use o [atividade de cópia](copy-activity-overview.md) para trazer dados de qualquer ADF fonte de dados e prepará-los em ADLS ou Blob para acesso por fluxo de dados.

