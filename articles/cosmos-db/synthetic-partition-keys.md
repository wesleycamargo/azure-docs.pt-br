---
title: Chaves de partição sintética no Azure Cosmos DB
description: Aprenda a usar chaves de partição sintéticas nos seus contêineres de banco de dados do Azure Cosmos
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: f6323549c0ecf8f0196d327779f7f7cb67c6e03f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263341"
---
# <a name="create-a-synthetic-partition-key"></a>Crie uma chave de partição sintética

É uma prática recomendada ter uma chave de partição com muitos valores distintos, como centenas ou milhares. O objetivo é distribuir seus dados e carga de trabalho uniformemente entre os itens associados a esses valores de chave de partição. Se tal propriedade não existir em seus dados, uma chave de partição sintética poderá ser construída. As seções a seguir descrevem várias técnicas básicas para gerar uma chave de partição sintética para seu contêiner.

## <a name="concatenating-multiple-properties-of-an-item"></a>Concatenando várias propriedades de um item

Você pode formar uma chave de partição concatenando vários valores de propriedade em uma única propriedade artificial `partitionKey`. Essas chaves são chamadas de chaves sintéticas. Por exemplo, considere o seguinte documento de exemplo:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Para o documento anterior, uma opção é definir / deviceId ou / date como a chave de partição, se você quiser particionar seu contêiner com base no ID ou na data do dispositivo. Outra opção é concatenar esses dois valores em uma propriedade `partitionKey` sintética que é usada como chave de partição.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Em cenários em tempo real, você pode ter milhares de documentos em um banco de dados, portanto, em vez de adicionar a chave sintética manualmente, deve definir a lógica do cliente para concatenar valores e inserir a chave sintética nos documentos.

## <a name="using-a-partition-key-with-random-suffix"></a>Usando uma chave de partição com sufixo aleatório

Outra estratégia possível para distribuir a carga de trabalho mais uniformemente é anexar um número aleatório no final do valor da chave de partição. A distribuição de itens dessa maneira permite que você execute operações de gravação paralelas nas partições.

Por exemplo, se uma chave de partição representa uma data, você pode escolher um número aleatório entre 1 e 400 e concatená-lo como um sufixo para a data. Esse método resulta em valores de chave de partição como 2018-08-09.1,2018-08-09.2 e assim por diante, até 2018-08-09.400. Como você está randomizando a chave de partição, as operações de gravação no contêiner em cada dia são distribuídas uniformemente em várias partições. Este método resulta em melhor paralelismo e maior rendimento global.

## <a name="using-a-partition-key-with-pre-calculated-suffixes"></a>Usando uma chave de partição com sufixos pré-calculados 

Embora a estratégia de aleatoriedade possa melhorar muito a taxa de transferência, é difícil ler um item específico porque você não sabe o valor de sufixo usado ao escrever o item. Para facilitar a leitura de itens individuais, você pode usar a estratégia de sufixos pré-calculada. Em vez de usar um número aleatório para distribuir os itens entre as partições, use um número que calcule com base em algo que você deseja consultar.

Considere o exemplo anterior, em que um contêiner usa uma data na chave de partição. Agora, suponha que cada item tenha um atributo accessibleVIN (Vehicle-Identification-Number) e se você executar consultas para encontrar itens por VIN, além da data. Antes de seu aplicativo gravar o item no contêiner, ele pode calcular um sufixo hash com base no VIN e anexá-lo à data da chave de partição. O cálculo pode gerar um número entre 1 e 400 que é distribuído uniformemente, semelhante aos resultados produzidos pelo método de estratégia aleatória. O valor da chave de partição será então a data concatenada com o resultado calculado.

Com essa estratégia, as gravações são distribuídas uniformemente pelos valores da chave de partição e pelas partições. Você pode facilmente ler um item e uma data em particular, porque você pode calcular o valor da chave de partição para um Número de Identificação de Veículo específico. O benefício desse método é que você pode evitar a criação de uma única chave de partição ativa (a chave de partição que recebe toda a carga de trabalho). 

## <a name="next-steps"></a>Próximas etapas

Você pode aprender mais sobre o conceito de particionamento nos seguintes artigos:

* Saiba mais sobre [partições lógicas](partition-data.md)
* Saiba mais sobre [Provisionando a produtividade em bancos de dados e contêineres do Cosmos](set-throughput.md)
* Aprenda [como provisionar a taxa de transferência em um contêiner Cosmos](how-to-provision-container-throughput.md)
* Aprenda [como provisionar a taxa de transferência em um banco de dados Cosmos](how-to-provision-database-throughput.md)
