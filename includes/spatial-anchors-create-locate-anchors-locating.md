---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631963"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Localize uma âncora de nuvem espacial

Ser capaz de localizar uma âncora de nuvem carregado anteriormente espacial é um dos motivos principais para usar a biblioteca âncoras espacial do Azure. Para localizar as âncoras de nuvem espacial, você precisará saber seus identificadores. As IDs de âncora podem ser armazenadas no serviço de back-end do seu aplicativo e acessível a todos os dispositivos que podem autenticar corretamente a ele. Para obter um exemplo desse tipo, consulte [Tutorial: Compartilhar âncoras espacial em todos os dispositivos](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Criar uma instância de um `AnchorLocateCriteria` do objeto, defina os identificadores que você está procurando e invocar o `CreateWatcher` método na sessão, fornecendo seu `AnchorLocateCriteria`.
