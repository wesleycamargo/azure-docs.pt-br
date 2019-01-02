---
title: Indexação no Azure Cosmos DB
description: Entenda como funciona a indexação no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: 0333bc5e95b74fc97cfff3d79adbe28aefff5d40
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834631"
---
# <a name="indexing-in-azure-cosmos-db"></a>Indexação no Azure Cosmos DB

O Azure Cosmos DB é um banco de dados independente de esquema e permite que você faça uma iteração no aplicativo rapidamente sem a necessidade de gerenciamento de índices ou esquema. Por padrão, o Azure Cosmos DB indexa automaticamente todos os itens no contêiner sem exigir esquemas ou índices secundários dos desenvolvedores.

## <a name="items-as-trees"></a>Itens como árvores

Projetando itens em um contêiner como documentos JSON e representando-os como árvores, o Azure Cosmos DB normaliza a estrutura e os valores da instância entre os itens no conceito unificador de uma  **estrutura de caminho codificada dinamicamente**. Nessa representação, cada rótulo em um documento JSON, que inclui os nomes das propriedades e seus valores, torna-se um nó da árvore. As folhas da árvore contêm valores reais e os nós intermediários contêm as informações do esquema. A imagem a seguir representa as árvores criadas para dois itens (1 e 2) em um contêiner:

![Representação em árvore de dois itens diferentes em um contêiner do Azure Cosmos](./media/index-overview/indexing-as-tree.png)

Um pseudo nó raiz é criado como pai para os nós reais correspondentes aos rótulos no documento abaixo. As estruturas de dados aninhadas direcionam a hierarquia na árvore. Nós artificiais intermediários rotulados com valores numéricos (por exemplo, 0, 1, ...) são empregados para representar enumerações e índices de matriz.

## <a name="index-paths"></a>Caminhos de índice

O Azure Cosmos DB projeta itens como documentos JSON e indexa como árvores. Portanto, é possível ajustar as políticas para os caminhos dentro da árvore. Você pode optar por incluir ou excluir caminhos da indexação. Isso pode oferecer melhor desempenho de gravação e diminuir o armazenamento de índice para cenários em que os padrões de consulta são conhecidos antecipadamente. para saber mais, consulte [Caminhos de Índice](index-paths.md).

## <a name="indexing-under-the-hood"></a>Indexação: Nos bastidores

O banco de dados do Azure Cosmos aplica a indexação automática aos dados, onde cada caminho em uma árvore é indexado, a menos que você configure para excluir determinados caminhos.

O banco de dados do Azure Cosmos emprega estrutura de dados de índice invertido para armazenar as informações de cada item e facilitar a representação eficiente para consulta. A árvore de índice é um documento que é construído com a união de todas as árvores representando itens individuais no contêiner. A árvore de índice cresce com o tempo à medida que novos itens são adicionados ou itens existentes são atualizados no contêiner. Ao contrário da indexação de banco de dados relacional, o Azure Cosmos DB não reinicia a indexação do zero à medida que novos campos são introduzidos, novos itens são adicionados à estrutura existente. 

Cada nó da árvore de índice é uma entrada de índice que contém os valores de rótulo e posição, chamados de termo, e as ids dos itens, chamadas de lançamentos. Os lançamentos nas chaves (por exemplo, {1,2}) no índice invertido correspondem aos itens, como Documento1 e Documento2, que contêm o valor do rótulo fornecido. Uma importante implicação de tratar uniformemente os rótulos de esquema e os valores de instância é que todos são compactados dentro de um índice grande. Um valor de instância que ainda está nas folhas não é repetido, pode estar em diferentes funções nos itens, com diferentes rótulos de esquema, mas é o mesmo valor. A imagem a seguir mostra a indexação invertida para itens diferentes:

![Indexação subjacente, Índice invertido](./media/index-overview/inverted-index.png)

> [!NOTE]
> O índice invertido pode parecer semelhante às estruturas de indexação usadas em um mecanismo de pesquisa no domínio de recuperação de informações. Com esse método, o Azure Cosmos DB permite que você pesquise qualquer item no banco de dado, independentemente da estrutura de esquema.

Para o caminho normalizado, o índice codifica o caminho de encaminhamento desde a raiz até o valor, juntamente com as informações de tipo do valor. O caminho e o valor são codificados para fornecer vários tipos de indexação como intervalo, tipos espaciais. A codificação de valor é projetada para fornecer valor exclusivo ou uma composição de um conjunto de caminhos.

## <a name="querying-with-indexes"></a>Consultar com índices

O índice invertido permite que uma consulta identifique rapidamente os documentos que correspondem ao predicado da consulta. Ao tratar ambos os valores de instância e esquema uniformemente em termos de caminhos, o índice invertido também será uma árvore. Assim, o índice e os resultados podem ser serializados para um documento JSON válido e retornados como documentos, na medida em que são retornados na representação de árvore. Esse método permite a recursão dos resultados para consultas adicionais. A imagem a seguir ilustra um exemplo de indexação em uma consulta de ponto:  

![Exemplo de consulta de ponto](./media/index-overview/index-point-query.png)

Para uma consulta de intervalo, GermanTax é uma função definida pelo usuário executada como parte do processamento de consulta. A função definida pelo usuário é qualquer função Javascript registrada que possa fornecer lógica de programação avançada integrada à consulta. A imagem a seguir ilustra um exemplo de indexação em uma consulta de intervalo:

![Exemplo de consulta de intervalo](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre indexação nos seguintes artigos:

- [Política de indexação](index-policy.md)
- [Tipos de índice](index-types.md)
- [Caminhos de índice](index-paths.md)
- [Como gerenciar a política de indexação](how-to-manage-indexing-policy.md)
