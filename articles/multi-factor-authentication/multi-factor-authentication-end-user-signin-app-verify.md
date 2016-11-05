
---
title: Conectando-se ao aplicativo móvel usando um código de verificação com o Azure Multi-Factor Authentication
description: Esta página descreverá como um usuário pode se conectar usando um código de verificação do aplicativo móvel com o Azure MFA.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtland

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2016
ms.author: kgremban

---
# Conectando-se ao aplicativo móvel usando um código de verificação com o Azure Multi-Factor Authentication
As informações a seguir descrevem a experiência de usar a autenticação multifator com seu aplicativo móvel quando você a está usando com um código de verificação.

## Para se conectar usando um código de verificação com seu aplicativo móvel
<ol>

<li>Entre em um aplicativo ou serviço, como o Office 365, usando seu nome de usuário e senha.</li>
<li>A Microsoft solicitará um código de verificação.</li>


<center>![Configuração](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)</center>

<li>Abra o aplicativo Azure Authenticator no seu telefone e digite o código na caixa de conexão.</li>

<center>![Configuração](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)</center>


<li>Agora você deve estar conectado.</li>

<!---HONumber=AcomDC_0921_2016-->