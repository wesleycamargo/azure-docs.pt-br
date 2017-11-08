---
title: "Fazer test drive de um aplicativo da área de trabalho do Azure AD B2C | Microsoft Docs"
description: "Faça um test drive das jornadas do usuário para entrar, inscrever-se, editar o perfil e redefinir a senha usando um ambiente de teste do Azure AD B2C"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 86293627-26fb-4e96-a76b-f263f9a945bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 9653d86dd5635016512bf6e6fafbf7195145ed07
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-desktop-application-configured-with-azure-ad-b2c"></a>Fazer test drive de um aplicativo da área de trabalho configurado com o Azure AD B2C

O Azure Active Directory B2C fornece gerenciamento de identidades de nuvem para manter seu aplicativo, negócios e clientes protegidos.  Este guia de início rápido usa um aplicativo da área de trabalho do WPF (Windows Presentation Foundation) de exemplo para demonstrar:

* Como usar a política **Criar conta ou entrar** para criar ou entrar com um provedor de identidade social ou uma conta local usando um endereço de email. 
* **Como chamar uma API** para recuperar o nome de exibição de um recurso protegido do Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com as cargas de trabalho a seguir:
    - **Desenvolvimento para área de trabalho .NET**

* Uma conta social do Facebook, Google, Microsoft ou Twitter. Se você não tiver uma conta social, será necessário ter um endereço de email válido.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Baixar o exemplo

[Baixar ou clonar o aplicativo de exemplo](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) do GitHub.

## <a name="run-the-app-in-visual-studio"></a>Executar o aplicativo no Visual Studio

Na pasta de projeto do aplicativo de exemplo, abra a solução `active-directory-b2c-wpf.sln` no Visual Studio. 

Selecione **Depurar > Iniciar depuração** para compilar e executar o aplicativo. 

## <a name="create-an-account"></a>Criar uma conta

Clique em **Entrar** para iniciar o fluxo de trabalho **Criar conta ou entrar**. Ao criar uma conta, você poderá usar a conta de um provedor de identidade social existente ou uma conta de email.

![Aplicativo de exemplo](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

### <a name="sign-up-using-a-social-identity-provider"></a>Inscrever-se usando um provedor de identidade social

Para inscrever-se usando um provedor de identidade social, clique no botão do provedor de identidade que você deseja usar. Se você preferir usar um endereço de email, vá para a seção [Criar conta usando um endereço de email](#sign-up-using-an-email-address).

![Provedor de criar conta ou entrar](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

Você precisará autenticar-se (entrar) usando as credenciais da sua conta social e autorizar o aplicativo a ler as informações dessa conta. Ao conceder o acesso, o aplicativo poderá recuperar informações de perfil da conta social, tais como seu nome e cidade. 

![Autenticar e autorizar usando uma conta social](media/active-directory-b2c-quickstarts-desktop-app/twitter-authenticate-authorize-wpf.png)

Os detalhes do perfil da sua nova conta são populados previamente com as informações da sua conta social. Modifique os detalhes, se você desejar, e clique em **Continuar**.

![Novos detalhes do perfil de criação de conta](media/active-directory-b2c-quickstarts-desktop-app/new-account-sign-up-profile-details-wpf.png)

Você criou com êxito uma nova conta de usuário do Azure AD B2C que usa um provedor de identidade. Depois de entrar, o token de acesso é exibido na caixa de texto *Informações de token*. O token de acesso é usado para acessar o recurso da API.

![Token de autorização](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

Próxima etapa: seção [Ir para Editar seu perfil](#edit-your-profile).

### <a name="sign-up-using-an-email-address"></a>Criar conta usando um endereço de email

Se você optar por não usar uma conta social para fornecer autenticação, você poderá criar uma conta de usuário do Azure AD B2C usando um endereço de email válido. Uma conta de usuário local do Azure AD B2C usa o Azure Active Directory como o provedor de identidade. Para usar seu endereço de email, clique no link **Não tem uma conta? Inscreva-se agora mesmo**.

![Criar conta ou entrar usando email](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-email-wpf.png)

Insira um endereço de email válido e clique em **Enviar o código de verificação**. É necessário ter um endereço de email válido para receber o código de verificação do Azure AD B2C.

Insira o código de verificação que você recebeu por email e clique em **Verificar código**.

Adicione suas informações de perfil e clique em **Criar**.

![Criar uma nova conta usando email](media/active-directory-b2c-quickstarts-desktop-app/sign-up-new-account-profile-email-wpf.png)

Você criou com êxito uma nova conta de usuário local do Azure AD B2C. Depois de entrar, o token de acesso é exibido na caixa de texto *Informações de token*. O token de acesso é usado para acessar o recurso da API.

![Token de autorização](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="edit-your-profile"></a>Editar o perfil

O Azure Active Directory B2C fornece funcionalidade para permitir que usuários atualizem seus perfis. Clique em **Editar perfil** para editar o perfil que você criou.

![Editar perfil](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

Escolha o provedor de identidade associado à conta que você criou. Por exemplo, se você usou o Twitter como o provedor de identidade quando criou a conta, escolha Twitter para modificar os detalhes do perfil associado.

![Escolher o provedor associado ao perfil a ser editado](media/active-directory-b2c-quickstarts-desktop-app/edit-account-choose-provider-wpf.png)

Altere seu **Nome de exibição** ou **Cidade**. 

![Atualizar perfil](media/active-directory-b2c-quickstarts-desktop-app/update-profile-wpf.png)

Um novo token de acesso é exibido na caixa de texto *Informações de token*. Se você quiser verificar as alterações no seu perfil, copie e cole o token de acesso no decodificador de token https://jwt.ms.

![Token de autorização](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="access-a-resource"></a>Acessar um recurso

Clique em **Chamar API** para fazer uma solicitação para o recurso protegido do Azure AD B2C https://fabrikamb2chello.azurewebsites.net/hello. 

![Chamar API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

O aplicativo inclui o token de acesso exibido na caixa de texto *Informações de token* na solicitação. A API retorna o nome de exibição contido no token de acesso.

## <a name="next-steps"></a>Próximas etapas

A próxima etapa será criar seu próprio locatário do Azure AD B2C e configurar o exemplo para ser executado usando o seu locatário. 

> [!div class="nextstepaction"]
> [Criar um locatário do Azure Active Directory B2C no Portal do Azure](active-directory-b2c-get-started.md)
