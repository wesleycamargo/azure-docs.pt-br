---
title: Gerenciar autenticação no Azure Mapas | Microsoft Docs
description: Você pode usar o portal do Azure para gerenciar a autenticação no Azure Mapas.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 68c3c8ac39f5803e01ee1038ec85ddb96ac80b30
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242678"
---
# <a name="manage-authentication-in-azure-maps"></a>Gerenciar autenticação no Azure Mapas

Depois de criar uma conta do Azure Mapas, a ID do cliente e as chaves são criados para oferecer suporte à autenticação do Azure Active Directory (Azure AD) ou da Chave Compartilhada.

Você pode encontrar seus detalhes de autenticação navegando até a página **Autenticação** em **Configurações** no portal do Azure. Navegue para sua conta. Em seguida, selecione **Autenticação** no menu.


## <a name="view-authentication-details"></a>Exibir detalhes de autenticação

Para exibir seus detalhes de autenticação, navegue até a opção **Autenticação** no menu de configurações.

![Exibir autenticação](./media/how-to-manage-authentication/how-to-view-auth.png)

 Para saber mais sobre autenticação e o Azure Mapas, consulte [Autenticação com o Azure Mapas](https://aka.ms/amauth).


## <a name="configure-azure-ad-app-registration"></a>Configurar o registro do Aplicativo Azure AD

Após criar uma conta do Azure Mapas, é necessário haver um link entre o locatário do Azure AD e o recurso do Azure Mapas. 

1. Acesse a folha do Azure AD e crie um Registro de Aplicativo com um nome e a URL de login como a página inicial do aplicativo Web/API, tal como "https://localhost/" . Se você já tem um aplicativo registrado, prossiga para a etapa 2.

    ![Registro do aplicativo](./media/how-to-manage-authentication/app-registration.png)

    ![Registro do aplicativo](./media/how-to-manage-authentication/app-create.png)

2. Atribua permissões de API delegada ao Azure Mapas navegando para o aplicativo nos registros do aplicativo e clique em **Configurações**.  Selecione **Permissões Necessárias** e escolha **Adicionar**. Pesquise e selecione Azure Mapas em **Selecionar uma API** e clique em **Selecionar**.

    ![Permissões de API do aplicativo](./media/how-to-manage-authentication/app-permissions.png)

3. Por fim, em Selecionar as permissões, escolha Acessar Azure Mapas e clique em Selecionar.

    ![Selecionar as permissões de API do aplicativo](./media/how-to-manage-authentication/select-app-permissions.png)

4. Siga as etapas a ou b, dependendo da implementação da autenticação.

    1. Se o aplicativo pretende usar a autenticação de token do usuário com o nosso SDK da Web do Azure Mapas, é preciso ativar `oauthEnableImplicitFlow` definindo-o como true na seção Manifesto da página de detalhes do registro do seu aplicativo.
    
       ![Manifesto de aplicativo](./media/how-to-manage-authentication/app-manifest.png)

    2. Se seu aplicativo usa autenticação de servidor/aplicativo, acesse a folha **Chaves** no registro do aplicativo e crie uma senha ou carregue um certificado de chave pública para o registro do aplicativo. Se você criar uma senha, depois de **Salvar**, copie a senha para uso posterior e armazene-a com segurança, você a usará para adquirir tokens do Azure AD.

       ![Chaves do aplicativo](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Conceder RBAC ao Azure Mapas

Após associar uma conta do Azure Mapas ao seu locatário do Azure AD, é possível conceder controle de acesso atribuindo o usuário ou o aplicativo a funções de controle de acesso do Azure Mapas disponíveis.

1. Navegue até a opção **Controle de Acesso**, clique em **Atribuições de Função** e **Adicionar Atribuição de Função**.

    ![Conceder RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Na janela pop-out Atribuição de Função, selecione Leitor de Data do Azure Mapas (versão prévia) **Função**, **Atribuir acesso a**: usuário, grupo ou entidade de serviço do Azure AD, **Selecione** Usuário ou Aplicativo no menu suspenso, e **Salve**.

    ![Adicionar atribuição de função](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Exibir funções disponíveis de RBAC do Azure Mapas

Para visualizar para quais funções de controle de acesso baseadas em função disponíveis para o Azure Mapas o acesso pode ser concedido, navegue até a opção **Controle de Acesso (IAM)**, clique em **Funções** e pesquise funções começando com **Azure Mapas**.

![Visualizar funções disponíveis](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-role-based-access-control-rbac"></a>Exibir o controle de acesso baseado em função (RBAC) do Azure Mapas

O Azure AD permite controle de acesso granular por meio de controle de acesso baseado em função (RBAC). 

Para exibir usuários ou aplicativos que receberam o RBAC para o Azure Mapas, navegue até a opção **Controle de Acesso (IAM)**, selecione **Atribuições de Função** e filtre por **Azure Mapas**. Todas as funções disponíveis aparecerão abaixo.

![Exibir RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Solicitar tokens para o Azure Mapas

Depois de registrar seu aplicativo e associá-lo ao Azure Mapas, é possível solicitar tokens de acesso.

* Se o aplicativo pretende usar a autenticação de token do usuário com o nosso SDK da Web do Azure Mapas (Web), é preciso configurar a sua página html com a ID do cliente do Azure Mapas e a ID do Aplicativo Azure AD.

* Para aplicativos que usam autenticação de servidor/aplicativo, solicite um token do ponto de extremidade de logon do Azure AD `https://login.microsoftonline.com` com a ID de recurso do Azure AD`https://atlas.microsoft.com/`, a ID do cliente do Azure Mapas, a ID do Aplicativo Azure AD e o certificado ou senha de registro do Aplicativo Azure AD.

Para saber mais sobre como solicitar tokens de acesso do Azure AD para usuários e entidades de serviço, consulte [Cenários de autenticação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a autenticação do Azure AD e o SDK da Web do Azure Mapas, consulte [Azure AD e o SDK da Web do Azure Mapas](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).