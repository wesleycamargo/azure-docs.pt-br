---
title: Provisionar taxa de transferência para o Azure Cosmos DB
description: Saiba como definir a taxa de transferência provisionada para seus contêineres e bancos de dados do Azure Cosmos DB.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: andrl
ms.openlocfilehash: a97032344b904442ed3606c6297251578c3b4ff7
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263886"
---
# <a name="provision-throughput-on-azure-cosmos-containers-and-databases"></a>Taxa de transferência de provisionamento nos contêineres e bancos de dados do Azure Cosmos

Um banco de dados do Azure Cosmos é uma unidade de gerenciamento para um conjunto de contêineres. Um banco de dados é composto por um conjunto de contêineres independentes de esquema. Um contêiner do Azure Cosmos é a unidade de escalabilidade para taxa de transferência e armazenamento. Um contêiner é particionado horizontalmente em um conjunto de máquinas em uma região do Azure e é distribuído em todas as regiões do Azure associadas à sua conta do Azure Cosmos.

O Azure Cosmos DB permite configurar o rendimento em duas granularidades - os **Contêineres do Azure Cosmos**  e os **bancos de dados do Azure Cosmos**.

# <a name="setting-throughput-on-a-azure-cosmos-container"></a>Definir a taxa de transferência em um contêiner do Azure Cosmos  

A taxa de transferência provisionada em um contêiner do Azure Cosmos é reservada exclusivamente para o contêiner. O contêiner recebe a taxa de transferência provisionada o tempo todo. A taxa de transferência provisionada em um contêiner é respaldada financeiramente por SLAs. Para configurar a taxa de transferência em um contêiner, consulte [Como provisionar a taxa de transferência em um contêiner do Azure Cosmos](how-to-provision-container-throughput.md).

A configuração da taxa de transferência provisionada em um contêiner é a opção mais utilizada. Embora seja possível escalar elasticamente a taxa de transferência para um contêiner por meio do provisionamento de qualquer valor da taxa de transferência (RUs), não é possível especificar seletivamente a taxa de transferência para partições lógicas. Quando a carga de trabalho em execução em uma partição lógica consumir mais do que a taxa de transferência alocada para a partição lógica específica, suas operações sofrerão limitações de taxa. Quando ocorre essa limitação de taxa, você pode aumentar a taxa de transferência para todo o contêiner ou tentar novamente a operação. Para saber mais sobre particionamento, confira [Partições lógicas](partition-data.md).

Recomendamos a configuração da taxa de transferência na granularidade do contêiner se você quiser um desempenho garantido para o contêiner.

A taxa de transferência provisionada em um contêiner do Azure Cosmos é uniformemente distribuída em todas as partições lógicas do contêiner. Como uma ou mais partições lógicas de um contêiner são hospedadas por uma partição de recursos, as partições físicas pertencem exclusivamente ao contêiner e dão suporte à taxa de transferência provisionada no contêiner. A imagem a seguir mostra como uma partição de recursos hospeda uma ou mais partições lógicas de um contêiner:

![Partição de recursos](./media/set-throughput/resource-partition.png)

# <a name="setting-throughput-on-a-azure-cosmos-database"></a>Taxa de transferência de configuração em um banco de dados Cosmos do Azure

Quando você provisiona a taxa de transferência em um banco de dados do Azure Cosmos, a taxa de transferência é compartilhada entre todos os contêineres no banco de dados, a menos que você tenha especificado uma taxa de transferência provisionada em contêineres específicos. O compartilhamento da taxa de transferência do banco de dados entre seus contêineres é análogo à hospedagem de um banco de dados em um cluster de computadores. Como todos os contêineres dentro de um banco de dados compartilham os recursos disponíveis em um computador, naturalmente, não é possível prever o desempenho em qualquer contêiner específico. Para configurar a taxa de transferência em um banco de dados, consulte [Como configurar a taxa de transferência provisionada em um banco de dados do Azure Cosmos](how-to-provision-database-throughput.md).

Definir a taxa de transferência em um banco de dados do Azure Cosmos garante que você receba a taxa de transferência provisionada o tempo todo. Como todos os contêineres do banco de dados compartilham a taxa de transferência provisionada, o Azure Cosmos DB não fornece nenhuma garantia previsível de taxa de transferência para um determinado contêiner nesse banco de dados. A parte da taxa de transferência que um contêiner específico pode receber depende do seguinte:

* Do número de contêineres
* Da escolha de chaves de partição para vários contêineres e
* Da distribuição da carga de trabalho entre várias partições lógicas dos contêineres. 

Recomendamos a configuração da taxa de transferência em um banco de dados quando você quiser compartilhar a taxa de transferência entre vários contêineres, mas não quiser dedicar a taxa de transferência a qualquer contêiner específico. Veja a seguir alguns exemplos nos quais é preferível provisionar a taxa de transferência no nível do banco de dados:

* O compartilhamento da taxa de transferência provisionada de um banco de dados entre um conjunto de contêineres é útil para aplicativos multilocatário. Cada usuário pode ser representado por um contêiner do Azure Cosmos distinto.

* Compartilhar a taxa de transferência provisionada de um banco de dados em um conjunto de contêineres é útil quando você está migrando um banco de dados NoSQL (como MongoDB, Cassandra) hospedado em um cluster de VMs ou de servidores físicos locais para o Azure Cosmos DB. Você pode pensar na taxa de transferência provisionada configurada em seu banco de dados do Azure Cosmos como um equivalente lógico (mas mais econômico e elástico) do que a capacidade de computação de seu cluster do MongoDB ou do Cassandra.  

A qualquer momento, a taxa de transferência alocada a um contêiner dentro de um banco de dados será distribuída entre todas as partições lógicas desse contêiner. Quando os contêineres compartilham a taxa de transferência provisionada em um banco de dados, não é possível aplicar seletivamente a taxa de transferência a um contêiner específico ou a uma partição lógica. Se a carga de trabalho em uma partição lógica consumir mais do que a taxa de transferência alocada a uma partição lógica específica, suas operações sofrerão limitação de taxa. Quando ocorre essa limitação de taxa, você pode aumentar a taxa de transferência para todo o contêiner ou tentar novamente a operação. Para saber mais sobre particionamento, confira [Partições lógicas](partition-data.md).

É possível hospedar várias partições lógicas que compartilham a taxa de transferência provisionada para um banco de dados em uma partição de recurso único. Embora o escopo de uma partição lógica individual de um contêiner esteja sempre dentro de uma partição de recursos, as partições lógicas "L" em contêineres "C" que compartilham a taxa de transferência provisionada de um banco de dados podem ser mapeadas e hospedadas em partições físicas "R". A imagem a seguir mostra como uma partição de recursos pode hospedar uma ou mais partições lógicas que pertencem a contêineres diferentes dentro de um banco de dados:

![Partição de recursos](./media/set-throughput/resource-partition2.png)

## <a name="setting-throughput-on-a-azure-cosmos-database-and-a-container"></a>A definição de taxa de transferência em um banco de dados Cosmos do Azure e um contêiner

Você pode combinar os dois modelos e provisionar a taxa de transferência no banco de dados e no contêiner. O exemplo a seguir mostra como provisionar a taxa de transferência em um banco de dados do Azure Cosmos e em um contêiner:

* Você pode criar um banco de dados do Azure Cosmos chamado 'Z' com taxa de transferência provisionada de 'K' RUs. 
* Em seguida, crie cinco contêineres chamados A, B, C, D e E no banco de dados.
* Configure explicitamente RUs "P" da taxa de transferência provisionada no contêiner "B".
* A taxa de transferência de RUs "K" é compartilhada entre os quatro contêineres: A, C, D e E. O valor exato da taxa de transferência disponível para A, C, D, ou E varia e não há SLAs para a taxa de transferência de cada contêiner.
* O contêiner "B" comprovadamente obtém a taxa de transferência de RUs "P" o tempo todo e isso é respaldado por SLAs.

## <a name="comparison-of-models"></a>Comparação de modelos

|**Cota**  |**Taxa de transferência provisionada em um banco de dados**  |**Taxa de transferência provisionada em um contêiner**|
|---------|---------|---------|
|Mínimo de RUs |400 |400|
|Mínimo de RUs por contêiner|100|400|
|Mínimo de RUs necessários para consumir 1 GB de armazenamento|40|40|
|Máximo de RUs|Ilimitado, no banco de dados|Ilimitado, no contêiner|
|RUs atribuídas/disponíveis para um contêiner específico|Sem garantias. As RUs atribuídas a um determinado contêiner dependem de propriedades como: escolha de chaves de partição de contêineres que compartilham a taxa de transferência, distribuição da carga de trabalho, número de contêineres. |Todas as RUs configuradas no contêiner são reservadas exclusivamente ao contêiner.|
|Armazenamento máximo para um contêiner|Ilimitado|Ilimitado|
|Taxa de transferência máxima por partição lógica de um contêiner|10 mil RUs|10 mil RUs|
|Taxa de transferência máxima (dados + índice) por partição lógica de um contêiner|10 GB|10 GB|

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [partições lógicas](partition-data.md)
* Aprenda [como provisionar a taxa de transferência em um contêiner Azure Cosmos](how-to-provision-container-throughput.md)
* Aprenda [como provisionar a taxa de transferência em um banco de dados do Azure Cosmos](how-to-provision-database-throughput.md)

