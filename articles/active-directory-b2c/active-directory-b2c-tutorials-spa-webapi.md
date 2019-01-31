---
title: Tutorial - Conceder acesso a uma API Web do ASP.NET Core de um aplicativo de página única usando o Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como usar o Active Directory B2C para proteger uma API Web ASP.NET e chamá-la de um aplicativo de página única.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 7c7d23f8b3792ceedc27a81e81be7787452c156e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181513"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-app-using-azure-active-directory-b2c"></a>Tutorial: Conceder acesso a uma API Web do ASP.NET Core de um aplicativo de página única usando o Azure Active Directory B2C

Este tutorial mostra como chamar um recurso da API Web ASP.NET Core protegido pelo Azure Active Directory (Azure AD) B2C de um aplicativo de página única.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Registrar uma API Web em seu locatário do Azure AD B2C
> * Definir e configurar escopos para uma API Web
> * Conceder permissões de aplicativo para a API Web
> * Atualizar o código de exemplo para usar o Azure AD B2C na proteção de uma API Web

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Concluir o [tutorial Autenticar usuários com o Azure Active Directory B2C em um aplicativo de página única](active-directory-b2c-tutorials-spa.md).
* Instalar o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com a carga de trabalho **ASP.NET e desenvolvimento Web**.
* [SDK do NET Core 2.0.0](https://www.microsoft.com/net/core) ou posterior
* Instale o [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>Registrar API Web

Recursos da API Web precisam ser registrados no seu locatário antes de poderem aceitar e responder a [solicitações de recurso protegido](../active-directory/develop/developer-glossary.md#resource-server) de [aplicativos clientes](../active-directory/develop/developer-glossary.md#client-application) que apresentem um [token de acesso](../active-directory/develop/developer-glossary.md#access-token) do Azure Active Directory. O registro estabelece o [objeto de aplicativo e de entidade de serviço](../active-directory/develop/developer-glossary.md#application-object) no locatário. 

Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**. Agora você deve estar usando o locatário criado no tutorial anterior.

2. Selecione **Aplicativos** e selecione **Adicionar**.

    Para registrar a API Web de exemplo no locatário, use as configurações a seguir.
    
    ![Adicionar uma nova API](media/active-directory-b2c-tutorials-spa-webapi/web-api-registration.png)
    
    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | API Core Olá | Insira um **nome** que descreve a API Web para os desenvolvedores. |
    | **Incluir aplicativo Web/API Web** | SIM | Selecione **Sim** para uma API Web. |
    | **Permitir fluxo implícito** | SIM | Selecione **Sim**, já que a API usa [entrada OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de Resposta** | `http://localhost:5000` | As URLs de Resposta são pontos de extremidade para onde o Azure AD B2C retornará os tokens que a API solicitar. Neste tutorial, o exemplo de API Web é executado localmente (localhost) e escuta na porta 5000 (configurada posteriormente neste tutorial). |
    | **URI da ID do Aplicativo** | HelloCoreAPI | O URI identifica a API de locatário exclusivamente. Isso permite que você registre várias APIs por locatário. [Escopos](../active-directory/develop/developer-glossary.md#scopes) regem o acesso ao recurso de API protegido e são definidos por URI de ID do aplicativo. |
    | **Cliente nativo** | Não  | Como essa é uma API Web e não um cliente nativo, selecione Não. |
    
3. Clique em **Criar** para registrar a API.

As APIs registradas são exibidas na lista de aplicativos para o locatário do Azure AD B2C. Selecione sua API Web na lista. O painel de propriedade da API Web é exibido.

![Propriedades da API Web](./media/active-directory-b2c-tutorials-spa-webapi/b2c-web-api-properties.png)

Anote a **ID do aplicativo cliente**. A ID identifica a API exclusivamente e é necessário para configurar a API mais adiante no tutorial.

O registro da API Web no Azure AD B2C define uma relação de confiança. Como a API está registrada no B2C, ela agora pode confiar nos tokens de acesso B2C que recebe de outros aplicativos.

## <a name="define-and-configure-scopes"></a>Definir e configurar escopos

[Escopos](../active-directory/develop/developer-glossary.md#scopes) fornecem uma maneira de controlar o acesso a recursos protegidos. Escopos são usados pela API Web para implementar o controle de acesso com base em escopo. Por exemplo, alguns usuários podem ter acesso de leitura e gravação, enquanto outros usuários podem ter permissões somente leitura. Neste tutorial, você define as permissões de leitura para a API Web.

### <a name="define-scopes-for-the-web-api"></a>Definir escopos para a API Web

As APIs registradas são exibidas na lista de aplicativos para o locatário do Azure AD B2C. Selecione sua API Web na lista. O painel de propriedade da API Web é exibido.

Clique em **Escopos publicados (Versão prévia)**.

Para configurar escopos para a API, adicione as entradas a seguir. 

![escopos definidos na API Web](media/active-directory-b2c-tutorials-spa-webapi/scopes-web-api.png)

| Configuração      | Valor sugerido  | DESCRIÇÃO                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Escopo** | demo.read | Acesso de leitura à API de demonstração |

Clique em **Salvar**.

Os escopos publicados podem ser usados para conceder permissão de aplicativo cliente para a API Web.

### <a name="grant-app-permissions-to-web-api"></a>Conceder permissões de aplicativo para API Web

Para chamar uma API Web protegida de um aplicativo, você precisa conceder permissões do aplicativo para a API. Neste tutorial, use o aplicativo de página única criado em [Autenticar usuários com o Azure Active Directory B2C em um aplicativo de página única (JavaScript)](active-directory-b2c-tutorials-spa.md).

1. No portal do Azure, selecione **Azure AD B2C** na lista de serviços e clique em **Aplicativos** para exibir a lista de aplicativos registrados.

2. Selecione **Meu Aplicativo de página única de exemplo** na lista de aplicativos e clique em **Acesso à API (Versão Prévia)** e em **Adicionar**.

3. Na lista suspensa **Selecionar API**, selecione a API Web registrada **API Core Olá**.

4. Na lista suspensa **Selecionar Escopos**, selecione os escopos que você definiu no registro da API Web.

    ![selecionando escopos para o aplicativo](media/active-directory-b2c-tutorials-spa-webapi/selecting-scopes-for-app.png)

5. Clique em **OK**.

O **Meu aplicativo de página única de exemplo** está registrado para chamar a **API Core Olá** protegida. Um usuário faz [autenticação](../active-directory/develop/developer-glossary.md#authentication) com o Azure AD B2C para usar o aplicativo de página única. O aplicativo de página única obtém uma [concessão de autorização](../active-directory/develop/developer-glossary.md#authorization-grant) do Azure AD B2C para acessar a API Web protegida.

## <a name="update-code"></a>Atualizar código

Agora que a API Web está registrada e você tem escopos definidos, precisa configurar a código da API Web para usar o locatário do Azure AD B2C. Neste tutorial, você deve configurar um aplicativo Web do .NET Core de exemplo que pode ser baixado do GitHub. 

[Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) ou clone o aplicativo Web de exemplo do GitHub.

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
        "ClientId": "<The Application ID for your web API obtained from the Azure portal>",
        "Policy": "<Your sign up sign in policy e.g. B2C_1_SiUpIn>",
        "ScopeRead": "demo.read"  
      },
    ```

#### <a name="enable-cors"></a>Habilitar CORS

Para permitir que seu aplicativo de página única chame a API Web ASP.NET Core, você precisa habilitar o [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

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

### <a name="configure-the-single-page-app"></a>Configurar o aplicativo de página única

O aplicativo de página única usa o Azure AD B2C para inscrição e entrada de usuários e chama a API Web ASP.NET Core. Você precisa atualizar o aplicativo de página única para chamar a API Web .NET Core.
Para alterar as configurações do aplicativo:

1. Abra o arquivo `index.html` no exemplo de aplicativo de página única do Node.js.
2. Configure o exemplo com as informações de registro do locatário do Azure AD B2C. No código a seguir, adicione seu nome de locatário para **b2cScopes** e altere o valor **webApi** para o valor *applicationURL* anotado anteriormente:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_SiUpIn",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/HelloCoreAPI/demo.read"],
        webApi: 'http://localhost:5000/api/values',
    };
    ```

## <a name="run-the-spa-app-and-web-api"></a>Executar a API Web e o aplicativo SPA

Você precisa executar o aplicativo de página única do Node.js e a API Web .NET Core.

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

    A janela de console exibe o número da porta onde o aplicativo está hospedado.
    
    ```
    Listening on port 6420...
    ```

4. Use um navegador para navegar até o endereço `http://localhost:6420` a fim de exibir o aplicativo.
5. Entre usando o endereço de email e a senha usada em [Autenticar usuários com o Azure Active Directory B2C em um aplicativo de página única (JavaScript)](active-directory-b2c-tutorials-spa.md).
6. Clique no botão **Chamar API**.

Depois de se inscrever ou entrar usando uma conta de usuário, o exemplo chamará a API Web protegida e retornará um resultado.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode usar o seu locatário do Azure AD B2C se planeja experimentar outros tutoriais do Azure AD B2C. Quando não for mais necessário, você poderá [excluir o locatário do Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Próximas etapas

Este artigo percorreu a proteção de uma API Web por meio do registro e da definição de escopos no Azure AD B2C. Saiba mais navegando os exemplos de código do Azure AD B2C disponíveis.

> [!div class="nextstepaction"]
> [Exemplos de código do Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
