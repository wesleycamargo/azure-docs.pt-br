---
title: Adicionar um aplicativo Web – Azure Active Directory B2C | Microsoft Docs
description: Saiba como adicionar um aplicativo Web ao seu locatário do Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: c1c640fa2c03f584aa9be62ae6ad123377ef53cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60383908"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Adicionar um aplicativo de API Web ao locatário do Azure Active Directory B2C

 Registre os recursos da API Web em seu locatário para que eles podem aceitar e responder às solicitações por aplicativos cliente que apresentam um token de acesso. Este artigo mostra como registrar o aplicativo no Azure Active Directory (Azure AD) B2C.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se que você estiver usando o diretório que contém o seu locatário do Azure AD B2C. Selecione o **filtro de diretório e assinatura** no menu superior e escolha o diretório que contém o seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo. Por exemplo, *webapi1*.
6. Para **Incluir aplicativo da Web / API da Web** e **Permitir fluxo implícito**, selecione **Sim**.
7. Para a **URL de resposta**, insira um ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Em seu aplicativo de produção, você pode definir a URL de resposta para um valor como `https://localhost:44332`. Para fins de teste, defina a URL de resposta `https://jwt.ms`.
8. Para o **ID do aplicativo URI**, insira o identificador usado para sua API da web. O URI do identificador completo, incluindo o domínio, é gerado para você. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página de propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo Web.

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. Escopos são usados pela API Web para implementar o controle de acesso com base em escopo. Por exemplo, os usuários da API Web podem ter tanto acesso de leitura quanto de gravação ou somente acesso de leitura. Neste tutorial, você usa escopos para definir as permissões de leitura e gravação da API Web.

1. Selecione **Aplicativos** e selecione *webapi1*.
2. Selecione **Escopos publicados**.
3. Em **escopo**, insira `Read` e, na descrição, insira `Read access to the application`.
4. Em **escopo**, insira `Write` e, na descrição, insira `Write access to the application`.
5. Clique em **Salvar**.

Os escopos publicados podem ser usados para conceder uma permissão de aplicativo cliente à API Web.

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de um aplicativo, é necessário conceder permissões de aplicativo à API. Por exemplo, no [Tutorial: Registre um aplicativo no Azure Active Directory B2C](tutorial-register-applications.md) e um aplicativo Web será criado no Azure AD B2C denominado *webapp1*. Você pode usar aplicativo para chamar a API da Web.

1. Selecione **Aplicativos**e selecione seu aplicativo Web.
2. Selecione **Acesso à API** e, em seguida, selecione **Adicionar**.
3. No menu suspenso **Selecionar API**, selecione *webapi1*.
4. No menu suspenso **Selecionar Escopos**, selecione os escopos **Leitura** e **Gravação** definidos anteriormente.
5. Clique em **OK**.

Seu aplicativo é registrado para chamar a API Web protegida. Um usuário autentica-se com o Azure AD B2C para usar o aplicativo. O aplicativo obtém uma concessão de autorização do Azure AD B2C para acessar a API Web protegida.
