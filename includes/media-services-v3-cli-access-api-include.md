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
ms.openlocfilehash: 7454e96a2a05bf89a0455674a4f144534c374c71
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38733279"
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
  "AadClientId": "00000000-4cdd-418a-8a72-0755ace03de5",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-02f5-4bf2-9057-1c4f7baff155",
  "AadTenantId": "00000000-86f1-41af-91ab-2d7cd011db47",
  "AccountName": "amsaccount22",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup2",
  "SubscriptionId": "00000000-6753-4ca2-b1ae-193798e2c9d8"
}
```

Se você gostaria de obter `xml` na resposta, use o seguinte comando:

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
