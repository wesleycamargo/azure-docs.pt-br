---
ms.openlocfilehash: 430302d8b1b2a01febbbe2f11057bb331ec80c28
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907627"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Localize uma âncora de nuvem espacial

Para localizar as âncoras de nuvem espacial, você precisará saber seus identificadores. As IDs de âncora podem ser armazenados no serviço de back-end do seu aplicativo e acessível a todos os dispositivos que podem autenticar corretamente a ele. Para obter um exemplo desse tipo, consulte [Tutorial: Compartilhar âncoras espacial em todos os dispositivos](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Criar uma instância de um objeto AnchorLocateCriteria, defina os identificadores que você está procurando e invoca o método de CreateWatcher na sessão, fornecendo seu AnchorLocateCriteria.
