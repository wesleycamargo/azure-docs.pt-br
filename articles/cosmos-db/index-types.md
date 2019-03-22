---
title: Tipos de índice no Azure Cosmos DB
description: Visão geral dos tipos de índice no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/13/2019
ms.author: mjbrown
ms.openlocfilehash: 56c0fcb24ac5d255c6a36bcffd327df76f459963
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57990548"
---
# <a name="index-types-in-azure-cosmos-db"></a>Tipos de índice no Azure Cosmos DB

Há várias opções nas quais você configura a política de indexação para um caminho. Você pode especificar uma ou mais definições de indexação para cada caminho:

- **Tipo de dados:** Cadeia de caracteres, Número, Ponto, Polígono ou LineString (pode conter apenas uma entrada por tipo de dados por caminho).

- **Tipo de índice:** Intervalo (igualdade, intervalo ou consultas ORDER BY) ou espacial (consultas espaciais).

- **Precisão:** Para um índice de intervalo, o valor de precisão máxima é -1, que também é o padrão.

## <a name="index-kind"></a>Tipo de índice

O Azure Cosmos DB dá suporte ao índice de intervalo para todos os caminhos que podem ser configurados para tipos de dados de cadeia de caracteres ou número, ou ambos.

- **Índice de intervalo** dá suporte a consultas de igualdade eficientes, consultas de junção, consultas de intervalo (usando >, <>, =, < =,! =) e consultas ORDER BY. Por padrão,consultas ORDER By também requerem precisão máxima de índice (-1). O tipo de dados pode ser Cadeia de Caracteres ou Número.

- O **índice espacial** dá suporte a consultas espaciais (interna e de distância) eficientes. Os tipos de dados podem ser Ponto, Polígono ou LineString. O Azure Cosmos DB também dá suporte ao tipo de índice espacial em todos os caminhos que podem ser especificados para os tipos de dados Ponto, Polígono ou LineString. O valor no caminho especificado deve ser um fragmento GeoJSON válido, assim como {"type": "Point", "coordinates": [0.0, 10.0]}. O Azure Cosmos DB dá suporte à indexação automática de tipos de dados Ponto, Polígono e LineString.

Aqui estão exemplos de consultas de intervalo e índices espaciais podem ser usados para servir:

| **Tipo de índice** | **Descrição/caso de uso** |
| ---------- | ---------------- |
| Intervalo      | O intervalo em over /prop/? (or /) pode ser usado para servir às seguintes consultas eficientemente:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop<br><br>O intervalo em over /Props/ [] /? (or / or /props/) pode ser usado para servir às consultas a seguir eficientemente:<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5  |
| Espacial    | O intervalo em over /prop/? (or /) pode ser usado para servir às seguintes consultas eficientemente:<br><br>SELECT FROM collection c c onde ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Point", ... }) --com a indexação nos pontos habilitada<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Polygon", ... }, c.prop) --com a indexação em polígonos habilitada. |

## <a name="default-behavior-of-index-kinds"></a>Comportamento padrão de tipos de índice

- Se não houver índices de intervalo de qualquer precisão para sinalizar que um exame poderá ser necessário para atender à consulta, nesse caso, por padrão, um erro será retornado para consultas com operadores de intervalo, como >=.

- As consultas de intervalo podem ser realizadas sem um índice do Intervalo, usando o cabeçalho "x-ms-documentdb-enable-scan" na API REST ou a opção de solicitação "EnableScanInQuery" usando o SDK do .NET. Se houver outros filtros na consulta nos quais o Azure Cosmos DB pode usar o índice para filtrar, nenhum erro será retornado.

- Por padrão, um erro será retornado para consultas espaciais se não houver um índice espacial ou outros filtros que possam ser exibidos a partir do índice. Essas consultas podem ser executadas como um exame, usando x-ms-documentdb-enable-scan ou EnableScanInQuery.

## <a name="index-precision"></a>Precisão de índice

> [!NOTE]
> Os contêineres do Azure Cosmos dão suporte a um novo layout de índice que não exige mais uma precisão de índice personalizado que não seja o valor de precisão máxima (-1). Com esse método, os caminhos sempre são indexados com a precisão máxima. Se você especificar um valor de precisão na política de indexação, as solicitações CRUD em um contêiner ignorarão silenciosamente o valor de precisão; a resposta do contêiner conterá somente o valor de precisão máxima (-1).  Por padrão, todos os novos contêineres do Cosmos usam o novo layout de índice.

- É possível usar a precisão do índice para fazer uma troca entre a sobrecarga do armazenamento de índice e o desempenho da consulta. Para números, recomendamos usar a configuração de precisão padrão -1 (máximo). Devido aos números serem de 8 bytes em JSON, isso será equivalente a uma configuração de 8 bytes. Escolher um valor menor para precisão, como 1 a 7, significa que os valores dentro de alguns intervalos podem ser mapeados para a mesma entrada de índice. Portanto, o espaço de armazenamento do índice é reduzido, mas a execução da consulta talvez necessite processar mais itens. Consequentemente, consumirá mais taxa de transferência/RUs.

- A precisão do índice tem uma aplicação mais prática com intervalos de cadeia de caracteres. Como as cadeias de caracteres podem ter qualquer comprimento arbitrário, a escolha da precisão do índice pode afetar o desempenho das consultas de intervalo de cadeias de caracteres. Isso também pode afetar a quantidade de espaço de armazenamento de índice necessária. Os índices do intervalo de cadeia de caracteres podem ser configurados com uma precisão de índice entre 1 e 100 ou -1 (máximo). Caso queira realizar consultas ORDER BY em propriedades de cadeias de caracteres, será necessário especificar uma precisão de -1 para os caminhos correspondentes.

- Os índices espaciais sempre usam a precisão de índice padrão para todos os tipos (Ponto, LineString e Polígono). A precisão de índice padrão para índices espaciais não pode ser substituída.

O Azure Cosmos DB retorna um erro quando uma consulta usa ORDER BY, mas não tem um índice do Intervalo no caminho consultado com a precisão máxima.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre indexação no Azure Cosmos DB, consulte os artigos a seguir:

- [Visão geral da indexação](index-overview.md)
- [Política de indexação](indexing-policies.md)
- [Caminhos de índices](index-paths.md)

