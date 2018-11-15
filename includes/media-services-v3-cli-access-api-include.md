---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/11/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 4dde0a47f0452da2dd951df86ccb6e02a44521ed
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616632"
---
## <a name="access-the-media-services-api"></a>Instalar a API de Serviços de Mídia

Use a autenticação de entidade de serviço do Azure AD para se conectar às APIs dos Serviços de Mídia do Azure. O comando a seguir cria um aplicativo do Azure AD e anexa uma entidade de serviço à conta. Você deve usar os valores retornados para configurar seu aplicativo.

Antes de executar o script, você pode substituir `amsaccount` e `amsResourceGroup` pelos nomes escolhidos na hora de criar esses recursos. `amsaccount` é o nome da conta dos Serviços de Mídia do Azure em que a entidade de serviço será anexada.

O comando a seguir retorna uma saída `json`:

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Esse comando produz uma resposta semelhante a esta:

```json
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-0000-0000-0000-000000000000",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": "amsaccount",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup",
  "SubscriptionId": "00000000-0000-0000-0000-000000000000"
}
```

Se você gostaria de obter `xml` na resposta, use o seguinte comando:

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
