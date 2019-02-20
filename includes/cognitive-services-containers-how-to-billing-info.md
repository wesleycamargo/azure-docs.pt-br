---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 002/08/2019
ms.openlocfilehash: ce7d8628c28a4a202a05aeea60e71655b4b7f1a2
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984880"
---
Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres dos Serviços Cognitivos não enviam dados do cliente (a declaração) para a Microsoft. O contêiner relata o uso a cada 10 a 15 minutos.

O `docker run` usa os seguintes argumentos para fins de cobrança:

| Opção | DESCRIÇÃO |
|--------|-------------|
| `ApiKey` | A chave de API do recurso de Serviço Cognitivo usada para rastrear informações de cobrança.<br/>O valor dessa opção deve ser definido como uma chave de API para o recurso provisionado especificado em `Billing`. |
| `Billing` | O ponto de extremidade do recurso de Serviço Cognitivo usado para rastrear informações de cobrança.<br/>O valor dessa opção deve ser definido como o URI do terminal de um recurso LUIS do Azure provisionado.|
| `Eula` | Indica que você aceitou a licença para o contêiner.<br/>O valor dessa opção deve ser definido como `accept`. |

> [!IMPORTANT]
> Todas as três opções devem ser especificadas com valores válidos ou o contêiner não será iniciado.
