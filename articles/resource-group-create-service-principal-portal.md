<properties
   pageTitle="Criar uma entidade de serviço no portal | Microsoft Azure"
   description="Descreve como criar um novo aplicativo do Active Directory e uma nova entidade de serviço que possam ser usados com o controle de acesso baseado em função no Gerenciador de Recursos do Azure, para gerenciar o acesso aos recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/19/2016"
   ms.author="tomfitz"/>

# Usar o portal para criar um aplicativo e entidade de serviço do Active Directory que pode acessar recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI do Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)


Quando você tiver um aplicativo que precisa acessar ou modificar os recursos, deverá configurar um aplicativo do Active Directory (AD) e atribuir as permissões necessárias a ele. Este tópico mostra como executar essas etapas no portal. Atualmente, você deve usar o portal clássico para criar um novo aplicativo do Active Directory e, em seguida, alternar para o portal do Azure para atribuir uma função ao aplicativo.

> [AZURE.NOTE] Você pode achar mais fácil configurar seu aplicativo do AD e a entidade de serviço por meio do [PowerShell](resource-group-authenticate-service-principal.md) ou da [CLI do Azure](resource-group-authenticate-service-principal-cli.md), especialmente se quiser usar um certificado para a autenticação. Este tópico não mostra como usar um certificado.

Para ter uma explicação dos conceitos do Active Directory, consulte [Objetos do Aplicativo e da Entidade de Serviço](./active-directory/active-directory-application-objects.md). Para obter mais informações sobre a autenticação do Active Directory, consulte [Cenários de autenticação do Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Para ver as etapas detalhadas sobre como integrar um aplicativo no Azure para gerenciar os recursos, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).

## Criar um aplicativo do Active Directory

1. Faça logon na sua conta do Azure por meio do [portal clássico](https://manage.windowsazure.com/).

2. Selecione **Active Directory** no painel à esquerda.

     ![selecionar Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. Selecione o Active Directory que você deseja usar para criar o novo aplicativo. Caso possua mais de um Active Directory, normalmente, é melhor que você crie o aplicativo no diretório em que reside a sua assinatura. Só é possível conceder acesso a recursos em sua assinatura para aplicativos no mesmo diretório de sua assinatura.

     ![escolher o diretório](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
    Se você precisar localizar o diretório de sua assinatura, selecione **Configurações** e procure o nome do diretório.
   
     ![encontrar diretório padrão](./media/resource-group-create-service-principal-portal/show-default-directory.png)

3. Para exibir os aplicativos em seu diretório, clique em **Aplicativos**.

     ![exibir aplicativos](./media/resource-group-create-service-principal-portal/view-applications.png)

4. Se você não criou um aplicativo nesse diretório antes de você verá algo semelhante à imagem a seguir. Clique em **ADICIONAR UM APLICATIVO**

     ![adicionar aplicativo](./media/resource-group-create-service-principal-portal/create-application.png)

     Ou então, clique em **Adicionar** no painel inferior.

     ![adicionar](./media/resource-group-create-service-principal-portal/add-icon.png)

5. Selecione o tipo de aplicativo que você deseja criar. Para este tutorial, selecione **Adicionar um aplicativo que minha organização está desenvolvendo**.

     ![novo aplicativo](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. Forneça um nome para o aplicativo e selecione o tipo de aplicativo que você deseja criar. Para este tutorial, crie um **APLICATIVO WEB E/OU API DA WEB** e clique no botão Avançar. Se você selecionar **APLICATIVO CLIENTE NATIVO**, o restante das etapas deste artigo não corresponderá à sua experiência.

     ![nomear aplicativo](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. Preencha as propriedades de seu aplicativo. Para a **URL DE LOGON**, forneça o URI para um site da Web que descreve seu aplicativo. A existência do site Web não é validada. Para **URI DE ID DO APLICATIVO**, forneça o URI que identifica seu aplicativo.

     ![propriedades do aplicativo](./media/resource-group-create-service-principal-portal/app-properties.png)

Você criou seu aplicativo.

## Obter chave de autenticação e a id de cliente

Ao fazer logon por meio de programação, você precisa da id para seu aplicativo. Se o aplicativo for executado com suas próprias credenciais, você também precisará de uma chave de autenticação.

1. Clique na guia **Configurar** para definir a senha do aplicativo.

     ![configurar o aplicativo](./media/resource-group-create-service-principal-portal/application-configure.png)

2. Copie a **ID do CLIENTE**.
  
     ![ID do cliente](./media/resource-group-create-service-principal-portal/client-id.png)

3. Se o aplicativo for executado com suas próprias credenciais, role para baixo até a seção **Chaves** e selecione por quanto tempo você deseja que a senha seja válida.

     ![chaves](./media/resource-group-create-service-principal-portal/create-key.png)

4. Selecione **Salvar** para criar a chave.

     ![salvar](./media/resource-group-create-service-principal-portal/save-icon.png)

     A chave salva é exibida e você pode copiá-la. Você não poderá recuperar a chave mais tarde, portanto convém copiá-la agora.

     ![chave salva](./media/resource-group-create-service-principal-portal/save-key.png)

## Obter a id do locatário

Ao fazer logon por meio de programação, você precisa passar a id de locatário com a solicitação de autenticação. Para os Aplicativos Web e Aplicativos de API Web, você pode recuperar a ID do locatário selecionando **Exibir pontos de extremidade** na parte inferior da tela e recuperando a ID, como mostrado abaixo.

   ![ID do locatário](./media/resource-group-create-service-principal-portal/save-tenant.png)

Você pode também recuperar a id do locatário por meio do PowerShell:

    Get-AzureRmSubscription

ou o CLI do Azure:

    azure account show --json

## Definir permissões delegadas

Se o aplicativo acessa recursos em nome do usuário conectado, você deve conceder ao aplicativo a permissão delegada para acessar outros aplicativos. Faça isso na seção **Permissões para outros aplicativos** da guia **Configurar**. Por padrão, uma permissão delegada já está habilitada para o Active Directory do Azure. Não altere essa permissão delegada.

1. Selecione **Adicionar aplicativo**.

2. Na lista, selecione a **API de Gerenciamento de Serviços do Azure**. Em seguida, clique no ícone Concluído.

      ![selecionar aplicativo](./media/resource-group-create-service-principal-portal/select-app.png)

3. Na lista suspensa das permissões delegadas, selecione **Acessar o Gerenciamento de Serviços do Azure como organização**.

      ![selecionar permissão](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Salve a alteração.

## Configurar aplicativo multilocatário

Se for permitido que os usuários de outros Active Directories do Azure deem concentimento para o aplicativo e entrar nele, habilite a multilocação. Na guia **Configurar**, defina **O aplicativo é multilocatário** como **Sim**.

![multilocatário](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## Atribuir aplicativo à função

Se seu aplicativo for executado com suas próprias credenciais, você deve atribuir o aplicativo a uma função. Você deve decidir qual função representa as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, confira [RBAC: funções internas](./active-directory/role-based-access-built-in-roles.md).

Para atribuir uma função, você deve ter o acesso `Microsoft.Authorization/*/Write` que é concedido por meio da função [Proprietário](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrador de Acesso do Usuário](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissões são herdadas de níveis inferiores do escopo (por exemplo, adicionar um aplicativo à função Leitor de um grupo de recursos significa que ele pode ler o grupo de recursos e todos os recursos que ele contiver).

1. Para atribuir o aplicativo a uma função, alterne do portal clássico para o [Portal do Azure](https://portal.azure.com).

1. No portal, navegue até o nível do escopo ao qual deseja atribuir o aplicativo. Neste tópico, você pode navegar para um grupo de recursos e, na folha de grupo de recursos, escolha o ícone **Acesso**.

     ![Selecionar usuários](./media/resource-group-create-service-principal-portal/select-users.png)

2. Selecione **Adicionar**.

     ![escolher adicionar](./media/resource-group-create-service-principal-portal/select-add.png)

3. Escolha a função **Leitor** (ou qualquer função à qual deseja atribuir o aplicativo).

     ![escolher função](./media/resource-group-create-service-principal-portal/select-role.png)

4. Quando visualizar a lista de usuários, você poderá adicionar à função; você não verá os aplicativos. Apenas o grupo e os usuários.

     ![mostrar usuários](./media/resource-group-create-service-principal-portal/show-users.png)

5. Para localizar o aplicativo, você deverá procurá-lo. Comece a digitar o nome do aplicativo, e a lista de opções disponíveis mudará. Escolha o aplicativo quando o visualizar na lista.

     ![atribuir à função](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. Escolha **OK** para finalizar a atribuição da função. Agora você deve ver o aplicativo na lista de usuários atribuídos a uma função do grupo de recursos.

     ![mostrar](./media/resource-group-create-service-principal-portal/show-app.png)

Para saber mais sobre como atribuir usuários e aplicativos a funções usando o portal, confira [Gerenciar o acesso usando o Portal de Gerenciamento do Azure](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal).

## Aplicativos de exemplo

Os aplicativos de exemplo a seguir mostram como fazer logon como a entidade de serviço.

**.NET**

- [Implantar uma VM Habilitada para SSH com um Modelo com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Gerenciar recursos e grupos de recursos do Azure com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Introdução aos Recursos - Implantar Usando o Modelo do Azure Resource Manager - em Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Introdução aos Recursos - Gerenciar o Grupo de Recursos - em Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Implantar uma VM Habilitada para SSH com um Modelo no Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Gerenciando o Recurso e Grupos de Recursos do Azure com o Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [Implantar uma VM Habilitada para SSH com um Modelo no Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Gerenciar recursos e grupos de recursos do Azure com Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Implantar uma VM Habilitada para SSH com um Modelo no Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Gerenciando o Recurso e Grupos de Recursos do Azure com Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## Próximas etapas

- Para aprender a especificar políticas de segurança, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md).
- Para obter uma demonstração em vídeo dessas etapas, confira [Habilitando o gerenciamento programático de um Recurso do Azure Resource com o Active Directory do Azure](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).

<!---HONumber=AcomDC_0720_2016-->