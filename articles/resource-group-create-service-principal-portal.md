<properties
   pageTitle="Criar um aplicativo do AD e uma entidade de serviço no portal | Microsoft Azure"
   description="Descreve como criar um novo aplicativo do Active Directory e uma nova entidade de serviço que possam ser usados com o controle de acesso baseado em função no Gerenciador de Recursos do Azure, para gerenciar o acesso aos recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="tomfitz"/>

# Criar o aplicativo do Active Directory e a entidade de serviço usando o portal

## Visão geral
Quando tiver um aplicativo ou processo automatizado que precisa acessar ou modificar um recursos, você poderá usar o portal clássico para criar um aplicativo do Active Directory. Quando você cria um aplicativo do Active Directory por meio do portal clássico, ele realmente cria o aplicativo e uma entidade de serviço. Você pode executar o aplicativo em sua própria identidade ou sob a identidade do usuário conectado de seu aplicativo. Esses dois métodos de autenticação dos aplicativos são chamados de interativo (o usuário se conecta) e não interativo (o aplicativo fornece suas próprias credenciais). No modo não interativo, você deve atribuir a entidade de serviço a uma função com a permissão correta.

Este tópico mostra como criar um novo aplicativo e uma nova entidade de serviço usando o portal clássico. Atualmente, você deve usar o portal clássico para criar um novo aplicativo do Active Directory. Essa capacidade será adicionada ao portal do Azure em uma versão posterior. Você pode usar o portal para atribuir o aplicativo a uma função. Você também pode executar essas etapas através do Azure PowerShell ou da CLI do Azure. Para obter mais informações sobre como usar o PowerShell ou a CLI com a entidade de serviço, confira [Autenticação de uma entidade de serviço com o Gerenciador de Recursos do Azure](resource-group-authenticate-service-principal.md).

## Conceitos
1. AAD (Active Directory do Azure) - um serviço de gerenciamento de identidades e acesso desenvolvido para a nuvem. Para mais detalhes, consulte: [O que é o Azure Active Directory](active-directory/active-directory-whatis.md)
2. Entidade de serviço - uma instância de um aplicativo em um diretório.
3. Aplicativo do AD - um registro de diretório no AAD que identifica um aplicativo ao AAD. 

Para obter uma explicação mais detalhada de aplicativos e entidades de serviço, consulte [Objetos de aplicativo e de entidade de serviço](active-directory/active-directory-application-objects.md). Para obter mais informações sobre a autenticação do Active Directory, consulte [Cenários de autenticação do Azure AD](active-directory/active-directory-authentication-scenarios.md).


## Criar aplicativo

Para aplicativos interativos e não interativos, você deve criar e configurar seu aplicativo do Active Directory.

1. Faça logon na sua conta do Azure por meio do [portal clássico](https://manage.windowsazure.com/).

2. Selecione **Active Directory** no painel à esquerda.

     ![selecionar Active Directory][1]

3. Selecione o diretório que você deseja usar para criar o novo aplicativo.

     ![escolher o diretório][2]

3. Para exibir os aplicativos em seu diretório, clique em **Aplicativos**.

     ![exibir aplicativos][11]

4. Se você não criou um aplicativo nesse diretório antes de você verá algo semelhante à imagem a seguir. Clique em **ADICIONAR UM APLICATIVO**

     ![adicionar aplicativo][6]

     Ou então, clique em **Adicionar** no painel inferior.

     ![adicionar][12]

5. Selecione o tipo de aplicativo que você deseja criar. Para este tutorial, não usaremos um aplicativo da Galeria.

     ![novo aplicativo][10]

6. Preencha o nome do aplicativo e selecione o tipo de aplicativo que você deseja usar. Selecione o tipo de aplicativo que você está criando. Para este tutorial, escolheremos criar um **APLICATIVO WEB E/OU API WEB** e clicaremos no botão Avançar.

     ![nomear aplicativo][9]

7. Preencha as propriedades de seu aplicativo. Para **URL DE LOGON**, forneça o URI para um site da Web que descreve seu aplicativo. A existência do site Web não é validada. Para **URI DE ID DO APLICATIVO**, forneça o URI que identifica seu aplicativo. A exclusividade ou existência do ponto de extremidade não está validada. Se você tiver selecionado **Aplicativo Cliente Nativo** para o tipo de aplicativo, forneça um valor para **URI de Redirecionamento**. Clique em **Concluir** para criar seu aplicativo AAD.

     ![propriedades do aplicativo][4]

Você criou seu aplicativo.

## Obter a id do cliente e a id do locatário

Ao acessar programaticamente seu aplicativo, você precisará da id dele. Escolha a guia **Configurar** e copie a **ID DO CLIENTE**.
  
   ![ID do cliente][5]

Em alguns casos, você precisa passar o ID de locatário com a solicitação de autenticação. Para aplicativos Web e aplicativos de API Web, você pode recuperar a ID de locatário selecionando **Exibir pontos de extremidade** na parte inferior da tela e recuperando a ID, conforme mostrado abaixo.

   ![ID do locatário](./media/resource-group-create-service-principal-portal/save-tenant.png)

Não existem pontos de extremidade disponíveis para Aplicativos Clientes Nativos. Em vez disso, você pode recuperar a ID do locatário por meio do PowerShell:

    PS C:\> Get-AzureRmSubscription

ou o CLI do Azure:

    azure account show --json

## Criar uma chave de autenticação

Se o aplicativo for executado com suas próprias credenciais, será necessário criar uma chave para o aplicativo.

1. Clique na guia **Configurar** para definir a senha do aplicativo.

     ![configurar o aplicativo][3]

2. Role para baixo até a seção **Chaves** e selecione por quanto tempo você gostaria que a senha fosse válida.

     ![chaves][7]

3. Selecione **Salvar** para criar a chave.

     ![salvar][13]

     A chave salva é exibida e você pode copiá-la. Você não poderá recuperar a chave mais tarde, portanto convém copiá-la agora.

     ![chave salva][8]

Seu aplicativo agora está pronto e a entidade de serviço criada em seu locatário. Ao fazer logon como uma entidade de serviço, certifique-se de usar:

* **ID DO CLIENTE** - como seu nome de usuário.
* **CHAVE** - como sua senha.

## Definir permissões delegadas

Se o aplicativo acessa recursos em nome do usuário conectado, você deve conceder ao aplicativo a permissão delegada para acessar outros aplicativos. Faça isso na seção **Permissões para outros aplicativos** da guia **Configurar**. Por padrão, uma permissão delegada já está habilitada para o Active Directory do Azure. Não altere essa permissão delegada.

1. Selecione **Adicionar aplicativo**.

2. Na lista, selecione a **API de gerenciamento de serviços do Azure**.

      ![selecionar aplicativo](./media/resource-group-create-service-principal-portal/select-app.png)

3. Adicione a permissão delegada **Acessar o Gerenciamento de Serviço do Azure (visualização)** à API de gerenciamento de serviços.

       ![selecionar permissão](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Salve a alteração.

## Configurar aplicativo multilocatário

Se for permitido que os usuários de outros Active Directories do Azure deem concentimento para o aplicativo e entrar nele, habilite a multilocação. Na guia **Configurar**, defina **O aplicativo é multilocatário** como **Sim**.

![multilocatário](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## Atribuir aplicativo à função

Se o aplicativo não estiver em execução sob a identidade de um usuário conectado, você deverá atribuir o aplicativo a uma função para conceder permissões de execução de ações. Para atribuir o aplicativo a uma função, alterne do portal clássico para o [Portal do Azure](https://portal.azure.com). Você deve decidir à qual função adicionar o aplicativo e em que escopo. Para saber mais sobre as funções disponíveis, confira [RBAC: funções internas](./active-directory/role-based-access-built-in-roles.md). Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissões são herdadas de níveis inferiores do escopo (por exemplo, adicionar um aplicativo à função Leitor de um grupo de recursos significa que ele pode ler o grupo de recursos e todos os recursos que ele contiver).

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

Para saber mais sobre como atribuir usuários e aplicativos a funções usando o portal, confira [Gerenciar o acesso usando o Portal de Gerenciamento do Azure](../role-based-access-control-configure/#manage-access-using-the-azure-management-portal).

## Obter o token de acesso no código

Se estiver usando o .NET, você poderá recuperar o token de acesso para o aplicativo com o código a seguir.

Primeiro, você deve instalar a Biblioteca de Autenticação do Active Directory em seu projeto do Visual Studio. A maneira mais fácil de fazer isso é usar o pacote do NuGet. Abra o Console do Gerenciador de Pacotes e digite os comandos a seguir.

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

Para entrar com sua ID do aplicativo e um segredo, use o seguinte método para recuperar o token.

    public static string GetAccessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
        var credential = new ClientCredential(clientId: "{client id}", clientSecret: "{application password}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

Para entrar em nome do usuário, use o seguinte método para recuperar o token.

    public static string GetAcessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", {client id}, new Uri({redirect uri});

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

Você pode passar o token no cabeçalho de solicitação com o seguinte código:

    string token = GetAcessToken();
    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);


## Próximas etapas

- Para aprender a especificar políticas de segurança, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md).  
- Para obter uma demonstração em vídeo dessas etapas, confira [Habilitando o gerenciamento programático de um Recurso do Azure Resource com o Active Directory do Azure](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).
- Para saber como usar o Azure PowerShell ou a CLI do Azure para trabalhar com aplicativos do Active Directory e entidades de serviço, incluindo como usar um certificado para autenticação, confira [Autenticação de uma entidade de serviço com o Gerenciador de Recursos do Azure](./resource-group-authenticate-service-principal.md).
- Para obter orientações sobre como implantar recursos de segurança com o Gerenciador de Recursos do Azure, confira [Considerações de segurança do Gerenciador de Recursos do Azure](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png

<!---HONumber=AcomDC_0316_2016-->