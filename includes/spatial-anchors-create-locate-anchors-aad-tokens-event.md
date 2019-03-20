---
ms.openlocfilehash: 307e4d4e21eca3a568a7e4761058b67aa7d5236f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751659"
---
Como com tokens de acesso, se um token do AAD não for definido, você deve tratar o evento TokenRequired ou implementar o método tokenRequired no protocolo delegado.

Você pode manipular o evento de forma síncrona, definindo a propriedade nos argumentos de evento.
