---
title: Tutorial - Permitir autenticação de aplicativos de página única com contas usando o Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como usar o Azure Active Directory B2C para fornecer o logon do usuário para um aplicativo de página única (JavaScript).
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 0632d97c85e4baf837329a2b573f4abe5c15bf26
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195692"
---
# <a name="tutorial-enable-single-page-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Tutorial: Permitir autenticação de aplicativos de página única com contas usando o Azure Active Directory B2C

Este tutorial mostra como usar o Azure Active Directory (Azure AD) B2C para inscrever e conectar usuários em um aplicativo de página única (SPA). O Azure AD B2C permite que seus aplicativos quem com contas sociais, corporativas e do Azure Active Directory usando protocolos padrão.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Registrar um aplicativo de exemplo de página única em seu diretório do Azure AD B2C.
> * Criar fluxos de usuário para inscrição, entrada, edição de perfil e redefinição de senha para usuários.
> * Configurar o aplicativo de exemplo para usar seu diretório do Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Use seu próprio [Diretório do Azure AD B2C](active-directory-b2c-get-started.md)
* Instalar o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com a carga de trabalho **ASP.NET e desenvolvimento Web**.
* [SDK do NET Core 2.0.0](https://www.microsoft.com/net/core) ou posterior
* Instale o [Node.js](https://nodejs.org/en/download/)

## <a name="register-single-page-app"></a>Registrar o aplicativo de página única

Aplicativos precisam ser [registrados](../active-directory/develop/developer-glossary.md#application-registration) em seu diretório antes de poderem receber [tokens de acesso](../active-directory/develop/developer-glossary.md#access-token) do Azure Active Directory. O registro do aplicativo cria uma [ID do aplicativo](../active-directory/develop/developer-glossary.md#application-id-client-id) para o aplicativo no seu diretório. 

Entre no [portal do Azure](https://portal.azure.com/) como administrador global do diretório do Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Selecione **Azure AD B2C** da lista de serviços no Portal do Azure. 

2. Nas configurações de B2C, clique em **Aplicativos** e em **Adicionar**. 

    Para registrar o aplicativo Web de exemplo no diretório, use as seguintes configurações:
    
    ![Adicionar um novo aplicativo](media/active-directory-b2c-tutorials-spa/spa-registration.png)
    
    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | Meu aplicativo de página única de exemplo | Insira um **Nome** que descreve seu aplicativo para os consumidores. | 
    | **Incluir aplicativo Web/API Web** | SIM | Selecione **Sim** para um aplicativo de página única. |
    | **Permitir fluxo implícito** | SIM | Selecione **Sim**, já que o aplicativo usa [entrada OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de Resposta** | `http://localhost:6420` | As URLs de Resposta são pontos de extremidade para onde o Azure AD B2C retornará os tokens que o aplicativo solicitar. Neste tutorial, o exemplo é executado localmente (localhost) e escuta na porta 6420. |
    | **Incluir cliente nativo** | Não  | Como esse é um aplicativo de página única e não um cliente nativo, selecione Não. |
    
3. Clique em **Criar** para registrar o aplicativo.

Os aplicativos registrados são exibidos na lista de aplicativos para o diretório do Azure AD B2C. Selecione o aplicativo de página única na lista. O painel de propriedades do aplicativo de página única registrado é exibido.

![Propriedades do aplicativo de página única](./media/active-directory-b2c-tutorials-spa/b2c-spa-properties.png)

Anote a **ID do aplicativo cliente**. A ID identifica o aplicativo exclusivamente e é necessário para configurá-lo mais adiante no tutorial.

## <a name="create-user-flows"></a>Criar fluxos de usuário

Um fluxo de usuário do Azure AD B2C define a experiência do usuário para uma tarefa de identidade. Por exemplo, inscrição, entrada, alteração de senhas e edição de perfis são fluxos de usuário comuns.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>Criar um fluxo de usuário de inscrição ou entrada

Para inscrever usuários para acesso e conectá-los ao aplicativo Web, crie um **fluxo de usuário de inscrição ou entrada**.

1. Na página do portal do Azure AD B2C, selecione **Fluxos de usuário** e clique em **Novo fluxo de usuário**.
2. Na guia **Recomendado**, clique em **Inscrever-se e entrar**.

    Para configurar o fluxo de usuário, use as configurações a seguir:

    ![Adicione um fluxo de usuário de inscrição ou entrada](media/active-directory-b2c-tutorials-spa/add-susi-user-flow.png)

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SiUpIn | Insira um **Nome** para o fluxo de usuário. O nome do fluxo de usuário é prefixado com **B2C_1_**. Use o nome do fluxo de usuário completo **B2C_1_SiUpIn** no código de exemplo. | 
    | **Provedores de Identidade** | Inscrição de email | O provedor de identidade usado para identificar o usuário exclusivamente. |

3. Em **Atributos de usuário e declarações**, clique em **Mostrar mais** e selecione as seguintes configurações:

    ![Adicione um fluxo de usuário de inscrição ou entrada](media/active-directory-b2c-tutorials-spa/add-attributes-and-claims.png)

    | Coluna      | Valor sugerido  | DESCRIÇÃO                                        |
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
    | **Nome** | SiPe | Insira um **Nome** para o fluxo de usuário. O nome do fluxo de usuário é prefixado com **B2C_1_**. Use o nome do fluxo de usuário completo **B2C_1_SiPe** no código de exemplo. | 
    | **Provedores de Identidade** | Entrada na conta local | O provedor de identidade usado para identificar o usuário exclusivamente. |

3.  Em **Atributos de usuário**, clique em **Mostrar mais** e selecione as configurações a seguir:

    | Coluna      | Valor sugerido  | DESCRIÇÃO                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Coletar atributo** | Nome de exibição e CEP | Selecione os atributos que os usuários podem modificar durante a edição de perfil. |
    | **Declaração de retorno** | Nome de exibição, CEP, ID de Objeto do Usuário | Selecione as [declarações](../active-directory/develop/developer-glossary.md#claim) que você deseja incluir no [token de acesso](../active-directory/develop/developer-glossary.md#access-token) após uma edição de perfil bem-sucedida. |

4. Clique em **OK**.
5. Clique em **Criar** para criar o seu fluxo de usuário. 

### <a name="create-a-password-reset-user-flow"></a>Criar um fluxo de usuário de redefinição de senha

Para habilitar a redefinição de senha no seu aplicativo, você precisa criar um **fluxo de usuário de redefinição de senha**. Esse fluxo de usuário descreve a experiência do consumidor durante a redefinição de senha e o conteúdo de tokens que o aplicativo recebe após a conclusão bem-sucedida.

1. Na página do portal do Azure AD B2C, selecione **Fluxos de usuário** e clique em **Novo fluxo de usuário**.
2. Na guia **Recomendado**, clique em **Redefinição de senha**.

    Para configurar o fluxo de usuário, use as configurações a seguir.

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SSPR | Insira um **Nome** para o fluxo de usuário. O nome do fluxo de usuário é prefixado com **B2C_1_**. Use o nome do fluxo de usuário completo **B2C_1_SSPR** no código de exemplo. | 
    | **Provedores de Identidade** | Redefinição de senha usando endereço de email | É o provedor de identidade usado para identificar o usuário exclusivamente. |

3. Em **Declarações de aplicativo**, clique em **Mostrar mais** e selecione a seguinte configuração:

    | Coluna      | Valor sugerido  | DESCRIÇÃO                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Declaração de retorno** | ID de objeto do usuário | Selecione as [declarações](../active-directory/develop/developer-glossary.md#claim) que você deseja incluir no [token de acesso](../active-directory/develop/developer-glossary.md#access-token) após uma redefinição de senha bem-sucedida. |

4. Clique em **OK**.
5. Clique em **Criar** para criar o seu fluxo de usuário. 

## <a name="update-single-page-app-code"></a>Atualizar o aplicativo de página única

Agora que você registrou um aplicativo e criou os fluxos de usuário, precisa configurar o aplicativo para usar o diretório do Azure AD B2C. Neste tutorial, você deve configurar um aplicativo de JavaScript de SPA de exemplo que pode ser baixado do GitHub. 

[Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clone o aplicativo Web de exemplo do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```
O aplicativo de exemplo demonstra como um aplicativo de página única pode usar o Azure AD B2C para usuário inscrever-se, entrar e chamar uma API Web protegida. Você precisa alterar o aplicativo para usar o registro do aplicativo em seu diretório e configurar os fluxos de usuário que você criou. 

Para alterar as configurações do aplicativo:

1. Abra o arquivo `index.html` no exemplo de aplicativo de página única do Node.js.
2. Configure o exemplo com as informações de registro do diretório do Azure AD B2C. Altere as seguintes linhas de código (substitua os valores pelos nomes do seu diretório e de suas APIs):

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://fabrikamb2c.b2clogin.com/tfp/fabrikamb2c.onmicrosoft.com/B2C_1_<Sign-up or sign-in policy name>",
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://fabrikamb2chello.azurewebsites.net/hello',
    };
    ```

    O nome do fluxo de usuário usado neste tutorial é **B2C_1_SiUpIn**. Se você estiver usando um nome de fluxo de usuário diferente, use o nome de fluxo de usuário no valor `authority`.

## <a name="run-the-sample"></a>Execute o exemplo

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

O aplicativo de exemplo dá suporte a inscrição, entrada, edição de perfil e redefinição de senha. Este tutorial destaca como um usuário se inscreve para usar o aplicativo usando um endereço de email. Você pode explorar os outros cenários por conta própria.

### <a name="sign-up-using-an-email-address"></a>Criar conta usando um endereço de email

1. Clique em **Logon** para inscrever-se como um usuário do aplicativo SPA. Este usa o fluxo de usuário **B2C_1_SiUpIn** definido em uma etapa anterior.

2. O Azure AD B2C apresenta uma página de entrada com um link de inscrição. Como você ainda não tem uma conta, clique no link **Inscrever-se agora**. 

3. O fluxo de trabalho de inscrição apresenta uma página para coletar e verificar a identidade do usuário usando um endereço de email. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo de usuário.

    Use um endereço de email válido e valide-o usando o código de verificação. Defina uma senha. Insira valores para os atributos necessários. 

    ![Fluxo de trabalho de inscrição](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Clique em **Criar** para criar uma conta local no diretório do Azure AD B2C.

Agora o usuário pode usar seu endereço de email para entrar e usar o aplicativo SPA.

> [!NOTE]
> Após o logon, o aplicativo exibirá um erro de "permissões insuficientes". Você recebe esse erro porque você está tentando acessar um recurso do diretório de demonstração. Uma vez que seu token de acesso só é válido para seu diretório do Azure AD, a chamada à API não é autorizada. Continue com o próximo tutorial para criar uma API Web protegida para seu diretório. 

## <a name="clean-up-resources"></a>Limpar recursos

Você pode usar o seu diretório do Azure AD B2C se planeja experimentar outros tutoriais do Azure AD B2C. Quando não for mais necessário, você poderá [excluir o diretório do Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um diretório do Azure AD B2C, a criar fluxos de usuário e a atualizar o exemplo de aplicativo de página única para usar o seu diretório do Azure AD B2C. Continue para o próximo tutorial a fim de aprender a registrar, configurar e chamar uma API Web protegida a partir do seu aplicativo de desktop.

> [!div class="nextstepaction"]
> [Exemplos de código do Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
