---
ms.openlocfilehash: 10eb16dc1757255c77ed8967dc132f42660e070f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57907609"
---
## <a name="provide-frames-to-the-session"></a>Fornecer quadros à sessão

A sessão de âncora espacial funciona, mapeando o espaço em torno do usuário. Isso ajuda a determinar onde se encontram as âncoras. Plataformas móveis (iOS e Android) requerem uma chamada de nativa para o feed para obter quadros da biblioteca de AR de sua plataforma de câmera. Em contraste, HoloLens está constantemente verificando o ambiente, portanto, não há nenhuma necessidade de uma chamada específica, como com o Mobile.