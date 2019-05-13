---
title: Criar Fluxo de Dados do Mapeamento do Azure Data Factory
description: Como criar um Azure Data Factory mapeamento de fluxo de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 366ed60534544ebbf889e2f72fe703f9b821f871
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235659"
---
# <a name="create-azure-data-factory-data-flow"></a>Criar Fluxo de Dados do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Os Fluxos de Dados de Mapeamento no ADF fornecem uma maneira de transformar dados em grande escala sem a necessidade de nenhuma codificação. Você pode criar um trabalho de transformação de dados no designer de fluxo de dados, criando uma série de transformações. Comece com qualquer número de transformações de código-fonte, seguidas pelas etapas de transformação de dados. Em seguida, conclua seu fluxo de dados com o coletor para descarregar seus resultados em um destino.

Comece criando primeiro uma nova fábrica de dados V2 do portal do Azure. Depois de criar sua nova fábrica, clique no bloco “Criar e Monitorar” para iniciar a IU do Data Factory.

![Opções de Fluxo de Dados](media/data-flow/v2portal.png "criar fluxo de dados")

Quando estiver na IU do Data Factory, você pode usar Fluxos de Dados de exemplo. Os exemplos estão disponíveis na Galeria de Modelos do ADF. Em ADF, crie “Pipeline de modelo” e selecione a categoria de Fluxo de Dados da galeria de modelos.

![Opções de Fluxo de Dados](media/data-flow/template.png "criar fluxo de dados")

Será solicitado que você insira suas informações de conta do Armazenamento de Blobs do Azure.

![Opções de Fluxo de Dados](media/data-flow/template2.png "criar fluxo de dados 2")

[Os dados usados para esses exemplos podem ser encontrados aqui](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Baixe os dados de exemplo e armazene os arquivos em suas contas de armazenamento de Blobs do Azure para que você possa executar os exemplos.

## <a name="create-new-data-flow"></a>Criar novo fluxo de dados

Use o recurso de criar "sinal de adição" botão na UI do ADF para criar fluxos de dados.

![Opções de fluxo de dados](media/data-flow/newresource.png "Novo Recurso")

## <a name="next-steps"></a>Próximas etapas

Comece a criar sua transformação de dados com um [transformação de origem](data-flow-source.md).
