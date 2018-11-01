---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: d44865dc3189a7f9dc05106baf9f4d120e5e8bf6
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133316"
---
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure, como os aplicativos de funções, bancos de dados e contas de armazenamento, são implantados e gerenciados.

O seguinte exemplo cria um grupo de recursos chamado `myResourceGroup`.  
Se você não estiver usando o Cloud Shell, primeiro você deverá entrar usando `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Em geral, você cria seu grupo de recursos e os recursos em uma região próxima a você. Para ver todos os locais com suporte para planos do Serviço de Aplicativo, execute o comando [az appservice list-locations](/cli/azure/appservice#az-appservice-list-locations).