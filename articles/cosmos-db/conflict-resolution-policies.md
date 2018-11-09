---
title: Resolução de conflitos no Azure Cosmos DB
description: Este artigo descreve as categorias de conflito e políticas de resolução de conflitos no Azure Cosmos DB.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 2f219ed6c3155e8b7d3d978116e1748f6c115fea
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243881"
---
# <a name="conflict-types-and-resolution-policies"></a>Tipos e políticas de resolução de conflitos

Conflitos e políticas de resolução de conflitos são aplicáveis se a conta do Cosmos é configurada com várias regiões de gravação.

Para contas do Cosmos DB configuradas com várias regiões de gravação, os conflitos de atualização podem ocorrer quando vários gravadores atualizam simultaneamente o mesmo item em várias regiões. Conflitos de atualização são classificados nos três tipos a seguir:

1. **Conflitos de inserção** podem ocorrer quando um aplicativo insere simultaneamente dois ou mais documentos com o mesmo índice exclusivo (por exemplo, a propriedade ID) de duas ou mais regiões. Nesse caso, todas as gravações podem ter êxito inicialmente em suas respectivas regiões locais, mas, com base na política de resolução de conflitos escolhida, apenas um item com a ID original é confirmado no final.
2. **Conflitos de substituição** podem ocorrer quando um aplicativo atualiza um único item simultaneamente em duas ou mais regiões.
3. **Conflitos de exclusão** podem ocorrer quando um aplicativo exclui um item de uma região atualiza-o de qualquer outra região, simultaneamente.

## <a name="conflict-resolution-policies"></a>Políticas de resolução de conflitos

O Cosmos DB oferece um mecanismo flexível controlado por políticas para resolução de conflitos de atualização. Você pode selecionar as duas políticas de resolução de conflitos a seguir em um contêiner do Cosmos:

- **LWW (Last-Write-Wins)** que, por padrão, usa uma propriedade de carimbo de data/hora definido pelo sistema (com base no protocolo de sincronização de hora do relógio). Como alternativa, ao usar a API do SQL, o Cosmos DB permite que você especifique qualquer outra propriedade numérica personalizada (também conhecida como o "caminho de resolução de conflitos") a ser usada para resolução de conflitos.  

Se dois ou mais itens entram em conflito em operações de inserção ou de substituição, o item que contém o valor mais alto para o "caminho de resolução de conflitos" torna-se o "vencedor". Se vários itens tiverem o mesmo valor numérico para o caminho de resolução de conflitos, a versão do "vencedor" selecionado será determinada pelo sistema. Todas as regiões convergirão asseguradamente para um único vencedor e terminarão com a versão idêntica do item confirmado. Se há conflitos de exclusão envolvidos, a versão excluída sempre vence com relação a conflitos de inserção ou de substituição, independentemente do valor do caminho de resolução de conflitos.

> [!NOTE]
> A LWW é a política de resolução de conflitos padrão e está disponível para APIs do SQL, de Tabela, do MongoDB, do Cassandra e do Gremlin.

Para saber mais, confira [exemplos usando políticas de resolução de conflitos LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Personalizado** Essa política destina-se a semântica definida pelo aplicativo para a reconciliação de conflitos. Ao definir essa política em seu contêiner do Cosmos, você também precisa registrar um procedimento armazenado de mesclagem, que é invocado automaticamente quando conflitos de atualização são detectados em uma transação de banco de dados no lado do servidor. O sistema fornece exatamente uma garantia para a execução de um procedimento de mesclagem como parte do protocolo de confirmação.  

No entanto, se você configurar seu contêiner com a opção de resolução personalizada mas falhar ao registrar um procedimento de mesclagem no contêiner ou se o procedimento de mesclagem gerar uma exceção em tempo de execução, os conflitos serão gravados no feed de conflitos. Seu aplicativo precisará resolver manualmente os conflitos no feed de conflitos. Para saber mais, confira [exemplos de como usar uma política de resolução personalizada e de como usar o feed de conflitos](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

> [!NOTE]
> A política de resolução de conflitos personalizada só está disponível para contas da API do SQL.

## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode aprender como configurar políticas de resolução de conflitos usando os seguintes artigos:

* [Como usar a política de resolução de conflitos LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Como usar a política de resolução de conflitos personalizada](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Como usar o feed de conflitos](how-to-manage-conflicts.md#read-from-conflict-feed)
