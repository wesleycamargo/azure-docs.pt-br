---
title: "Criar uma entidade de serviço no portal | Microsoft Docs"
description: "Descreve como criar um novo aplicativo do Active Directory e uma nova entidade de serviço que possam ser usados com o controle de acesso baseado em função no Gerenciador de Recursos do Azure, para gerenciar o acesso aos recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/30/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 4312002b311ec17f175f6eb6bc45fbe1ce7c7a01
ms.openlocfilehash: 3232aa0356353e3856286c38d931543a254fd9fd


---
# <a name="use-portal-to-create-active-directory-application-and-service-principal-that-can-access-resources"></a>Usar o portal para criar um aplicativo e entidade de serviço do Active Directory que pode acessar recursos
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [CLI do Azure](resource-group-authenticate-service-principal-cli.md)
> * [Portal](resource-group-create-service-principal-portal.md)
>
>

Quando você tiver um aplicativo que precisa acessar ou modificar os recursos, deverá configurar um aplicativo do Active Directory (AD) e atribuir as permissões necessárias a ele. Este tópico mostra como executar essas etapas no portal. Ele se concentra em um aplicativo de locatário único que se destina a ser executado dentro de uma única organização. Você normalmente usa os aplicativos com um único locatário para os aplicativos da linha de negócios executados em sua organização.
 
## <a name="required-permissions"></a>Permissões necessárias
Para concluir este tópico, você deve ter permissões suficientes para registrar um aplicativo com o Active Directory e atribuir o aplicativo a uma função em sua assinatura do Azure. Vamos verificar se você tem as permissões corretas para executar essas etapas.

### <a name="check-active-directory-permissions"></a>Verifique as permissões do Active Directory
1. Entre na sua conta do Azure por meio do [Portal do Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory**.

     ![selecionar azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)
3. No Active Directory, selecione **Configurações de Usuário**.

     ![selecionar configurações de usuário](./media/resource-group-create-service-principal-portal/select-user-settings.png)
4. Verifique a configuração **Registros do Aplicativo**. Se estiver definida como **Sim**, usuários que não são administradores podem registrar aplicativos do AD. Essa configuração significa que qualquer usuário no Active Directory pode registrar um aplicativo. Você pode prosseguir com a [verificação das permissões de assinatura do Azure](#check-azure-subscription-permissions).

     ![exibir registros de aplicativo](./media/resource-group-create-service-principal-portal/view-app-registrations.png)
5. Se a configuração de registros do aplicativo estiver definida como **Não**, somente usuários que são administradores podem registrar aplicativos. Você precisa verificar se sua conta é de administrador do Active Directory. Selecione **Visão Geral** e **Localizar um usuário** nas Tarefas Rápidas.

     ![localizar usuário](./media/resource-group-create-service-principal-portal/find-user.png)
6. Procure por sua conta e selecione-a quando encontrá-la.

     ![pesquisar usuário](./media/resource-group-create-service-principal-portal/show-user.png)
7. Para sua conta, selecione **função de diretório**. 

     ![função de diretório](./media/resource-group-create-service-principal-portal/select-directory-role.png)
8. Exiba sua função atribuída para o Active Directory. Se sua conta estiver atribuída com a função de usuário, mas a configuração de registro de aplicativo (de etapas anteriores) estiver limitada a usuários administradores, peça ao administrador para atribuir uma função de administrador a você ou para permitir que os usuários registrem aplicativos.

     ![exibir função](./media/resource-group-create-service-principal-portal/view-role.png)

### <a name="check-azure-subscription-permissions"></a>Verificar permissões de assinatura do Azure
Em sua assinatura do Azure, sua conta deve ter acesso de `Microsoft.Authorization/*/Write` para atribuir um aplicativo do AD a uma função. Esta ação deve ser concedida pela função [Proprietário](../active-directory/role-based-access-built-in-roles.md#owner) ou pela função [Administrador de Acesso do Usuário](../active-directory/role-based-access-built-in-roles.md#user-access-administrator). Se sua conta tiver a função **Colaborador**, você não tem a permissão adequada. Você receberá um erro ao tentar atribuir a entidade de serviço a uma função. 

Para verificar suas permissões de assinatura:

1. Se você não entrou em sua conta do Active Directory usando as etapas anteriores, selecione **Azure Active Directory** no painel à esquerda.

2. Localize sua conta do Active Directory. Selecione **Visão Geral** e **Localizar um usuário** nas Tarefas Rápidas.

     ![localizar usuário](./media/resource-group-create-service-principal-portal/find-user.png)
2. Procure por sua conta e selecione-a quando encontrá-la.

     ![pesquisar usuário](./media/resource-group-create-service-principal-portal/show-user.png) 
     
3. Selecione **Recursos do Azure**.

     ![selecionar recursos](./media/resource-group-create-service-principal-portal/select-azure-resources.png) 
3. Exiba suas funções atribuídas e determine se você tem as permissões adequadas para atribuir um aplicativo do AD a uma função. Caso contrário, peça ao administrador da assinatura para adicioná-lo à função Administrador de Acesso do Usuário. Na imagem a seguir, o usuário é atribuído à função Proprietário para duas assinaturas, o que significa que o usuário tem as permissões adequadas. 

     ![mostrar permissões](./media/resource-group-create-service-principal-portal/view-assigned-roles.png)

## <a name="create-an-active-directory-application"></a>Criar um aplicativo do Active Directory
1. Entre na sua conta do Azure por meio do [Portal do Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory**.

     ![selecionar azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

4. Selecione **Registros do Aplicativo**.   

     ![selecionar registros do aplicativo](./media/resource-group-create-service-principal-portal/select-app-registrations.png)
5. Selecione **Adicionar**.

     ![adicionar aplicativo](./media/resource-group-create-service-principal-portal/select-add-app.png)

6. Forneça um nome e uma URL para o aplicativo. Selecione **aplicativo Web/API** ou **Nativo** para o tipo de aplicativo que você deseja criar. Depois de definir os valores, selecione **Criar**.

     ![nomear aplicativo](./media/resource-group-create-service-principal-portal/create-app.png)

Você criou seu aplicativo.

## <a name="get-application-id-and-authentication-key"></a>Obter chave de autenticação e ID do aplicativo
Ao fazer logon por meio de programação, você precisa da ID para seu aplicativo e de uma chave de autenticação. Para obter esses valores, use as seguintes etapas:

1. De **Registros do Aplicativo** no Active Directory, selecione seu aplicativo.

     ![selecionar aplicativo](./media/resource-group-create-service-principal-portal/select-app.png)
2. Copie a **ID do aplicativo** e armazene-a no código do aplicativo. Os aplicativos na seção [Aplicativos de exemplo](#sample-applications) referem-se a esse valor como a ID do cliente.

     ![ID do CLIENTE](./media/resource-group-create-service-principal-portal/copy-app-id.png)
3. Para gerar uma chave de autenticação, selecione **Chaves**.

     ![selecionar chaves](./media/resource-group-create-service-principal-portal/select-keys.png)
4. Forneça uma descrição da chave e uma duração para a chave. Ao terminar, escolha **Salvar**.

     ![salvar chave](./media/resource-group-create-service-principal-portal/save-key.png)

     Após salvar a chave, o valor da chave é exibido. Copie este valor, pois não é possível recuperar a chave posteriormente. Forneça o valor da chave com a ID do aplicativo para fazer logon como o aplicativo. Armazene o valor da chave onde seu aplicativo possa recuperá-lo.

     ![chave salva](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>Obter a ID do locatário
Ao fazer logon por meio de programação, você precisa passar a ID de locatário com a solicitação de autenticação. 

1. Para obter a ID de locatário, selecione **Propriedades** em seu Active Directory. 

     ![selecionar propriedades do active directory](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

2. Copie a **ID de diretório**. Esse valor é a ID do locatário.

     ![ID do locatário](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Atribuir aplicativo à função
Para acessar recursos em sua assinatura, você deve atribuir o aplicativo a uma função. Decida qual função representa as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md).

Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, adicionar um aplicativo à função Leitor de um grupo de recursos significa que ele pode ler o grupo de recursos e todos os recursos que ele contiver.

1. Navegue até o nível do escopo ao qual quer atribuir o aplicativo. Por exemplo, para atribuir uma função no escopo da assinatura, escolha **Assinaturas**. Em vez disso, você pode selecionar um grupo de recursos ou um recurso.

     ![selecione a assinatura](./media/resource-group-create-service-principal-portal/select-subscription.png)

2. Escolha a assinatura específica (grupo de recursos ou recurso) à qual atribuir o aplicativo.

     ![selecionar assinatura para atribuição](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

3. Selecione **Controle de Acesso (IAM)**.

     ![selecionar acesso](./media/resource-group-create-service-principal-portal/select-access-control.png)

4. Selecione **Adicionar**.

     ![escolher adicionar](./media/resource-group-create-service-principal-portal/select-add.png)
6. Selecione a função que deseja atribuir ao aplicativo. A imagem a seguir mostra a função **Leitor**.

     ![escolher função](./media/resource-group-create-service-principal-portal/select-role.png)

8. Procure seu aplicativo e selecione-o.

     ![pesquisar aplicativo](./media/resource-group-create-service-principal-portal/search-app.png)
9. Selecione **OK** para finalizar a atribuição da função. Agora você vê o aplicativo na lista de usuários atribuídos a uma função para esse escopo.

Seu aplicativo agora está configurado no Active Directory. Você tem uma ID e a chave a ser usada para fazer logon como o aplicativo. O aplicativo está atribuído a uma função que oferece determinadas ações que ele pode executar. Você pode procurar os aplicativos de exemplo para saber mais sobre como realizar tarefas no código do aplicativo.

## <a name="sample-applications"></a>Aplicativos de exemplo
Os aplicativos de exemplo a seguir mostram como fazer logon como o aplicativo do AD.

**.NET**

* [Implantar uma VM Habilitada para SSH com um Modelo com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Gerenciar recursos e grupos de recursos do Azure com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [Introdução aos Recursos - Implantar Usando o Modelo do Azure Resource Manager - em Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [Introdução aos Recursos - Gerenciar o Grupo de Recursos - em Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Implantar uma VM Habilitada para SSH com um Modelo no Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Gerenciando o Recurso e Grupos de Recursos do Azure com o Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

* [Implantar uma VM Habilitada para SSH com um Modelo no Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Gerenciar recursos e grupos de recursos do Azure com Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Implantar uma VM Habilitada para SSH com um Modelo no Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Gerenciando o Recurso e Grupos de Recursos do Azure com Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Próximas etapas
* Para configurar um aplicativo multilocatário, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).
* Para aprender a especificar as políticas de segurança, consulte [Controle de Acesso baseado nas Funções do Azure](../active-directory/role-based-access-control-configure.md).  




<!--HONumber=Dec16_HO1-->


