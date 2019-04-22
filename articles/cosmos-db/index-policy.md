---
title: Políticas de indexação no Azure Cosmos DB
description: Entenda como funciona a indexação no Azure Cosmos DB. Saiba como configurar e alterar a política de indexação para indexação automática e um melhor desempenho.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: 6998db1679e67f8ac4bf7c81ea9373c66a9618ee
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278556"
---
# <a name="index-policy-in-azure-cosmos-db"></a>Indexar política no Azure Cosmos DB

Você pode substituir a política de indexação padrão em um contêiner do Azure Cosmos configurando os seguintes parâmetros:

* **Incluir ou excluir itens e os caminhos do índice**: Você pode excluir ou incluir itens específicos no índice, quando você insere ou substituir os itens dentro de um contêiner. Você também pode incluir ou excluir caminhos / propriedades específicos a serem indexados em contêineres. Os caminhos podem incluir padrões de caracteres curinga, por exemplo, *.

* **Configurar tipos de índice**: Além para o intervalo de caminhos indexados, você pode adicionar outros tipos de índices, tais como espacial.

* **Configurar modos de índice**: Usando a política de indexação em um contêiner, você pode configurar diferentes modos de indexação, como *Consistente* ou *Nenhum*.

## <a name="indexing-modes"></a>Modos de indexação

O Azure Cosmos DB dá suporte a dois modos de indexação que podem ser configuradas em um contêiner do Cosmos do Azure por meio da política de indexação:

* **Consistentee**: Se a política de um contêiner Azure Cosmos está definida como *consistente*, as consultas em um contêiner específico seguirão o mesmo nível de consistência especificado para leituras de ponto (por exemplo, forte, desatualização limitada, sessão ou eventual). 

  O índice é atualizado de forma síncrona conforme você atualiza os itens. Por exemplo, inserir, substituir, atualizar e excluir operações em um item resultará na atualização do índice. A indexação consistente dá suporte a consultas consistentes ao custo que afetam a taxa de transferência de gravação. A redução na taxa de transferência de gravação depende de "caminhos incluídos no índice" e "nível de consistência". Modo de indexação consistente foi projetado para manter o índice atualizado com todas as atualizações e para atender a consultas imediatamente.

* **Nenhum**: Um contêiner que possui um modo de índice Nenhum não possui um índice associado a ele. Isso é comumente usado se o banco de dados do Azure Cosmos for usado como um armazenamento de valor-chave e os itens forem acessados apenas por sua propriedade de ID.

  > [!NOTE]
  > Configurando o modo de indexação como um *None* tem o efeito colateral de remover quaisquer índices existentes. Você deve usar essa opção se seus padrões de acesso exigirem apenas ID ou auto-link.

Níveis de consistência de consulta são mantidos semelhantes para as operações de leitura regulares. Banco de dados Cosmos do Azure retornará um erro se você consultar o contêiner que possui um *None* modo de indexação. Você pode executar as consultas como verificações por meio de explícito **x-ms-documentdb-enable-scan** cabeçalho na API REST ou o **EnableScanInQuery** opção de solicitação usando o SDK do .NET. Alguns recursos de consulta, como ORDER BY, atualmente não são suportados com **EnableScanInQuery**, porque eles exigem um índice correspondente.

## <a name="modifying-the-indexing-policy"></a>Modificando a política de indexação

No Azure Cosmos DB, você pode atualizar a política de indexação de um contêiner a qualquer momento. Uma alteração na política de indexação em um contêiner do Azure Cosmos pode levar a uma alteração na forma do índice. Essa alteração afeta os caminhos que podem ser indexados, sua precisão e o modelo de consistência do próprio índice. Uma mudança na política de indexação efetivamente exige uma transformação do índice antigo em um novo índice.

### <a name="index-transformations"></a>Transformações de índice

Todas as transformações de índice são realizadas online. Os itens indexados pela política antiga serão transformados com eficiência pela nova política sem afetar a disponibilidade de gravação ou a taxa de transferência provisionada no contêiner. A consistência de leitura e gravação operações que são executadas usando a API REST, SDKs, ou usando de procedimentos armazenados e gatilhos não é afetado durante a transformação de índice.

Alterar a política de indexação é uma operação assíncrona, e o tempo para concluir a operação depende do número de itens, taxa de transferência provisionada e o tamanho dos itens. Enquanto a reindexação está em andamento, sua consulta não pode retornar todos os resultados correspondentes, se as consultas usam o índice que está sendo modificado, e as consultas não retornará quaisquer erros/falhas. Enquanto a reindexação está em andamento, as consultas são eventualmente consistentes, independentemente da configuração do modo indexação. Após o índice transformação ser concluído, você continuará ver resultados consistentes. Isso se aplica a consultas emitidas pelas interfaces, como API REST, SDKs ou procedimentos armazenados e disparadores. Transformação de índice é executada de forma assíncrona, em segundo plano, nas réplicas por usando os recursos que estão disponíveis para réplicas específicas.

Todas as transformações de índice são feitas em vigor. O Azure Cosmos DB não mantém duas cópias do índice. Portanto, nenhum espaço em disco adicional é necessário ou consumido em seus contêineres enquanto ocorre a transformação de índice.

Quando você altera a política de indexação, as alterações são aplicadas para mover o índice antigo para o novo índice e são principalmente com base nas configurações do modo de indexação. As configurações do modo de indexação desempenham um papel importante quando comparadas a outras propriedades, como caminhos incluídos / excluídos, tipos de índice e precisão.

Se as políticas de indexação antigas e novas usarem a indexação **Consistente**, o banco de dados do Azure Cosmos executará uma transformação de índice online. Não será possível aplicar outra alteração de política de indexação que tenha um modo de indexação Consistente, enquanto a transformação estiver em andamento. Ao mudar para Nenhum, o índice será imediatamente ignorado. Mover para Nenhum é útil quando você deseja cancelar uma transformação em andamento e começar de novo com uma política de indexação diferente.

## <a name="modifying-the-indexing-policy---examples"></a>Modificando a política de indexação - exemplos

A seguir é os casos de uso mais comuns quando você deseja atualizar uma política de indexação:

* Se você quiser ter resultados consistentes durante a operação normal, mas ao fazer fallback para o **None** modo de indexação durante importações de dados em massa.

* Se você quiser começar a usar os recursos de indexação nos contêineres atuais do Azure Cosmos. Por exemplo, você pode usar a consulta geoespacial, que requer o tipo de índice espacial, ou consultas ORDER BY / string range, que requerem o tipo de índice de intervalo de cadeia.

* Se você deseja selecionar manualmente as propriedades a serem indexadas e alterá-las ao longo do tempo para ajustar-se às suas cargas de trabalho.

* Se você quiser ajustar a precisão de indexação para melhorar o desempenho da consulta ou para reduzir o armazenamento consumido.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre indexação nos artigos a seguir:

* [Visão geral de indexação](index-overview.md)
* [Tipos de índice](index-types.md)
* [Caminhos de índice](index-paths.md)
* [Como gerenciar a política de indexação](how-to-manage-indexing-policy.md)
