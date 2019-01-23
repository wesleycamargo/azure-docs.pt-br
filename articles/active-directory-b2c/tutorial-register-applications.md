---
title: Tutorial – Registrar seus aplicativos no Azure Active Directory B2C | Microsoft Docs
description: Saiba como registrar seus aplicativos no Azure Active Directory B2C usando o portal do Azure.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/11/2019
ms.author: davidmu
ms.openlocfilehash: 511e1e9f29e6ae7602a977819f5295f76236595d
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54248718"
---
# <a name="tutorial-register-your-applications-in-azure-active-directory-b2c"></a>Tutorial: Registrar seus aplicativos no Azure Active Directory B2C

Antes que os [aplicativos](active-directory-b2c-apps.md) possam interagir com o Azure AD (Azure Active Directory) B2C, eles deverão ser registrados em um locatário que você gerencia. Este tutorial mostra como registrar aplicativos usando o portal do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Registrar um aplicativo Web
> * Registrar uma API Web
> * Registrar um aplicativo móvel ou nativo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não criou seu próprio [locatário do Azure AD B2C](tutorial-create-tenant.md), crie um agora. Você pode usar um locatário existente.

## <a name="register-a-web-application"></a>Registrar um aplicativo Web

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.

    ![Alternar para o diretório de assinatura](./media/tutorial-register-applications/switch-directories.png)

2. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
3. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.

    ![Adicionar aplicativo](./media/tutorial-register-applications/add-application.png)

4. Insira um nome para o aplicativo. Por exemplo, *webapp1*.
5. Para **Incluir aplicativo da Web / API da Web** e **Permitir fluxo implícito**, selecione **Sim**.
6. Para a **URL de resposta**, insira um ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Por exemplo, pode defini-lo para escutar localmente em `https://localhost:44316` Se você ainda não souber o número da porta, poderá inserir um valor de espaço reservado e alterá-lo posteriormente. Para fins de teste, é possível defini-lo como `https://jwt.ms`, que exibe o conteúdo de um token para inspeção. Para este tutorial, defina-o como `https://jwt.ms`. 

    A URL de resposta deve começar com o esquema `https` e todos os valores de URL de resposta devem compartilhar um único domínio DNS. Por exemplo, se o aplicativo tiver uma URL de resposta de `https://login.contoso.com`, você poderá adicioná-la como essa URL `https://login.contoso.com/new`. Ou pode fazer referência a um subdomínio DNS de `login.contoso.com`, como `https://new.login.contoso.com`. Se você quiser um aplicativo com `login-east.contoso.com` e `login-west.contoso.com` como URLs de resposta, adicione essas URLs de resposta nesta ordem: `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. As duas últimas podem ser adicionadas porque são subdomínios da primeira URL de resposta: `contoso.com`.

7. Clique em **Criar**.

    ![Definir as propriedades do aplicativo](./media/tutorial-register-applications/application-properties.png)

### <a name="create-a-client-secret"></a>Criar um segredo do cliente

Caso seu aplicativo troque um código por um token, você precisará criar um segredo do aplicativo.

1. Selecione **teclas** e, em seguida, clique em **gerar chave**.

    ![Gerar chaves](./media/tutorial-register-applications/generate-keys.png)

2. Selecione **salvar** para exibir a chave. Anote o valor da **Chave do Aplicativo**. Use o valor como o segredo do aplicativo no código do seu aplicativo.

    ![Salvar a chave](./media/tutorial-register-applications/save-key.png)
    
3. Selecione **acesso à API**, clique em **Add**e selecione sua API da web e escopos (permissões).

    ![Configurar o acesso à API](./media/tutorial-register-applications/api-access.png)

## <a name="register-a-web-api"></a>Registrar uma API Web

1. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
3. Insira um nome para o aplicativo. Por exemplo, *webapi1*.
4. Para **Incluir aplicativo da Web / API da Web** e **Permitir fluxo implícito**, selecione **Sim**.
5. Para a **URL de resposta**, insira um ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Por exemplo, você pode defini-lo para escutar localmente na `https://localhost:44316`. Se você ainda não souber o número da porta, poderá inserir um valor de espaço reservado e alterá-lo posteriormente.
6. Para o **ID do aplicativo URI**, insira o identificador usado para sua API da web. O URI do identificador completo, incluindo o domínio, é gerado para você. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
7. Clique em **Criar**.
8. Selecione o aplicativo *webapi1* que você criou e, em seguida, selecione **Escopos publicados** para adicionar mais escopos conforme necessário. Por padrão, o escopo `user_impersonation` é definido. O escopo `user_impersonation` oferece a outros aplicativos a capacidade de acessar essa API em nome do usuário conectado. Se desejar, o `user_impersonation` escopo pode ser removido.

    ![Definir os escopos publicados](./media/tutorial-register-applications/published-scopes.png)


## <a name="register-a-mobile-or-native-application"></a>Registrar um aplicativo móvel ou nativo

1. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
2. Insira um nome para o aplicativo. Por exemplo, *nativeapp1*.
3. Para **incluir o aplicativo web / API web**, selecione **não**.
4. Para **incluir cliente nativo**, selecione **Sim**.
5. Para **URI de redirecionamento**, insira um URI de redirecionamento válido com um esquema personalizado. Há duas considerações importantes ao escolher um URI de redirecionamento:

    - **Exclusivo** – o esquema do URI de redirecionamento deve ser exclusivo para cada aplicativo. No exemplo `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` é o esquema. Esse padrão deve ser seguido. Se dois aplicativos compartilharem o mesmo esquema, o usuário terá a opção de escolher um aplicativo. Se o usuário fizer uma escolha incorreta, ocorrerá falha na conexão.
    - **Completo** – o URI de redirecionamento deve ter um esquema e um caminho. O caminho deve conter pelo menos uma barra após o domínio. Por exemplo, `//contoso/` funciona e `//contoso` falha. Certifique-se de que o URI de redirecionamento não inclua caracteres especiais, como sublinhados.

6. Clique em **Criar**.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Registrar um aplicativo Web
> * Registrar uma API Web
> * Registrar um aplicativo móvel ou nativo

> [!div class="nextstepaction"]
> [Criar fluxos dos usuários no Azure Active Directory B2C](tutorial-create-user-flows.md)