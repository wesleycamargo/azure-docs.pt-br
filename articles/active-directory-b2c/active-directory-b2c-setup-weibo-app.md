---
title: Configurar a inscrição e entrada com a conta do Weibo usando o Azure Active Directory B2C | Microsoft Docs
description: Forneça a inscrição e entrada aos consumidores com contas do Weibo em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ca2b2b7303af0f713c4f21f4fac0af738e25951c
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54842475"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Weibo usando o Azure Active Directory B2C

> [!NOTE]
> Esse recurso está em visualização.
> 

## <a name="create-a-weibo-application"></a>Criar um aplicativo Weibo

Para usar uma conta do Weibo como um provedor de identidade no Azure AD (Azure Active Directory) B2C, você precisará criar um aplicativo no locatário que o representa. Se ainda não tiver uma conta do Weibo, obtenha uma conta em [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us).

1. Acesse o [portal do desenvolvedor do Weibo](https://open.weibo.com/) com suas credenciais de conta do Weibo.
2. Depois de entrar, selecione seu nome para exibição no canto superior direito.
3. No menu suspenso, selecione **编辑开发者信息** (editar informações do desenvolvedor).
4. Insira as informações necessárias e selecione **提交** (enviar).
5. Conclua o processo de verificação de email.
6. Acesse a [página de verificação de identidade](https://open.weibo.com/developers/identity/edit).
7. Insira as informações necessárias e selecione **提交** (enviar).

### <a name="register-a-weibo-application"></a>Registrar um aplicativo Weibo

1. Acesse a [nova página de registro de aplicativo Weibo](https://open.weibo.com/apps/new).
2. Insira as informações necessárias de aplicativo.
3. Selecione **创建** (criar).
4. Copie os valores de **Chave do Aplicativo** e **Segredo do Aplicativo**. Você precisa de ambos para adicionar o provedor de identidade para seu locatário.
5. Carregue as fotos necessárias e insira as informações necessárias.
6. Selecione **保存以上信息** (salvar).
7. Selecione **高级信息** (informações avançadas).
8. Selecione **编辑** (editar) ao lado do campo referente a OAuth2.0 **授权设置** (URL de redirecionamento).
9. Digite `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` para OAuth2.0 **授权设置**(URL de redirecionamento). Por exemplo, se o nome do locatário é contoso, defina a URL para ser `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
10. Selecione **提交** (enviar).  

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Configurar a conta do Weibo como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Escolha **Provedores de identidade** e escolha **Adicionar**.
5. Forneça um **Nome**. Por exemplo, insira *Weibo*.
6. Escolha **Tipo de provedor de identidade**, marque **Weibo (Versão prévia)** e clique em **OK**.
7. Escolha **Configurar este provedor de identidade** e insira a Chave do Aplicativo que você registrou anteriormente como a **ID do Cliente** e insira o Segredo do Aplicativo que você registrou como o **Segredo do cliente** do aplicativo Weibo criado anteriormente.
8. Clique em **OK** e em **Criar** para salvar sua configuração Weibo.
