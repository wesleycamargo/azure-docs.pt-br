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
ms.date: 09/07/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 1a0889e16d0b0e4d361f573ac34ad868936ed20c


---
# <a name="use-portal-to-create-active-directory-application-and-service-principal-that-can-access-resources"></a>Usar o portal para criar um aplicativo e entidade de serviço do Active Directory que pode acessar recursos
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [CLI do Azure](resource-group-authenticate-service-principal-cli.md)
> * [Portal](resource-group-create-service-principal-portal.md)
>
>

Quando você tiver um aplicativo que precisa acessar ou modificar os recursos, deverá configurar um aplicativo do Active Directory (AD) e atribuir as permissões necessárias a ele. Este tópico mostra como executar essas etapas no portal. Atualmente, você deve usar o portal clássico para criar um novo aplicativo do Active Directory e, em seguida, alternar para o portal do Azure para atribuir uma função ao aplicativo.

> [!NOTE]
> As etapas neste tópico se aplicam somente ao usar o **portal clássico** para criar o aplicativo do AD. **Se você usar o portal do Azure para criar o aplicativo do AD, essas etapas não terão êxito.**
>
> Você pode achar mais fácil configurar seu aplicativo do AD e a entidade de serviço por meio do [PowerShell](resource-group-authenticate-service-principal.md) ou da [CLI do Azure](resource-group-authenticate-service-principal-cli.md), especialmente se quiser usar um certificado para a autenticação. Este tópico não mostra como usar um certificado.
>
>

Para ter uma explicação dos conceitos do Active Directory, consulte [Objetos do Aplicativo e da Entidade de Serviço](../active-directory/active-directory-application-objects.md).
Para obter mais informações sobre a autenticação do Active Directory, consulte [Cenários de Autenticação do Azure AD](../active-directory/active-directory-authentication-scenarios.md).

Para ver as etapas detalhadas sobre como integrar um aplicativo no Azure para gerenciar os recursos, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).

## <a name="create-an-active-directory-application"></a>Criar um aplicativo do Active Directory
1. Faça logon na sua conta do Azure por meio do [Portal Clássico](https://manage.windowsazure.com/).
2. Tenha certeza de saber o Active Directory padrão para sua assinatura. Você pode conceder acesso para aplicativos apenas no mesmo diretório que sua assinatura. Escolha **Configurações** e procure o nome do diretório associado à sua assinatura.  Para saber mais sobre, confira [Como as assinaturas do Azure são associadas ao Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

     ![encontrar diretório padrão](./media/resource-group-create-service-principal-portal/show-default-directory.png)
3. Selecione **Active Directory** no painel à esquerda.

     ![selecionar Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
4. Escolha o Active Directory que quer usar para criar o aplicativo. Se tiver mais de um Active Directory, crie o aplicativo no diretório padrão para sua assinatura.   

     ![escolher o diretório](./media/resource-group-create-service-principal-portal/active-directory-details.png)
5. Para exibir os aplicativos em seu diretório, escolha **Aplicativos**.

     ![exibir aplicativos](./media/resource-group-create-service-principal-portal/view-applications.png)
6. Se você não criou um aplicativo nesse diretório antes, você verá algo semelhante à imagem a seguir. Escolha **ADICIONAR UM APLICATIVO**

     ![Adicionar aplicativo](./media/resource-group-create-service-principal-portal/create-application.png)

     Ou então, clique em **Adicionar** no painel inferior.

     ![Adicionar](./media/resource-group-create-service-principal-portal/add-icon.png)
7. Selecione o tipo de aplicativo que você deseja criar. Para este tutorial, escolha **Adicionar um aplicativo que minha organização está desenvolvendo**.

     ![novo aplicativo](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)
8. Forneça um nome para o aplicativo e selecione o tipo de aplicativo que você deseja criar. Para este tutorial, crie um **APLICATIVO WEB E/OU API WEB** e clique no botão Avançar. Se você escolher **APLICATIVO CLIENTE NATIVO**, o restante das etapas deste artigo não corresponderá à sua experiência.

     ![nomear aplicativo](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)
9. Preencha as propriedades de seu aplicativo. Para a **URL DE ENTRADA**, forneça o URI para um site da Web que descreve seu aplicativo. A existência do site Web não é validada.
   Para **URI DE ID DO APLICATIVO**, forneça o URI que identifica seu aplicativo.

     ![propriedades do aplicativo](./media/resource-group-create-service-principal-portal/app-properties.png)

Você criou seu aplicativo.

## <a name="get-client-id-and-authentication-key"></a>Obter chave de autenticação e a id de cliente
Ao fazer logon por meio de programação, você precisa da id para seu aplicativo. Se o aplicativo for executado com suas próprias credenciais, você também precisará de uma chave de autenticação.

1. Escolha a guia **Configurar** para definir a senha do aplicativo.

     ![configurar o aplicativo](./media/resource-group-create-service-principal-portal/application-configure.png)
2. Copie a **ID do CLIENTE**.

     ![ID do CLIENTE](./media/resource-group-create-service-principal-portal/client-id.png)
3. Se o aplicativo for executado com suas próprias credenciais, role para baixo até a seção **Chaves** e selecione por quanto tempo você deseja que a senha seja válida.

     ![Chaves](./media/resource-group-create-service-principal-portal/create-key.png)
4. Selecione **Salvar** para criar a chave.

     ![Salvar](./media/resource-group-create-service-principal-portal/save-icon.png)

     A chave salva é exibida e você pode copiá-la. Não é possível recuperar a chave mais tarde, por isso, copie-a agora.

     ![chave salva](./media/resource-group-create-service-principal-portal/save-key.png)

## <a name="get-tenant-id"></a>Obter a id do locatário
Ao fazer logon por meio de programação, você precisa passar a id de locatário com a solicitação de autenticação. Para os Aplicativos Web e Aplicativos de API Web, você pode recuperar a ID do locatário escolhendo **Exibir pontos de extremidade** na parte inferior da tela e recuperando a ID, como mostrado na imagem abaixo.  

   ![ID do locatário](./media/resource-group-create-service-principal-portal/save-tenant.png)

Você pode também recuperar a id do locatário por meio do PowerShell:

    Get-AzureRmSubscription

ou o CLI do Azure:

    azure account show --json

## <a name="set-delegated-permissions"></a>Definir permissões delegadas
Se o aplicativo acessa recursos em nome do usuário conectado, você deve conceder ao aplicativo a permissão delegada para acessar outros aplicativos. Você concede este acesso na seção **permissões para outros aplicativos** da guia **Configurar**. Por padrão, uma permissão delegada já está habilitada para o Active Directory do Azure. Não altere essa permissão delegada.

1. Escolha **Adicionar aplicativo**.
2. Na lista, escolha a **API de Gerenciamento de Serviço do Microsoft Azure**. Em seguida, clique no ícone Concluído.

      ![selecionar aplicativo](./media/resource-group-create-service-principal-portal/select-app.png)
3. Na lista suspensa das permissões delegadas, escolha **Acessar o Gerenciamento de Serviços do Azure como organização**.

      ![selecionar permissão](./media/resource-group-create-service-principal-portal/select-permissions.png)
4. Salve a alteração.

## <a name="assign-application-to-role"></a>Atribuir aplicativo à função
Se seu aplicativo for executado com suas próprias credenciais, você deve atribuir o aplicativo a uma função. Decida qual função representa as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md).

Para atribuir uma função a um aplicativo, você deve ter as permissões corretas. Especificamente, você deve ter o acesso `Microsoft.Authorization/*/Write` concedido por meio da função [Proprietário](../active-directory/role-based-access-built-in-roles.md#owner) ou da função [Administrador de Acesso do Usuário](../active-directory/role-based-access-built-in-roles.md#user-access-administrator). A função Colaborador não tem o acesso correto.

Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, adicionar um aplicativo à função Leitor de um grupo de recursos significa que ele pode ler o grupo de recursos e todos os recursos que ele contiver.

1. Para atribuir o aplicativo a uma função, alterne do portal clássico para o [Portal do Azure](https://portal.azure.com).
2. Verifique suas permissões para ter certeza de que você pode atribuir a entidade de serviço a uma função. Escolha **Minhas permissões** para sua conta.

    ![selecionar minhas permissões](./media/resource-group-create-service-principal-portal/my-permissions.png)
3. Exiba as permissões atribuídas para sua conta. Conforme observado anteriormente, você deve pertencer às funções Proprietário ou Administrador de Acesso do Usuário, que concede acesso de gravação para Microsoft.Authorization. A imagem a seguir mostra uma conta que é atribuída à função Colaborador para a assinatura, que não tem permissões adequadas para atribuir um aplicativo a uma função.

    ![mostrar minhas permissões](./media/resource-group-create-service-principal-portal/show-permissions.png)

     Se você não tiver as permissões corretas para conceder acesso a um aplicativo, será preciso solicitar que o seu administrador da assinatura adicione você à função Administrador de Acesso do Usuário ou solicitar que um administrador conceda acesso ao aplicativo.
4. Navegue até o nível do escopo ao qual quer atribuir o aplicativo. Para atribuir uma função no escopo da assinatura, escolha **Assinaturas**.

     ![selecione a assinatura](./media/resource-group-create-service-principal-portal/select-subscription.png)

     Escolha a assinatura específica à qual atribuir o aplicativo.

     ![selecionar assinatura para atribuição](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

     Escolha o ícone **Acesso** no canto superior direito.

     ![selecionar acesso](./media/resource-group-create-service-principal-portal/select-access.png)

     Ou, para atribuir uma função no escopo do grupo de recursos, navegue para um grupo de recursos. Na folha do grupo de recursos, escolha **Controle de acesso**.

     ![Selecionar usuários](./media/resource-group-create-service-principal-portal/select-users.png)

     As etapas a seguir são as mesmas para qualquer escopo.
5. Selecione **Adicionar**.

     ![escolher adicionar](./media/resource-group-create-service-principal-portal/select-add.png)
6. Escolha a função **Leitor** (ou qualquer função à qual deseja atribuir o aplicativo).

     ![escolher função](./media/resource-group-create-service-principal-portal/select-role.png)
7. Quando visualizar a lista de usuários, você poderá adicionar à função; você não verá os aplicativos. Apenas o grupo e os usuários.

     ![mostrar usuários](./media/resource-group-create-service-principal-portal/show-users.png)
8. Para localizar o aplicativo, você deverá procurá-lo. Comece a digitar o nome do aplicativo, e a lista de opções disponíveis mudará. Escolha o aplicativo quando o visualizar na lista.

     ![atribuir à função](./media/resource-group-create-service-principal-portal/assign-to-role.png)
9. Escolha **OK** para finalizar a atribuição da função. Agora você deve ver o aplicativo na lista de usuários atribuídos a uma função do grupo de recursos.

Para saber mais sobre como atribuir usuários e aplicativos às funções usando o portal, consulte [Usar as atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](../active-directory/role-based-access-control-configure.md#add-access).

## <a name="sample-applications"></a>Aplicativos de exemplo
Os aplicativos de exemplo a seguir mostram como fazer logon como a entidade de serviço.

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
* Para aprender a especificar as políticas de segurança, consulte [Controle de Acesso baseado nas Funções do Azure](../active-directory/role-based-access-control-configure.md).  
* Para obter uma demonstração em vídeo dessas etapas, confira [Habilitando o gerenciamento programático de um Recurso do Azure Resource com o Active Directory do Azure](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).



<!--HONumber=Nov16_HO3-->


