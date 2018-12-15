---
title: Saída do Azure Stream Analytics para Cosmos DB
description: Este artigo descreve como usar o Azure Stream Analytics para salvar a saída no Azure Cosmos DB para saída JSON, para arquivamento de dados e consultas de baixa latência em dados JSON não estruturados.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: c5017817c0f823a149dd0f9bced48ecca9f3c488
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106559"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Saída do Azure Stream Analytics para Azure Cosmos DB  
O Stream Analytics pode direcionar o [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) para uma saída em JSON, possibilitando o arquivamento de dados e consultas de baixa latência em dados JSON não estruturados. Este documento aborda algumas práticas recomendadas para implementar essa configuração.

Para aqueles que não estão familiarizados com o Cosmos DB, consulte [Roteiro de aprendizagem do Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para obter uma introdução. 

> [!Note]
> Neste momento, o Azure Stream Analytics oferece suporte apenas à conexão ao Microsoft Azure CosmosDB usando a **API do SQL**.
> Ainda não há suporte para outras APIs do Azure Cosmos DB. Se você apontar o Azure Stream Analytics para as contas do Azure Cosmos DB criado com as outras APIs, talvez os dados não sejam armazenados corretamente. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Conceitos básicos do Cosmos DB como um destino de saída
A saída do Azure Cosmos DB no Stream Analytics permite a gravação dos resultados do processamento de fluxo como uma saída em JSON nas coleções do Cosmos DB. O Stream Analytics não cria coleções em seu banco de dados, em vez disso, ele exige a criação delas antecipadamente. Isso serve para que os custos de cobrança das coleções de Cosmos DB sejam controlados por você e para que você possa ajustar o desempenho, a consistência e a capacidade de suas coleções diretamente com as [APIs do Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Você deve adicionar 0.0.0.0 à lista de IPs permitidos no firewall do Microsoft Azure Cosmos DB.

Algumas das opções de coleção do Cosmos DB são detalhadas abaixo.

## <a name="tune-consistency-availability-and-latency"></a>Ajustar a consistência, a disponibilidade e a latência
Para atender aos requisitos do aplicativo, o Microsoft Azure Cosmos DB permite o ajuste do banco de dados e das coleções, além de compensações entre consistência, disponibilidade e latência. Dependendo dos níveis de consistência de leitura exigidos pelo seu cenário em relação à latência de leitura e de gravação, você poderá escolher um nível de consistência em sua conta de banco de dados. Também por padrão, o Microsoft Azure Cosmos DB habilita a indexação síncrona em cada operação CRUD da coleção. Essa é outra opção útil para controlar o desempenho de leitura/gravação no Microsoft Azure Cosmos DB. Para obter mais informações, consulte o artigo [alterar o banco de dados e níveis de consistência de consulta](../cosmos-db/consistency-levels.md).

## <a name="upserts-from-stream-analytics"></a>Inserções e atualizações a partir do Stream Analytics
A integração do Stream Analytics ao Microsoft Azure Cosmos DB permite inserir ou atualizar registros na coleção do Microsoft Azure Cosmos DB de acordo com determinada coluna de ID do Documento. Isso também é chamado de *Upsert*.

O Stream Analytics utiliza uma abordagem upsert otimista, na qual as atualizações são feitas somente quando a inserção falha com um conflito de ID do Documento. Essa atualização é executada como um PATCH. Assim, é possível realizar atualizações parciais no documento, ou seja, a adição de novas propriedades ou a substituição de uma propriedade existente é executada de forma incremental. Porém, as alterações nos valores de propriedades de matriz em seu documento JSON resultam na substituição de toda a matriz, ou seja, a matriz não é mesclada.

Se o documento JSON recebido tiver um campo de ID existente, esse campo será usado automaticamente como coluna de ID do documento do Cosmos DB e quaisquer gravações subsequentes serão tratadas como tal, levando a uma destas situações:
- IDs exclusivas levam a inserção
- IDs duplicadas e 'ID do documento' definida como 'ID' levam a upsert
- IDs duplicadas e 'ID do documento' não definida leva a erros após o primeiro documento

Se você quiser salvar <i>todos</i> os documentos, incluindo aqueles com uma ID duplicada, renomeie o campo de ID em sua consulta (com a palavra-chave AS) e permita que o Cosmos DB crie o campo de ID ou substitua a ID por outro valor de coluna (usando a palavra-chave AS ou usando a configuração de ‘ID do documento’).

## <a name="data-partitioning-in-cosmos-db"></a>Particionamento de dados no Cosmos DB
O Microsoft Azure Cosmos DB [ilimitado](../cosmos-db/partition-data.md) é a abordagem recomendada para particionar seus dados, como o Microsoft Azure Cosmos DB automaticamente dimensiona partições com base em sua carga de trabalho. Ao gravar os contêineres ilimitados, o Stream Analytics usa tantos gravadores paralelos como etapa de consulta anterior ou entrada de esquema de particionamento.
> [!Note]
> No momento, o Azure Stream Analytics dá suporte apenas a coleções ilimitadas com chaves de partição no nível superior. Por exemplo, `/region` tem suporte. Não há suporte para chaves de partição aninhadas (por exemplo, `/region/name`). 

Para coleções do Microsot Azure Cosmos DB fixas, o Stream Analytics permite que nenhuma maneira de escalar vertical ou horizontalmente, depois que estão cheias. Eles têm um limite de 10 GB e taxa de transferência de 10.000 RU/s.  Para migrar dados de um contêiner fixo para um contêiner ilimitado (por exemplo, um com pelo menos 1.000 RU/s e uma chave de partição), você precisa usar a [ferramenta de migração de dados](../cosmos-db/import-data.md) ou a [biblioteca de feed de alterações](../cosmos-db/change-feed.md).

Gravar em vários contêineres fixos está sendo preterido e não é a abordagem recomendada para escalar horizontalmente seu trabalho do Stream Analytics. O artigo [Particionamento e escala no Microsoft Azure Cosmos DB](../cosmos-db/sql-api-partition-data.md) também apresenta detalhes sobre isso.

## <a name="cosmos-db-settings-for-json-output"></a>Configurações do Cosmos DB para saída em JSON
A criação do Cosmos DB como uma saída no Stream Analytics gera uma solicitação de informações, conforme mostrado abaixo. Esta seção fornece uma explicação da definição de propriedades.


![tela de saída do stream analytics do banco de dados de documentos](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

Campo           | DESCRIÇÃO 
-------------   | -------------
Alias de saída    | Um alias para se referir a essa saída em sua consulta ASA   
Nome da conta    | O nome ou o URI do ponto de extremidade da conta do Microsoft Azure Cosmos DB 
Chave de conta     | A chave de acesso compartilhado da conta do Microsoft Azure Cosmos DB
Banco de dados        | O nome do banco de dados do Microsoft Azure Cosmos DB
Nome da coleção | O nome da coleção para a coleção a ser usada. `MyCollection` é uma entrada válida do exemplo – uma coleção denominada `MyCollection` deve existir.  
ID do documento     | Opcional. O nome da coluna em eventos de saída usado como a chave exclusiva que inserir ou atualizar as operações que devem ser baseadas. Se deixado vazio, todos os eventos serão inseridos, com nenhuma opção de atualização.
