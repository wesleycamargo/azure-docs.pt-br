---
title: Tipos de índice no Azure Cosmos DB
description: Visão geral dos tipos de índice no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: 02055ec07de2b08abdc949e17c668912431e00ce
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55871244"
---
# <a name="index-types-in-azure-cosmos-db"></a>Tipos de índice no Azure Cosmos DB

Há várias opções nas quais você configura a política de indexação para um caminho. Você pode especificar uma ou mais definições de indexação para cada caminho:

- **Tipo de dados:** Cadeia de caracteres, Número, Ponto, Polígono ou LineString (pode conter apenas uma entrada por tipo de dados por caminho).

- **Tipo de índice:** Hash (consultas de igualdade), Intervalo (consultas ORDER BY, intervalo ou igualdade) ou Espacial (consultas espaciais).

- **Precisão:** Para um índice de hash, isso varia de 1 a 8 para cadeias de caracteres e números e o valor padrão é 3. Para um índice de intervalo, o valor de precisão máxima é -1. Pode variar entre 1 e 100 (precisão máxima) para valores de cadeia de caracteres ou números.

## <a name="index-kind"></a>Tipo de índice

O Azure Cosmos DB dá suporte a índice de hash e índice de intervalo para cada caminho que pode ser configurado para os tipos de dados de Cadeia de Caracteres ou Número, ou ambos.

- O **índice de hash** dá suporte a igualdade eficiente e consultas JOIN. Na maioria dos casos de uso, os índices de Hash não exigem uma precisão maior do que o valor padrão de 3 bytes. O tipo de dados pode ser Cadeia de Caracteres ou Número.

  > [!NOTE]
  > Os contêineres do Azure Cosmos dão suporte a um novo layout de índice que não usa mais o tipo de índice de Hash. Se você especificar um tipo de índice de Hash na política de indexação, as solicitações CRUD no contêiner ignorarão silenciosamente o tipo de índice; a resposta do contêiner conterá somente o tipo de índice de intervalo. Por padrão, todos os novos contêineres do Cosmos usam o novo layout de índice. 
  
- O **índice de intervalo** dá suporte a consultas de igualdade eficientes, consultas de intervalo (usando >, <, >=, <=, !=) e consultas ORDER BY. Por padrão,consultas ORDER By também requerem precisão máxima de índice (-1). O tipo de dados pode ser Cadeia de Caracteres ou Número.

- O **índice espacial** dá suporte a consultas espaciais (interna e de distância) eficientes. Os tipos de dados podem ser Ponto, Polígono ou LineString. O Azure Cosmos DB também dá suporte ao tipo de índice espacial em todos os caminhos que podem ser especificados para os tipos de dados Ponto, Polígono ou LineString. O valor no caminho especificado deve ser um fragmento GeoJSON válido, assim como {"type": "Point", "coordinates": [0.0, 10.0]}. O Azure Cosmos DB dá suporte à indexação automática de tipos de dados Ponto, Polígono e LineString.

A seguir, são apresentados exemplos de consultas em que os índices de Hash, Intervalo e Espaciais podem ser usados:

| **Tipo de índice** | **Descrição/caso de uso** |
| ---------- | ---------------- |
| Hash  | O hash em /prop/? (or /) pode ser usado para servir às seguintes consultas eficientemente:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Hash em /props/[]/? (or / or /props/) pode ser usado para servir às consultas a seguir eficientemente:<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5  |
| Intervalo  | O intervalo em over /prop/? (or /) pode ser usado para servir às seguintes consultas eficientemente:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop   |
| Espacial     | O intervalo em over /prop/? (or /) pode ser usado para servir às seguintes consultas eficientemente:<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Point", ... }) --com a indexação nos pontos habilitada<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Polygon", ... }, c.prop) --com a indexação em polígonos habilitada.     |

## <a name="default-behavior-of-index-kinds"></a>Comportamento padrão de tipos de índice

- Se não houver índices de intervalo de qualquer precisão para sinalizar que um exame poderá ser necessário para atender à consulta, nesse caso, por padrão, um erro será retornado para consultas com operadores de intervalo, como >=.

- As consultas de intervalo podem ser realizadas sem um índice do Intervalo, usando o cabeçalho "x-ms-documentdb-enable-scan" na API REST ou a opção de solicitação "EnableScanInQuery" usando o SDK do .NET. Se houver outros filtros na consulta nos quais o Azure Cosmos DB pode usar o índice para filtrar, nenhum erro será retornado.

- Por padrão, um erro será retornado para consultas espaciais se não houver um índice espacial ou outros filtros que possam ser exibidos a partir do índice. Essas consultas podem ser executadas como um exame, usando x-ms-documentdb-enable-scan ou EnableScanInQuery.

## <a name="index-precision"></a>Precisão de índice

- É possível usar a precisão do índice para fazer uma troca entre a sobrecarga do armazenamento de índice e o desempenho da consulta. Para números, recomendamos usar a configuração de precisão padrão -1 (máximo). Devido aos números serem de 8 bytes em JSON, isso será equivalente a uma configuração de 8 bytes. Escolher um valor menor para precisão, como 1 a 7, significa que os valores dentro de alguns intervalos podem ser mapeados para a mesma entrada de índice. Portanto, o espaço de armazenamento do índice é reduzido, mas a execução da consulta talvez necessite processar mais itens. Consequentemente, consumirá mais taxa de transferência/RUs.

- A precisão do índice tem uma aplicação mais prática com intervalos de cadeia de caracteres. Como as cadeias de caracteres podem ter qualquer comprimento arbitrário, a escolha da precisão do índice pode afetar o desempenho das consultas de intervalo de cadeias de caracteres. Isso também pode afetar a quantidade de espaço de armazenamento de índice necessária. Os índices do intervalo de cadeia de caracteres podem ser configurados com uma precisão de índice entre 1 e 100 ou -1 (máximo). Caso queira realizar consultas ORDER BY em propriedades de cadeias de caracteres, será necessário especificar uma precisão de -1 para os caminhos correspondentes.

- Os índices espaciais sempre usam a precisão de índice padrão para todos os tipos (Ponto, LineString e Polígono). A precisão de índice padrão para índices espaciais não pode ser substituída.

O Azure Cosmos DB retorna um erro quando uma consulta usa ORDER BY, mas não tem um índice do Intervalo no caminho consultado com a precisão máxima.

> [!NOTE]
> Os contêineres do Azure Cosmos dão suporte a um novo layout de índice que não exige mais uma precisão de índice personalizado que não seja o valor de precisão máxima (-1). Com esse método, os caminhos sempre são indexados com a precisão máxima. Se você especificar um valor de precisão na política de indexação, as solicitações CRUD em um contêiner ignorarão silenciosamente o valor de precisão; a resposta do contêiner conterá somente o valor de precisão máxima (-1).  Por padrão, todos os novos contêineres do Cosmos usam o novo layout de índice.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre indexação no Azure Cosmos DB, consulte os artigos a seguir:

- [Visão geral da indexação](index-overview.md)
- [Política de indexação](indexing-policies.md)
- [Caminhos de índices](index-paths.md)

