---
title: Crie uma chave de partição sintética no Azure Cosmos DB para distribuir seus dados e a carga de trabalho uniformemente.
description: Aprenda a usar chaves de partição sintéticas em seus contêineres do Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: mjbrown
ms.openlocfilehash: 8becfe375f2e887348729cf1d76820fc41156d2a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997087"
---
# <a name="create-a-synthetic-partition-key"></a>Crie uma chave de partição sintética

É uma prática recomendada ter uma chave de partição com muitos valores distintos, como centenas ou milhares. O objetivo é distribuir seus dados e carga de trabalho uniformemente entre os itens associados a esses valores de chave de partição. Se tal propriedade não existir em seus dados, uma chave de partição sintética poderá ser construída. As seções a seguir descrevem várias técnicas básicas para gerar uma chave de partição sintética para seu contêiner.

## <a name="concatenate-multiple-properties-of-an-item"></a>Concatenar várias propriedades de um item

Você pode formar uma chave de partição concatenando vários valores de propriedade em uma única propriedade artificial `partitionKey`. Essas chaves são chamadas de chaves sintéticas. Por exemplo, considere o seguinte documento de exemplo:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Para o documento anterior, uma opção é definir /deviceId ou /date como a chave de partição. Use esta opção se você quiser particionar seu contêiner com base na identificação do dispositivo ou na data. Outra opção é concatenar esses dois valores em uma propriedade `partitionKey` sintética que é usada como chave de partição.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Em cenários em tempo real, você pode ter milhares de documentos em um banco de dados. Em vez de adicionar manualmente a chave sintética, defina a lógica do lado do cliente para concatenar valores e inserir a chave sintética nos documentos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Usar uma chave de partição com um sufixo aleatório

Outra estratégia possível para distribuir a carga de trabalho mais uniformemente é anexar um número aleatório no final do valor da chave de partição. Quando distribui itens dessa maneira, você pode executar operações de gravação paralelas nas partições.

Um exemplo é quando uma chave de partição representa uma data. Você pode escolher um número aleatório entre 1 e 400 e concatená-lo como um sufixo à data. Esse método resulta em valores de chave de partição como 2018-08-09.1,2018-08-09.2 e assim por diante, até 2018-08-09.400. Como você está randomizando a chave de partição, as operações de gravação no contêiner em cada dia são distribuídas uniformemente em várias partições. Este método resulta em melhor paralelismo e maior rendimento global.

## <a name="use-a-partition-key-with-precalculated-suffixes"></a>Usar uma chave de partição com sufixos pré-calculados 

A estratégia de randomização pode melhorar consideravelmente a taxa de transferência de gravação, mas é difícil ler um item específico. Você não sabe qual valor de sufixo é usado quando você grava o item. Para facilitar a leitura de itens individuais, use a estratégia dos sufixos pré-calculados. Em vez de usar um número aleatório para distribuir os itens entre as partições, use um número que calcule com base em algo que você deseja consultar.

Considere o exemplo anterior, em que um contêiner usa uma data na chave de partição. Agora, suponha que cada item tem um atributo de VIN (número de identificação de veículo) acessível. Suponha, além disso, que você executa frequentemente consultas para localizar itens por VIN, além da data. Antes de seu aplicativo gravar o item no contêiner, ele pode calcular um sufixo hash com base no VIN e anexá-lo à data da chave de partição. O cálculo poderá gerar um número entre 1 e 400 que é distribuído uniformemente. Esse resultado é semelhante aos resultados produzidos pelo método da estratégia aleatória. O valor da chave de partição será, então, a data concatenada com o resultado calculado.

Com essa estratégia, as gravações são distribuídas uniformemente pelos valores da chave de partição e pelas partições. Você pode facilmente ler um item e uma data em particular, porque pode calcular o valor da chave de partição para um número de identificação de veículo específico. O benefício desse método é que você pode evitar a criação de uma única chave de partição ativa. Uma chave de partição ativa é a chave de partição que recebe toda a carga de trabalho. 

## <a name="next-steps"></a>Próximas etapas

Você pode aprender mais sobre o conceito de particionamento nos seguintes artigos:

* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba mais sobre como [provisionar taxa de transferência para contêineres e bancos de dados do Azure Cosmos DB](set-throughput.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
