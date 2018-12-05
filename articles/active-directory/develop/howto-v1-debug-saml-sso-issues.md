---
title: Depurar o logon único baseado em SAML – Azure Active Directory | Microsoft Docs
description: Depurar o logon único baseado em SAML para aplicativos no Azure Active Directory.
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/15/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin, dastrock, smalser
ms.openlocfilehash: 7f653eca0c768cc35df039cbd51153484710d80a
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422246"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Depurar o logon único baseado em SAML para aplicativos no Azure Active Directory

Saiba como localizar e corrigir problemas de [logon único](../manage-apps/what-is-single-sign-on.md) em aplicativos no Azure AD (Azure Active Directory) compatíveis com o [SAML (Security Assertion Markup Language) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Antes de começar
É recomendado instalar a [My Apps Secure Sign-in Extension](../user-help/active-directory-saas-access-panel-user-help.md#i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension). Essa extensão do navegador facilita a coleta de informações de solicitação e de resposta SAML que você precisa para resolver problemas de logon único. Caso você não possa instalar a extensão, este artigo mostrará como resolver problemas com e sem a extensão instalada.

Para baixar e instalar a My Apps Secure Sign-in Extension, use um dos links a seguir.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>Testar o logon único baseado em SAML

Para testar o logon único baseado em SAML entre o AAD e um aplicativo de destino:

1.  Entre no [portal do Azure](https://portal.azure.com) como um administrador global ou outro administrador autorizado a gerenciar aplicativos.
2.  Na folha da esquerda, clique em **Azure Active Directory** e, em seguida, clique em **Aplicativos empresariais**. 
3.  Na lista de aplicativos empresariais, clique no aplicativo cujo logon único deseja testar e, em seguida, nas opções à esquerda, clique em **Logon único**.
4.  Para abrir a experiência de teste de logon único baseado em SAML, na seção **Domínio e URLs** clique em **Testar configuração de SAML**. Se o botão Testar Configuração de SAML estiver esmaecido, será necessário preencher e salvar os atributos necessários primeiro.
5.  Na folha **Testar logon único**, use as credenciais corporativas para entrar no aplicativo de destino. Você pode entrar como o usuário atual ou como um usuário diferente. Se você entrar como um usuário diferente, um prompt solicitará que você se autentique.

    ![Página para testar SAML](./media/howto-v1-debug-saml-sso-issues/testing.png)


Se você tiver entrado com êxito, o teste terá sido aprovado. Nesse caso, o Azure AD emitiu um token de resposta SAML para o aplicativo. O aplicativo usou o token SAML para você entrar com êxito.

Se houver um erro na página de entrada da empresa ou na página do aplicativo, use uma das seções a seguir para resolver o erro.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Resolver um erro de entrada na página de entrada da empresa

Ao tentar entrar, você verá um erro na página de entrada da empresa. 

![Erro de entrada](./media/howto-v1-debug-saml-sso-issues/error.png)

Para depurar esse erro, você precisa da mensagem de erro e da solicitação SAML. A My Apps Secure Sign-in Extension coleta essas informações automaticamente e exibe as diretrizes de resolução no Azure AD. 

Para resolver o erro de entrada com a MyApps Secure Sign-in Extension instalada:

1.  Quando ocorre um erro, a extensão o redireciona para a folha **Testar logon único** do Azure AD. 
2.  Na folha **Testar logon único**, clique em **Baixar solicitação SAML**. 
3.  Serão exibidas diretrizes de resolução específicas com base no erro e nos valores na solicitação SAML. Examinar as diretrizes.

Para resolver o erro sem instalar a MyApps Secure Sign-in Extension:

1. Copie a mensagem de erro no canto inferior direito da página. A mensagem de erro inclui:
    - Um CorrelationID e um carimbo de data/hora. Esses valores são importantes quando você cria um caso de suporte com a Microsoft porque eles ajudam os engenheiros a identificar o problema e a fornecer uma resolução precisa do problema.
    - Uma instrução que identifica a causa raiz do problema.
2.  Volte para o Azure AD e encontre a folha **Testar logon único**.
3.  Na caixa de texto acima de **Obter diretrizes de resolução**, cole a mensagem de erro.
3.  Clique em **Obter diretrizes de resolução** para exibir as etapas para resolver o problema. As diretrizes podem exigir informações da solicitação SAML ou da resposta SAML. Se você não estiver usando a MyApps Secure Sign-in Extension, talvez seja necessário usar uma ferramenta como a [Fiddler](https://www.telerik.com/fiddler) para recuperar a resposta e a solicitação SAML.
4.  Verifique se o destino na solicitação SAML corresponde à URL do Serviço de Logon Único de SAML obtida no Azure Active Directory
5.  Verifique se o emissor na solicitação SAML é o mesmo identificador que você configurou para o aplicativo no Azure Active Directory. O Azure AD usa o emissor para localizar um aplicativo no diretório.
6.  Verifique se AssertionConsumerServiceURL é onde o aplicativo espera receber o token SAML do Azure Active Directory. Você pode configurar esse valor no Azure Active Directory, mas isso não será obrigatório se ele fizer parte da solicitação SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Resolver um erro de entrada na página do aplicativo

Você pode entrar com êxito e, em seguida, encontrar um erro na página do aplicativo. Isso ocorre quando o Azure AD emite um token para o aplicativo, mas o aplicativo não aceita a resposta.   

Para resolver o erro:

1. Se o aplicativo estiver na Galeria do Azure AD, verifique se você seguiu todas as etapas para integrar o aplicativo com o Azure AD. Para obter as instruções de integração do aplicativo, confira a [lista de tutoriais de integração de aplicativos SaaS](../saas-apps/tutorial-list.md).
2. Recupere a resposta de SAML.
    - Se a My Apps Secure Sign-in Extension estiver instalada, na folha **Testar logon único**, clique em **Baixar resposta SAML**.
    - Se a extensão não estiver instalada, use uma ferramenta como a [Fiddler](https://www.telerik.com/fiddler) para recuperar a resposta SAML. 
3. Observe estes elementos no token da resposta SAML:
    - Identificador exclusivo do usuário do valor e do formato de NameID
    - Declarações emitidas no token
    - Certificado usado para assinar o token. Para obter informações de como examinar a resposta SAML, confira [Protocolo SAML de Logon Único](single-sign-on-saml-protocol.md).
4. Para obter mais informações sobre a resposta SAML, confira [Protocolo SAML de Logon Único](single-sign-on-saml-protocol.md).
5. Agora que você já examinou a resposta SAML, confira [Erro em uma página de aplicativo após a entrada](../manage-apps/application-sign-in-problem-application-error.md) para obter diretrizes de como resolver o problema. 
6. Se você ainda não conseguiu entrar com êxito, pergunte ao fornecedor do aplicativo o que está faltando na resposta SAML.


## <a name="next-steps"></a>Próximas etapas
Agora que o logon único está funcionando para o aplicativo, é possível [Automatizar o provisionamento e o desprovisionamento de usuário para aplicativos SaaS](../manage-apps/user-provisioning.md) ou passar examinar uma [Introdução ao acesso condicional](../conditional-access/app-based-conditional-access.md).


