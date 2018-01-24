---
title: "Introdução ao servidor Web ASP.NET no Azure Active Directory v1 | Microsoft Docs"
description: "Implementando a opção Entrar com uma Conta da Microsoft em uma solução ASP.NET com um aplicativo tradicional baseado em navegador da Web usando o padrão OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/08/2017
ms.author: andret
ms.openlocfilehash: b23afd26f7ac1828381a0410d2455206c8f43c88
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
<!--start-intro-->
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Adicionar a opção Entrar com uma Conta da Microsoft a um aplicativo Web ASP.NET

Este guia demonstra como implementar a opção Entrar com uma Conta da Microsoft usando uma solução ASP.NET MVC com um aplicativo tradicional baseado em navegador da Web usando o OpenID Connect. 

No final deste guia, seu aplicativo aceitará entradas de contas corporativas e de estudantes de organizações com integração ao Azure Active Directory.

> [!NOTE]
> Esta instalação interativa ajuda você a habilitar entradas de contas corporativas e de estudantes em seu aplicativo ASP.NET. Se você estiver interessado habilitar entradas para contas pessoais, além de contas corporativas e de estudantes, poderá usar o [ponto de extremidade v2](../active-directory-v2-compare.md). Consulte [esta instalação interativa do ASP.NET para o ponto de extremidade v2](./active-directory-aspnetwebapp.md), bem como [este documento](../active-directory-v2-limitations.md) explicando as limitações atuais do ponto de extremidade v2.
<br/><br/>

<!--separator-->

> Este guia exige o Visual Studio 2015 Atualização 3 ou o Visual Studio 2017.  Ainda não tem?  [Baixar o Visual Studio 2017 gratuitamente](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>Como funciona este guia

![Como funciona este guia](media/active-directory-aspnetwebapp-v1/aspnet-intro.png)

Este guia se baseia no cenário em que um navegador acessa um site ASP.NET, solicitando a autenticação de um usuário por meio de um botão de conexão. Nesse cenário, a maior parte do trabalho de renderização da página da Web ocorre no lado do servidor.

> [!NOTE]
> Esta instalação interativa demonstra como realizar a entrada de usuários em um aplicativo Web do ASP.NET a partir de um modelo vazio e incluir etapas como adicionar um botão de entrada e todos os controladores e métodos, enquanto também explica alguns conceitos. Como alternativa, você também pode criar um projeto para realizar a entrada de usuários do Azure Active Directory (contas corporativas e de estudantes) usando o [modelo web do Visual Studio](https://docs.microsoft.com/aspnet/visual-studio/overview/2013/creating-web-projects-in-visual-studio#organizational-account-authentication-options) e selecionando *Contas organizacionais* e, em seguida, uma das opções de nuvem -- essa opção usa um modelo mais rico, com controladores, métodos e modos de exibição adicionais.

## <a name="libraries"></a>Bibliotecas

Este guia usa os seguintes pacotes:

|Biblioteca|DESCRIÇÃO|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware que permite que um aplicativo use OpenIdConnect para autenticação|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware que permite que um aplicativo mantenha a sessão de usuário usando cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Permite que os aplicativos baseado em OWIN sejam executados no IIS usando o pipeline de solicitação do ASP.NET|


<!--end-intro-->

<!--start-setup-->

## <a name="set-up-your-project"></a>Configurar o seu projeto

Esta seção mostra as etapas para instalar e configurar o pipeline de autenticação por meio do middleware OWIN em um projeto do ASP.NET usando o OpenID Connect. 

> Prefere baixar este projeto do Visual Studio de exemplo? [Baixe um projeto](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/GuidedSetup.zip) e vá para a [Etapa de configuração](#configure-your-webconfig-and-register-an-application) para configurar o exemplo de código antes de executá-lo.

## <a name="create-your-aspnet-project"></a>Criar seu projeto do ASP.NET
1. No Visual Studio: `File` > `New` > `Project`<br/>
2. Em *Visual C#\Web*, selecione `ASP.NET Web Application (.NET Framework)`.
3. Nomeie o aplicativo e clique em *OK*
4. Selecione `Empty` e então marque a caixa de seleção para adicionar referências `MVC`

## <a name="add-authentication-components"></a>Adicionar componentes de autenticação

1. No Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Adicione *Pacotes NuGet do middleware OWIN* digitando o seguinte na janela do Console do Gerenciador de Pacotes:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-packages"></a>Sobre esses pacotes
>As bibliotecas acima possibilitam o SSO (logon único) usando o OpenID Connect por meio da autenticação baseada em cookie. Depois que a autenticação for concluída e o token que representa o usuário for enviado ao aplicativo, o middleware OWIN criará um cookie de sessão. Em seguida, o navegador usará esse cookie nas próximas solicitações, de forma que o usuário não precise se autenticar e nenhuma verificação adicional será necessária.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Configurar o pipeline de autenticação
As etapas a seguir são usadas para criar uma *Classe de Inicialização* do middleware OWIN para configurar a autenticação do OpenID Connect. Essa classe é executada automaticamente.

> [!TIP]
> Se o projeto não tiver um arquivo `Startup.cs` na pasta raiz:<br/>
> 1. Clique com o botão direito do mouse na pasta raiz do projeto: > `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Nomeie-o `Startup.cs`<br/>
>
>> Verifique se a classe selecionada é uma Classe de Inicialização OWIN e não uma classe C# padrão. Confirme isso verificando se você consegue ver `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` acima do namespace.


1. Adicione os namespaces *OWIN* e *Microsoft.IdentityModel* a `Startup.cs`:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=AddedNameSpaces "Startup.cs")]

2. Substitua a classe de Inicialização pelo seguinte código:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=Startup "Startup.cs")]
    
<!--start-collapse-->
> [!NOTE]
> Os parâmetros fornecidos em *OpenIDConnectAuthenticationOptions* servem como coordenadas para seu aplicativo se comunicar com o Azure AD. Como o middleware OpenID Connect usa cookies, você também precisa configurar a autenticação de cookie, conforme o código acima mostra. O valor *ValidateIssuer* informa OpenIdConnect para não restringir o acesso a uma organização específica.
<!--end-collapse-->

<!--end-setup-->

<!--start-use-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Adicionar um controlador para manipular solicitações de entrada e saída

Esta etapa mostra como criar um novo controlador para expor métodos de entrada e saída.

1.  Clique com botão direito na pasta `Controllers` e selecione `Add` > `Controller`
2.  Selecione `MVC (.NET version) Controller – Empty`.
3.  Clique em *Adicionar*
4.  Nomeie-o `HomeController` e clique em *Adicionar*
5.  Adicione namespaces *OWIN* à classe:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=AddedNameSpaces "HomeController.cs")]

6. Adicione os seguintes métodos para manipular a entrada e saída do controlador iniciando um desafio de autenticação por meio de código:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=SigInAndSignOut "HomeController.cs")]
    
## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Criar a home page do aplicativo para conectar os usuários por meio de um botão de conexão

No Visual Studio, crie uma nova exibição para adicionar o botão de conexão e exibir informações de usuário após a autenticação:

1.  Clique com botão direito na pasta `Views\Home` e selecione `Add View`
2.  Nomeie-o `Index`.
3.  Adicione o seguinte HTML, que inclui o botão de conexão, ao arquivo:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Home/Index.cshtml "Index.cshtml")]

<!--start-collapse-->
> [!NOTE]
> Esta página adiciona um botão de conexão no formato SVG com uma tela de fundo preta:<br/>![Entrar com uma Conta da Microsoft](media/active-directory-aspnetwebapp-v1/aspnetsigninbuttonsample.png)<br/> Para obter mais botões de conexão, acesse [esta página](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Diretrizes de identidade visual").
<!--end-collapse-->

## <a name="display-users-claims-by-adding-a-controller"></a>Exiba as declarações do usuário adicionando um controlador
Esse controlador demonstra os usos do atributo `[Authorize]` para proteger um controlador. Esse atributo restringe o acesso ao controlador permitindo apenas usuários autenticados. O código a seguir usa o atributo para exibir as declarações de usuário que foram recuperadas como parte da conexão.

1.  Clique com botão direito na pasta `Controllers`: `Add` > `Controller`
2.  Selecione `MVC {version} Controller – Empty`.
3.  Clique em *Adicionar*
4.  Nomeie-o `ClaimsController`
5.  Substitua o código da classe de controlador pelo código a seguir – isso adiciona o atributo `[Authorize]` à classe:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\ClaimsController.cs?name=ClaimsController "ClaimsController.cs")]

<!--start-collapse-->
> [!NOTE]
> Devido ao uso do atributo `[Authorize]`, todos os métodos desse controlador podem ser executados apenas se o usuário está autenticado. Se o usuário não estiver autenticado e tentar acessar o controlador, o OWIN iniciará um desafio de autenticação e forçará o usuário a se autenticar. O código acima examina a coleção de declarações do usuário em busca de atributos específicos incluídos no token do usuário. Esses atributos incluem o nome completo do usuário e o nome de usuário, bem como a entidade de identificador de usuário global. Também contém a *ID de Locatário*, que representa a ID da organização do usuário. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Criar uma exibição para exibir as declarações do usuário

No Visual Studio, crie uma nova exibição para exibir as declarações do usuário em uma página da Web:

1.  Clique com botão direito na pasta `Views\Claims` e: `Add View`
2.  Nomeie-o `Index`.
3.  Adicione o seguinte HTML ao arquivo:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Claims/Index.cshtml "Index.cshtml")]
    
<!--end-use-->

<!--start-configure-->
## <a name="configure-your-webconfig-and-register-an-application"></a>Configurar seu *Web.config* e registrar um aplicativo

1. No Visual Studio, adicione o seguinte a `web.config` (localizado na pasta raiz), na seção `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="RedirectUrl" value="Enter_the_Redirect_Url_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
    ```
2. No Gerenciador de Soluções, selecione o projeto e examine a janela <i>Propriedades</i> (se uma janela Propriedades não for exibida, pressione F4)
3. Altere SSL habilitado para <code>True</code>
4. Copie URL de SSL do projeto para a área de transferência:<br/><br/>![Propriedades do projeto](media/active-directory-aspnetwebapp-v1/visual-studio-project-properties.png)<br />
5. Em <code>web.config</code>, substitua <code>Enter_the_Redirect_URL_here</code> pela URL do SSL do projeto 

### <a name="register-your-application-in-the-azure-portal-then-add-its-information-to-webconfig"></a>Registre seu aplicativo no Portal do Azure, em seguida, adicione as informações a *web.config*

1. Acesse o [Portal do Microsoft Azure - Registro de aplicativos](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) para registrar um aplicativo
2. Selecione `New application registration`
3. Insira um nome para seu aplicativo
4. Cole a *URL do SSL* do projeto do Visual Studio em `Sign-on URL` (essa URL também é adicionada automaticamente à lista de URLs de resposta para o aplicativo que você está registrando)
5. Clique em `Create` para registrar o aplicativo. Essa ação o levará de volta para a lista de aplicativos
6. Agora, pesquise e/ou selecione o aplicativo que você acabou de criar para abrir suas propriedades
7. Copie o guid em `Application ID` para a área de transferência
8. Volte ao Visual Studio e, em `web.config`, substitua `Enter_the_Application_Id_here` pela ID do Aplicativo do aplicativo que você acabou de registrar

> [!TIP]
> Se sua conta está configurada para acesso a vários diretórios, verifique se você selecionou o diretório certo para a organização que deseja que o aplicativo seja registrado clicando no nome da sua conta no canto superior direito no Portal do Azure e, em seguida, verificando o diretório selecionado como indicado:<br/>![Selecionando o diretório certo](media/active-directory-aspnetwebapp-v1/tenantselector.png)

## <a name="configure-sign-in-options"></a>Configurar opções de entrada

Você pode configurar seu aplicativo para permitir que apenas os usuários que pertençam à instância do Azure Active Directory de uma organização entrem, ou aceitar entradas de usuários que pertençam a qualquer organização. Siga as instruções de uma das seguintes opções:

### <a name="configure-your-application-to-allow-sign-ins-of-work-and-school-accounts-from-any-company-or-organization-multi-tenant"></a>Configurar seu aplicativo para permitir entradas de contas corporativas e de estudantes de qualquer empresa ou organização (multilocatário)

Siga as etapas a seguir se você quiser aceitar entradas de contas corporativas e de estudantes de qualquer empresa ou organização com integração ao Azure Active Directory. Este é um cenário comum para *aplicativos SaaS*:

1. Volte para [Portal do Microsoft Azure - Registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) e localize o aplicativo que você acabou de registrar
2. Em `All Settings`, selecione `Properties`
3. Altere a propriedade `Multi-tenanted` para `Yes` e clique em `Save`

Para obter mais informações sobre essa configuração e o conceito de aplicativos de multilocação, consulte [este artigo](../active-directory-devhowto-multi-tenant-overview.md "Visão geral de multilocatário").

### <a name="restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Restrinja usuários da instância do Active Directory de uma única organização entre no seu aplicativo (único locatário)

Essa opção é um cenário comum para *aplicativos LOB*: se você quiser que seu aplicativo aceite entradas somente de contas que pertençam a uma instância específica do Azure Active Directory (incluindo *contas de convidados* dessa instância), substitua o parâmetro `Tenant` em *web.config* de `Common` pelo nome do locatário da organização – por exemplo, *contoso.onmicrosoft.com*. Depois disso, altere o argumento `ValidateIssuer` na [*Classe de Inicialização OWIN*](#configure-the-authentication-pipeline) para `true`.

Para permitir usuários apenas de uma lista de organizações específicas, defina `ValidateIssuer` como verdadeiro e use o parâmetro `ValidIssuers` para especificar uma lista de organizações.

Outra opção é implementar um método personalizado para validar os emissores usando o parâmetro *IssuerValidator*. Para saber mais sobre `TokenValidationParameters`, veja [este artigo do MSDN](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "Artigo do MSDN TokenValidationParameters").

<!--end-configure-->

<!--start-configure-arp-->
<!--
## Configure your ASP.NET Web App with the application's registration information

In this step, you will configure your project to use SSL, and then use the SSL URL to configure your application’s registration information. After this, add the application’ registration information to your solution via *web.config*.

1.  In Solution Explorer, select the project and look at the `Properties` window (if you don’t see a Properties window, press F4)
2.  Change `SSL Enabled` to `True`
3.  Copy the value from `SSL URL` above and paste it in the `Redirect URL` field on the top of this page, then click *Update*:<br/><br/>![Project properties](media/active-directory-aspnetwebapp-v1/vsprojectproperties.png)<br />
4.  Add the following in `web.config` file located in root’s folder, under section `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
```
-->
<!--end-configure-arp-->
<!--start-test-->
## <a name="test-your-code"></a>Testar seu código

Pressione `F5` para executar o projeto no Visual Studio. O navegador é aberto e direciona você para *http://localhost:{port}*, em qual você pode ver o botão *Entrar com uma conta da Microsoft*. Vá em frente e clique nele para entrar.

Quando você estiver pronto para testar, use uma conta de trabalho (Azure Active Directory) para entrar. 

![Janela do navegador Entrar com uma Conta da Microsoft](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin.png)

![Janela do navegador Entrar com uma Conta da Microsoft](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Resultados esperados
Depois de se conectar, o usuário é redirecionado para a home page do site, que é a URL HTTPS especificada nas informações de registro do aplicativo no Portal de Registro de Aplicativos da Microsoft. Essa página agora mostra *Olá, {User}* e um link para a saída, além de um link para consultar as declarações do usuário – que é um link para o controlador Autorizar criado anteriormente.

### <a name="see-users-claims"></a>Consultar as declarações do usuário
Selecione o hiperlink para consultar as declarações do usuário. Essa ação leva você para o controlador e a exibição que está disponível somente para usuários autenticados.

#### <a name="expected-results"></a>Resultados esperados
 Você deverá ver uma tabela que contém as propriedades básicas do usuário conectado:

| Propriedade | Valor | DESCRIÇÃO|
|---|---|---|
| NOME | {Nome completo do usuário} | Nome e sobrenome do usuário
|Nome de Usuário | <span>user@domain.com</span>| O nome de usuário usado para identificar o usuário conectado
| Assunto| {Subject}|Uma cadeia de caracteres para identificar exclusivamente o logon do usuário na Web|
| ID do locatário| {Guid}| Um *guid* para representar exclusivamente a organização do Azure Active Directory do usuário.|

Além disso, você pode ver uma tabela que inclui todas as declarações de usuário incluídas na solicitação de autenticação. Para obter uma lista de todas as declarações em um Token de ID e suas explicações, veja este [artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "Lista de declarações no Token de ID").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Testar o acesso a um método que tem um atributo *[Authorize]* (opcional)
Nesta etapa, você testa o acesso ao controlador de Declarações como um usuário anônimo:<br/>
Selecione o link para desconectar o usuário e concluir o processo de saída.<br/>
Agora no navegador, digite http://localhost:{port}/claims para acessar o controlador que está protegido com o atributo `[Authorize]`

#### <a name="expected-results"></a>Resultados esperados
Você deverá receber o prompt exigindo a autenticação para ver a exibição.

## <a name="additional-information"></a>Informações adicionais

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Proteger todo o seu site
Para proteger todo o seu site, adicione o `AuthorizeAttribute` a `GlobalFilters` no método `Global.asax` `Application_Start`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

<!--end-test-->