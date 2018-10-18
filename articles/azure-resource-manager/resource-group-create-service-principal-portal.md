---
title: Criar identidade para o aplicativo do Azure no portal | Microsoft Docs
description: Descreve como criar um novo aplicativo do Azure Active Directory e uma nova entidade de serviço, que possam ser usados com o controle de acesso baseado em função no Azure Resource Manager para gerenciar o acesso aos recursos.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: tomfitz
ms.openlocfilehash: 2f053f6dd98b9f4e97d69e51bce933a003633277
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2018
ms.locfileid: "46497936"
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory que possa acessar recursos

Quando você tiver um código que precisa acessar ou modificar os recursos, deverá configurar um aplicativo do Azure AD (Active Directory). Desse modo, será possível atribuir as permissões necessárias ao aplicativo do AD. Essa abordagem é preferível executar o aplicativo com suas próprias credenciais, porque você pode atribuir permissões para a identidade do aplicativo que são diferentes de suas próprias permissões. Normalmente, essas permissões são restritas a exatamente o que o aplicativo precisa fazer.

Este artigo mostra como executar essas etapas no portal. Ele se concentra em um aplicativo de locatário único que se destina a ser executado dentro de uma única organização. Você normalmente usa os aplicativos com um único locatário para os aplicativos da linha de negócios executados em sua organização.

> [!IMPORTANT]
> Em vez de criar uma entidade de serviço, considere o uso de identidades gerenciadas para recursos do Azure para a identidade do aplicativo. Se o código for executado em um serviço que dá suporte a identidades gerenciadas e acessa recursos que dão suporte à autenticação do Azure Active Directory, as identidades gerenciadas serão uma opção melhor para você. Para saber mais sobre identidades gerenciadas dos recursos do Azure, incluindo os serviços atualmente com suporte, consulte [O que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="required-permissions"></a>Permissões necessárias

Para concluir este artigo, você deve ter permissões suficientes para registrar um aplicativo com o locatário do Azure AD e atribuir o aplicativo a uma função em sua assinatura do Azure. Vamos verificar se você tem as permissões corretas para executar essas etapas.

### <a name="check-azure-active-directory-permissions"></a>Verificar as permissões do Azure Active Directory

1. Selecione **Azure Active Directory**.

   ![selecionar azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. No Azure Active Directory, selecione **Configurações de Usuário**.

   ![selecionar configurações de usuário](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Verifique a configuração **Registros do Aplicativo**. Se estiver definida como **Sim**, usuários que não são administradores podem registrar aplicativos do AD. Essa configuração significa que qualquer usuário no locatário do Azure AD pode registrar um aplicativo. Você pode prosseguir com a [verificação das permissões de assinatura do Azure](#check-azure-subscription-permissions).

   ![exibir registros de aplicativo](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Se a configuração de registros de aplicativo estiver definida como **Não**, somente os [administradores globais](../active-directory/users-groups-roles/directory-assign-admin-roles.md) poderão registrar aplicativos. Verifique se sua conta é de administrador para o locatário do Azure AD. Selecione **Visão geral** e examine as informações do usuário. Se a conta estiver atribuída com a função de usuário, mas a configuração de registro de aplicativo (da etapa anterior) estiver limitada a usuários administradores, peça ao administrador para atribuir a você a função de administrador global ou para permitir que os usuários registrem aplicativos.

   ![localizar usuário](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>Verificar permissões de assinatura do Azure

Em sua assinatura do Azure, sua conta deve ter acesso de `Microsoft.Authorization/*/Write` para atribuir um aplicativo do AD a uma função. Esta ação deve ser concedida pela função [Proprietário](../role-based-access-control/built-in-roles.md#owner) ou pela função [Administrador de Acesso do Usuário](../role-based-access-control/built-in-roles.md#user-access-administrator). Se sua conta tiver a função **Colaborador**, você não terá a permissão adequada. Você verá um erro ao tentar atribuir a entidade de serviço a uma função.

Para verificar suas permissões de assinatura:

1. Selecione sua conta no canto superior direito, depois selecione **Minhas permissões**.

   ![selecione permissões de usuário](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. Na lista suspensa, selecione a assinatura. Selecione **Clique aqui para exibir o acesso completo detalhes para essa assinatura**.

   ![localizar usuário](./media/resource-group-create-service-principal-portal/view-details.png)

1. Exiba suas funções atribuídas e determine se você tem as permissões adequadas para atribuir um aplicativo do AD a uma função. Caso contrário, peça ao administrador da assinatura para adicioná-lo à função Administrador de Acesso do Usuário. Na imagem a seguir, o usuário é atribuído à função Proprietário, o que significa que o usuário tem as permissões adequadas.

   ![mostrar permissões](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>Criar um aplicativo do Azure Active Directory

1. Entre sua conta do Azure através do [portal do Microsoft Azure](https://portal.azure.com).
1. Selecione **Azure Active Directory**.

   ![selecionar azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Selecione **Registros do Aplicativo**.

   ![selecionar registros do aplicativo](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Selecione **Novo registro de aplicativo**.

   ![adicionar aplicativo](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Forneça um nome e uma URL para o aplicativo. Selecione **aplicativo Web/API** para o tipo de aplicativo que você deseja criar. Não é possível criar as credenciais para um [Aplicativo nativo](../active-directory/manage-apps/application-proxy-configure-native-client-application.md); portanto, esse tipo não funciona para um aplicativo automatizado. Depois de definir os valores, selecione **Criar**.

   ![nomear aplicativo](./media/resource-group-create-service-principal-portal/create-app.png)

Você criou seu aplicativo.

## <a name="get-application-id-and-authentication-key"></a>Obter chave de autenticação e ID do aplicativo

Ao fazer logon por meio de programação, você precisa da ID para seu aplicativo e de uma chave de autenticação. Para obter esses valores, use as seguintes etapas:

1. Em **Registros do Aplicativo** no Azure Active Directory, selecione seu aplicativo.

   ![selecionar aplicativo](./media/resource-group-create-service-principal-portal/select-app.png)

1. Copie a **ID do aplicativo** e armazene-a no código do aplicativo. Alguns [aplicativos de exemplo](#log-in-as-the-application)se refere a esse valor como a ID do Cliente.

   ![ID do cliente](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. Para gerar uma chave de autenticação, selecione **Configurações**.

   ![selecione configurações](./media/resource-group-create-service-principal-portal/select-settings.png)

1. Para gerar uma chave de autenticação, selecione **Chaves**.

   ![selecionar chaves](./media/resource-group-create-service-principal-portal/select-keys.png)

1. Forneça uma descrição da chave e uma duração para a chave. Ao terminar, escolha **Salvar**.

   ![salvar chave](./media/resource-group-create-service-principal-portal/save-key.png)

   Após salvar a chave, o valor da chave é exibido. Copie este valor, pois não é possível recuperar a chave posteriormente. Forneça o valor da chave com a ID do aplicativo para fazer logon como o aplicativo. Armazene o valor da chave onde seu aplicativo possa recuperá-lo.

   ![chave salva](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>Obter a ID do locatário

Ao fazer logon por meio de programação, você precisa passar a ID de locatário com a solicitação de autenticação.

1. Selecione **Azure Active Directory**.

   ![selecionar azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Para obter a ID de locatário, selecione **Propriedades** do seu locatário do Azure AD.

   ![selecionar propriedades do Azure AD](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. Copie a **ID de diretório**. Esse valor é a ID do locatário.

   ![ID do locatário](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Atribuir aplicativo à função

Para acessar recursos em sua assinatura, você deve atribuir o aplicativo a uma função. Decida qual função representa as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: funções internas](../role-based-access-control/built-in-roles.md).

Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, adicionar um aplicativo à função Leitor de um grupo de recursos significa que ele pode ler o grupo de recursos e todos os recursos que ele contiver.

1. Navegue até o nível do escopo ao qual quer atribuir o aplicativo. Por exemplo, para atribuir uma função no escopo da assinatura, escolha **Assinaturas**. Em vez disso, você pode selecionar um grupo de recursos ou um recurso.

   ![selecione a assinatura](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Escolha a assinatura específica (grupo de recursos ou recurso) à qual atribuir o aplicativo.

   ![selecionar assinatura para atribuição](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. Selecione **Controle de Acesso (IAM)**.

   ![selecionar acesso](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Selecione **Adicionar**.

   ![escolher adicionar](./media/resource-group-create-service-principal-portal/select-add.png)

1. Selecione a função que deseja atribuir ao aplicativo. Para permitir que o aplicativo execute ações como **reiniciar**, **iniciar** e **parar** instâncias, será necessário selecionar a função **Colaborador**. A imagem a seguir mostra a função **Leitor**.

   ![escolher função](./media/resource-group-create-service-principal-portal/select-role.png)

1. Por padrão, os aplicativos do Azure Active Directory não são exibidos nas opções disponíveis. Para localizar o aplicativo, é necessário fornecer o nome do aplicativo no campo de pesquisa. Selecione-o.

   ![pesquisar aplicativo](./media/resource-group-create-service-principal-portal/search-app.png)

1. Selecione **Salvar** para finalizar a atribuição da função. Agora você vê o aplicativo na lista de usuários atribuídos a uma função para esse escopo.

## <a name="next-steps"></a>Próximas etapas
* Para configurar um aplicativo multilocatário, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).
* Para aprender a especificar as políticas de segurança, consulte [Controle de Acesso baseado nas Funções do Azure](../role-based-access-control/role-assignments-portal.md).  
* Para obter uma lista de ações disponíveis que podem ser concedidas ou negadas a usuários, consulte [Operações do Provedor de Recursos do Azure Resource Manager](../role-based-access-control/resource-provider-operations.md).
