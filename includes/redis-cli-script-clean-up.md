---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 23b92f7d1b1c3ef488e182a6443f275365a03d9a
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571725"
---
## <a name="clean-up-deployment"></a>Limpar a implantação 

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos, instância do Cache Redis do Azure e todos os recursos relacionados no grupo de recursos.

```azurecli
az group delete --name contosoGroup
```