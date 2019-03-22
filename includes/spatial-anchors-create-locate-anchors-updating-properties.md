---
ms.openlocfilehash: eafe71902ee849c310847f4537214722e129ad42
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907602"
---
## <a name="update-properties"></a>Atualizar propriedades

Para atualizar as propriedades em uma âncora, use o método UpdateAnchorProperties. Se dois ou mais dispositivos tentarem atualizar as propriedades para a mesma âncora ao mesmo tempo, usamos um modelo de simultaneidade otimista. O que significa que a primeira gravação será a vencedora.  Todas as outras gravações obterá um erro de "Simultaneidade": uma atualização das propriedades seria necessários antes de tentar novamente.
