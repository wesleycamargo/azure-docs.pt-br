---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/29/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: f4600413e05950446db71f988c4c4302f0dcacb3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39062946"
---
## <a name="access-the-media-services-api"></a>Instalar a API de Serviços de Mídia

Use a autenticação de entidade de serviço do Azure AD para se conectar às APIs dos Serviços de Mídia do Azure. O comando a seguir cria um aplicativo do Azure AD e anexa uma entidade de serviço à conta. Você deve usar os valores retornados para configurar seu aplicativo.

Antes de executar o script, você pode substituir `amsaccount` e `amsResourceGroup` pelos nomes escolhidos na hora de criar esses recursos. `amsaccount` é o nome da conta dos Serviços de Mídia do Azure em que a entidade de serviço será anexada.

O comando a seguir retorna uma saída `json`:

```azurecli-interactive
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

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
