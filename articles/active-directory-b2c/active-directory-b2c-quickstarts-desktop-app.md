---
title: Início Rápido - Configurar a entrada para um aplicativo de área de trabalho usando o Azure Active Directory B2C | Microsoft Docs
description: Execute um aplicativo de área de trabalho ASP.NET de exemplo que usa o Azure Active Directory B2C para fornecer a entrada na conta.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 161c1ec72b14f4cc1b2517a0dc6282f0548575b3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182295"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Início Rápido: configurar a entrada para um aplicativo da área de trabalho usando o Azure Active Directory B2C 

O Azure Active Directory (Azure AD) B2C fornece gerenciamento de identidades de nuvem para manter seu aplicativo, negócios e clientes protegidos. O Azure AD B2C permite que seus aplicativos se autentiquem com contas sociais e corporativas usando protocolos padrão. Neste início rápido, você pode usar um aplicativo de área de trabalho WPF (Windows Presentation Foundation) para iniciar sessão usando um provedor de identidade de redes sociais e chamar uma API Web do Azure AD B2C protegida.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2017](https://www.visualstudio.com/downloads/) com a carga de trabalho **ASP.NET e desenvolvimento para a Web**. 
- Uma conta social do Facebook, Google, Microsoft ou Twitter.
- [Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip) ou clone o aplicativo Web de exemplo do GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Executar o aplicativo no Visual Studio

1. Na pasta de projeto do aplicativo de exemplo, abra a solução **active-directory-b2c-wpf.sln** no Visual Studio.
2. Pressione **F5** para depurar o aplicativo.

## <a name="sign-in-using-your-account"></a>Iniciar sessão usando sua conta

1. Clique em **Entrar** para iniciar o fluxo de trabalho **Criar conta ou entrar**.

    ![Aplicativo de exemplo](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

    O exemplo dá suporte a várias opções de inscrição, incluindo o uso de um provedor de identidade social ou a criação de uma conta local usando um endereço de email. Para este guia de início rápido, use uma conta de provedor de identidade social do Facebook, do Google, da Microsoft ou do Twitter. 


2. O Azure AD B2C apresenta uma página de logon personalizada para uma marca fictícia chamada Wingtip Toys para o aplicativo Web de exemplo. Para inscrever-se usando um provedor de identidade social, clique no botão do provedor de identidade que você deseja usar. 

    ![Provedor de criar conta ou entrar](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

    Você se autentica (entra) usando as credenciais da conta social e autoriza o aplicativo a ler as informações dessa conta. Ao conceder o acesso, o aplicativo poderá recuperar informações de perfil da conta social, tais como seu nome e cidade. 

2. Conclua o processo de entrada para o provedor de identidade.

    Os detalhes do perfil da sua nova conta são populados previamente com as informações da sua conta social.

## <a name="edit-your-profile"></a>Editar o perfil

O Azure AD B2C fornece funcionalidade para permitir que usuários atualizem seus perfis. O aplicativo Web de exemplo usa um fluxo de usuário de perfil de edição do Azure AD B2C no fluxo de trabalho. 

1. Na barra de menus do aplicativo, clique em **Editar perfil** para editar o perfil que você criou.

    ![Editar perfil](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

2. Escolha o provedor de identidade associado à conta que você criou. Por exemplo, se você usou o Twitter como o provedor de identidade quando criou a conta, escolha Twitter para modificar os detalhes do perfil associado.

3. Altere seu **Nome de exibição** ou **Cidade** e clique em **Continuar**.

    Um novo token de acesso é exibido na caixa de texto *Informações de token*. Se você quiser verificar as alterações no seu perfil, copie e cole o token de acesso no decodificador de token https://jwt.ms.

## <a name="access-a-protected-api-resource"></a>Acessar um recurso de API protegido

Clique em **Chamada à API** para fazer uma solicitação para o recurso protegido. 

    ![Call API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

    The application includes the Azure AD access token in the request to the protected web API resource. The web API sends back the display name contained in the access token.

Você usou com êxito sua conta de usuário do Azure AD B2C para fazer uma chamada autorizada a uma API Web protegida pelo Azure AD B2C.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode usar o locatário do Azure AD B2C se planeja experimentar outros tutoriais ou inícios rápidos do Azure AD B2C. Quando não for mais necessário, você poderá [excluir o locatário do Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Próximas etapas

Neste início rápido você usou um aplicativo da área de trabalho para entrar com uma página de logon personalizada, entrar com um provedor de identidade de redes sociais, criar uma conta do Azure AD B2C e chamar uma API Web protegida pelo Azure AD B2C. 

Introdução à criação de seu próprio locatário do Azure AD B2C. 

> [!div class="nextstepaction"]
> [Criar um locatário do Azure Active Directory B2C no Portal do Azure](tutorial-create-tenant.md)
