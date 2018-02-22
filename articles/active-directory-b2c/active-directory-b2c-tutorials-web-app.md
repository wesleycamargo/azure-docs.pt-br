---
title: "Tutorial: Usar o Azure Active Directory B2C para autenticação de usuário em um aplicativo Web ASP.NET"
description: "Tutorial sobre como usar o Azure Active Directory B2C para inscrever e conectar usuários em um aplicativo Web ASP.NET."
services: active-directory-b2c
author: PatAltimore
ms.author: patricka
ms.reviewer: saraford
ms.date: 1/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: ee006476f9e40e9d1a6e7213cb1881ca46ea75c2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-authenticate-users-with-azure-active-directory-b2c-in-an-aspnet-web-app"></a>Tutorial: Autenticar usuários com o Azure Active Directory B2C em um aplicativo Web ASP.NET

Este tutorial mostra como usar o Azure Active Directory (Azure AD) B2C para inscrever e conectar usuários em um aplicativo Web ASP.NET. O Azure AD B2C permite que seus aplicativos quem com contas sociais, corporativas e do Azure Active Directory usando protocolos padrão.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Registrar um aplicativo Web ASP.NET de exemplo em seu locatário do Azure AD B2C.
> * Criar políticas de inscrição, entrada, edição de perfil e redefinição de senha para usuários.
> * Configurar o aplicativo Web de exemplo para usar o locatário do Azure AD B2C. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>pré-requisitos

* Criar seu próprio [locatário do Azure AD B2C](active-directory-b2c-get-started.md)
* Instalar o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com a carga de trabalho **ASP.NET e desenvolvimento Web**.

## <a name="register-web-app"></a>Registrar o aplicativo Web

Aplicativos precisam ser [registrados](../active-directory/develop/active-directory-dev-glossary.md#application-registration) em seu locatário antes de poderem receber [tokens de acesso](../active-directory/develop/active-directory-dev-glossary.md#access-token) do Azure Active Directory. O registro do aplicativo cria uma [id do aplicativo](../active-directory/develop/active-directory-dev-glossary.md#application-id-client-id) para o aplicativo no locatário. 

Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Selecione **Azure AD B2C** da lista de serviços no Portal do Azure.

2. Nas configurações de B2C, clique em **Aplicativos** e em **Adicionar**.

    Para registrar o aplicativo Web de exemplo no locatário, use as configurações a seguir.

    ![Adicionar um novo aplicativo](media/active-directory-b2c-tutorials-web-app/web-app-registration.png)

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | Meu aplicativo Web de exemplo | Insira um **Nome** que descreve seu aplicativo para os consumidores. | 
    | **Incluir aplicativo Web/API Web** | sim | Selecione **Sim** para um aplicativo Web. |
    | **Permitir fluxo implícito** | sim | Selecione **Sim**, já que o aplicativo usa [entrada OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de Resposta** | `https://localhost:44316` | As URLs de Resposta são pontos de extremidade para onde o Azure AD B2C retornará os tokens que o aplicativo solicitar. Neste tutorial, o exemplo é executado localmente (localhost) e escuta na porta 44316. |
    | **Cliente nativo** | Não  | Como esse é um aplicativo Web e não um cliente nativo, selecione Não. |

3. Clique em **Criar** para registrar o aplicativo.

Os aplicativos registrados são exibidos na lista de aplicativos para o locatário do Azure AD B2C. Selecione o aplicativo Web na lista. O painel de propriedade da do aplicativo Web é exibido.

![Propriedades do aplicativo Web](./media/active-directory-b2c-tutorials-web-app/b2c-web-app-properties.png)

Anote a **ID do aplicativo cliente**. A ID identifica o aplicativo exclusivamente e é necessário para configurá-lo mais adiante no tutorial.

### <a name="create-a-client-password"></a>Criar uma senha de cliente

O Azure AD B2C usa autorização OAuth2 para [aplicativos clientes](../active-directory/develop/active-directory-dev-glossary.md#client-application). Os aplicativos Web são [clientes confidenciais](../active-directory/develop/active-directory-dev-glossary.md#web-client) e exigem um segredo do cliente (senha). O segredo do cliente e a ID de cliente do aplicativo são usados quando o aplicativo Web autentica com o Azure Active Directory. 

1. Selecione a página Chaves para o aplicativo Web registrado e clique em **Gerar chave**.

2. Clique em **Salvar** para exibir a chave.

    ![página de chaves gerais do aplicativo](media/active-directory-b2c-tutorials-web-app/app-general-keys-page.png)

A chave é exibida uma vez no portal. É importante copiar e salvar o valor da chave. Você precisa desse valor para configurar seu aplicativo. Proteja a chave. Não compartilhe a chave publicamente.

## <a name="create-policies"></a>Criar políticas

Uma política do Azure AD B2C define fluxos de trabalho do usuário. Por exemplo, inscrição, entrada, alteração de senha e edição de perfis são fluxos de trabalho comuns.

### <a name="create-a-sign-up-or-sign-in-policy"></a>Criar uma política de inscrição ou credenciais

Para inscrever usuários para acesso e conectá-los no aplicativo Web, crie uma **política de inscrição ou entrada**.

1. Na página do portal do Azure AD B2C, selecione **Políticas de inscrição ou entrada** e clique em **Adicionar**.

    Para configurar a política, use as seguintes configurações:

    ![Adicionar política de inscrição ou de entrada](media/active-directory-b2c-tutorials-web-app/add-susi-policy.png)

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SiUpIn | Insira um **Nome** para a política. O nome da política é prefixado com **b2c_1_**. Use o nome da política completa **b2c_1_SiUpIn** no código de exemplo. | 
    | **Provedor de identidade** | Inscrição de email | O provedor de identidade usado para identificar o usuário exclusivamente. |
    | **Atributos de inscrição** | Nome de exibição e CEP | Selecione os atributos a serem coletados do usuário durante a inscrição. |
    | **Declarações do aplicativo** | Nome de exibição, CEP, Usuário é novo, ID de objeto do usuário | Selecione as [declarações](../active-directory/develop/active-directory-dev-glossary.md#claim) que você deseja incluir no [token de acesso](../active-directory/develop/active-directory-dev-glossary.md#access-token). |

2. Clique em **Criar** para criar a sua política. 

### <a name="create-a-profile-editing-policy"></a>Criar uma política de edição de perfil

Para permitir que os usuários redefinam suas informações de perfil de usuário por conta própria, crie uma **política de edição de perfil**.

1. Na página do portal do Azure AD B2C, selecione **Políticas de edição de perfil** e clique em **Adicionar**.

    Para configurar a política, use as seguintes configurações:

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SiPe | Insira um **Nome** para a política. O nome da política é prefixado com **b2c_1_**. Use o nome da política completa **b2c_1_SiPe** no código de exemplo. | 
    | **Provedor de identidade** | Entrada na conta local | O provedor de identidade usado para identificar o usuário exclusivamente. |
    | **Atributos de perfil** | Nome de exibição e CEP | Selecione os atributos que os usuários podem modificar durante a edição de perfil. |
    | **Declarações do aplicativo** | Nome de exibição, CEP, Usuário é novo, ID de objeto do usuário | Selecione as [declarações](../active-directory/develop/active-directory-dev-glossary.md#claim) que você deseja incluir no [token de acesso](../active-directory/develop/active-directory-dev-glossary.md#access-token) após uma edição de perfil bem-sucedida. |

2. Clique em **Criar** para criar a sua política. 

### <a name="create-a-password-reset-policy"></a>Criar uma política de redefinição de senha

Para habilitar a redefinição de senha no seu aplicativo, você precisará criar uma **política de redefinição de senha**. Essa política descreve a experiência do consumidor durante a redefinição de senha e o conteúdo de tokens que o aplicativo recebe após a conclusão bem-sucedida.

1. Na página do portal do Azure AD B2C, selecione **Políticas de redefinição de senha** e clique em **Adicionar**.

    Para configurar a política, use as configurações a seguir.

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SSPR | Insira um **Nome** para a política. O nome da política é prefixado com **b2c_1_**. Use o nome da política completa **b2c_1_SSPR** no código de exemplo. | 
    | **Provedor de identidade** | Redefinição de senha usando endereço de email | É o provedor de identidade usado para identificar o usuário exclusivamente. |
    | **Declarações do aplicativo** | ID de objeto do usuário | Selecione as [declarações](../active-directory/develop/active-directory-dev-glossary.md#claim) que você deseja incluir no [token de acesso](../active-directory/develop/active-directory-dev-glossary.md#access-token) após uma redefinição de senha bem-sucedida. |

2. Clique em **Criar** para criar a sua política. 

## <a name="update-web-app-code"></a>Atualizar o código do aplicativo Web

Agora que você registrou o aplicativo Web e criou as políticas, precisa configurar o aplicativo para usar o locatário do Azure AD B2C. Neste tutorial, você configura um aplicativo Web de exemplo. 

[Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) ou clone o aplicativo Web de exemplo do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

O aplicativo Web ASP.NET de exemplo é um aplicativo de lista de tarefas simples para criação e atualização de uma lista de tarefas pendentes. O aplicativo usa [componentes de middleware do Microsoft OWIN](https://docs.microsoft.com/en-us/aspnet/aspnet/overview/owin-and-katana/) para permitir que os usuários se inscrevam para usar o aplicativo em seu locatário do Azure AD B2C. Ao criar uma política do Azure AD B2C, os usuários podem usar uma conta social ou criar uma conta para usar como identidade na hora de acessar o aplicativo. 

Há dois projetos na solução de exemplo:

**Aplicativo Web de exemplo (TaskWebApp):** o aplicativo Web para criar e editar uma lista de tarefas. O aplicativo Web usa a política **inscrever-se ou entrar** para inscrever ou fazer logon de usuários com um endereço de email.

**O aplicativo de exemplo de API Web (TaskService):** API Web que dá suporte às funções criar, ler, atualizar e excluir a lista de tarefas. A API Web é protegida pelo Azure AD B2C e chamada pelo aplicativo Web.

Você precisa alterar o aplicativo para usar o registro do aplicativo em seu locatário. Você também precisa configurar as políticas criadas. O aplicativo Web de exemplo define os valores de configuração como configurações de aplicativo no arquivo Web.config. Para alterar as configurações do aplicativo:

1. Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.

2. No projeto de aplicativo Web **TaskWebApp**, abra o arquivo **Web.config** e faça as seguintes atualizações:

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    
    <add key="ida:ClientId" value="The Application ID for your web app registered in your tenant" />
    
    <add key="ida:ClientSecret" value="Client password (client secret)" />
    ```
3. Atualize as configurações de política com o nome gerado ao criar as políticas.

    ```C#
    <add key="ida:SignUpSignInPolicyId" value="b2c_1_SiUpIn" />
    <add key="ida:EditProfilePolicyId" value="b2c_1_SiPe" />
    <add key="ida:ResetPasswordPolicyId" value="b2c_1_SSPR" />
    ```

## <a name="run-the-sample-web-app"></a>Executar o aplicativo Web de exemplo

No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **TaskWebApp** e clique em **Definir como Projeto de Inicialização**

Pressione **F5** para iniciar o aplicativo Web. O navegador padrão inicia no endereço do site local `https://localhost:44316/`. 

O aplicativo de exemplo dá suporte a inscrição, entrada, edição de perfil e redefinição de senha. Abaixo vemos como um usuário se inscreve para usar o aplicativo com um endereço de email. Você pode experimentar os outros cenários por conta própria.

### <a name="sign-up-using-an-email-address"></a>Criar conta usando um endereço de email

1. Clique no link **Inscrever-se/Entrar** na faixa superior para inscrever-se como um usuário do aplicativo Web. Isso usa a política **b2c_1_SiUpIn** definida em uma etapa anterior.

2. O Azure AD B2C apresenta uma página de entrada com um link de inscrição. Como você ainda não tem uma conta, clique no link **Inscrever-se agora**. 

3. O fluxo de trabalho de inscrição apresenta uma página para coletar e verificar a identidade do usuário usando um endereço de email. O fluxo de trabalho de inscrição também coleta atributos solicitados definidos na política e a senha do usuário.

    Use um endereço de email válido e valide-o usando o código de verificação. Defina uma senha. Insira valores para os atributos necessários. 

    ![Fluxo de trabalho de inscrição](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Clique em **Criar** para criar uma conta local no locatário do Azure AD B2C.

Agora o usuário pode usar seu endereço de email para entrar e usar o aplicativo Web.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode usar o seu locatário do Azure AD B2C se planeja experimentar outros tutoriais do Azure AD B2C. Quando não for mais necessário, você poderá [excluir o locatário do Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um locatário do Azure AD B2C, a criar políticas e a atualizar o aplicativo Web de exemplo para usar o seu locatário do Azure AD B2C. Continue para o próximo tutorial a fim de aprender a registrar, configurar e chamar uma API Web ASP.NET protegida por seu locatário do Azure AD B2C.

> [!div class="nextstepaction"]
> [Usar o Azure Active Directory B2C para proteger uma API Web ASP.NET](active-directory-b2c-tutorials-web-api.md)
