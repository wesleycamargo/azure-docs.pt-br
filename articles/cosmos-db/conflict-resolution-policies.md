---
title: Tipos de resolução de conflitos e políticas de resolução no Azure Cosmos DB
description: Este artigo descreve as categorias de conflito e políticas de resolução de conflitos no Azure Cosmos DB.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 1b2a122cc8a04d4f0044ecb0fe0341357bc29c0f
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514818"
---
# <a name="conflict-types-and-resolution-policies"></a>Tipos e políticas de resolução de conflitos

Conflitos e políticas de resolução de conflitos são aplicáveis se a conta do Cosmos é configurada com várias regiões de gravação.

Para contas Cosmos configuradas com várias regiões de gravação, conflitos de atualização podem ocorrer quando vários gravadores atualizam simultaneamente o mesmo item em várias regiões. Conflitos de atualização são classificados nos três tipos a seguir:

1. **Conflitos de inserção:** Esses conflitos podem ocorrer quando um aplicativo insere simultaneamente dois ou mais itens com o mesmo índice exclusivo (por exemplo, propriedade de ID) de duas ou mais regiões. Nesse caso, todas as gravações podem ter êxito inicialmente em suas respectivas regiões locais, mas, com base na política de resolução de conflitos escolhida, apenas um item com a ID original é confirmado no final.

1. **Substituir conflitos:** Esses conflitos podem ocorrer quando um aplicativo atualiza um único item simultaneamente de duas ou mais regiões.

1. **Excluir conflitos:** Esses conflitos podem ocorrer quando um aplicativo exclui simultaneamente um item de uma região e o atualiza de outra região.

## <a name="conflict-resolution-policies"></a>Políticas de resolução de conflitos

O Cosmos DB oferece um mecanismo flexível orientado por diretivas para resolver conflitos de atualização. Você pode selecionar as duas políticas de resolução de conflitos a seguir em um contêiner do Cosmos:

- **Last-Write-Wins (LWW):** Esta política de resolução, por padrão, usa uma propriedade de registro de data e hora definida pelo sistema (com base no protocolo de relógio de sincronização de horário). Como alternativa, ao usar a API do SQL, o Cosmos DB permite que você especifique qualquer outra propriedade numérica personalizada (também chamada de "caminho de resolução de conflito") a ser usada para resolução de conflitos.  

  Se dois ou mais itens entram em conflito em operações de inserção ou de substituição, o item que contém o valor mais alto para o "caminho de resolução de conflitos" torna-se o "vencedor". Se vários itens tiverem o mesmo valor numérico para o caminho de resolução de conflitos, a versão do "vencedor" selecionado será determinada pelo sistema. Todas as regiões convergirão asseguradamente para um único vencedor e terminarão com a versão idêntica do item confirmado. Se houver conflitos de exclusão envolvidos, a versão excluída sempre vencerá os conflitos de inserção ou substituição, independentemente do valor do caminho de resolução de conflitos.

  > [!NOTE]
  > Last-Write-Wins é a política de resolução de conflitos padrão e está disponível para as contas SQL, Table, MongoDB, Cassandra e Gremlin API.

  Para saber mais, confira [exemplos usando políticas de resolução de conflitos LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Personalizado:** Esta política de resolução é projetada para semântica definida pelo aplicativo para reconciliação de conflitos. Ao configurar essa política no contêiner Cosmos, você também precisa registrar um procedimento armazenado de mesclagem, que é chamado automaticamente quando conflitos de atualização são detectados em uma transação de banco de dados no servidor. O sistema fornece exatamente uma garantia para a execução de um procedimento de mesclagem como parte do protocolo de confirmação.  

  No entanto, se você configurar seu contêiner com a opção de resolução personalizada mas falhar ao registrar um procedimento de mesclagem no contêiner ou se o procedimento de mesclagem gerar uma exceção em tempo de execução, os conflitos serão gravados no feed de conflitos. Seu aplicativo precisará resolver manualmente os conflitos no feed de conflitos. Para saber mais, confira [exemplos de como usar uma política de resolução personalizada e de como usar o feed de conflitos](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > A política de resolução de conflitos personalizada só está disponível para contas da API do SQL.

## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode aprender como configurar políticas de resolução de conflitos usando os seguintes artigos:

* [Como usar a política de resolução de conflitos LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Como usar a política de resolução de conflitos personalizada](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Como usar o feed de conflitos](how-to-manage-conflicts.md#read-from-conflict-feed)
