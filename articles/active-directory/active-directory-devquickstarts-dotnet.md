<properties
	pageTitle="Introdução ao .NET do AD do Azure | Microsoft Azure"
	description="Como compilar um aplicativo .NET de área de trabalho do Windows que se integre ao AD do Azure para entrada e que chame APIs protegidas do AD do Azure usando OAuth."
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="dastrock"/>


# Integrar o AD do Azure em um aplicativo WPF de área de trabalho do Windows

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se você estiver desenvolvendo um aplicativo de desktop, o AD do Azure torna a autenticação dos usuários com suas contas do Active Directory simples e direta. Ele também habilita seu aplicativo a consumir com segurança qualquer API da Web protegida pelo AD do Azure, como as APIs do Office 365 ou a API do Azure.

Para clientes nativos .NET que precisam acessar recursos protegidos, o AD do Azure fornece a biblioteca de autenticação do Active Directory, ou ADAL. A única finalidade da ADAL é tornar mais fácil para seu aplicativo obter tokens de acesso. Para demonstrar como é fácil, vamos compilar aqui um aplicativo de lista de tarefas pendentes para .NET WPF que:

-	Obtém tokens de acesso para chamar a Graph API do AD do Azure usando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Pesquisa um diretório para usuários com um determinado alias.
-	Desconecta usuários.

Para compilar o aplicativo em funcionamento completo, você precisará:

2. Registrar seu aplicativo no AD do Azure.
3. Instalar e configurar a ADAL.
5. Usar a ADAL para obter tokens do AD do Azure.

Para começar, [baixe o esqueleto do aplicativo](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) ou [baixe o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Você também precisará de um locatário do AD do Azure no qual você possa criar usuários e registrar um aplicativo. Se você ainda não tiver um locatário [saiba como obter um](active-directory-howto-tenant.md).

## *1. Registrar o aplicativo DirectorySearcher*
Para habilitar seu aplicativo para obter tokens, primeiro será necessário registrá-lo no seu locatário do AD do Azure e conceder permissão para acessar a Graph API do AD do Azure:

-	Entre no Portal de Gerenciamento do Azure
-	Clique em **Active Directory** no painel de navegação à esquerda
-	Selecione um locatário no qual registrar o aplicativo.
-	Clique na guia **Aplicativos** e clique em **Adicionar** na lista de botões.
-	Siga os prompts e crie um novo **Aplicativo cliente nativo**.

    -	O **Nome** do aplicativo descreverá seu aplicativo para os usuários finais
    -	O **URI de redirecionamento** é uma combinação de esquema e de cadeia de caracteres que o AD do Azure usará para retornar respostas de tokens. Insira um valor específico para seu aplicativo, por exemplo, `http://DirectorySearcher`.
-	Depois de concluir o registro, o AAD atribuirá a seu aplicativo um identificador de cliente único. Você precisará desse valor nas próximas seções, então copie-o da guia **Configurar**.
- Também na guia **Configurar**, clique na seção “Permissões para outros aplicativos”. Para o aplicativo "Active Directory do Azure", adicione a permissão **Acessar o diretório de sua organização** em **Permissões delegadas**. Isso permitirá que seu aplicativo consulte a Graph API para usuários.

## *2. Instalar e configurar a ADAL*
Agora que você tem um aplicativo no AD do Azure, você pode instalar a ADAL e escrever seu código relacionado à identidade. Para que a ADAL seja capaz de se comunicar com o AD do Azure, é necessário fornecer algumas informações sobre o registro de seu aplicativo. Comece adicionando a ADAL ao projeto DirectorySearcher usando o Console do Gerenciador de Pacotes.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-	No projeto DirectorySearcher, abra `app.config`. Substitua os valores dos elementos na seção `<appSettings>` para refletir os valores inseridos por você no Portal do Azure. Seu código fará referência a esses valores sempre que ele usar a ADAL.
    -	O `ida:Tenant` é o domínio do seu locatário do AD do Azure, por exemplo, contoso.onmicrosoft.com.
    -	O `ida:ClientId` é a clientId do seu aplicativo que você copiou do portal.
    -	O `ida:RedirectUri` é a URL de redirecionamento registrada no portal.

## *3. Usar a ADAL para obter tokens do AD do Azure*
O princípio básico da ADAL é que sempre que seu aplicativo precisa de um token de acesso, ele simplesmente chama `authContext.AcquireToken(...)`, e a ADAL faz o resto.

-	No projeto `DirectorySearcher`, abra `MainWindow.xaml.cs` e localize o método `MainWindow()`. A primeira etapa é inicializar o `AuthenticationContext` de seu aplicativo: a classe principal da ADAL. É aqui que você passa à ADAL as coordenadas necessárias para se comunicar com o AD do Azure e informar a ele como armazenar tokens em cache.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());
    ...
}
```

- Agora localize o método `Search(...)`, que será chamado quando os usuário clicar no botão "Pesquisar" na interface do usuário do aplicativo. Esse método faz uma solicitação GET para que a Graph API do AD do Azure procure por usuários cujo UPN começa com o termo de pesquisa fornecido. Mas para consultar a Graph API, você precisa incluir um access_token no cabeçalho `Authorization` da solicitação - é aí que entra a ADAL.

```C#
private void Search(object sender, RoutedEventArgs e)
{
    ...

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = authContext.AcquireToken(graphResourceId, clientId, redirectUri);
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
- Quando o aplicativo solicita um token chamando `AcquireToken(...)`, a ADAL tentará retornar um token sem pedir as credenciais ao usuário. Se a ADAL determina que o usuário precisa entrar para obter um token, exibirá uma caixa de diálogo de logon, coletar as credenciais do usuário e retornar um token após uma autenticação bem-sucedida. Se a ADAL não puder retornar um token por qualquer motivo, o status exibirá `AdalException`.
- Observe que o objeto `AuthenticationResult` contém um objeto `UserInfo` que pode ser usado para coletar informações que seu aplicativo pode precisar. Em DirectorySearcher, `UserInfo` é usado para personalizar a interface do usuário do aplicativo com a ID do usuário.

- Quando o usuário clica no botão "Sair", queremos garantir que a próxima chamada para `AcquireToken(...)` solicitará que o usuário entre. Com a ADAL, isso é tão fácil quanto limpar o cache de tokens:

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

- No entanto, se o usuário não clicar no botão "Sair", convém manter a sessão do usuário para a próxima vez que ele executar o DirectorySearcher. Quando o aplicativo for iniciado, você pode verificar o cache de token da ADAL e procurar um token existente e atualizar a interface do usuário de acordo. Em `MainWindow()`, faça outra chamada para `AcquireToken(...)`, desta vez, aprovando o parâmetro `PromptBehavior.Never`. `PromptBehavior.Never` informará à ADAL que não se deve solicitar a entrada do usuário e a ADAL, em vez disso, deve emitir uma exceção se não for possível retornar um token.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = authContext.AcquireToken(graphResourceId, clientId, redirectUri, PromptBehavior.Never);
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

Parabéns! Agora você tem um aplicativo .NET WPF que tem a capacidade de autenticar usuários, chamar APIs da Web com segurança usando OAuth 2.0 e obter informações básicas sobre o usuário. Se você ainda não fez isso, agora é o momento de preencher seu locatário com alguns usuários. Execute o aplicativo DirectorySearcher e entre com um desses usuários. Procure por outros usuários com base em seus UPNs. Feche o aplicativo e execute-o novamente. Observe como a sessão do usuário permanece intacta. Saia e entre novamente como outro usuário.

A ADAL facilita a incorporar todos esses recursos comuns de identidade em seu aplicativo. Ele se encarrega de todo o trabalho difícil para você - gerenciamento de cache, suporte a protocolo OAuth, apresentação de uma IU de logon ao usuário, atualização de tokens expirados e mais. Tudo o que você realmente precisa saber é uma única chamada à API, `authContext.AcquireToken(...)`.

Para referência, o exemplo concluído (sem seus valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Agora você pode passar para cenários de adicionais. Você pode desejar experimentar:

[Proteger uma API da Web .NET com o AD do Azure >>](active-directory-devquickstarts-webapi-dotnet.md)

Para obter recursos adicionais, confira: - [AzureADSamples no GitHub >>](https://github.com/AzureAdSamples) - [CloudIdentity.com >>](https://cloudidentity.com) - Documentação do AD do Azure em [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)

<!---HONumber=58--> 