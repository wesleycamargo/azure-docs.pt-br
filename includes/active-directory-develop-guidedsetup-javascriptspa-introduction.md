---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 0aadfd809fefa73af6869d496f6d88d6dc9df59e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36269571"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Chamar a API do Microsoft Graph de um SPA (Aplicativo de Página Única) JavaScript

Este guia demonstra como um SPA (Aplicativo de Página Única) JavaScript pode entrar em contas pessoais, corporativas e de estudante, obter um token de acesso e chamar a API do Microsoft Graph ou outras APIs que exigem tokens de acesso do ponto de extremidade do Azure Active Directory v2.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona

![Como o aplicativo de exemplo gerado por este guia funciona](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Mais informações

O aplicativo de exemplo criado por este guia permite que um SPA JavaScript consulte a API do Microsoft Graph ou uma API Web que aceita tokens do ponto de extremidade do Azure Active Directory v2. Para esse cenário, depois que um usuário se conecta, um token de acesso é adicionado às solicitações HTTP por meio do cabeçalho de autorização. A aquisição e a renovação de tokens são manipuladas pela MSAL (Biblioteca de Autenticação da Microsoft).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotecas

Este guia usa a seguinte biblioteca:

|Biblioteca|DESCRIÇÃO|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Versão prévia da Biblioteca de Autenticação da Microsoft para JavaScript|

> [!NOTE]
> *msal.js* usa como destino o *ponto de extremidade do Azure Active Directory v2*, que permite que contas pessoais, corporativas e de estudante entrem e adquiram tokens. O *ponto de extremidade do Azure Active Directory v2* tem [algumas limitações](..\articles\active-directory\develop\active-directory-v2-limitations.md). Se você estiver interessado apenas em contas corporativas e de estudante, use *adal.js* e o *ponto de extremidade V1*. Para entender as diferenças entre os pontos de extremidade v1 e v2, leia a [comparação v1-v2](..\articles\active-directory\develop\active-directory-v2-compare.md).

<!--end-collapse-->
