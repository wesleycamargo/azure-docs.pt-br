---
title: "Introdução ao Android no Azure AD v2 – Testar | Microsoft Docs"
description: Como um aplicativo do Android pode obter um token de acesso e chamar a API do Microsoft Graph ou APIs que exigem tokens de acesso por meio do ponto de extremidade do Azure Active Directory v2
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 85f9b475a77af9865882f4193c602756e345cc02
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
## <a name="test-your-code"></a>Testar seu código

1. Implante o código no dispositivo/emulador.
2. Quando você estiver pronto para testar, use uma conta do Microsoft Azure Active Directory (conta organizacional) ou uma Conta da Microsoft (live.com, outlook.com) para se conectar. 

![Captura de tela de exemplo](media/active-directory-mobileanddesktopapp-android-test/mainwindow.png)
<br/><br/>
![Conexão](media/active-directory-mobileanddesktopapp-android-test/usernameandpassword.png)

### <a name="consent"></a>Consentimento
Na primeira vez que um usuário entrar no aplicativo, será apresentada uma tela de consentimento semelhante à tela abaixo, na qual ele precisa aceitar explicitamente: 

![Consentimento](media/active-directory-mobileanddesktopapp-android-test/androidconsent.png)


### <a name="expected-results"></a>Resultados esperados
Você deverá ver os resultados de uma chamada ao ponto de extremidade “me” da API do Microsoft Graph usado para obter o perfil do usuário – https://graph.microsoft.com/v1.0/me. Para obter uma lista de pontos de extremidade comuns do Microsoft Graph, consulte este [artigo](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas
A API do Graph precisa do escopo `user.read` para ler o perfil do usuário. Esse escopo é adicionado por padrão a todos os aplicativos registrados por meio de nosso portal de registro. Algumas outras APIs do Graph, bem como APIs personalizadas do servidor de back-end, exigem escopos adicionais. Por exemplo, para o Graph, `Calendars.Read` é necessário para listar os calendários do usuário. Para acessar o calendário do usuário no contexto de um aplicativo, você precisa adicionar as informações de registro desse aplicativo delegado e, em seguida, adicionar `Calendars.Read` à chamada `AcquireTokenAsync`. O usuário pode precisar fornecer consentimento adicional à medida que o número de escopos aumenta.

Se uma API de back-end não exigir um escopo (não recomendado), você poderá usar a `ClientId` como o escopo na chamada `AcquireTokenAsync`.
<!--end-collapse-->

