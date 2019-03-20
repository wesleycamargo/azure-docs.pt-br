---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: bb73f58c5dca5e49cdb075b046f883ffeb77c95c
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203279"
---
## <a name="test-your-code"></a>Testar seu código

### <a name="test-with-node"></a>Testar com Node

Se você não estiver usando o Visual Studio, verifique se o servidor Web está iniciado.

1. Configure o servidor para escutar uma porta TCP com base no local do seu arquivo **index.html**. Para o Node, inicie o servidor Web para escutar a porta executando os comandos a seguir em um prompt de linha de comando da pasta de aplicativo:

    ```bash
    npm install
    node server.js
    ```
1. Abra o navegador e digite http://<span></span>localhost:30662 ou http://<span></span>localhost:{port} em que **port** é a porta que o seu servidor Web está escutando. Você deve ver o conteúdo do seu arquivo index.html e o botão **Entrar**.

<p/><!-- -->

### <a name="test-with-visual-studio"></a>Testar com Visual Studio

Se você estiver usando o Visual Studio, verifique se selecionou a solução de projeto e pressione **F5** para executar o projeto. O navegador abre para o local http://<span></span>localhost:{port} e você visualiza o botão **Entrar**.

## <a name="test-your-application"></a>Teste seu aplicativo

Depois que o navegador carregar seu arquivo index.html, clique em **Entrar**. Você será solicitado a entrar com o ponto de extremidade do Azure AD (Microsoft Azure Active Directory) v2.0:

![Entre na sua conta SPA do JavaScript](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Fornecer autorização para acesso de aplicativo

Na primeira vez em que entrar no seu aplicativo, você também será solicitado a fornecer o consentimento para permitir que o aplicativo acesse seu perfil e entrar no:

![Forneça sua autorização para acesso do aplicativo](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Veja os resultados de aplicativo

Depois de entrar, você deverá ver suas informações de perfil do usuário retornadas na resposta da API do Microsoft Graph exibida na página.

![Resultados esperados da chamada à API do Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo `user.read` para ler um perfil do usuário. Este escopo é automaticamente adicionado por padrão em cada aplicativo registrado no portal de registro. Outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. Por exemplo, a API do Microsoft Graph requer o escopo `Calendars.Read` para listar os calendários do usuário.

Para acessar os calendários do usuário no contexto de um aplicativo, adicione a permissão delegada `Calendars.Read` às informações de registro de aplicativo. Em seguida, adicione o escopo `Calendars.Read` à chamada `acquireTokenSilent`.

>[!NOTE]
>Talvez o usuário precise fornecer autorizações adicionais à medida que o número de escopos aumentar.

Se uma API de back-end não exigir um escopo (não recomendado), você poderá usar a `clientId` como o escopo nas chamadas para obter tokens.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]