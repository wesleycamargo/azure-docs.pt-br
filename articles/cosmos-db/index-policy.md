---
title: Políticas de indexação no Azure Cosmos DB
description: Entenda como funciona a indexação no Azure Cosmos DB. Saiba como configurar e alterar a política de indexação para indexação automática e um melhor desempenho.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/1/2019
ms.author: mjbrown
ms.openlocfilehash: 0ba5cdd4f92390634d6d2bea8add8309cb1f4d3e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014513"
---
# <a name="index-policy-in-azure-cosmos-db"></a>Indexar política no Azure Cosmos DB

Você pode substituir a política de indexação padrão em um contêiner do Azure Cosmos configurando os seguintes parâmetros:

* **Incluir ou excluir itens e os caminhos do índice**: Você pode excluir ou incluir itens específicos no índice ao inserir ou substituir os itens em um contêiner. Você também pode incluir ou excluir caminhos / propriedades específicos a serem indexados em contêineres. Os caminhos podem incluir padrões de caracteres curinga, por exemplo, *.

* **Configurar tipos de índice**: Além dos caminhos indexados do intervalo, você pode adicionar outros tipos de índices, como espacial.

* **Configurar modos de índice**: Usando a política de indexação em um contêiner, você pode configurar diferentes modos de indexação, como *Consistente* ou *Nenhum*.

## <a name="indexing-modes"></a>Modos de indexação

O Azure Cosmos DB oferece suporte a dois modos de indexação que você pode configurar em um contêiner do Azure Cosmos. Você pode configurar os dois modos de indexação a seguir por meio da política de indexação:

* **Consistentee**: Se uma política do contêiner do Azure Cosmos estiver definida como Consistente, as consultas em um contêiner específico seguirão o mesmo nível de consistência especificado para leituras de ponto (por exemplo, forte, desatualização limitada, sessão, ou eventual). 

  O índice é atualizado de forma síncrona conforme você atualiza os itens. Por exemplo, inserir, substituir, atualizar e excluir operações em um item resultará na atualização do índice. A indexação consistente dá suporte a consultas consistentes ao custo que afetam a taxa de transferência de gravação. A redução na taxa de transferência de gravação depende dos "caminhos incluídos na indexação" e do "nível de consistência". O modo de indexação consistente é projetado para escrever rapidamente e consultar cargas de trabalho imediatamente.

* **Nenhum**: Um contêiner que possui um modo de índice Nenhum não possui um índice associado a ele. Isso é comumente usado se o banco de dados do Azure Cosmos for usado como um armazenamento de valor-chave e os itens forem acessados apenas por sua propriedade de ID.

  > [!NOTE]
  > Configurar o modo de indexação como Nenhum tem o efeito colateral de descartar quaisquer índices existentes. Você deve usar essa opção se seus padrões de acesso exigirem apenas ID ou auto-link.

Níveis de consistência de consulta são mantidos semelhantes para as operações de leitura regulares. O banco de dados do Azure Cosmos retornará um erro se você consultar o contêiner que possui um modo de indexação Nenhum. Você pode executar as consultas como varreduras através do cabeçalho explícito  **x-ms-documentdb-enable-scan** na API REST ou na opção de solicitação **EnableScanInQuery** usando o .NET SDK. Alguns recursos de consulta, como ORDER BY, atualmente não são suportados com **EnableScanInQuery**, porque eles exigem um índice correspondente.

## <a name="modifying-the-indexing-policy"></a>Modificando a política de indexação

No Azure Cosmos DB, você pode atualizar a política de indexação de um contêiner a qualquer momento. Uma alteração na política de indexação em um contêiner do Azure Cosmos pode levar a uma alteração na forma do índice. Essa alteração afeta os caminhos que podem ser indexados, sua precisão e o modelo de consistência do próprio índice. Uma mudança na política de indexação efetivamente exige uma transformação do índice antigo em um novo índice.

### <a name="index-transformations"></a>Transformações de índice

Todas as transformações de índice são realizadas online. Os itens indexados pela política antiga são transformados com eficiência de acordo com a nova política, sem afetar a disponibilidade de gravação ou a taxa de transferência provisionada para o contêiner. A consistência das operações de leitura e gravação que são executadas usando a API REST, SDKs ou de procedimentos armazenados e acionadores não é afetada durante a transformação de índice.

Alterar a política de indexação é uma operação assíncrona, e o tempo para concluir a operação depende do número de itens, da taxa de transferência provisionada e do tamanho dos itens. Enquanto a reindexação está em andamento, sua consulta pode não retornar todos os resultados correspondentes, se as consultas usarem o índice que está sendo modificado e as consultas não retornarem erros / falhas. Enquanto a reindexação está em andamento, as consultas são eventualmente consistentes, independentemente da configuração do modo de indexação. Após o índice transformação ser concluído, você continuará ver resultados consistentes. Isso se aplica a consultas emitidas pelas interfaces, como API REST, SDKs ou procedimentos armazenados e disparadores. A transformação de índice é executada de forma assíncrona em segundo plano nas réplicas, usando os recursos de reserva disponíveis para uma réplica específica.

Todas as transformações de índice são feitas em vigor. O Azure Cosmos DB não mantém duas cópias do índice. Portanto, nenhum espaço em disco adicional é necessário ou consumido em seus contêineres enquanto ocorre a transformação de índice.

Quando você altera a política de indexação, as mudanças são aplicadas para mover do índice antigo para o novo índice, principalmente com base nas configurações do modo de indexação. As configurações do modo de indexação desempenham um papel importante quando comparadas a outras propriedades, como caminhos incluídos / excluídos, tipos de índice e precisão.

Se as políticas de indexação antigas e novas usarem a indexação **Consistente**, o banco de dados do Azure Cosmos executará uma transformação de índice online. Não será possível aplicar outra alteração de política de indexação que tenha um modo de indexação Consistente, enquanto a transformação estiver em andamento. Ao mudar para Nenhum, o índice será imediatamente ignorado. Mover para Nenhum é útil quando você deseja cancelar uma transformação em andamento e começar de novo com uma política de indexação diferente.

Para que a transformação de índice seja concluída com êxito, verifique se o contêiner possui espaço de armazenamento suficiente. Se o contêiner atingir sua cota de armazenamento, a transformação do índice será pausada. A transformação de índice é retomada automaticamente quando o espaço de armazenamento está disponível, por exemplo, quando você exclui alguns itens.

## <a name="modifying-the-indexing-policy---examples"></a>Modificando a política de indexação - exemplos

A seguir, os casos de uso mais comuns em que você atualiza uma política de indexação:

* Se você deseja obter resultados consistentes durante a operação normal, mas retornar ao modo de indexação **Nenhum** durante as importações de dados em massa.

* Se você quiser começar a usar os recursos de indexação nos contêineres atuais do Azure Cosmos. Por exemplo, você pode usar a consulta geoespacial, que requer o tipo de índice espacial, ou consultas ORDER BY / string range, que requerem o tipo de índice de intervalo de cadeia.

* Se você deseja selecionar manualmente as propriedades a serem indexadas e alterá-las ao longo do tempo para ajustar-se às suas cargas de trabalho.

* Se você quiser ajustar a precisão de indexação para melhorar o desempenho da consulta ou para reduzir o armazenamento consumido.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre indexação nos artigos a seguir:

* [Visão geral de indexação](index-overview.md)
* [Tipos de índice](index-types.md)
* [Caminhos de índice](index-paths.md)
* [Como gerenciar a política de indexação](how-to-manage-indexing-policy.md)
