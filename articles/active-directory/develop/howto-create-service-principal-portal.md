---
title: Criar identidade para o aplicativo do Azure no portal | Microsoft Docs
description: Descreve como criar um novo aplicativo do Azure Active Directory e uma nova entidade de serviço, que possam ser usados com o controle de acesso baseado em função no Azure Resource Manager para gerenciar o acesso aos recursos.
services: active-directory
documentationcenter: na
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2019
ms.author: celested
ms.reviewer: tomfitz
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee12cb2a0fdd4547ffae8dfce4fddee03167e539
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257398"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Como: Usar o portal para criar um aplicativo e uma entidade de serviço do Microsoft Azure Active Directory que possa acessar recursos

Este artigo mostra como criar um novo aplicativo do Azure Active Directory e o serviço principal que pode ser usado com o controle de acesso baseado em função. Se você tiver código que precise acessar ou modificar recursos, crie uma identidade para o aplicativo. Essa identidade é conhecida como uma entidade de serviço. Desse modo, é possível atribuir as permissões necessárias à entidade de serviço. Este artigo mostra como usar o portal para criar a entidade de serviço. Ele se concentra em um aplicativo de locatário único que se destina a ser executado dentro de uma única organização. Você normalmente usa os aplicativos com um único locatário para os aplicativos da linha de negócios executados em sua organização.

> [!IMPORTANT]
> Em vez de criar uma entidade de serviço, considere o uso de identidades gerenciadas para recursos do Azure para a identidade do aplicativo. Se o código for executado em um serviço que dá suporte a identidades gerenciadas e acessa recursos que dão suporte à autenticação do Azure Active Directory (Azure AD), as identidades gerenciadas serão uma opção melhor para você. Para saber mais sobre identidades gerenciadas dos recursos do Azure, incluindo os serviços atualmente com suporte, consulte [O que são identidades gerenciadas para recursos do Azure?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Criar um aplicativo do Azure Active Directory

Vamos diretamente para a criação da identidade. Se você encontrar um problema, verifique as [permissões necessárias](#required-permissions) para garantir que sua conta possa criar a identidade.

1. Entre sua conta do Azure através do [portal do Microsoft Azure](https://portal.azure.com).
1. Selecione **Azure Active Directory**.
1. Selecione **Registros do Aplicativo**.

   ![Selecione registros do aplicativo](./media/howto-create-service-principal-portal/select-app-registrations.png)

1. Selecione **Novo registro de aplicativo**.

   ![Adicione o aplicativo](./media/howto-create-service-principal-portal/select-add-app.png)

1. Forneça um nome e uma URL para o aplicativo. Selecione **aplicativo Web/API** para o tipo de aplicativo que você deseja criar. Não é possível criar as credenciais para um [Aplicativo nativo](../manage-apps/application-proxy-configure-native-client-application.md). Não é possível usar esse tipo para um aplicativo automatizado. Depois de definir os valores, selecione **Criar**.

   ![Nomeie o aplicativo](./media/howto-create-service-principal-portal/create-app.png)

Você criou o aplicativo e a entidade de serviço do Azure AD.

## <a name="assign-the-application-to-a-role"></a>Atribuir o aplicativo a uma função

Para acessar recursos em sua assinatura, você deve atribuir o aplicativo a uma função. Decida qual função oferece as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: Funções internas](../../role-based-access-control/built-in-roles.md).

Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, adicionar um aplicativo à função Leitor de um grupo de recursos significa que ele pode ler o grupo de recursos e todos os recursos que ele contiver.

1. Navegue até o nível do escopo ao qual quer atribuir o aplicativo. Por exemplo, para atribuir uma função no escopo da assinatura, escolha **Todos os serviços** e **Assinaturas**.

   ![Escolha a assinatura](./media/howto-create-service-principal-portal/select-subscription.png)

1. Escolha a assinatura específica à qual atribuir o aplicativo.

   ![Selecione a assinatura para atribuição](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Se você não vir a assinatura que está procurando, selecione **filtro de assinaturas globais**. Garanta que a assinatura desejada seja selecionada para o portal. 

1. Selecione **IAM (Controle de acesso)**.
1. Selecione **Adicionar atribuição de função**.

   ![Selecione adicionar atribuição de função](./media/howto-create-service-principal-portal/select-add.png)

1. Selecione a função que deseja atribuir ao aplicativo. Para permitir que o aplicativo execute ações como **reinicializar**, **iniciar** e **parar** instâncias, selecione a função de **Colaborador**. Por padrão, os aplicativos do Azure AD não são exibidos nas opções disponíveis. Para localizar seu aplicativo, pesquise o nome e selecione-o.

   ![Escolher função](./media/howto-create-service-principal-portal/select-role.png)

1. Selecione **Salvar** para finalizar a atribuição da função. Agora você vê o aplicativo na lista de usuários atribuídos a uma função para esse escopo.

A entidade de serviço está configurada. Você pode começar a usá-lo para executar seus scripts ou aplicativos. A próxima seção mostra como obter valores necessários ao entrar de modo programático.

## <a name="get-values-for-signing-in"></a>Obter valores para entrar

### <a name="get-tenant-id"></a>Obter a ID do locatário

Ao entrar de modo programático, você precisa passar a ID de locatário com a solicitação de autenticação.

1. Selecione **Azure Active Directory**.
1. Selecione **Propriedades**.

   ![Selecione as propriedades do Azure AD](./media/howto-create-service-principal-portal/select-ad-properties.png)

1. Copie a **ID do Diretório** para obter sua ID de locatário.

   ![ID do locatário](./media/howto-create-service-principal-portal/copy-directory-id.png)

### <a name="get-application-id-and-authentication-key"></a>Obter chave de autenticação e ID do aplicativo

Você também precisa da ID para seu aplicativo e de uma chave de autenticação. Para obter esses valores, use as seguintes etapas:

1. Em **Registros de aplicativo** no Azure AD, selecione seu aplicativo.

   ![Selecionar aplicativo](./media/howto-create-service-principal-portal/select-app.png)

1. Copie a **ID do aplicativo** e armazene-a no código do aplicativo.

   ![ID do cliente](./media/howto-create-service-principal-portal/copy-app-id.png)

1. Escolha a opção **Configurações**.

   ![Selecionar configurações](./media/howto-create-service-principal-portal/select-settings.png)

1. Selecione **Chaves**.
1. Forneça uma descrição da chave e uma duração para a chave. Ao terminar, escolha **Salvar**.

   ![Salve a chave](./media/howto-create-service-principal-portal/save-key.png)

   Após salvar a chave, o valor da chave é exibido. Copie este valor, pois não é possível recuperar a chave posteriormente. Forneça o valor da chave com a ID do aplicativo para fazer logon como o aplicativo. Armazene o valor da chave onde seu aplicativo possa recuperá-lo.

   ![Chave salva](./media/howto-create-service-principal-portal/copy-key.png)

## <a name="required-permissions"></a>Permissões necessárias

Você deve ter permissões suficientes para registrar um aplicativo com o locatário do Azure AD e atribuir o aplicativo a uma função em sua assinatura do Azure.

### <a name="check-azure-ad-permissions"></a>Verifique as permissões do Azure AD

1. Selecione **Azure Active Directory**.
1. Anote sua função. Se você tiver a função **Usuário**, garanta que não administradores possam registrar aplicativos.

   ![Localize o usuário](./media/howto-create-service-principal-portal/view-user-info.png)

1. Selecione **Configurações de usuário**.

   ![Selecione as configurações de usuário](./media/howto-create-service-principal-portal/select-user-settings.png)

1. Verifique a configuração **Registros do Aplicativo**. Esse valor só pode ser definido por um administrador. Se for definido como **Sim**, qualquer usuário no locatário do Azure AD poderá registrar um aplicativo.

   ![Exiba os registros de aplicativo](./media/howto-create-service-principal-portal/view-app-registrations.png)

Se a configuração de registros de aplicativo está definida como **Não**, somente os usuários com uma função de administrador podem registrar esses tipos de aplicativos. Confira as [funções disponíveis](../users-groups-roles/directory-assign-admin-roles.md#available-roles) e as [permissões de função](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) para saber mais sobre as funções de administrador disponíveis e as permissões específicas no Azure AD fornecidas para cada função. Se sua conta está atribuída à função Usuário, mas a configuração de registro de aplicativo está limitada a usuários administradores, peça a seu administrador para atribuir a você uma das funções de administrador que podem criar e gerenciar todos os aspectos de registros do aplicativo ou para permitir que os usuários registrem aplicativos.

### <a name="check-azure-subscription-permissions"></a>Verificar permissões de assinatura do Azure

Em sua assinatura do Azure, sua conta deve ter acesso de `Microsoft.Authorization/*/Write` para atribuir um aplicativo do AD a uma função. Esta ação deve ser concedida pela função [Proprietário](../../role-based-access-control/built-in-roles.md#owner) ou pela função [Administrador de Acesso do Usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator). Se sua conta tiver a função **Colaborador**, você não terá a permissão adequada. Você verá um erro ao tentar atribuir a entidade de serviço a uma função.

Para verificar suas permissões de assinatura:

1. Selecione sua conta no canto superior direito, depois selecione **Minhas permissões**.

   ![Selecione as permissões de usuário](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. Na lista suspensa, selecione a assinatura em que você deseja criar o serviço principal. Em seguida, selecione **Clique aqui para exibir o acesso completo detalhes para essa assinatura**.

   ![Localize o usuário](./media/howto-create-service-principal-portal/view-details.png)

1. Exiba suas funções atribuídas e determine se você tem as permissões adequadas para atribuir um aplicativo do AD a uma função. Caso contrário, peça ao administrador da assinatura para adicioná-lo à função Administrador de Acesso do Usuário. Na imagem a seguir, o usuário é atribuído à função Proprietário, o que significa que o usuário tem as permissões adequadas.

   ![Mostre as permissões](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Próximos passos

* Para configurar um aplicativo multilocatário, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](../../azure-resource-manager/resource-manager-api-authentication.md).
* Para aprender a especificar as políticas de segurança, consulte [Controle de Acesso baseado nas Funções do Azure](../../role-based-access-control/role-assignments-portal.md).  
* Para obter uma lista de ações disponíveis que podem ser concedidas ou negadas a usuários, consulte [Operações do Provedor de Recursos do Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md).
