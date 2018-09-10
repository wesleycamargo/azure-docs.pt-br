---
title: Tutorial - Conceder acesso a uma API Web Node.js de um aplicativo de desktop usando o Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como usar o Active Directory B2C para proteger uma API Web Node.js e chamá-la de um aplicativo de desktop .NET.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/01/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 98c86f5613116dce5423aa9ca6a2ff43e5414592
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594773"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Tutorial - Conceder acesso a uma API Web Node.js de um aplicativo de desktop usando o Azure Active Directory B2C

Este tutorial mostra como chamar um recurso da API Web Node.js protegida pelo Azure Active Directory (Azure AD) B2C de um aplicativo de desktop do Windows Presentation Foundation (WPF).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Registrar uma API Web em seu locatário do Azure AD B2C
> * Definir e configurar escopos para uma API Web
> * Conceder permissões de aplicativo para a API Web
> * Atualizar o código de exemplo para usar o Azure AD B2C na proteção de uma API Web

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Concluir o [tutorial Autenticar usuários com o Azure Active Directory B2C em um aplicativo de desktop](active-directory-b2c-tutorials-desktop-app.md).
* [Instale o Visual Studio 2017](https://www.visualstudio.com/downloads/) com as cargas de trabalho de **desenvolvimento de desktop .NET** e de **desenvolvimento para a Web e ASP.NET**.
* Instale o [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>Registrar API Web

Recursos da API Web precisam ser registrados no seu locatário antes de poderem aceitar e responder a [solicitações de recurso protegido](../active-directory/develop/developer-glossary.md#resource-server) de [aplicativos clientes](../active-directory/develop/developer-glossary.md#client-application) que apresentem um [token de acesso](../active-directory/develop/developer-glossary.md#access-token) do Azure Active Directory. O registro estabelece o [objeto de aplicativo e de entidade de serviço](../active-directory/develop/developer-glossary.md#application-object) no locatário. 

Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Selecione **Azure AD B2C** da lista de serviços no Portal do Azure.

2. Nas configurações de B2C, clique em **Aplicativos** e em **Adicionar**.

    Para registrar a API Web de exemplo no locatário, use as configurações a seguir.
    
    ![Adicionar uma nova API](media/active-directory-b2c-tutorials-desktop-app-webapi/web-api-registration.png)
    
    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | Meu aplicativo Web Node.js de exemplo | Insira um **nome** que descreve a API Web para os desenvolvedores. |
    | **Incluir aplicativo Web/API Web** | SIM | Selecione **Sim** para uma API Web. |
    | **Permitir fluxo implícito** | SIM | Selecione **Sim**, já que a API usa [entrada OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de Resposta** | `http://localhost:5000` | As URLs de Resposta são pontos de extremidade para onde o Azure AD B2C retornará os tokens que a API solicitar. Neste tutorial, o exemplo de API Web é executado localmente (localhost) e escuta na porta 5000. |
    | **URI da ID do Aplicativo** | demoapi | O URI identifica a API de locatário exclusivamente. Isso permite que você registre várias APIs por locatário. [Escopos](../active-directory/develop/developer-glossary.md#scopes) regem o acesso ao recurso de API protegido e são definidos por URI de ID do aplicativo. |
    | **Cliente nativo** | Não  | Como essa é uma API Web e não um cliente nativo, selecione Não. |
    
3. Clique em **Criar** para registrar a API.

As APIs registradas são exibidas na lista de aplicativos para o locatário do Azure AD B2C. Selecione sua API Web na lista. O painel de propriedade da API Web é exibido.

![Propriedades da API Web](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

Anote a **ID do aplicativo cliente**. A ID identifica a API exclusivamente e é necessário para configurar a API mais adiante no tutorial.

O registro da API Web no Azure AD B2C define uma relação de confiança. Como a API está registrada no B2C, ela agora pode confiar nos tokens de acesso B2C que recebe de outros aplicativos.

## <a name="define-and-configure-scopes"></a>Definir e configurar escopos

[Escopos](../active-directory/develop/developer-glossary.md#scopes) fornecem uma maneira de controlar o acesso a recursos protegidos. Escopos são usados pela API Web para implementar o controle de acesso com base em escopo. Por exemplo, alguns usuários podem ter acesso de leitura e gravação, enquanto outros usuários podem ter permissões somente leitura. Neste tutorial, você define as permissões de leitura e gravação para a API Web.

### <a name="define-scopes-for-the-web-api"></a>Definir escopos para a API Web

As APIs registradas são exibidas na lista de aplicativos para o locatário do Azure AD B2C. Selecione sua API Web na lista. O painel de propriedade da API Web é exibido.

Clique em **Escopos publicados (Versão prévia)**.

Para configurar escopos para a API, adicione as entradas a seguir. 

![escopos definidos na API Web](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| Configuração      | Valor sugerido  | DESCRIÇÃO                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Escopo** | demo.read | Acesso de leitura à API de demonstração|

Clique em **Salvar**.

Os escopos publicados podem ser usados para conceder permissão de aplicativo cliente para a API Web.

### <a name="grant-app-permissions-to-web-api"></a>Conceder permissões de aplicativo para API Web

Para chamar uma API Web protegida de um aplicativo, você precisa conceder permissões do aplicativo para a API. Neste tutorial, use o aplicativo de desktop criado em [Autenticar usuários com o Azure Active Directory B2C em um aplicativo de desktop](active-directory-b2c-tutorials-desktop-app.md).

1. No portal do Azure, selecione **Azure AD B2C** na lista de serviços e clique em **Aplicativos** para exibir a lista de aplicativos registrados.

2. Selecione **Meu Aplicativo WPF de Exemplo** na lista de aplicativos e clique em **Acesso à API (Versão Prévia)** e em **Adicionar**.

3. Na lista suspensa **Selecionar API**, selecione a API Web registrada **Minha API Web Node.js de exemplo**.

4. Na lista suspensa **Selecionar Escopos**, selecione os escopos que você definiu no registro da API Web.

    ![selecionando escopos para o aplicativo](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Clique em **OK**.

O **Meu Aplicativo WPF de Exemplo** está registrado para chamar **Minhas API Web Node.js de Exemplo** protegidas. Um usuário [autentica](../active-directory/develop/developer-glossary.md#authentication) com o Azure AD B2C para usar o aplicativo de área de trabalho WPF. O aplicativo de área de trabalho obtém uma [concessão de autorização](../active-directory/develop/developer-glossary.md#authorization-grant) do Azure AD B2C para acessar a API Web protegida.

## <a name="update-web-api-code"></a>Atualizar o código da API Web

Agora que a API Web está registrada e você tem escopos definidos, precisa configurar a código da API Web para usar o locatário do Azure AD B2C. Neste tutorial, você deve configurar um aplicativo Web Node.js de exemplo que pode ser baixado do GitHub. 

[Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) ou clone o aplicativo Web de exemplo do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
O exemplo de API Web Node.js usa a biblioteca de Passport.js para habilitar o Azure AD B2C a proteger chamadas à API. 

### <a name="configure-the-web-api"></a>Configurar a API Web

1. Abra o arquivo `index.html` no exemplo de API Web Node.js.
2. Configure o exemplo com as informações de registro do locatário do Azure AD B2C. Alterar as seguintes linhas de código:

```nodejs
var tenantID = "<your-tenant-name>.onmicrosoft.com";
var clientID = "<Application ID for your Node.js Web API>";
var policyName = "B2C_1_SiUpIn";  // Sign-in / sign-up policy name
```

### <a name="configure-the-desktop-app"></a>Configurar o aplicativo de desktop

1. Abrir a solução `active-directory-b2c-wpf` no [tutorial Autenticar usuários com o Azure Active Directory B2C em um aplicativo de desktop](active-directory-b2c-tutorials-desktop-app.md) no Visual Studio.

## <a name="run-the-sample"></a>Execute o exemplo

Executar a API Web Node.Js:

1. Inicie um prompt de comando de Node.js.
2. Altere o diretório que contém o exemplo de Node.js. Por exemplo `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Execute os seguintes comandos:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```
Executar o aplicativo de desktop:

1. Pressione **F5** para executar o aplicativo de desktop.
2. Entre usando o endereço de email e a senha usada no [tutorial Autenticar usuários com o Azure Active Directory B2C em um aplicativo de desktop](active-directory-b2c-tutorials-desktop-app.md).
3. Clique no botão **Chamar API**. 

O aplicativo de área de trabalho faz uma solicitação para a API Web e obtém uma resposta com o nome de exibição do usuário conectado. Seu aplicativo de desktop protegido está chamando a API Web protegida no seu locatário do Azure AD B2C.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode usar o seu locatário do Azure AD B2C se planeja experimentar outros tutoriais do Azure AD B2C. Quando não for mais necessário, você poderá [excluir o locatário do Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Próximas etapas

Este artigo percorreu a proteção de uma API Web ASP.NET por meio do registro e da definição de escopos no Azure AD B2C. Saiba mais navegando os exemplos de código do Azure AD B2C disponíveis.

> [!div class="nextstepaction"]
> [Exemplos de código do Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
