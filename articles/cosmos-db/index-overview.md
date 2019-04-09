---
title: Indexação no Azure Cosmos DB
description: Entenda como funciona a indexação no Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: ecf53251020ce1b639a5bf8da65f5d31ff699db9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265688"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Gerenciar indexação no Azure Cosmos DB – visão geral

O Azure Cosmos DB é um banco de dados independente de esquema e permite que você faça uma iteração no aplicativo rapidamente sem a necessidade de gerenciamento de índices ou esquema. Por padrão, o Azure Cosmos DB indexa automaticamente todos os itens no contêiner sem exigir esquemas ou índices secundários dos desenvolvedores.

## <a name="items-as-trees"></a>Itens como árvores

Ao projetar itens em um contêiner, como documentos JSON e que os representam como árvores, Azure Cosmos DB normaliza a estrutura e os valores de instância em itens em conceito unificador de um **codificado dinamicamente a estrutura de caminho** . Essa representação, cada rótulo em um documento JSON, que inclui os nomes de propriedade e seus valores, torna-se um nó da árvore. As folhas da árvore contêm os valores reais e os nós intermediários contêm as informações de esquema. A imagem a seguir representa as árvores criadas para dois itens (1 e 2) em um contêiner do Azure Cosmos:

![Representação em árvore de dois itens diferentes em um contêiner do Azure Cosmos](./media/index-overview/indexing-as-tree.png)

Um nó de raiz de pseudo é criado como um pai de nós reais correspondente para os rótulos no documento JSON abaixo. As estruturas de dados aninhadas direcionam a hierarquia na árvore. Nós artificiais intermediários rotulados com valores numéricos (por exemplo, 0, 1, ...) são empregados para representar enumerações e índices de matriz.

## <a name="index-paths"></a>Caminhos de índice

Itens em um contêiner do Azure Cosmos como documentos JSON e o índice como árvores de projetos do Azure Cosmos DB. Em seguida, você pode ajustar as políticas de índice para caminhos dentro da árvore. Você pode optar por incluir ou excluir caminhos da indexação. Isso pode oferecer melhor desempenho de gravação e diminuir o armazenamento de índice para cenários em que os padrões de consulta são conhecidos antecipadamente. Para obter mais informações, consulte [caminhos de índice](index-paths.md).

## <a name="indexing-under-the-hood"></a>Indexação: Nos bastidores

Aplica-se o banco de dados do Azure Cosmos *indexação automática* aos dados, onde todos os caminhos em uma árvore é indexado, a menos que você configure para excluir determinados caminhos.

O banco de dados do Azure Cosmos emprega estrutura de dados de índice invertido para armazenar as informações de cada item e facilitar a representação eficiente para consulta. A árvore de índice é um documento que é construído com a união de todas as árvores que representam os itens individuais em um contêiner. A árvore de índice cresce ao longo do tempo conforme novos itens são adicionados ou itens existentes são atualizados no contêiner. Ao contrário de indexação do banco de dados relacional, Azure Cosmos DB não reinicie a indexação do zero, como novos campos são introduzidos. Novos itens são adicionados à estrutura de índice existente. 

Cada nó de árvore de índice é uma entrada de índice que contém os valores de rótulo e a posição, chamados de *termo*e as IDs dos itens, chamados a *lançamentos*. Os lançamentos entre colchetes (por exemplo {1,2}) na Figura índice invertido correspondem aos itens, como *Documento1* e *Document2* que contém o valor de rótulo fornecido. Uma implicação importante do tratamento os rótulos de esquema e os valores de instância de maneira uniforme é que tudo o que é empacotado dentro de um índice grande. Um valor de instância que ainda está nas folhas não é repetido, pode estar em diferentes funções nos itens, com diferentes rótulos de esquema, mas é o mesmo valor. A imagem a seguir mostra a indexação invertido para dois itens diferentes:

![Indexação subjacente, Índice invertido](./media/index-overview/inverted-index.png)

> [!NOTE]
> O índice invertido pode parecer semelhante às estruturas de indexação usadas em um mecanismo de pesquisa no domínio de recuperação de informações. Com esse método, o Azure Cosmos DB permite que você pesquise qualquer item no banco de dado, independentemente da estrutura de esquema.

Para o caminho normalizado, o índice codifica o caminho de encaminhamento desde a raiz até o valor, juntamente com as informações de tipo do valor. O caminho e o valor são codificados para fornecer vários tipos de indexação, como o intervalo, espacial, etc. A codificação de valor é projetada para fornecer valor exclusivo ou uma composição de um conjunto de caminhos.

## <a name="querying-with-indexes"></a>Consultar com índices

O índice invertido permite que uma consulta identifique rapidamente os documentos que correspondem ao predicado da consulta. Tratando o esquema e os valores de instância uniformemente em termos de caminhos, o índice invertido é também uma árvore. Assim, o índice e os resultados podem ser serializados para um documento JSON válido e retornados como documentos, na medida em que são retornados na representação de árvore. Esse método permite a recursão dos resultados para consultas adicionais. A imagem a seguir ilustra um exemplo de indexação em uma consulta de ponto:  

![Exemplo de consulta de ponto](./media/index-overview/index-point-query.png)

Para uma consulta de intervalo *GermanTax* é um [função definida pelo usuário](stored-procedures-triggers-udfs.md#udfs) executado como parte do processamento de consulta. A função definida pelo usuário é qualquer função JavaScript registrada, que pode fornecer a lógica de programação avançada integrada na consulta. A imagem a seguir ilustra um exemplo de indexação em uma consulta de intervalo:

![Exemplo de consulta de intervalo](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre indexação nos seguintes artigos:

- [Política de indexação](index-policy.md)
- [Tipos de índice](index-types.md)
- [Caminhos de índice](index-paths.md)
- [Como gerenciar a política de indexação](how-to-manage-indexing-policy.md)
