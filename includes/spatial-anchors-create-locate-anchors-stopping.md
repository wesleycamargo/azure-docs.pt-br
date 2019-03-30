---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632014"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausar, redefinir ou parar a sessão

Para parar a sessão temporária, você pode invocar `Stop()`. Isso irá parar qualquer processamento de ambiente e observadores, mesmo se você invocar ProcessFrame(). Em seguida, você pode invocar `Start()` para continuar o processamento. Ao retomar, dados de ambiente já capturados na sessão são mantidos.
