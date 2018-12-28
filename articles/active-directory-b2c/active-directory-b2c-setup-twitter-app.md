---
title: Configurar a inscrição e entrada com a conta do Twitter usando o Azure Active Directory B2C | Microsoft Docs
description: Forneça a inscrição e entrada aos consumidores com contas do Twitter em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f0f0b8e0cbb5fbab81a07a28a9d4a2c264be6545
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52719854"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Twitter usando o Azure Active Directory B2C

## <a name="create-an-application"></a>Criar um aplicativo

Para usar o Twitter como provedor de identidade no Azure AD B2C, você precisará criar um aplicativo do Twitter. Se ainda não tiver uma conta do Twitter, consiga uma conta em [https://twitter.com/signup](https://twitter.com/signup).

1. Entre no site [Desenvolvedores do Twitter](https://developer.twitter.com/en/apps) com suas credencias de conta do Twitter.
2. Selecione **Criar um aplicativo**.
3. Insira um **Nome do aplicativo** e uma **Descrição do aplicativo**.
4. Na **URL do site**, insira `https://your-tenant.b2clogin.com`. Substitua `your-tenant` pelo nome do seu locatário. Por exemplo, https://contosob2c.b2clogin.com.
5. Insira `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` como o valor da **URL de Retorno de Chamada**. Substitua `your-tenant` pelo nome do locatário e `your-user-flow-Id` pelo identificador do fluxo de usuário. Por exemplo, `b2c_1A_signup_signin_twitter`. Todas as letras que você usar ao inserir o nome do locatário precisarão ser minúsculas, mesmo se o locatário estiver definido com letras maiúsculas no Azure AD B2C.
6. Na parte inferior da página, leia e aceite os termos e, em seguida, selecione **Criar**.
7. Na página **Detalhes do aplicativo**, selecione **Editar > Editar detalhes**, marque a caixa de **Habilitar entrada com o Twitter** e, em seguida, selecione **Salvar**.
8. Selecione **Chaves e tokens** e registre os valores da **Chave da API do consumidor** e da **Chave secreta da API do consumidor** que serão usados mais tarde.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurar o Twitter como um provedor de identidade em seu locatário

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Escolha **Provedores de identidade** e escolha **Adicionar**.
5. Forneça um **Nome**. Por exemplo, insira *Twitter*.
6. Selecione **Tipo de provedor de identidade**, selecione **Twitter** e clique em **OK**.
7. Selecione **Configurar este provedor de identidade** e insira a Chave da API para a **ID do cliente** e a Chave secreta da API para o **Segredo do cliente**.
8. Clique em **OK** e em **Criar** para salvar sua configuração do Twitter.