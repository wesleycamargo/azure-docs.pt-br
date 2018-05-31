---
title: Usando b2clogin.com| Microsoft Docs
description: Saiba mais sobre como usar b2clogin.com em vez de login.microsoftonline.com.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: d2737e995b91caa2dcc55027baa2b552e64af23e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33660413"
---
#<a name="using-b2clogincom"></a>Usando b2clogin.com

>[!IMPORTANT]
>Esse recurso está em versão prévia 
>

Agora você tem a opção de usar o serviço do Azure AD B2C com `<YourTenantName>.b2clogin.com` em vez de usar `login.microsoftonline.com`.  Isso traz muitos benefícios:
* Você não irá mais compartilhar o mesmo limite de tamanho do cabeçalho do cookie com outros produtos da Microsoft
* Você pode remover todas as referências à Microsoft em sua URL (você pode substituir `<YourTenantName>.onmicrosoft.com` com sua ID de locatário)

 Para tirar proveito dos b2clogin.com, você precisa definir alguns dos seguintes:

1. Para **Executar o ponto de extremidade agora** verifique se você está usando `<YourTenantName>.b2clogin.com` em vez de usar `login.microsoftonline.com`.
2. Se você estiver usando MSAL, você precisa definir `validateauthority=false`.  Isso ocorre porque o emissor do token torna-se`<YourTenantName>.b2clogin.com`.