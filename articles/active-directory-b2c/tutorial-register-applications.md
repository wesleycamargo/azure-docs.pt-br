---
title: Tutorial – Registrar um aplicativo – Azure Active Directory B2C | Microsoft Docs
description: Saiba como registrar seus aplicativo Web no Azure Active Directory B2C usando o portal do Azure.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/05/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 849bcfe2b5ee177d06b8e4cf62fd29459d2e59ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359731"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Tutorial: Registrar um aplicativo no Azure Active Directory B2C

Antes que os [aplicativos](active-directory-b2c-apps.md) possam interagir com o Azure AD (Azure Active Directory) B2C, eles deverão ser registrados em um locatário que você gerencia. Este tutorial mostra como registrar um aplicativo Web usando o portal do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Registrar um aplicativo Web
> * Criar um segredo do cliente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não criou seu próprio [locatário do Azure AD B2C](tutorial-create-tenant.md), crie um agora. É possível usar um locatário existente do Azure AD B2C.

## <a name="register-a-web-application"></a>Registrar um aplicativo Web

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
2. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
3. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
4. Insira um nome para o aplicativo. Por exemplo, *webapp1*.
5. Para **Incluir aplicativo da Web / API da Web** e **Permitir fluxo implícito**, selecione **Sim**.
6. Para a **URL de resposta**, insira um ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Por exemplo, pode defini-lo para escutar localmente em `https://localhost:44316` Se você ainda não souber o número da porta, poderá inserir um valor de espaço reservado e alterá-lo posteriormente. Para fins de teste, é possível defini-lo como `https://jwt.ms`, que exibe o conteúdo de um token para inspeção. Para este tutorial, defina-o como `https://jwt.ms`. 

    A URL de resposta deve começar com o esquema `https` e todos os valores de URL de resposta devem compartilhar um único domínio DNS. Por exemplo, se o aplicativo tiver uma URL de resposta de `https://login.contoso.com`, você poderá adicioná-la como essa URL `https://login.contoso.com/new`. Ou pode fazer referência a um subdomínio DNS de `login.contoso.com`, como `https://new.login.contoso.com`. Se você quiser um aplicativo com `login-east.contoso.com` e `login-west.contoso.com` como URLs de resposta, adicione essas URLs de resposta nesta ordem: `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. As duas últimas podem ser adicionadas porque são subdomínios da primeira URL de resposta: `contoso.com`.

7. Clique em **Criar**.

## <a name="create-a-client-secret"></a>Criar um segredo do cliente

Se seu aplicativo troca um código para um token, você precisará criar um segredo do aplicativo.

1. Selecione **teclas** e, em seguida, clique em **gerar chave**.
2. Selecione **salvar** para exibir a chave. Anote o valor da **Chave do Aplicativo**. Use o valor como o segredo do aplicativo no código do seu aplicativo.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Registrar um aplicativo Web
> * Criar um segredo do cliente

> [!div class="nextstepaction"]
> [Criar fluxos dos usuários no Azure Active Directory B2C](tutorial-create-user-flows.md)
