---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/21/2018
ms.author: wesmc
ms.openlocfilehash: dd9700c9472e07daf294eca12b766e3dc4832955
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111714"
---
### <a name="cacheskuname"></a>cacheSKUName
O tipo de preço do novo Cache do Azure para Redis.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },

O modelo define os valores que são permitidos para esse parâmetro (Basic ou Standard) e atribui um valor padrão (Basic) se nenhum valor for especificado. Basic fornece um único nó com vários tamanhos disponíveis, até 53 GB.
Standard fornece Principal/Réplica de dois nós com vários tamanhos disponíveis, até 53 GB e SLA de 99,9%.

### <a name="cacheskufamily"></a>cacheSKUFamily
A família do SKU.

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
O tamanho da nova instância do Cache do Azure para Redis. 

    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }


O modelo define os valores que são permitidos para esse parâmetro (0, 1, 2, 3, 4, 5 ou 6) e atribui um valor padrão (0) se nenhum valor é especificado. Esses números correspondem aos tamanhos de cache a seguir: 0 = 250 MB, 1 = 1 GB, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

