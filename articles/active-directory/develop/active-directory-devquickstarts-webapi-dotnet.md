---
title: "Introdução ao .NET do Azure AD | Microsoft Docs"
description: "Como compilar uma API da Web do .NET MVC que se integre ao AD do Azure para autenticação e autorização."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: c401d473d3951ed5853170e8761a09a915329164


---
# <a name="protect-a-web-api-using-bearer-tokens-from-azure-ad"></a>Proteger uma API da Web usando os tokens de portador do AD do Azure
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Se você estiver criando um aplicativo que fornece acesso a recursos protegidos, você precisará saber como proteger esses recursos de acessos não garantidos.
O Azure faz com que seja simples e direto proteger uma API da Web usando tokens de acesso do OAuth Bearer 2.0 com apenas algumas linhas de código.

Em aplicativos da Web Asp.NET, você pode fazer isso usando a implementação da Microsoft do middleware OWIN voltado à comunidade, incluído no .NET Framework 4.5.  Aqui, você usará o OWIN para compilar uma API Web de “Lista de Tarefas Pendentes” que:

* Designa quais APIs estão protegidas.
* Valida que as chamadas à API Web contêm um Token de Acesso válido.

Para isso, você precisará:

1. Registrar um aplicativo com o Active Directory do Azure
2. Configurar seu aplicativo para usar o pipeline de autenticação OWIN.
3. Configurar um aplicativo cliente chamar a API da Web da lista de tarefas

Para começar, [baixe o esqueleto do aplicativo](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) ou [baixe o exemplo concluído](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Cada um é uma solução do Visual Studio 2013.  Você também precisará de um locatário do AD do Azure no qual registrar seu aplicativo.  Se você ainda não tiver um locatário, [saiba como obter um](active-directory-howto-tenant.md).

## <a name="1----register-an-application-with-azure-ad"></a>1.    Registrar um aplicativo com o AD do Azure
Para proteger seu aplicativo, você primeiro precisará criar um aplicativo em seu locatário e fornecer algumas informações cruciais ao AD do Azure.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique na sua conta e, na lista **Diretório**, escolha o locatário do Active Directory em que você deseja registrar seu aplicativo.
3. Clique em **Mais Serviços** no painel de navegação à esquerda e escolha **Azure Active Directory**.
4. Clique em **Registros do Aplicativo** e escolha **Adicionar**.
5. Siga os prompts e crie um novo **Aplicativo Web e/ou WebAPI**.
  * O **Nome** do aplicativo descreverá seu aplicativo para os usuários finais.  Digite "To Do List Service".
  * O **URI de redirecionamento** é uma combinação de esquema e de sequência de caracteres que o AD do Azure usa para retornar tokens solicitados pelo aplicativo. Digite `https://localhost:44321/` para esse valor.
  * Para o campo **URI de AppID**, insira um identificador específico do locatário, por exemplo, `https://contoso.onmicrosoft.com/TodoListService`
6. Salve a configuração.  Deixe o portal aberto – você também precisará registrar seu aplicativo cliente em breve.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>2. Configure seu aplicativo para usar o pipeline de autenticação OWIN
Agora que já registrou um aplicativo no AD do Azure, você precisa configurar o aplicativo para se comunicar com o AD do Azure para validar solicitações de entrada e tokens.

* Para começar, abra a solução e adicione os pacotes do NuGet de middleware OWIN ao projeto TodoListService usando o Console do Gerenciador de Pacotes.

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

* Adicionar uma classe de inicialização OWIN no projeto TodoListService chamado `Startup.cs`.  Clique com o botão direito do mouse no projeto --> **Adicionar** --> **Novo item** --> pesquise por "OWIN".  O middleware OWIN invocará o método `Configuration(…)` quando seu aplicativo for iniciado.
* Altere a declaração de classe para `public partial class Startup`; já implementamos parte dessa classe para você em outro arquivo.  No método `Configuration(…)`, faça uma chamada para ConfgureAuth(...) para configurar a autenticação para seu aplicativo Web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

* Abra o arquivo `App_Start\Startup.Auth.cs` e implemente o método `ConfigureAuth(…)`.  Os parâmetros que você fornece em `WindowsAzureActiveDirectoryBearerAuthenticationOptions` servirão como coordenadas para seu aplicativo para se comunicar com o AD do Azure.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.UseWindowsAzureActiveDirectoryBearerAuthentication(
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions
        {
            Audience = ConfigurationManager.AppSettings["ida:Audience"],
            Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
        });
}
```

* Agora você pode usar `[Authorize]` atributos para proteger os controladores e ações com a autenticação de portador JWT.  Decore a classe `Controllers\TodoListController.cs` com uma marca de autorização.  Isso forçará o usuário a entrar antes de acessar essa página.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

* Quando um chamador autorizado invoca com êxito uma das `TodoListController` APIs, a ação pode precisar ter acesso às informações sobre o chamador.  O OWIN fornece acesso às declarações dentro do token de portador por meio do objeto `ClaimsPrincpal` .  
* É um requisito comum para APIs da Web é validar os "escopos" presentes no token. Isso garante que o usuário final concedeu as permissões necessárias para acessar o serviço de lista de tarefas:

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
    {
        throw new HttpResponseException(new HttpResponseMessage {
          StatusCode = HttpStatusCode.Unauthorized,
          ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
        });
    }
    ...
}
```

* Por fim, abra o arquivo `web.config` na raiz do projeto TodoListService e insira os valores de configuração na seção `<appSettings>`.
  * O `ida:Tenant` é o nome do seu locatário do AD do Azure, por exemplo, "contoso.onmicrosoft.com".
  * O `ida:Audience` é o URI de ID do aplicativo que você inseriu no Portal do Azure.

## <a name="3-configure-a-client-application--run-the-service"></a>3. Configurar um aplicativo cliente e executar o serviço
Antes de poder ver o serviço de lista de tarefas em ação, você precisa configurar o cliente de lista de tarefas para poder obter tokens do AAD e fazer chamadas para o serviço.

* Navegue de volta até o [Portal do Azure](https://portal.azure.com)
* Crie um novo aplicativo no locatário do AD do Azure e selecione **aplicativo cliente nativo** no prompt resultante.
  * O **Nome** do aplicativo descreverá seu aplicativo para os usuários finais
  * Digite `http://TodoListClient/` para o valor **URI de redirecionamento** .
* Depois de concluir o registro, o AAD atribuirá a seu aplicativo uma **ID de Aplicativo** exclusiva. Você precisará desse valor nas próximas etapas, portanto, copie-o da página do aplicativo.
* Na página **Configurações**, escolha **Permissões Necessárias** e escolha **Adicionar**.  Localize e selecione TodoListService e adicione a permissão **Acessar TodoListService** em **Permissões Delegadas** e escolha **Concluído**.

* No Visual Studio, abra `App.config` no projeto TodoListClient e insira seus valores de configuração na seção `<appSettings>`.
  
  * O `ida:Tenant` é o nome do seu locatário do AD do Azure, por exemplo, "contoso.onmicrosoft.com".
  * A ID do seu aplicativo `ida:ClientId` que você copiou do Portal do Azure.
  * O `todo:TodoListResourceId` é o URI de ID do aplicativo de serviço de lista de tarefas que você inseriu no Portal do Azure.

Por fim, limpe, compile e execute cada projeto!  Se você ainda não fez isso, agora é o momento de criar um novo usuário em seu locatário com um domínio *.onmicrosoft.com.  Entre no cliente de lista de tarefas com esse usuário e adicione algumas tarefas na lista de tarefas do usuário.

Para referência, o exemplo concluído (sem seus valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Agora, você pode seguir para cenários de identidade adicionais.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]




<!--HONumber=Jan17_HO3-->


