---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: acb9bdf294dd66005df203f957c155540b658698
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33830095"
---
## <a name="access-the-media-services-api"></a>Instalar a API de Serviços de Mídia

Use a autenticação de entidade de serviço do Azure AD para se conectar às APIs dos Serviços de Mídia do Azure. O comando a seguir cria um aplicativo do Azure AD e anexa uma entidade de serviço à conta. Você usará os valores retornados para configurar o aplicativo .NET, conforme mostrado na etapa a seguir.

Antes de executar o script, você pode substituir `amsaccount` e `amsResourceGroup` pelos nomes escolhidos na hora de criar esses recursos. `amsaccount` é o nome da conta dos Serviços de Mídia do Azure em que a entidade de serviço será anexada. <br/>O comando a seguir usa a opção `xml` que retorna um xml para ser colado em app.config. Se você omitir a opção `xml`, a resposta será em `json`.

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```

O comando produzirá uma resposta semelhante a esta:

```xml
<add key="Region" value="West US 2" />
<add key="ResourceGroup" value="amsResourceGroup" />
<add key="AadEndpoint" value="https://login.microsoftonline.com" />
<add key="AccountName" value="amsaccount" />
<add key="SubscriptionId" value="111111111-0000-2222-3333-55555555555" />
<add key="ArmAadAudience" value="https://management.core.windows.net/" />
<add key="AadTenantId" value="2222222222-0000-2222-3333-6666666666666" />
<add key="AadSecret" value="33333333-0000-2222-3333-55555555555" />
<add key="AadClientId" value="44444444-0000-2222-3333-55555555555" />
<add key="ArmEndpoint" value="https://management.azure.com/" />
```

### <a name="configure-the-sample-app"></a>Configurar o aplicativo de exemplo

Para executar o aplicativo e acessar as APIs dos Serviços de Mídia, você precisará especificar os valores de acesso corretos em App.config. 

1. Abra o Visual Studio.
2. Navegue até a solução clonada.
3. No Gerenciador de Soluções, desdobre o projeto *EncodeAndStreamFiles*.
4. Defina esse projeto como o projeto de inicialização.
5. Abra App.config.
6. Substitua os valores de appSettings pelos valores que você obteve na etapa anterior.

 ```xml
 <add key="Region" value="value" />
 <add key="ResourceGroup" value="value" />
 <add key="AadEndpoint" value="value" />
 <add key="AccountName" value="value" />
 <add key="SubscriptionId" value="value" />
 <add key="ArmAadAudience" value="value" />
 <add key="AadTenantId" value="value" />
 <add key="AadSecret" value="value" />
 <add key="AadClientId" value="value" />
 <add key="ArmEndpoint" value="value" />
 ```    
 
7. Pressione Ctrl+Shift+B para criar a solução.
