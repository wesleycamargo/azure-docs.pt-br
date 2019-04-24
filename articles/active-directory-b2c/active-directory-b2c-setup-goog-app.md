---
title: Configurar a inscrição e a entrada com a conta do Google usando o Azure Active Directory B2C | Microsoft Docs
description: Forneça a inscrição e entrada aos consumidores com contas do Google em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1d4f7460d53e721ca1cba7aba6c6ef9c45111ee5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316401"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Google usando o Azure Active Directory B2C

## <a name="create-a-google-application"></a>Criar um aplicativo do Google

Para usar uma conta do Google como um [provedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure AD (Azure Active Directory) B2C, é preciso criar um aplicativo no locatário que o representa. Se ainda não tiver uma conta do Google, consiga uma conta em [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Entre no [Console de Desenvolvedores do Google](https://console.developers.google.com/) com suas credenciais de conta do Google.
2. No canto superior esquerdo da página, selecione a lista de projeto e, em seguida, selecione **novo projeto**.
3. Insira um **Nome do Projeto**, clique em **Criar**e verifique se você está usando o novo projeto.
4. Selecione **Credenciais** no menu à esquerda e, em seguida, selecione **Criar Credenciais** > **ID do cliente Oauth**.
5. Em **Tipo de aplicativo**, selecione **Aplicativo Web**.
6. Insira um **Nome** para seu aplicativo, insira `https://your-tenant-name.b2clogin.com` em **Origens de JavaScript autorizadas** e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URIs de redirecionamento autorizados**. Substitua `your-tenant-name` pelo nome do seu locatário. Todas as letras que você usar ao inserir o nome do locatário precisarão ser minúsculas, mesmo se o locatário estiver definido com letras maiúsculas no Azure AD B2C.
7. Clique em **Criar**.
8. Copie os valores de **ID do cliente** e **Segredo do cliente**. Você precisará de ambos para configurar o Google como um provedor de identidade no seu locatário. **Segredo do cliente** é uma credencial de segurança importante.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configurar a conta do Google como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Escolha **Provedores de identidade** e escolha **Adicionar**.
5. Insira um **Nome**. Por exemplo, insira *Google*.
6. Selecione **Tipo de provedor de identidade**, selecione **Google** e clique em **OK**.
7. Selecione **Configurar este provedor de identidade**. Insira a ID do cliente que você anotou anteriormente como a **ID do cliente** e insira o segredo do cliente que você registrou como o **Segredo do cliente** do aplicativo do Google que criou anteriormente.
8. Clique em **OK** e em **Criar** para salvar a configuração do Google.

