---
title: Conectar usuários e chamar a API do Microsoft Graph em um aplicativo da área de trabalho .NET (WPF) | Microsoft Docs
description: Saiba como criar um aplicativo da área de trabalho .NET do Windows que se integre ao Azure AD para entrada e que chame as APIs protegidas do Azure AD usando o OAuth 2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b06ceb86e8406657766be1375889fcfe1b4673d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204535"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Início rápido: Conectar usuários e chamar a API do Microsoft Graph de um aplicativo da área de trabalho .NET (WPF)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Para clientes nativos do .NET que precisam acessar recursos protegidos, o Azure AD (Azure Active Directory) fornece a ADAL (Biblioteca de Autenticação do Active Directory). A ADAL facilita a obtenção de tokens de acesso para o aplicativo. 

Neste início rápido, você aprenderá a criar um aplicativo de lista de tarefas pendentes .NET do WPF que:

* Obtém tokens de acesso para chamar a API do Graph do Azure AD usando o protocolo de autenticação OAuth 2.0.
* Pesquisa um diretório para usuários com um determinado alias.
* Desconecta usuários.

Para criar o aplicativo em funcionamento completo, você precisará:

1. Registrar seu aplicativo no Azure AD.
2. Instalar e configurar a ADAL.
3. Usar a ADAL para obter tokens do AD do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, conclua estes pré-requisitos:

* [Baixar o esqueleto do aplicativo](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) ou [Baixar o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)
* Ter um locatário do Azure AD para criar usuários e registrar um aplicativo. Se você ainda não tiver um locatário [saiba como obter um](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Etapa 1: Registrar o aplicativo DirectorySearcher

Para permitir que seu aplicativo obtenha tokens, registre-o no locatário do Azure AD e conceda-lhe permissão para acessar a API do Graph do Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, selecione sua conta e, na lista **Diretório**, escolha o locatário do Active Directory em que você deseja registrar seu aplicativo.
3. Selecione **Todos os serviços** no navegador esquerdo e escolha **Azure Active Directory**.
4. Em **Registros de aplicativo** escolha **Adicionar**.
5. Siga os prompts e crie um aplicativo cliente **nativo**.
    * O **Nome** do aplicativo descreverá seu aplicativo para os usuários finais
    * O **URI de redirecionamento** é uma combinação de esquema e de cadeia de caracteres que o AD do Azure usará para retornar respostas de tokens. Insira um valor específico para seu aplicativo, por exemplo, `http://DirectorySearcher`.

6. Depois de concluir o registro, o AAD atribuirá a seu aplicativo uma ID do Aplicativo única. Você precisará desse valor nas próximas seções, então copie-o da página do aplicativo.
7. Na página **Configurações**, escolha **Permissões necessárias** e, em seguida, **Adicionar**. Escolha o **Microsoft Graph** como a API e adicione a permissão **Ler Dados do Diretório** em **Permissões Delegadas**. A definição dessa permissão permite que seu aplicativo consulte usuários na API do Graph.

## <a name="step-2-install-and-configure-adal"></a>Etapa 2: Instalar e configurar a ADAL

Agora que você tem um aplicativo no AD do Azure, você pode instalar a ADAL e escrever seu código relacionado à identidade. Para que a ADAL possa se comunicar com o Azure AD, é necessário fornecer a ela algumas informações sobre o registro do aplicativo.

1. Comece adicionando a ADAL ao projeto `DirectorySearcher` usando o console do gerenciador de pacotes.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1. No projeto `DirectorySearcher`, abra `app.config`.
1. Substitua os valores dos elementos na seção `<appSettings>` para refletir os valores que você inseriu no portal do Azure. Seu código fará referência a esses valores sempre que ele usar a ADAL.
  * O `ida:Tenant` é o domínio do locatário do Azure AD, por exemplo, contoso.onmicrosoft.com
  * O `ida:ClientId` é a ID do cliente do aplicativo que você copiou do portal.
  * O `ida:RedirectUri` é a URL de redirecionamento que você registrou no portal.

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Etapa 3: Usar a ADAL para obter tokens do Azure AD

O princípio básico da ADAL é que sempre que o aplicativo precisa de um token de acesso, ele simplesmente chama `authContext.AcquireTokenAsync(...)`, e a ADAL cuida do resto.

1. No projeto `DirectorySearcher`, abra `MainWindow.xaml.cs`.
1. Localize o método `MainWindow()`. 
1. Inicialize o `AuthenticationContext` do aplicativo, ou seja, a classe principal da ADAL. `AuthenticationContext` é onde você passa à ADAL as coordenadas que ela precisa para se comunicar com o Azure AD e informá-lo como armazenar tokens em cache.

    ```csharp
    public MainWindow()
    {
        InitializeComponent();

        authContext = new AuthenticationContext(authority, new FileCache());

        CheckForCachedToken();
    }
    ```

1. Localize o método `Search(...)`, que será chamado quando o usuário selecionar o botão **Pesquisar** na interface do usuário do aplicativo. Esse método faz uma solicitação GET para que a API do Graph do AD do Azure procure por usuários cujo UPN começa com o termo de pesquisa fornecido.
1. Para consultar a API do Graph, inclua um access_token no cabeçalho da solicitação `Authorization`, que é onde a ADAL entra.

    ```csharp
    private async void Search(object sender, RoutedEventArgs e)
    {
        // Validate the Input String
        if (string.IsNullOrEmpty(SearchText.Text))
        {
            MessageBox.Show("Please enter a value for the To Do item name");
            return;
        }

        // Get an Access Token for the Graph API
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
            UserNameLabel.Content = result.UserInfo.DisplayableId;
            SignOutButton.Visibility = Visibility.Visible;
        }
        catch (AdalException ex)
        {
            // An unexpected error occurred, or user canceled the sign in.
            if (ex.ErrorCode != "access_denied")
                MessageBox.Show(ex.Message);

            return;
        }

        ...
    }
    ```

    Quando o aplicativo solicita um token chamando `AcquireTokenAsync(...)`, a ADAL tentará retornar um token sem pedir as credenciais ao usuário.
    * Se a ADAL determina que o usuário precisa entrar para obter um token, exibirá uma caixa de diálogo de logon, coletar as credenciais do usuário e retornar um token após uma autenticação bem-sucedida. 
    * Se a ADAL não puder retornar um token por qualquer motivo, o status exibirá `AdalException`.

1. Observe que o objeto `AuthenticationResult` contém um objeto `UserInfo` que pode ser usado para coletar informações que seu aplicativo pode precisar. Em DirectorySearcher, `UserInfo` é usado para personalizar a interface do usuário do aplicativo com a ID do usuário.
1. Quando o usuário selecionar o botão **Sair**, faça com que a próxima chamada para `AcquireTokenAsync(...)` solicite que o usuário entre. Você pode fazer isso facilmente com a ADAL limpando o cache de token:

    ```csharp
    private void SignOut(object sender = null, RoutedEventArgs args = null)
    {
        // Clear the token cache
        authContext.TokenCache.Clear();

        ...
    }
    ```

    Se o usuário não clicar no botão **Sair**, será necessário manter a sessão do usuário para a próxima vez que ele executar o DirectorySearcher. Quando o aplicativo for iniciado, você pode verificar o cache de token da ADAL e procurar um token existente e atualizar a interface do usuário de acordo.

1. No método `CheckForCachedToken()`, faça outra chamada para `AcquireTokenAsync(...)`, desta vez, aprovando o parâmetro `PromptBehavior.Never`. `PromptBehavior.Never` informará à ADAL que não se deve solicitar a entrada do usuário e a ADAL, em vez disso, deve emitir uma exceção se não for possível retornar um token.

    ```csharp
    public async void CheckForCachedToken() 
    {
        // As the application starts, try to get an access token without prompting the user. If one exists, show the user as signed in.
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "user_interaction_required")
            {
                // An unexpected error occurred.
                MessageBox.Show(ex.Message);
            }

            // If user interaction is required, proceed to main page without singing the user in.
            return;
        }

        // A valid token is in the cache
        SignOutButton.Visibility = Visibility.Visible;
        UserNameLabel.Content = result.UserInfo.DisplayableId;
    }
    ```

Parabéns! Agora você tem um aplicativo .NET do WPF com a capacidade de autenticar usuários, chamar APIs Web com segurança usando OAuth 2.0 e obter informações básicas sobre o usuário. Se você ainda não fez isso, agora é o momento de preencher seu locatário com alguns usuários. Execute o aplicativo DirectorySearcher e entre com um desses usuários. Procure por outros usuários com base em seus UPNs. Feche o aplicativo e execute-o novamente. Observe como a sessão do usuário permanece intacta. Saia e faça logon novamente como outro usuário.

A ADAL facilita a incorporação desses recursos de identidade comuns em seu aplicativo. Ela cuida de todo o trabalho difícil para você, como gerenciamento de cache, suporte ao protocolo OAuth, apresentação de uma interface do usuário de logon ao usuário, atualização de tokens expirados e muito mais. Tudo o que você realmente precisa saber é uma única chamada à API, `authContext.AcquireTokenAsync(...)`.

Para referência, consulte o exemplo concluído (sem seus valores de configuração) [no GitHub](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Próximas etapas

Saiba como proteger uma API Web usando tokens de acesso do portador OAuth 2.0.
> [!div class="nextstepaction"]
> [Proteger uma API da Web .NET com o Azure AD >>](quickstart-v1-dotnet-webapi.md)
