---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631986"
---
Como com tokens de acesso, se um token do AD do Azure não for definido, você deve tratar o evento TokenRequired ou implementar o método tokenRequired no protocolo delegado.

Você pode manipular o evento de forma síncrona, definindo a propriedade nos argumentos de evento.
