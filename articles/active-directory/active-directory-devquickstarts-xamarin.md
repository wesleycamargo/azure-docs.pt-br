<properties
	pageTitle="Introdução ao Xamarin do AD do Azure | Microsoft Azure"
	description="Como criar um aplicativo do Xamarin que se integre ao AD do Azure para entrar e chame APIs protegidas do AD do Azure usando OAuth."
	services="active-directory"
	documentationCenter="xamarin"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>


# Integrar o AD do Azure em um aplicativo Xamarin

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

O Xamarim permite a você gravar aplicativos móveis em C# que podem ser executados no iOS, no Android e no Windows (dispositivos móveis e computadores). Se você estiver desenvolvendo um aplicativo usando o Xamarin, o AD do Azure torna simples e direto autenticar os usuários com suas contas do Active Directory. Ele também permite que seu aplicativo consuma com segurança qualquer API da Web protegida pelo AD do Azure, como as APIs do Office 365 ou a API do Azure.

Para aplicativos do Xamarin que precisam acessar recursos protegidos, o AD do Azure fornece a biblioteca de autenticação do Active Directory, ou ADAL. Única finalidade da ADAL é tornar mais fácil para seu aplicativo obter tokens de acesso. Para demonstrar como isso é fácil, aqui vamos criar um aplicativo "Directory Searcher" que:

-	É executado no iOS, Android, Área de trabalho do Windows, Windows Phone e Windows Store.
- Usa uma única PCL (portable class library - biblioteca de classes portátil) para autenticar usuários e obter tokens para a Graph API do AD do Azure
-	Pesquisa um diretório para usuários com um determinado UPN.

Para compilar o aplicativo completo funcionando, você precisará:

2. Configurar seu ambiente de desenvolvimento do Xamarin
2. Registrar seu aplicativo no Azure AD.
3. Instalar e configurar a ADAL.
5. Usar a ADAL para obter tokens do AD do Azure.

Para começar, [baixe um projeto de esqueleto](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) ou [baixe o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Cada um é uma solução do Visual Studio 2013. Você também precisará de um locatário do AD do Azure no qual você possa criar usuários e registrar um aplicativo. Se você ainda não tiver um locatário [saiba como obter um](active-directory-howto-tenant.md).

## *0. Configurar seu ambiente de desenvolvimento do Xamarin*
Como este tutorial inclui projetos para iOS, Android e Windows, será necessário o Visual Studio e o Xamarin juntos. Para criar o ambiente necessário, siga as instruções completas em [Configuração e Instalação do Visual Studio e do Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) no MSDN. Essas instruções incluem material que você pode examinar para saber mais sobre o Xamarin enquanto aguarda os instaladores serem concluídos.

Quando você tiver concluído a configuração necessária, abra a solução no Visual Studio para começar. Você encontrará seis projetos: cinco projetos específicos para determinada plataforma e uma biblioteca de classes portátil que será compartilhada entre todas as plataformas, `DirectorySearcher.cs`

## *1. Registrar o aplicativo Directory Searcher*
Para habilitar seu aplicativo para obter tokens, primeiro será necessário registrá-lo no seu locatário do AD do Azure e conceder permissão para acessar a Graph API do AD do Azure:

-	Entre no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com)
-	Clique em **Active Directory** no painel de navegação à esquerda
-	Selecione um locatário no qual registrar o aplicativo.
-	Clique na guia **Aplicativos** e clique em **Adicionar** na lista de botões.
-	Siga os prompts e crie um novo **Aplicativo cliente nativo**.  

    -	O **Nome** do aplicativo descreverá seu aplicativo para os usuários finais
    -	O **URI de redirecionamento** é uma combinação de esquema e de cadeia de caracteres que o AD do Azure usará para retornar respostas de tokens. Insira um valor, por exemplo `http://DirectorySearcher`.
-	Depois de concluir o registro, o AAD atribuirá a seu aplicativo um identificador de cliente único. Você precisará desse valor nas próximas seções, então copie-o da guia **Configurar**.
- Também na guia **Configurar**, clique na seção “Permissões para outros aplicativos”. Para o aplicativo "Active Directory do Azure", adicione a permissão **Acessar o diretório de sua organização** em **Permissões delegadas**. Isso permitirá que seu aplicativo consulte a Graph API para usuários.

## *2. Instalar e configurar a ADAL*
Agora que você tem um aplicativo no AD do Azure, você pode instalar a ADAL e escrever seu código relacionado à identidade. Para que a ADAL possa se comunicar com o Azure AD, é necessário fornecer a ela algumas informações sobre o registro do aplicativo.
-	Comece adicionando a ADAL a cada um dos projetos na solução usando o Console do Gerenciador de Pacotes.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
`

- Você deve observar que as duas referências de biblioteca são adicionadas a cada projeto - a parte PCL da ADAL e uma parte específica da plataforma.

-	No projeto DirectorySearcherLib, abra `DirectorySearcher.cs`. Substitua os valores pertencentes à classe para refletir os valores inseridos por você no Portal do Azure. Seu código fará referência a esses valores sempre que ele usar a ADAL.
    -	O `tenant` é o domínio do seu locatário do AD do Azure, por exemplo, contoso.onmicrosoft.com.
    -	O `clientId` é a clientId do seu aplicativo que você copiou do portal.
    - O `returnUri` é o redirectUri inserido no portal, por exemplo, `http://DirectorySearcher`.

## *3. Usar a ADAL para obter tokens do AD do Azure*
*Quase* toda lógica de autenticação do aplicativo está em `DirectorySearcher.SearchByAlias(...)`. Tudo o que é necessário nos projetos específicos da plataforma é passar um parâmetro contextual para o PCL do `DirectorySearcher`.

- Primeiro, abra `DirectorySearcher.cs` e adicione um novo parâmetro ao método `SearchByAlias(...)`. `IPlatformParameters` é o parâmetro contextual que encapsula os objetos específicos da plataforma de que a ADAL precisa para realizar a autenticação.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-	Em seguida, inicialize a classe principal da `AuthenticationContext` -ADAL. É aqui que você passa à ADAL as coordenadas necessárias para se comunicar com o AD do Azure. Em seguida, chame `AcquireTokenAsync(...)`, que aceita o objeto `IPlatformParameters` e invoca o fluxo de autenticação necessário para retornar um token para o aplicativo.

```C#
...
    AuthenticationResult authResult = null;
    try
    {
        AuthenticationContext authContext = new AuthenticationContext(authority);
        authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
    }
    catch (Exception ee)
    {
        results.Add(new User { error = ee.Message });
        return results;
    }
...
```
- `AcquireTokenAsync(...)` tentará primeiro retornar um token para o recurso solicitado (a Graph API neste caso) sem solicitar que o usuário insira suas credenciais (por meio de caching ou atualizando tokens antigos). Se necessário, ele exibirá ao usuário a página de entrada do AD do Azure antes de adquirir o token solicitado.


- Então, você poderá anexar o token à solicitação de Graph API no cabeçalho Autorização:

```C#
...
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

Isso é tudo para a PCL do `DirectorySearcher` e o código relacionado à identidade do seu aplicativo. Tudo o que resta fazer é chamar o método `SearchByAlias(...)` nos modos de exibição de cada plataforma e, onde necessário, adicionar código para tratar corretamente o ciclo de vida da interface do usuário.

####Android:
- Em `MainActivity.cs`, adicione uma chamada para `SearchByAlias(...)` no manipulador de cliques do botão:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- Você também precisa substituir o método de ciclo de vida `OnActivityResult` para encaminhar qualquer autenticação, fazendo com que ela seja redirecionada para o método apropriado. A ADAL fornece um método auxiliar para isso no Android:

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####Área de trabalho do Windows:
- Em `MainWindow.xaml.cs`, basta fazer uma chamada para `SearchByAlias(...)` passando um `WindowInteropHelper` no objeto `PlatformParameters` da área de trabalho:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####iOS:
- Em `DirSearchClient_iOSViewController.cs`, o objeto `PlatformParameters` do iOS simplesmente pega uma referência para o controlador de exibição:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####Windows Universal:
- No Windows Universal, abra `MainPage.xaml.cs` e implemente o método `Search`, que usa um método auxiliar em um projeto compartilhado para atualizar a interface do usuário conforme necessário.

```C#
...
    List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Parabéns! Agora você tem um aplicativo Xamarin em funcionamento que tem a capacidade de autenticar usuários e chamar as APIs de Web com segurança usando OAuth 2.0 em cinco plataformas diferentes. Se você ainda não fez isso, agora é o momento de preencher seu locatário com alguns usuários. Execute o aplicativo DirectorySearcher e faça logon com um desses usuários. Procure por outros usuários com base em seus UPNs.

A ADAL facilita a incorporação de recursos comuns de identidade em seu aplicativo. Ele se encarrega de todo o trabalho difícil para você - gerenciamento de cache, suporte a protocolo OAuth, apresentação de uma IU de logon ao usuário, atualização de tokens expirados e mais. Tudo o que você realmente precisa saber é uma única chamada à API, `authContext.AcquireToken*(…)`.

Para referência, o exemplo concluído (sem seus valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Agora você pode passar para cenários de identidade adicionais. Você pode desejar experimentar:

[Proteger uma API da Web .NET com o AD do Azure >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->