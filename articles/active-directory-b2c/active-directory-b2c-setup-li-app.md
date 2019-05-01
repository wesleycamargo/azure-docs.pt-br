---
title: Configurar a inscrição e entrada com a conta do LinkedIn - Azure Active Directory B2C | Microsoft Docs
description: Forneça a inscrição e entrada aos consumidores com contas do LinkedIn em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9cf75bd8a0fc0c078801887e22468a143154e462
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703819"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do LinkedIn usando o Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Criar um aplicativo do LinkedIn

Para usar uma conta do LinkedIn como um [provedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure AD (Azure Active Directory) B2C, você precisará criar um aplicativo no locatário que o representa. Se ainda não tiver uma conta do LinkedIn, consiga uma conta em [https://www.linkedin.com/](https://www.linkedin.com/).

1. Entre no [site de Desenvolvedores do LinkedIn](https://www.developer.linkedin.com/) com suas credencias de conta do LinkedIn.
2. Escolha **Meus Aplicativos** e clique em **Criar Aplicativo**.
3. Insira **Nome da Empresa**, **Nome do Aplicativo**, **Descrição do Aplicativo**, **Logotipo do Aplicativo**, **Uso do Aplicativo**, **URL do Site**, **Email Comercial** e **Telefone Comercial**.
4. Aceite os **Termos de Uso da API do LinkedIn** e clique em **Enviar**.
5. Copie os valores de **ID do cliente** e **Segredo do cliente**. Você pode encontrá-los em **Chaves de Autenticação**. Você precisará de ambos para configurar o LinkedIn como um provedor de identidade no seu locatário. **Segredo do Cliente** é uma credencial de segurança importante.
6. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URLs de Redirecionamento Autorizadas**. Substitua `your-tenant-name` pelo nome do seu locatário. Todas as letras que você usar ao inserir o nome do locatário precisarão ser minúsculas, mesmo se o locatário estiver definido com letras maiúsculas no Azure AD B2C. Selecione **Adicionar** e depois clique em **Atualizar**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configurar uma conta do LinkedIn como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Escolha **Provedores de identidade** e escolha **Adicionar**.
5. Forneça um **Nome**. Por exemplo, insira *LinkedIn*.
6. Selecione **Tipo do provedor de identidade**, selecione **LinkedIn** e clique em **OK**.
7. Escolha **Configurar este provedor de identidade** e insira o ID do Cliente que você registrou anteriormente como a **ID do Cliente** e insira o Segredo do Cliente que você registrou como o **Segredo do Cliente** do aplicativo de conta do LinkedIn criado anteriormente.
8. Clique em **OK** e em **Criar** para salvar sua configuração de conta do LinkedIn.

## <a name="migration-from-v10-to-v20"></a>Migração de v 1.0 para v 2.0

LinkedIn recentemente [atualizada suas APIs da v1.0 v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Como parte da migração, o Azure AD B2C só é capaz de obter o nome completo do usuário no LinkedIn durante a inscrição. Se um endereço de email é um dos atributos que são coletados durante a inscrição, o usuário deve manualmente inserir o endereço de email e validá-la.
