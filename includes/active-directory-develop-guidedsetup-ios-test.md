---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: d8d7d5649ca1dc215f85f928d111ff1367c60bb5
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988222"
---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Testar a consulta da API do Microsoft Graph de seu aplicativo iOS

Para executar o código no simulador, pressione **Comando** + **R**.

![Testar o aplicação no simulador](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

Quando estiver pronto para testar, selecione **Chamar a API do Microsoft Graph**. Quando solicitado, digite seu nome de usuário e senha.

### <a name="provide-consent-for-application-access"></a>Fornecer autorização para acesso de aplicativo

Na primeira vez em que entrar no seu aplicativo, você também será solicitado a fornecer o consentimento para permitir que o aplicativo acesse seu perfil e entrar no:

![Forneça sua autorização para acesso do aplicativo](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>Veja os resultados de aplicativo
Depois de entrar, você deverá ver suas informações de perfil de usuário retornadas pela chamada da API do Microsoft Graph na seção de **Log**. 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo **user.read** para ler o perfil do usuário. Este escopo é automaticamente adicionado por padrão em cada aplicativo registrado no portal de registro. Outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. A API do Microsoft Graph requer o escopo **Calendars.Read** para listar os calendários do usuário.

Para acessar os calendários do usuário no contexto de um aplicativo, adicione a permissão delegada **Calendars.Read** às informações de registro do aplicativo. Em seguida, adicione o escopo **Calendars.Read** à chamada **acquireTokenSilent**.

>[!NOTE]
>Talvez o usuário precise fornecer autorizações adicionais à medida que o número de escopos aumentar.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
