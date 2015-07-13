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
	ms.date="04/28/2015"
	ms.author="dastrock"/>


# Integrar o AD do Azure em um aplicativo Xamarin

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

O Xamarin permite que você escreva aplicativos em C# que podem ser executados em diversas plataformas, incluindo dispositivos móveis e PCs, sem distinção. Se você estiver desenvolvendo um aplicativo usando o Xamarin, o AD do Azure torna simples e direto autenticar os usuários com suas contas do Active Directory. Ele também permite que seu aplicativo consuma com segurança qualquer API da Web protegida pelo AD do Azure, como as APIs do Office 365 ou a API do Azure.

Para aplicativos do Xamarin que precisam acessar recursos protegidos, o AD do Azure fornece a biblioteca de autenticação do Active Directory, ou ADAL. Única finalidade da ADAL é tornar mais fácil para seu aplicativo obter tokens de acesso. Para demonstrar como isso é fácil, aqui vamos criar um aplicativo "Directory Searcher" que:

-	É executado no iOS, Android, Área de trabalho do Windows, Windows Phone e Windows Store.
- Usa uma única PCL (portable class library - biblioteca de classes portátil) para autenticar usuários e obter tokens para a Graph API do AD do Azure
-	Pesquisa um diretório para usuários com um determinado UPN.

Para compilar o aplicativo completo funcionando, você precisará:

2. Configurar seu ambiente de desenvolvimento do Xamarin
2. Registrar seu aplicativo com o AD do Azure.
3. Instalar e configurar a ADAL.
5. Usar a ADAL para obter tokens do AD do Azure.

Para começar, [baixe um projeto de esqueleto](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) ou [baixe o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Cada um é uma solução do Visual Studio 2013. Você também precisará de um locatário do AD do Azure no qual você possa criar usuários e registrar um aplicativo. Se você ainda não tiver um locatário [saiba como obter um](active-directory-howto-tenant.md).

## *0. Configurar seu ambiente de desenvolvimento do Xamarin*
Há várias maneiras diferentes em que você talvez queira configurar o Xamarin, dependendo de plataformas específicas que você deseje atingir. Como este tutorial inclui projetos para iOS, Android e Windows, optaremos por usar o Visual Studio 2013 e o [Host de compilação Xamarin.iOS](http://developer.xamarin.com/guides/ios/getting_started/installation/windows/), que exigirão: - um computador Windows para executar o Visual Studio e os aplicativos do Windows - um computador OSX (se você deseja ser capaz de executar o aplicativo iOS) - Uma assinatura Xamarin Business (uma [avaliação gratuita](http://developer.xamarin.com/guides/cross-platform/getting_started/beginning_a_xamarin_trial/) é suficiente) - [Xamarin para Windows](https://xamarin.com/download), que inclui Xamarin.iOS, Xamarin.Android e a Integração com Visual Studio (recomendado para este exemplo). - [Xamarin para OS X](https://xamarin.com/download), que inclui Xamarin.iOS (e o Host de compilação Xamarin.iOS), Xamarin.Android, Xamarin.Mac e Xamarin Studio.

É recomendável começar com a [Página de Download do Xamarin](https://xamarin.com/download), instalando Xamarin em seu PC e Mac. Se você não tiver ambos esses computadores disponíveis, você ainda pode executar o exemplo, mas determinados projetos precisarão ser omitidos. Execute os [guias detalhados de instalação](http://developer.xamarin.com/guides/cross-platform/getting_started/installation/) para iOS e Android, e se você quiser saber mais sobre as opções disponíveis para desenvolvimento, dê uma olhada no guia [Criando aplicativos de plataforma cruzada](http://developer.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/part_1_-_understanding_the_xamarin_mobile_platform/). Você não precisa configurar um dispositivo para o desenvolvimento no momento, nem precisa de uma assinatura do programa de desenvolvedores da Apple (a menos, é claro, que você deseje executar o aplicativo iOS em um dispositivo).

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
Agora que você tem um aplicativo no AD do Azure, você pode instalar a ADAL e escrever seu código relacionado à identidade. Para que a ADAL seja capaz de se comunicar com o AD do Azure, é necessário fornecer algumas informações sobre o registro de seu aplicativo. Comece adicionando a ADAL a cada um dos projetos na solução usando o Console do Gerenciador de Pacotes.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal.Windows -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal.WindowsPhone -IncludePrerelease
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

####Windows Store
- Na Windows Store, abra `MainPage.xaml.cs` e implemente o método `Search`, que usa um método auxiliar em um projeto compartilhado para atualizar a interface do usuário conforme necessário.

```C#
await UnivDirectoryHelper.Search(
  sender, e,
  SearchResults,
  SearchTermText,
  StatusResult,
  new PlatformParameters(PromptBehavior.Auto, false));
```

####Windows Phone
- No Windows Phone, abra `MainPage.xaml.cs` e implemente o método `Search`, que usa o mesmo método auxiliar em um projeto compartilhado para atualizar a interface do usuário.

```C#
await UnivDirectoryHelper.Search(
  sender, e,
  SearchResults,
  SearchTermText,
  StatusResult,
  new PlatformParameters());
```

Parabéns! Agora você tem um aplicativo Xamarin em funcionamento que tem a capacidade de autenticar usuários e chamar as APIs de Web com segurança usando OAuth 2.0 em cinco plataformas diferentes. Se você ainda não fez isso, agora é o momento de preencher seu locatário com alguns usuários. Execute o aplicativo DirectorySearcher e faça logon com um desses usuários. Procure por outros usuários com base em seus UPNs.

A ADAL facilita a incorporar recursos comuns de identidade em seu aplicativo. Ele se encarrega de todo o trabalho difícil para você - gerenciamento de cache, suporte a protocolo OAuth, apresentação de uma IU de logon ao usuário, atualização de tokens expirados e mais. Tudo o que você realmente precisa saber é uma única chamada à API, `authContext.AcquireToken*(…)`.

Para referência, o exemplo concluído (sem seus valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Agora você pode passar para cenários de identidade adicionais. Você pode desejar experimentar:

[Proteger uma API da Web .NET com o AD do Azure >>](active-directory-devquickstarts-webapi-dotnet.md)

Para obter recursos adicionais, confira: - [AzureADSamples no GitHub >>](https://github.com/AzureAdSamples) - [CloudIdentity.com >>](https://cloudidentity.com) - Documentação do AD do Azure no [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)
 

<!---HONumber=62-->