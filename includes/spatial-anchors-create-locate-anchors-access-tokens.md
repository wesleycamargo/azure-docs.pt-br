---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 0e4c0b6d622fe0d8d20692ec6c0c889779e4601a
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751609"
---
### <a name="access-tokens"></a>Tokens de Acesso

Tokens de acesso são um método mais robusto para autenticar com âncoras espacial do Azure. Especialmente à medida que você prepare seu aplicativo para uma implantação de produção. O resumo dessa abordagem é configurar um serviço de back-end que seu aplicativo cliente pode autenticar com segurança. Suas interfaces de serviço de back-end com o AAD em tempo de execução e com o serviço de Azure espacial âncoras STS para solicitar um Token de acesso. Esse token é, em seguida, entregue ao aplicativo cliente e usado no SDK para autenticar com âncoras espacial do Azure.
