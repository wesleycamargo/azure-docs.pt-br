---
title: Registrar um aplicativo no Azure Active Directory B2C | Microsoft Docs
description: Saiba como registrar seu aplicativo no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fcebada4ef10c3e0bb298e9308d66ecb37247832
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999270"
---
# <a name="register-an-application-in-azure-active-directory-b2c"></a>Registrar um aplicativo no Azure Active Directory B2C

Para criar um [aplicativo](active-directory-b2c-apps.md) que aceite a inscrição e o login do consumidor, primeiro você precisa registrar o aplicativo em um locatário do Azure AD B2C. Este artigo o ajuda a registrar um aplicativo em um locatário B2C do Microsoft Azure Active Directory (Azure AD) em poucos minutos. Quando terminar, seu aplicativo estará registrado para uso no locatário B2C do Azure AD.

## <a name="prerequisites"></a>Pré-requisitos

Obtenha seu próprio locatário usando as etapas em [criar um locatário do Azure Active Directory B2C](tutorial-create-tenant.md).

Escolha as próximas etapas com base em seu tipo de aplicativo:

- [Registrar um aplicativo Web](#register-a-web-application)
- [Registrar uma API Web](#register-a-web-api)
- [Registrar um aplicativo móvel ou nativo](#register-a-mobile-or-native-application)

## <a name="register-a-web-application"></a>Registrar um aplicativo Web

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
2. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
3. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
4. Insira um nome para o aplicativo. Por exemplo, *testapp1*.
5. Para **Incluir aplicativo da Web / API da Web** e **Permitir fluxo implícito**, selecione **Sim**.
6. Para o **URL de resposta**, insira o ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Por exemplo, você pode defini-lo para escutar localmente na `https://localhost:44316`. Se você ainda não souber o número da porta, poderá inserir um valor de espaço reservado e alterá-lo posteriormente.
7. Clique em **Criar**.

### <a name="create-a-client-secret"></a>Criar um segredo do cliente

Se o seu aplicativo chamar uma API da web protegida pelo Azure AD B2C, você precisará criar um segredo de aplicativo.

1. Selecione **teclas** e, em seguida, clique em **gerar chave**. 
2. Selecione **salvar** para exibir a chave. Anote o valor da **Chave do Aplicativo**. Use o valor como o segredo do aplicativo no código do seu aplicativo.
3. Selecione **acesso à API**, clique em **Add**e selecione sua API da web e escopos (permissões).

## <a name="register-a-web-api"></a>Registrar uma API Web

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
2. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
3. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
4. Insira um nome para o aplicativo. Por exemplo, *testapp2*.
5. Para **Incluir aplicativo da Web / API da Web** e **Permitir fluxo implícito**, selecione **Sim**.
6. Para o **URL de resposta**, insira o ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Por exemplo, você pode defini-lo para escutar localmente na `https://localhost:44316`. Se você ainda não souber o número da porta, poderá inserir um valor de espaço reservado e alterá-lo posteriormente.
7. Para o **ID do aplicativo URI**, insira o identificador usado para sua API da web. O URI do identificador completo, incluindo o domínio, é gerado para você. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
8. Clique em **Criar**.
9. Selecione **Escopos publicados** para adicionar mais escopos, conforme necessário. Por padrão, o escopo `user_impersonation` é definido. O escopo `user_impersonation` oferece a outros aplicativos a capacidade de acessar essa API em nome do usuário conectado. Se desejar, o `user_impersonation` escopo pode ser removido.

## <a name="register-a-mobile-or-native-application"></a>Registrar um aplicativo móvel ou nativo

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
2. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
3. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
4. Insira um nome para o aplicativo. Por exemplo *testapp3*.
5. Para **incluir o aplicativo web / API web**, selecione **não**.
6. Para **incluir cliente nativo**, selecione **Sim**.
7. Para **URI de redirecionamento**, insira um [URI de redirecionamento com um esquema personalizado](active-directory-b2c-apps.md). Verifique se você escolher um bom URI de redirecionamento e não inclua caracteres especiais, como sublinhados.
8. Clique em **Criar**.

### <a name="create-a-client-secret"></a>Criar um segredo do cliente

Se o seu aplicativo chamar uma API da web protegida pelo Azure AD B2C, você precisará criar um segredo de aplicativo.

1. Selecione **teclas** e, em seguida, clique em **gerar chave**. 
2. Selecione **salvar** para exibir a chave. Anote o valor da **Chave do Aplicativo**. Use o valor como o segredo do aplicativo no código do seu aplicativo.
3. Selecione **acesso à API**, clique em **Add**e selecione sua API da web e escopos (permissões).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como os tokens de acesso são usados por aplicativos conceder permissões às APIs em [solicitando tokens de acesso](active-directory-b2c-access-tokens.md)
