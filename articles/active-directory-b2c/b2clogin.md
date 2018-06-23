---
title: Usando b2clogin.com | Microsoft Docs
description: Saiba mais sobre como usar b2clogin.com em vez de login.microsoftonline.com.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c41c02acaeffa170d55f3c59f34a4b1ecae1c523
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712446"
---
# <a name="using-b2clogincom"></a>Usando b2clogin.com

>[!IMPORTANT]
>Esse recurso está em versão prévia 
>

Agora você tem a opção de usar o serviço do Azure AD B2C com `<YourTenantName>.b2clogin.com` em vez de usar `login.microsoftonline.com`.  Isso traz muitos benefícios:
* Você não irá mais compartilhar o mesmo limite de tamanho do cabeçalho do cookie com outros produtos da Microsoft
* Você pode remover todas as referências à Microsoft em sua URL (você pode substituir `<YourTenantName>.onmicrosoft.com` com sua ID de locatário)

 Para tirar proveito dos b2clogin.com, você precisa definir alguns dos seguintes:

1. Para **Executar o ponto de extremidade agora** verifique se você está usando `<YourTenantName>.b2clogin.com` em vez de usar `login.microsoftonline.com`.
2. Se você estiver usando MSAL, você precisa definir `validateauthority=false`.  Isso ocorre porque o emissor do token torna-se`<YourTenantName>.b2clogin.com`.