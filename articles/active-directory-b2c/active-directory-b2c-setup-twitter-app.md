---
title: Configurar a inscrição e entrada com a conta do Twitter usando o Azure Active Directory B2C | Microsoft Docs
description: Forneça a inscrição e entrada aos consumidores com contas do Twitter em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6d8e9245e95c08aad69cd05f338b6260e554469b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337783"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Twitter usando o Azure Active Directory B2C

## <a name="create-a-twitter-application"></a>Criar um aplicativo do Twitter

Para usar uma conta do Twitter como um provedor de identidade no Azure AD (Azure Active Directory) B2C, você precisará criar um aplicativo no locatário que o representa. Se ainda não tiver uma conta do Twitter, consiga uma conta em [https://twitter.com/signup](https://twitter.com/signup).

1. Entre nos [Aplicativos do Twitter](https://apps.twitter.com/) com suas credenciais do Twitter.
2. Selecione **Criar novo aplicativo**.
3. Insira **Nome**, **Descrição** e **Site**.
4. Insira `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/{policyId}/oauth1/authresp` em **URLs de retorno de chamada**. Substitua **{tenant}** pelo nome do locatário (por exemplo, contosob2c) e **{policyId}** pela ID da política (por exemplo, b2c_1_policy). Você deve adicionar uma URL de retorno de chamada para todas as políticas que usam a conta do Twitter. 
5. Concorde com o **Contrato de Desenvolvedor** e selecione **Criar seu aplicativo do Twitter**.
7. Selecione a guia **Chaves e Tokens de Acesso** .
8. Copie o valor de **Chave do consumidor** e **Segredo do consumidor**. Você precisa de ambos para configurar uma conta do Twitter como um provedor de identidade no seu locatário.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurar o Twitter como um provedor de identidade em seu locatário

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C alternando para ele no canto superior direito do portal do Azure. Selecione as informações da sua assinatura e depois selecione **Alternar diretório**. 

    ![Alternar para seu locatário do Azure AD B2C](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

    Escolha o diretório que contém seu locatário.

    ![Selecionar diretório](./media/active-directory-b2c-setup-twitter-app/select-directory.png)

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Escolha **Provedores de identidade** e escolha **Adicionar**.
5. Forneça um **Nome**. Por exemplo, insira *Twitter*.
6. Selecione **Tipo de provedor de identidade**, selecione **Twitter** e clique em **OK**.
7. Selecione **Configurar esse provedor de identidade** e insira a Chave do Consumidor para a **ID do cliente** e o **Segredo do Consumidor** para o **Segredo do cliente**.
8. Clique em **OK** e em **Criar** para salvar sua configuração do Twitter.