---
title: Tutorial - Permitir que um aplicativo Web autentique com contas usando o Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como usar o Azure Active Directory B2C para fornecer o logon do usuário para um aplicativo Web ASP.NET.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 30a94cb5de2d618938f17c4e5733821ac7247785
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54851514"
---
# <a name="tutorial-enable-a-web-application-to-authenticate-with-accounts-using-azure-active-directory-b2c"></a>Tutorial: Permitir que um aplicativo Web autentique com contas usando o Azure Active Directory B2C

Este tutorial mostra como usar o Azure Active Directory (Azure AD) B2C para inscrever e conectar usuários em um aplicativo Web ASP.NET. O Azure AD B2C permite que seus aplicativos quem com contas sociais, corporativas e do Azure Active Directory usando protocolos padrão.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Registrar um aplicativo Web ASP.NET de exemplo em seu locatário do Azure AD B2C.
> * Criar fluxos de usuário para inscrição, entrada, edição de perfil e redefinição de senha para usuários.
> * Configurar o aplicativo Web de exemplo para usar o locatário do Azure AD B2C. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Criar seu próprio [locatário do Azure AD B2C](active-directory-b2c-get-started.md)
* Instalar o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com a carga de trabalho **ASP.NET e desenvolvimento Web**.

## <a name="register-web-app"></a>Registrar o aplicativo Web

Aplicativos precisam ser [registrados](../active-directory/develop/developer-glossary.md#application-registration) em seu locatário antes de poderem receber [tokens de acesso](../active-directory/develop/developer-glossary.md#access-token) do Azure Active Directory. O registro do aplicativo cria uma [id do aplicativo](../active-directory/develop/developer-glossary.md#application-id-client-id) para o aplicativo no locatário. 

Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**. Agora você deve estar usando o locatário criado no tutorial anterior. 

2. Nas configurações de B2C, clique em **Aplicativos** e em **Adicionar**. 

    Para registrar o aplicativo Web de exemplo no locatário, use as seguintes configurações:

    ![Adicionar um novo aplicativo](media/active-directory-b2c-tutorials-web-app/web-app-registration.png)
    
    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | Meu aplicativo Web de exemplo | Insira um **Nome** que descreve seu aplicativo para os consumidores. | 
    | **Incluir aplicativo Web/API Web** | SIM | Selecione **Sim** para um aplicativo Web. |
    | **Permitir fluxo implícito** | SIM | Selecione **Sim**, já que o aplicativo usa [entrada OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de Resposta** | `https://localhost:44316` | As URLs de Resposta são pontos de extremidade para onde o Azure AD B2C retornará os tokens que o aplicativo solicitar. Neste tutorial, o exemplo é executado localmente (localhost) e escuta na porta 44316. |
    | **Incluir cliente nativo** | Não  | Como esse é um aplicativo Web e não um cliente nativo, selecione Não. |
    
3. Clique em **Criar** para registrar o aplicativo.

Os aplicativos registrados são exibidos na lista de aplicativos para o locatário do Azure AD B2C. Selecione o aplicativo Web na lista. O painel de propriedade da do aplicativo Web é exibido.

![Propriedades do aplicativo Web](./media/active-directory-b2c-tutorials-web-app/b2c-web-app-properties.png)

Anote a **ID do aplicativo**. A ID identifica o aplicativo exclusivamente e é necessário para configurá-lo mais adiante no tutorial.

### <a name="create-a-client-password"></a>Criar uma senha de cliente

O Azure AD B2C usa autorização OAuth2 para [aplicativos clientes](../active-directory/develop/developer-glossary.md#client-application). Os aplicativos Web são [clientes confidenciais](../active-directory/develop/developer-glossary.md#web-client) e exigem uma ID de cliente ou ID do aplicativo e um segredo do cliente, uma senha do cliente ou uma chave de aplicativo.

1. Selecione a página Chaves para o aplicativo Web registrado e clique em **Gerar chave**.

2. Clique em **Salvar** para exibir a chave do aplicativo.

    ![página de chaves gerais do aplicativo](media/active-directory-b2c-tutorials-web-app/app-general-keys-page.png)

A chave é exibida uma vez no portal. É importante copiar e salvar o valor da chave. Você precisa desse valor para configurar seu aplicativo. Proteja a chave. Não compartilhe a chave publicamente.

## <a name="create-user-flows"></a>Criar fluxos de usuário

Um fluxo de usuário do Azure AD B2C define a experiência do usuário para uma tarefa de identidade. Por exemplo, inscrição, entrada, alteração de senhas e edição de perfis são fluxos de usuário comuns.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>Criar um fluxo de usuário de inscrição ou entrada

Para inscrever usuários para acesso e conectá-los ao aplicativo Web, crie um **fluxo de usuário de inscrição ou entrada**.

1. Na página do portal do Azure AD B2C, selecione **Fluxos de usuário** e clique em **Novo fluxo de usuário**.
2. Na guia **Recomendado**, clique em **Inscrever-se e entrar**.

    Para configurar o fluxo de usuário, use as configurações a seguir:

    ![Adicione um fluxo de usuário de inscrição ou entrada](media/active-directory-b2c-tutorials-web-app/add-susi-user-flow.png)

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SiUpIn | Insira um **Nome** para o fluxo de usuário. O nome do fluxo de usuário é prefixado com **b2c_1_**. Use o nome do fluxo de usuário completo **b2c_1_SiUpIn** no código de exemplo. | 
    | **Provedores de Identidade** | Inscrição de email | O provedor de identidade usado para identificar o usuário exclusivamente. |

3. Em **Atributos de usuário e declarações**, clique em **Mostrar mais** e selecione as seguintes configurações:

    ![Adicione um fluxo de usuário de inscrição ou entrada](media/active-directory-b2c-tutorials-web-app/add-attributes-and-claims.png)

    | Coluna      | Valores sugeridos  | DESCRIÇÃO                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Coletar atributo** | Nome de exibição e CEP | Selecione os atributos a serem coletados do usuário durante a inscrição. |
    | **Declaração de retorno** | Nome de exibição, CEP, Usuário é novo, ID de objeto do usuário | Selecione as [declarações](../active-directory/develop/developer-glossary.md#claim) que você deseja incluir no [token de acesso](../active-directory/develop/developer-glossary.md#access-token). |

4. Clique em **OK**.
5. Clique em **Criar** para criar o seu fluxo de usuário. 

### <a name="create-a-profile-editing-user-flow"></a>Criar um fluxo de usuário de edição de perfil

Para permitir que os usuários redefinam suas informações de perfil de usuário por conta própria, crie um **fluxo de usuário de edição de perfil**.

1. Na página do portal do Azure AD B2C, selecione **Fluxos de usuário** e clique em **Novo fluxo de usuário**.
2. Na guia **Recomendado**, clique em **Inscrever-se e entrar**.

    Para configurar o fluxo de usuário, use as configurações a seguir:

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SiPe | Insira um **Nome** para o fluxo de usuário. O nome do fluxo de usuário é prefixado com **b2c_1_**. Use o nome do fluxo de usuário completo **b2c_1_SiPe** no código de exemplo. | 
    | **Provedores de Identidade** | Entrada na conta local | O provedor de identidade usado para identificar o usuário exclusivamente. |

3. Em **Atributos de usuário**, clique em **Mostrar mais** e selecione as configurações a seguir:

    | Coluna      | Valores sugeridos  | DESCRIÇÃO                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Coletar atributo** | Nome de exibição e CEP | Selecione os atributos que os usuários podem modificar durante a edição de perfil. |
    | **Declaração de retorno** | Nome de exibição, CEP, ID de Objeto do Usuário | Selecione as [declarações](../active-directory/develop/developer-glossary.md#claim) que você deseja incluir no [token de acesso](../active-directory/develop/developer-glossary.md#access-token) após uma edição de perfil bem-sucedida. |

4. Clique em **OK**.
5. Clique em **Criar** para criar o seu fluxo de usuário. 

### <a name="create-a-password-reset-user-flow"></a>Criar um fluxo de usuário de redefinição de senha

Para habilitar a redefinição de senha no seu aplicativo, você precisa criar um **fluxo de usuário de redefinição de senha**. Esse fluxo de usuário descreve a experiência do consumidor durante a redefinição de senha e o conteúdo de tokens que o aplicativo recebe após a conclusão bem-sucedida.

1. Na página do portal do Azure AD B2C, selecione **Políticas de redefinição de senha** e clique em **Adicionar**.
2. Na guia **Recomendado**, clique em **Redefinição de senha**.

    Para configurar o fluxo de usuário, use as configurações a seguir.

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SSPR | Insira um **Nome** para o fluxo de usuário. O nome do fluxo de usuário é prefixado com **b2c_1_**. Use o nome do fluxo de usuário completo **b2c_1_SSPR** no código de exemplo. | 
    | **Provedores de Identidade** | Redefinição de senha usando endereço de email | É o provedor de identidade usado para identificar o usuário exclusivamente. |

3. Em **Declarações de aplicativo**, clique em **Mostrar mais** e selecione a seguinte configuração:
    | Coluna      | Valor sugerido  | DESCRIÇÃO                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Declaração de retorno** | ID de objeto do usuário | Selecione as [declarações](../active-directory/develop/developer-glossary.md#claim) que você deseja incluir no [token de acesso](../active-directory/develop/developer-glossary.md#access-token) após uma redefinição de senha bem-sucedida. |

4. Clique em **OK**.
5. Clique em **Criar** para criar o fluxo de usuário. 

## <a name="update-web-app-code"></a>Atualizar o código do aplicativo Web

Agora que você registrou o aplicativo Web e criou os fluxos de usuário, precisa configurar o aplicativo para usar o locatário do Azure AD B2C. Neste tutorial, você deve configurar um aplicativo Web de exemplo que pode ser baixado do GitHub. 

[Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) ou clone o aplicativo Web de exemplo do GitHub. Extraia o arquivo de exemplo em uma pasta cujo tamanho total de caracteres do caminho seja menor que 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

O aplicativo Web ASP.NET de exemplo é um aplicativo de lista de tarefas simples para criação e atualização de uma lista de tarefas pendentes. O aplicativo usa [componentes de middleware do Microsoft OWIN](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/) para permitir que os usuários se inscrevam para usar o aplicativo em seu locatário do Azure AD B2C. Ao criar um fluxo de usuário do Azure AD B2C, os usuários poderão usar uma conta social ou criar uma conta para usar como identidade na hora de acessar o aplicativo. 

Há dois projetos na solução de exemplo:

**Aplicativo de exemplo de aplicativo Web (TaskWebApp):** aplicativo Web para criar e editar uma lista de tarefas. O aplicativo Web usa o fluxo de usuário de **inscrição ou entrada** para inscrever ou fazer logon de usuários.

**Aplicativo de exemplo de API Web (TaskService):** a API Web que dá suporte às funções criar, ler, atualizar e excluir para a lista de tarefas. A API Web é protegida pelo Azure AD B2C e chamada pelo aplicativo Web.

Você precisará alterar o aplicativo para usar o registro do aplicativo em seu locatário, o que inclui a ID do aplicativo e a chave registrada anteriormente. Você também precisa configurar os fluxos de usuário criados. O aplicativo Web de exemplo define os valores de configuração como configurações de aplicativo no arquivo Web.config. Para alterar as configurações do aplicativo:

1. Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.

2. No projeto de aplicativo Web **TaskWebApp**, abra o arquivo **Web.config**. Substitua o valor de `ida:Tenant` pelo nome do locatário que você criou. Substitua o valor de `ida:ClientId` pela ID do aplicativo que você registrou. Substitua o valor de `ida:ClientSecret` pela chave que você registrou.

3. No arquivo **Web.config**, substitua o valor de `ida:SignUpSignInPolicyId` por `b2c_1_SiUpIn`. Substitua o valor de `ida:EditProfilePolicyId` por `b2c_1_SiPe`. Substitua o valor de `ida:ResetPasswordPolicyId` por `b2c_1_SSPR`.

## <a name="run-the-sample-web-app"></a>Executar o aplicativo Web de exemplo

No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **TaskWebApp** e clique em **Definir como Projeto de Inicialização**

Pressione **F5** para iniciar o aplicativo Web. O navegador padrão inicia no endereço do site local `https://localhost:44316/`. 

O aplicativo de exemplo dá suporte a inscrição, entrada, edição de perfil e redefinição de senha. Este tutorial destaca como um usuário se inscreve para usar o aplicativo usando um endereço de email. Você pode explorar os outros cenários por conta própria.

### <a name="sign-up-using-an-email-address"></a>Criar conta usando um endereço de email

1. Clique no link **Inscrever-se/Entrar** na faixa superior para inscrever-se como um usuário do aplicativo Web. Para isso, use o fluxo de usuário**b2c_1_SiUpIn** definido em uma etapa anterior.

2. O Azure AD B2C apresenta uma página de entrada com um link de inscrição. Como você ainda não tem uma conta, clique no link **Inscrever-se agora**. 

3. O fluxo de trabalho de inscrição apresenta uma página para coletar e verificar a identidade do usuário usando um endereço de email. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo de usuário.

    Use um endereço de email válido e valide-o usando o código de verificação. Defina uma senha. Insira valores para os atributos necessários. 

    ![Fluxo de trabalho de inscrição](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Clique em **Criar** para criar uma conta local no locatário do Azure AD B2C.

Agora o usuário pode usar seu endereço de email para entrar e usar o aplicativo Web.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode usar o seu locatário do Azure AD B2C se planeja experimentar outros tutoriais do Azure AD B2C. Quando não for mais necessário, você poderá [excluir o locatário do Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um locatário do Azure AD B2C, a criar fluxos de usuário e a atualizar o aplicativo Web de exemplo para usar o seu locatário do Azure AD B2C. Continue para o próximo tutorial a fim de aprender a registrar, configurar e chamar uma ASP.NET Web API protegida por seu locatário do Azure AD B2C.

> [!div class="nextstepaction"]
> [Tutorial: usar o Azure Active Directory B2C para proteger uma ASP.NET Web API](active-directory-b2c-tutorials-web-api.md)
