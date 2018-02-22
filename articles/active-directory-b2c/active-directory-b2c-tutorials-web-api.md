---
title: 'Tutorial: Usar o Azure Active Directory B2C para proteger uma API Web ASP.NET'
description: "Tutorial sobre como usar Active Directory B2C para proteger API Web ASP.NET e chamá-la de um aplicativo Web ASP.NET."
services: active-directory-b2c
author: PatAltimore
ms.author: patricka
ms.reviewer: saraford
ms.date: 1/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: 0e9f324cec0d242c013a461d8580abd4faa97c8d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-use-azure-active-directory-b2c-to-protect-an-aspnet-web-api"></a>Tutorial: Usar o Azure Active Directory B2C para proteger uma API Web ASP.NET

Este tutorial mostra como chamar um recurso da API Web protegida pelo Azure Active Directory (Azure AD) B2C de um aplicativo Web ASP.NET.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Registrar uma API Web em seu locatário do Azure AD B2C
> * Definir e configurar escopos para uma API Web
> * Conceder permissões de aplicativo para a API Web
> * Atualizar o código de exemplo para usar o Azure AD B2C na proteção de uma API Web

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>pré-requisitos

* Concluir o [Tutorial: Usar o Azure Active Directory B2C para autenticação de usuário em um aplicativo Web ASP.NET](active-directory-b2c-tutorials-web-app.md).
* Instalar o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com a carga de trabalho **ASP.NET e desenvolvimento Web**.

## <a name="register-web-api"></a>Registrar API Web

Recursos da API Web precisam ser registrados no seu locatário antes de poderem aceitar e responder a [solicitações de recurso protegido](../active-directory/develop/active-directory-dev-glossary.md#resource-server) de [aplicativos clientes](../active-directory/develop/active-directory-dev-glossary.md#client-application) que apresentem um [token de acesso](../active-directory/develop/active-directory-dev-glossary.md#access-token) do Azure Active Directory. O registro estabelece o [objeto de aplicativo e de entidade de serviço](../active-directory/develop/active-directory-dev-glossary.md#application-object) no locatário. 

Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Selecione **Azure AD B2C** da lista de serviços no Portal do Azure.

2. Nas configurações de B2C, clique em **Aplicativos** e depois em **+Adicionar**.

    Para registrar a API Web de exemplo no locatário, use as configurações a seguir.
    
    ![Adicionar uma nova API](media/active-directory-b2c-tutorials-web-api/web-api-registration.png)
    
    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | Minha API Web de exemplo | Insira um **nome** que descreve a API Web para os desenvolvedores. |
    | **Incluir aplicativo Web/API Web** | sim | Selecione **Sim** para uma API Web. |
    | **Permitir fluxo implícito** | sim | Selecione **Sim**, já que a API usa [entrada OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de Resposta** | `https://localhost:44332` | As URLs de Resposta são pontos de extremidade para onde o Azure AD B2C retornará os tokens que a API solicitar. Neste tutorial, o exemplo de API Web é executado localmente (localhost) e escuta na porta 44332. |
    | **URI da ID do Aplicativo** | myAPISample | O URI identifica a API de locatário exclusivamente. Isso permite que você registre várias APIs por locatário. [Escopos](../active-directory/develop/active-directory-dev-glossary.md#scopes) regem o acesso ao recurso de API protegido e são definidos por URI de ID do aplicativo. |
    | **Cliente nativo** | Não  | Como essa é uma API Web e não um cliente nativo, selecione Não. |
    
3. Clique em **Criar** para registrar a API.

As APIs registradas são exibidas na lista de aplicativos para o locatário do Azure AD B2C. Selecione sua API Web na lista. O painel de propriedade da API Web é exibido.

![Propriedades da API Web](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

Anote a **ID do aplicativo cliente**. A ID identifica a API exclusivamente e é necessário para configurar a API mais adiante no tutorial.

O registro da API Web no Azure AD B2C define uma relação de confiança. Como a API está registrada no B2C, ela agora pode confiar nos tokens de acesso B2C que recebe de outros aplicativos.

## <a name="define-and-configure-scopes"></a>Definir e configurar escopos

[Escopos](../active-directory/develop/active-directory-dev-glossary.md#scopes) fornecem uma maneira de controlar o acesso a recursos protegidos. Escopos são usados pela API Web para implementar o controle de acesso com base em escopo. Por exemplo, alguns usuários podem ter acesso de leitura e gravação, enquanto outros usuários podem ter permissões somente leitura. Neste tutorial, você define as permissões de leitura e gravação para a API Web.

### <a name="define-scopes-for-the-web-api"></a>Definir escopos para a API Web

As APIs registradas são exibidas na lista de aplicativos para o locatário do Azure AD B2C. Selecione sua API Web na lista. O painel de propriedade da API Web é exibido.

Clique em **Escopos publicados (Versão prévia)**.

Para configurar escopos para a API, adicione as entradas a seguir. 

![escopos definidos na API Web](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| Configuração      | Valor sugerido  | DESCRIÇÃO                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Escopo** | Hello.Read | Acesso de leitura a hello |
| **Escopo** | Hello.Write | Acesso de gravação a hello |

Os escopos publicados podem ser usados para conceder permissão de aplicativo cliente para a API Web.

### <a name="grant-app-permissions-to-web-api"></a>Conceder permissões de aplicativo para API Web

Para chamar uma API Web protegida de um aplicativo, você precisa conceder permissões do aplicativo para a API. 

1. No portal do Azure, selecione **Azure AD B2C** na lista de serviços e clique em **Aplicativos** para exibir a lista de aplicativos registrados.

2. Selecione **Meu Aplicativo Web de Exemplo** na lista de aplicativos e clique em **Acesso à API (Versão Prévia)** e em **Adicionar**.

3. Na lista suspensa **Selecionar API**, selecione a API Web registrada **Minha API Web de Exemplo**.

4. Na lista suspensa **Selecionar Escopos**, selecione os escopos que você definiu no registro da API Web.

    ![selecionando escopos para o aplicativo](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Clique em **OK**.

O **Meu Aplicativo Web de Exemplo** está registrado para chamar **Minhas API Web de Exemplo** protegidas. Um usuário [autentica](../active-directory/develop/active-directory-dev-glossary.md#authentication) com o Azure AD B2C para usar o aplicativo Web. O aplicativo Web obtém uma [concessão de autorização](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant) do Azure AD B2C para acessar a API Web protegida.

## <a name="update-web-api-code"></a>Atualizar o código da API Web

Agora que a API Web está registrada e você tem escopos definidos, precisa configurar a código da API Web para usar o locatário do Azure AD B2C. Neste tutorial, você configura uma API Web de exemplo. 

A API Web de exemplo está incluída no projeto baixado no tutorial de pré-requisito: [Usar o Azure Active Directory B2C para autenticação de usuário em um aplicativo Web ASP.NET](active-directory-b2c-tutorials-web-app.md). Se você ainda não concluiu o tutorial de pré-requisito, faça isso antes de continuar.

Há dois projetos na solução de exemplo:

**Aplicativo Web de exemplo (TaskWebApp):** o aplicativo Web para criar e editar uma lista de tarefas. O aplicativo Web usa a política **inscrever-se ou entrar** para inscrever ou fazer logon de usuários com um endereço de email.

**O aplicativo de exemplo de API Web (TaskService):** API Web que dá suporte às funções criar, ler, atualizar e excluir a lista de tarefas. A API Web é protegida pelo Azure AD B2C e chamada pelo aplicativo Web.

O aplicativo Web de exemplo e a API Web definem os valores de configuração como configurações de aplicativo no arquivo Web.config de cada projeto.

Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.

### <a name="configure-the-web-app"></a>Configurar o aplicativo Web

1. Abra **Web.config** no projeto **TaskWebApp**.

2. Para executar a API localmente, use a configuração de localhost para **api:TaskServiceUrl**. Altere o Web.config da seguinte maneira: 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Configure o URI da API. Esse é o URI que o aplicativo Web usa para fazer a solicitação à API. Além disso, configure as permissões solicitadas.

```C#
<add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/myAPISample/" />
<add key="api:ReadScope" value="Hello.Read" />
<add key="api:WriteScope" value="Hello.Write" />
```

### <a name="configure-the-web-api"></a>Configurar a API Web

1. Abra **Web.config** no projeto **TaskService**.

2. Configure a API para usar seu locatário.

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Defina a ID do cliente como a ID do aplicativo registrado para sua API.

    ```C#
    <add key="ida:ClientId" value="<The Application ID for your web API obtained from the Azure portal>"/>
    ```

4. Atualize a configuração de política com o nome gerado quando você criou sua política de inscrição e entrada.

    ```C#
    <add key="ida:SignUpSignInPolicyId" value="b2c_1_SiUpIn" />
    ```

5. Configure os escopos de configuração para corresponder ao que você criou no portal.

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample-web-app-and-web-api"></a>Executar a API Web e os aplicativos Web de exemplo

Você precisa executar os projetos **TaskWebApp** e **TaskService**. 

1. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e selecione **Definir projetos de inicialização...**. 
2. Selecione o botão de opção **Vários projetos de inicialização**.
3. Altere a **Ação** para ambos os projetos para **Iniciar**.
4. Clique em OK para salvar a configuração.
5. Pressione **F5** para executar os dois aplicativos. Cada aplicativo é aberto em sua própria guia do navegador. `https://localhost:44316/` é o aplicativo Web.
    `https://localhost:44332/` é a API Web.

6. No aplicativo Web, clique no link Inscrever-se/Entrar na faixa de menu para se inscrever no aplicativo Web. Use a conta que você criou no [tutorial de aplicativo Web](active-directory-b2c-tutorials-web-app.md). 
7. Depois de conectado, clique no link **Lista de Tarefas Pendentes** e crie um item de lista de tarefas pendentes.

Quando você cria um item de lista de tarefas pendentes, o aplicativo Web faz uma solicitação à API para gerar o item de lista de tarefas pendentes da Web. Seu aplicativo Web protegido está chamando a API Web protegida no seu locatário do Azure AD B2C.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode usar o seu locatário do Azure AD B2C se planeja experimentar outros tutoriais do Azure AD B2C. Quando não for mais necessário, você poderá [excluir o locatário do Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Próximas etapas

Este artigo percorreu a proteção de uma API Web ASP.NET por meio do registro e da definição de escopos no Azure AD B2C. Para obter mais detalhes sobre como desenvolver esse cenário, incluindo instruções passo a passo do código, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Criar um aplicativo Web ASP.NET com inscrição, entrada, edição de perfil e redefinição de senha do Azure Active Directory B2C](active-directory-b2c-devquickstarts-web-dotnet-susi.md)