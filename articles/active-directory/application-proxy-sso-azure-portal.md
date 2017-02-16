---
title: "Logon único para aplicativos com o Proxy de Aplicativo do Azure AD | Microsoft Docs"
description: "Ative o logon único para aplicativos locais publicados com o Proxy de Aplicativo do Azure AD no Portal do Azure."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: asteen
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1fcec43ad50b9c78443ada116b9ca444605c4730
ms.openlocfilehash: fa2a2f8ee61b6edf92db8d1f92ab9aca8bc673c5

---


# <a name="provide-single-sign-on-with-azure-ad-application-proxy---public-preview"></a>Fornecer logon único com o Proxy de Aplicativo do Azure AD - Visualização pública

O Proxy de Aplicativo do Azure Active Directory o ajuda a aprimorar a produtividade ao publicar aplicativos locais de forma que funcionários remotos também possam acessá-los com segurança. No Portal do Azure, você também pode configurar o logon único (SSO) para esses aplicativos. Agora, os usuários só precisam autenticar com o Azure AD, assim podem acessar seu aplicativo corporativo sem precisar entrar novamente.

Neste artigo, usaremos o exemplo de um aplicativo baseado em senha para mostrar como o cofre de senha fornece uma experiência de SSO simples. 

Você já deve ter publicado e testado seu aplicativo com o Proxy de Aplicativo. Caso contrário, siga as etapas em [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD - Visualização pública](application-proxy-publish-azure-portal.md) e depois volte para este artigo. 

Ou, se você for iniciante com o Proxy de Aplicativo, aprenda mais sobre este recurso com o artigo [Como fornecer acesso remoto seguro a aplicativos locais](active-directory-application-proxy-get-started.md).

## <a name="set-up-password-vaulting-for-your-application"></a>Definir o cofre de senha para seu aplicativo

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Selecione **Azure Active Directory** > **Aplicativos Empresariais** > **Todos os Aplicativos**.
3. Na lista, selecione o aplicativo para o qual deseja configurar o SSO. Se tiver muitos aplicativos, pode usar a caixa de pesquisa para filtrar o aplicativo correto.  
4. Na seção Gerenciar, selecione **Logon único**.

   ![Selecione Logon único](./media/application-proxy-sso-azure-portal/select-sso.png)

5. Para o modo SSO, escolha **Logon Único Baseado em Senha**.
6. Para a URL de logon, insira a URL da página em que os usuários devem inserir o nome de usuário e a senha para entrar em seu aplicativo. Esta deve ser a URL externa criada ao publicar o aplicativo por meio do Proxy de Aplicativo. 

   ![Escolha Logon Único Baseado em Senha e digite a URL](./media/application-proxy-sso-azure-portal/password-sso.png)

7. Selecione **Salvar**.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testar seu aplicativo

Vá para o [site MyApps](https://myapps.microsoft.com) e selecione o aplicativo que você acaba de configurar. Entrar com suas credenciais para o aplicativo (ou as credenciais da conta de teste que tem acesso). Depois que você entrar com êxito, deve conseguir sair do aplicativo e voltar a ele sem ter que inserir suas credenciais novamente. 

## <a name="next-steps"></a>Próximas etapas

Leia sobre outras maneiras de implementar o [Logon único com o Proxy de aplicativo](active-directory-application-proxy-sso-using-kcd.md)



<!--HONumber=Jan17_HO2-->


