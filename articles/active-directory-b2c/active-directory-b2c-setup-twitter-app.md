---
title: Configuração do Twitter para o Azure AD B2C | Microsoft Docs
description: Forneça inscrição e credenciais para consumidores com contas do Twitter em seus aplicativos protegidos pelo Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/17/2018
ms.author: davidmu
ms.openlocfilehash: 40e4c5549414765dabc6f37c5ffb5aea519ae673
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Fornecer inscrição e credenciais para consumidores com contas do Twitter usando o Azure AD B2C

## <a name="create-a-twitter-application"></a>Criar um aplicativo do Twitter
Para usar o Twitter como um provedor de identidade no Azure AD (Azure Active Directory) B2C, você precisa criar um aplicativo Twitter e fornecer a ele os parâmetros certos. Você precisa de uma conta do Twitter para fazer isso. Se você não tiver uma, é possível obtê-la em [https://twitter.com/signup](https://twitter.com/signup).

1. Vá para o [Aplicativo do Twitter](https://apps.twitter.com/) e entre com suas credenciais.
2. Clique em **Criar Novo Aplicativo**. 
3. No formulário, forneça um valor para o **Nome**, **Descrição**, e **Site**.
4. Insira `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` como o valor da **URL de Retorno de Chamada**. Certifique-se de substituir **{tenant}** pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com).
5. Marque a caixa para concordar com o **Contrato de Desenvolvedor** e clique em **Criar seu aplicativo do Twitter**.
6. Depois que o aplicativo for criado, clique na guia **Chaves e Tokens de acesso**.
7. Copie o valor de **Chave do consumidor** e **Segredo do consumidor**. Você precisará de ambos para configurar o Twitter como um provedor de identidade no seu locatário.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurar o Twitter como um provedor de identidade em seu locatário
1. Entre no [Portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C. 
2. Para alternar para seu locatário do Azure AD B2C, selecione o diretório do Azure AD B2C no canto superior direito do portal.
3. Clique em **Todos os serviços**, em seguida, selecione **Azure AD B2C**na lista de serviços em **Security + Identity**.
4. Clique em **Provedores de Identidade**.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira "Twitter".
5. Clique em **Tipo de provedor de identidade**, selecione **Twitter (versão prévia)** e clique em **OK**.
6. Clique em **Configurar esse provedor de identidade** e insira a **Chave do Consumidor** do Twitter para a **ID do cliente** e o **Segredo do Consumidor** do Twitter para o **Segredo do cliente**.
7. Clique em **OK** e em **Criar** para salvar sua configuração do Twitter.

## <a name="next-steps"></a>Próximas etapas

Criar ou editar uma [política interna](active-directory-b2c-reference-policies.md) e adicionar o Twitter como provedor de identidade.