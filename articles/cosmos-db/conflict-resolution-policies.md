---
title: Tipos de resolução de conflitos e políticas de resolução no Azure Cosmos DB
description: Este artigo descreve as categorias de conflito e políticas de resolução de conflitos no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 4af3f4c60f186c34d0f5522461ba70c68b59d1ab
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033886"
---
# <a name="conflict-types-and-resolution-policies"></a>Tipos e políticas de resolução de conflitos

Conflitos e políticas de resolução de conflitos são aplicáveis se a conta do Azure Cosmos DB for configurada com várias regiões de gravação.

Para contas do Azure Cosmos DB configuradas com várias regiões de gravação, os conflitos de atualização podem ocorrer quando gravadores atualizam simultaneamente o mesmo item em várias regiões. Conflitos de atualização são classificados nos três tipos a seguir:

* **Conflitos da importação**: Esses conflitos podem ocorrer quando um aplicativo insere simultaneamente dois ou mais itens com o mesmo índice exclusivo (por exemplo, a propriedade ID) de duas ou mais regiões. Por exemplo, esse conflito pode ocorrer com uma propriedade de ID. Todas as gravações podem ser bem-sucedidas inicialmente em suas respectivas regiões locais. Mas, com base na política de resolução de conflitos que você escolher, somente um item com a ID original é finalmente confirmado.

* **Substituir conflitos**: Esses conflitos podem ocorrer quando um aplicativo atualiza um único item simultaneamente em duas ou mais regiões.

* **Excluir conflitos**: Esses conflitos podem ocorrer quando um aplicativo atualiza a partir de outra região e exclui um item de uma região simultaneamente.

## <a name="conflict-resolution-policies"></a>Políticas de resolução de conflitos

O Azure Cosmos DB oferece um mecanismo flexível orientado por diretivas para resolver conflitos de atualização. Você pode selecionar as duas políticas de resolução de conflitos a seguir em um contêiner do Azure Cosmos DB:

- **LWW (Última Gravação Vence)**: Essa política de resolução, por padrão, usa uma propriedade de carimbo de hora definida pelo sistema. Ele se baseia no protocolo de sincronização de hora de relógio. Se você usar a API de SQL do Azure Cosmos DB, você pode especificar qualquer outra propriedade numérica personalizada a ser usada para resolução de conflitos. Uma propriedade numérica personalizada também é conhecida como o caminho de resolução de conflito. 

  Se dois ou mais itens entram em conflito ou substituem operações, o item com o valor mais alto para o "caminho de resolução de conflitos" torna-se o "vencedor". O sistema determina o ganhador se vários itens tiverem o mesmo valor numérico para o caminho de resolução de conflito. Todas as regiões têm a garantia de convergir para um único vencedor e terminarão com a versão igual do item confirmado. Ao excluir conflitos que estão envolvidos, a versão excluída sempre supera o inserir ou substituir os conflitos. Esse resultado ocorre independentemente do valor do caminho de resolução de conflito.

  > [!NOTE]
  > O Last Writer Wins é o modo de resolução de conflitos padrão. Está disponível para contas SQL, Azure Cosmos DB Table, MongoDB, Cassandra e Gremlin API.

  Para saber mais, confira [exemplos que usam políticas de resolução de conflitos LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Personalizado**: Esta política de resolução é projetada para semântica definida pelo aplicativo para reconciliação de conflitos. Quando você definir essa política em seu contêiner do Azure Cosmos DB, você também precisará registrar um procedimento armazenado de mesclagem. Esse procedimento é invocado automaticamente quando os conflitos são detectados em uma transação de banco de dados no servidor. O sistema fornece exatamente uma garantia para a execução de um procedimento de mesclagem como parte do protocolo de confirmação.  

  Há dois pontos a serem lembrados se você configurar seu contêiner com a opção de resolução personalizada. Se houver falhar ao registrar um procedimento de mesclagem no contêiner ou se o procedimento de mesclagem gerar uma exceção em tempo de execução, os conflitos serão gravados no feed de conflitos. Seu aplicativo precisará resolver manualmente os conflitos no feed de conflitos. Para saber mais, confira [exemplos de como usar uma política de resolução personalizada e de como usar o feed de conflitos](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > A política de resolução de conflitos personalizada só está disponível para contas da API do SQL.

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar políticas de resolução de conflito. Confira os seguintes artigos:

* [Usar a política de resolução de conflitos LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Usar a política de resolução de conflitos personalizada](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Usar os conflitos de feed](how-to-manage-conflicts.md#read-from-conflict-feed)
