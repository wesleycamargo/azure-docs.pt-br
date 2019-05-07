---
title: Nós no banco de dados do Azure para PostgreSQL – em hiperescala (Citus) (visualização)
description: Os dois tipos de nós em um grupo de servidores.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: c6b948ed63f43f1597103d123be5ed39f42bd276
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077269"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Nós no banco de dados do Azure para PostgreSQL – em hiperescala (Citus) (visualização)

Hiperescala (Citus) (visualização) tipo de hospedagem permite que o banco de dados do Azure para servidores PostgreSQL (chamados de nós) para coordenar entre si em uma arquitetura de "nada compartilhado". Os nós em um grupo de servidores, coletivamente, armazenar mais dados e usam mais núcleos de CPU que seria possível em um único servidor. A arquitetura também permite que o banco de dados dimensionar adicionando mais nós ao grupo de servidores.

## <a name="coordinator-and-workers"></a>Coordenador e trabalhadores

Cada grupo de servidor tem um nó coordenador e vários trabalhadores. Os aplicativos enviam suas consultas para o nó coordenador, que retransmite para os trabalhadores relevantes e acumula seus resultados. Aplicativos não são capazes de se conectar diretamente aos trabalhadores.

Para cada consulta, o coordenador ou encaminhá-la a um nó único trabalhador ou paraleliza-lo em vários dependendo se os dados necessários residem em um nó único ou múltiplo. O coordenador decide o que fazer consultando as tabelas de metadados. Essas tabelas acompanham os nomes DNS e integridade de nós de trabalho e a distribuição de dados entre nós.

## <a name="next-steps"></a>Próximas etapas
- Saiba como armazenam nós [dados distribuídos](concepts-hyperscale-distributed-data.md)
