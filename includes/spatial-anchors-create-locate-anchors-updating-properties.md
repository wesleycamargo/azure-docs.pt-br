---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631959"
---
## <a name="update-properties"></a>Atualizar propriedades

Para atualizar as propriedades em uma âncora, você deve usar o `UpdateAnchorProperties()` método. Se dois ou mais dispositivos tentarem atualizar as propriedades para a mesma âncora ao mesmo tempo, usamos um modelo de simultaneidade otimista. O que significa que a primeira gravação será a vencedora.  Todas as outras gravações obterá um erro de "Simultaneidade": uma atualização das propriedades seria necessários antes de tentar novamente.
