---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/18/2018
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 5c1bcdb3ad524040fe4c74c58938305cceee7762
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395980"
---
[!INCLUDE [resource group intro text](resource-group.md)]

No Cloud Shell, crie um grupo de recursos com o comando [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create). O exemplo a seguir cria um grupo de recursos nomeado *myResourceGroup* no localização *Centro-Sul dos EUA*. Para ver todos os locais com suporte para o Serviço de Aplicativo no nível **Gratuito**, execute o comando [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Em geral, você cria seu grupo de recursos e os recursos em uma região próxima a você. 

Quando o comando for concluído, uma saída JSON mostra as propriedades do grupo de recursos.