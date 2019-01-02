---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 71d19b5d922616944c176cdda98470607c515af9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111660"
---
| Recurso | Limite |
| --- | --- |
| Tamanho do cache |530 GB |
| Bancos de dados |64 |
| Máx. de clientes conectados |40.000 |
| Réplicas de Cache Redis do Azure (para alta disponibilidade) |1 |
| Fragmentos em um cache premium com clustering |10 |

Os limites e tamanhos do Cache Redis do Azure são diferentes para cada camada de preços. Para ver os tipos de preço e seus tamanhos associados, confira [Cache Redis do Azure para Preços Redis](https://azure.microsoft.com/pricing/details/cache/).

Para obter informações sobre os limites de configuração do Cache Redis do Azure, veja [Configuração padrão do servidor do Redis](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Como configuração e gerenciamento de instâncias do Cache Redis do Azure é feito pela Microsoft, nem todos os comandos do Redis são compatíveis no Cache Redis do Azure. Para obter mais informações, confira [Comandos Redis não têm suporte no Cache Redis do Azure](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

