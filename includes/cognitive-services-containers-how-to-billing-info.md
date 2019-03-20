---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2019
ms.openlocfilehash: 200e2dfd2dd4f9aedd9256b307491a0b207ea124
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57964055"
---
Consultas para o contêiner são cobradas ao tipo de preço do recurso do Azure usado para o `<ApiKey>`.

Contêineres de serviços cognitivos não são licenciados para execução sem estar conectado ao ponto de extremidade de cobrança para a medição. Os clientes precisam habilitar contêineres para comunicar informações de cobrança com a cobrança do ponto de extremidade em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft. 

### <a name="connecting-to-azure"></a>Conectar-se ao Azure

O contêiner precisa os valores de argumento de cobrança para ser executado. Esses valores permitem que o contêiner para se conectar ao ponto de extremidade de cobrança. O contêiner relata o uso a cada 10 a 15 minutos. Se o contêiner não se conectar dentro da janela de tempo permitido para o Azure, o contêiner continuará executada, mas não atender a consultas até que o ponto de extremidade de cobrança seja restaurado. A conexão for tentada 10 vezes no mesmo intervalo de tempo de 10 a 15 minutos. Se ele não pode se conectar ao ponto de extremidade cobrança dentro de 10 tentativas, o contêiner de execução será interrompida. 

### <a name="billing-arguments"></a>Argumentos de cobrança

Todas as três opções a seguir devem ser especificados com os valores válidos para que o `docker run` comando para iniciar o contêiner:

| Opção | Descrição |
|--------|-------------|
| `ApiKey` | A chave de API do recurso de Serviço Cognitivo usada para rastrear informações de cobrança.<br/>O valor dessa opção deve ser definido como uma chave de API para o recurso provisionado especificado em `Billing`. |
| `Billing` | O ponto de extremidade do recurso de Serviço Cognitivo usado para rastrear informações de cobrança.<br/>O valor dessa opção deve ser definido como o URI do terminal de um recurso LUIS do Azure provisionado.|
| `Eula` | Indica que você aceitou a licença para o contêiner.<br/>O valor dessa opção deve ser definido como `accept`. |


