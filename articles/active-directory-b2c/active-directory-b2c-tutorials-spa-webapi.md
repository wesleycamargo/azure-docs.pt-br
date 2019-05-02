---
title: Tutorial – Permitir acesso a uma API Web do ASP.NET Core de um aplicativo de página única usando – Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como usar o Active Directory B2C para proteger uma API Web ASP.NET e chamá-la de um aplicativo de página única.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 13fedae2798311a59a5cee2805ce9e09b1bd5a0f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64724681"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Tutorial: Permitir acesso a uma API Web ASP.NET Core de um aplicativo de página única usando o Azure Active Directory B2C

Este tutorial mostra como chamar um recurso da API Web ASP.NET Core protegido pelo Azure AD (Azure Active Directory) B2C de um aplicativo de página única.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um aplicativo API Web
> * Configurar escopos para uma API Web
> * Conceder permissões à API Web
> * Configurar o exemplo para usar o aplicativo

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas e os pré-requisitos no [Tutorial: Habilitar autenticação de aplicativos de página única com contas usando o Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).

## <a name="add-a-web-api-application"></a>Adicionar um aplicativo API Web

Os recursos da API Web precisam ser registrados no seu locatário antes de poderem aceitar e responder a solicitações de recurso protegido de aplicativos clientes que apresentem um token de acesso.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo. Por exemplo, *webapi1*.
6. Para **Incluir aplicativo da Web / API da Web** e **Permitir fluxo implícito**, selecione **Sim**.
7. Para a **URL de resposta**, insira um ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Neste tutorial, o exemplo é executado localmente e escuta em `https://localhost:5000`.
8. Para o **ID do aplicativo URI**, insira o identificador usado para sua API da web. O URI do identificador completo, incluindo o domínio, é gerado para você. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página de propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo Web.

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. Escopos são usados pela API Web para implementar o controle de acesso com base em escopo. Por exemplo, alguns usuários podem ter acesso de leitura e gravação, enquanto outros usuários podem ter permissões somente leitura. Neste tutorial, você define as permissões de leitura para a API Web.

1. Selecione **Aplicativos** e, em seguida, selecione *webapi1*.
2. Selecione **Escopos publicados**.
3. Em **escopo**, insira `Hello.Read` e, na descrição, insira `Read access to hello`.
4. Em **escopo**, insira `Hello.Write` e, na descrição, insira `Write access to hello`.
5. Clique em **Save** (Salvar).

Os escopos publicados podem ser usados para conceder permissão de aplicativo cliente para a API Web.

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de um aplicativo, é necessário conceder permissões de aplicativo à API. No tutorial de pré-requisito, você criou um aplicativo Web no Azure AD B2C chamado *webapp1*. Use este aplicativo para chamar a API Web.

1. Selecione **Aplicativos** e, em seguida, selecione *webapp1*.
2. Selecione **Acesso à API** e, em seguida, selecione **Adicionar**.
3. No menu suspenso **Selecionar API**, selecione *webapi1*.
4. No menu suspenso **Selecionar Escopos**, selecione os escopos **Hello.Read** e **Hello.Write** definidos anteriormente.
5. Clique em **OK**.

O **Meu aplicativo de página única de exemplo** está registrado para chamar a **API Core Olá** protegida. Um usuário autentica-se com o Azure AD B2C para usar o aplicativo de página única. O aplicativo de página única obtém uma concessão de autorização do Azure AD B2C para acessar a API Web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a API Web está registrada e você tem escopos definidos, configure o código da API Web para usar o locatário do Azure AD B2C. Neste tutorial, você deve configurar um aplicativo Web do .NET Core de exemplo que pode ser baixado do GitHub. [Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) ou clone o aplicativo Web de exemplo do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Configurar a API Web

1. Abra a solução **B2C-WebAPI.sln** no Visual Studio.
2. Abra o arquivo **appsettings.json**. Atualize os valores abaixo a fim de configurar a API Web para usar o seu locatário:

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<application-ID>",
        "Policy": "B2C_1_signupsignin1>",
        "ScopeRead": "Hello.Read"  
      },
    ```

#### <a name="enable-cors"></a>Habilitar CORS

Para permitir que seu aplicativo de página única chame a API Web ASP.NET Core, é necessário habilitar o [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

1. Em **Startup.cs**, adicione CORS ao método `ConfigureServices()`.

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. Em **Startup.cs**, configure o CORS no método `Configure()`.

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. Abra o arquivo **launchSettings.json** em **Propriedades**, localize a configuração **iisSettings** *applicationURL* e defina o número da porta como aquele registrado pela URL de Resposta da API `http://localhost:5000`.

### <a name="configure-the-single-page-application"></a>Configure o aplicativo de página única

O aplicativo de página única usa o Azure AD B2C para inscrição e conexão de usuários e chama a API Web ASP.NET Core. Atualize o aplicativo de página única para chamar a API Web .NET Core.

Para alterar as configurações do aplicativo:

1. Abra o arquivo `index.html` .
2. Configure o exemplo com as informações de registro do locatário do Azure AD B2C. No código a seguir, adicione seu nome de locatário para **b2cScopes** e altere o valor **webApi** para o valor *applicationURL* anotado anteriormente:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<application-ID>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/api/Hello.Read"],
        webApi: 'http://localhost:5000/api/values',
    };
    ```

## <a name="run-the-spa-application-and-web-api"></a>Executar o aplicativo SPA e API Web

É necessário executar o aplicativo de página única do Node.js e a API Web .NET Core.

### <a name="run-the-aspnet-core-web-api"></a>Executar a API Web ASP.NET Core 

Pressione **F5** para depurar a solução **B2C-WebAPI.sln** no Visual Studio.

Quando o projeto é iniciado, uma página da Web é exibida no navegador padrão anunciando que a API Web está disponível para solicitações.

### <a name="run-the-single-page-app"></a>Executar o aplicativo de página única

1. Inicie um prompt de comando de Node.js.
2. Altere o diretório que contém o exemplo de Node.js. Por exemplo `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Execute os seguintes comandos:
    ```
    npm install && npm update
    node server.js
    ```

    A janela de console exibe o número da porta em que o aplicativo está hospedado.
    
    ```
    Listening on port 6420...
    ```

4. Use um navegador para navegar até o endereço `http://localhost:6420` a fim de exibir o aplicativo.
5. Entre usando o endereço de email e a senha usada em [Autenticar usuários com o Azure Active Directory B2C em um aplicativo de página única (JavaScript)](active-directory-b2c-tutorials-spa.md).
6. Clique em **Chamar API**.

Depois de se inscrever ou entrar usando uma conta de usuário, o exemplo chamará a API Web protegida e retornará um resultado.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Adicionar um aplicativo API Web
> * Configurar escopos para uma API Web
> * Conceder permissões à API Web
> * Configurar o exemplo para usar o aplicativo

> [!div class="nextstepaction"]
> [Tutorial: Adicionar provedores de identidade aos aplicativos no Azure Active Directory B2C](tutorial-add-identity-providers.md)
