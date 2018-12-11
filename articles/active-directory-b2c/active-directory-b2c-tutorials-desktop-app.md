---
title: Tutorial - Permitir autenticação de aplicativos de desktop com contas usando o Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como usar o Azure Active Directory B2C para fornecer o logon do usuário para um aplicativo de desktop .NET.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: a135dd0b350a6129d94f1c6b0b185c3fb272668f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834479"
---
# <a name="tutorial-enable-desktop-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Tutorial - Permitir autenticação de aplicativos de desktop com contas usando o Azure Active Directory B2C

Este tutorial mostra como usar o Azure Active Directory (Azure AD) B2C para inscrever e conectar usuários em um aplicativo de desktop do Windows Presentation Foundation (WPF). O Azure AD B2C permite que seus aplicativos quem com contas sociais, corporativas e do Azure Active Directory usando protocolos padrão.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Registrar um aplicativo de desktop de exemplo em seu locatário do Azure AD B2C.
> * Criar fluxos de usuário para inscrição, entrada, edição de perfil e redefinição de senha para usuários.
> * Configurar o aplicativo de exemplo para usar o locatário do Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Criar seu próprio [locatário do Azure AD B2C](active-directory-b2c-get-started.md)
* [Instale o Visual Studio 2017](https://www.visualstudio.com/downloads/) com as cargas de trabalho de **desenvolvimento de desktop .NET** e de **desenvolvimento para a Web e ASP.NET**.

## <a name="register-desktop-app"></a>Registrar o aplicativo de desktop

Aplicativos precisam ser [registrados](../active-directory/develop/developer-glossary.md#application-registration) em seu locatário antes de poderem receber [tokens de acesso](../active-directory/develop/developer-glossary.md#access-token) do Azure Active Directory. O registro do aplicativo cria uma [id do aplicativo](../active-directory/develop/developer-glossary.md#application-id-client-id) para o aplicativo no locatário. 

Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Selecione **Azure AD B2C** da lista de serviços no Portal do Azure. 

2. Nas configurações de B2C, clique em **Aplicativos** e em **Adicionar**. 

    Para registrar o aplicativo Web de exemplo no locatário, use as seguintes configurações:
    
    ![Adicionar um novo aplicativo](media/active-directory-b2c-tutorials-desktop-app/desktop-app-registration.png)
    
    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | Meu aplicativo WPF de exemplo | Insira um **Nome** que descreve seu aplicativo para os consumidores. | 
    | **Incluir aplicativo Web/API Web** | Não  | Selecione **Não** para um aplicativo de desktop. |
    | **Incluir cliente nativo** | SIM | Como este é um aplicativo de desktop ele é considerado um cliente nativo. |
    | **URI de redirecionamento** | Valores padrão | Identificador exclusivo para o qual o Azure AD B2C redireciona o agente do usuário em uma resposta do OAuth 2.0. |
    | **URI de Redirecionamento Personalizado** | `com.onmicrosoft.contoso.appname://redirect/path` | Insira `com.onmicrosoft.<your tenant name>.<any app name>://redirect/path` Fluxos de usuário enviam tokens para esse URI. |
    
3. Clique em **Criar** para registrar o aplicativo.

Os aplicativos registrados são exibidos na lista de aplicativos para o locatário do Azure AD B2C. Selecione o aplicativo de desktop na lista. O painel de propriedades do aplicativo de desktop registrado é exibido.

![Propriedades do aplicativo de desktop](./media/active-directory-b2c-tutorials-desktop-app/b2c-desktop-app-properties.png)

Anote a **ID do aplicativo cliente**. A ID identifica o aplicativo exclusivamente e é necessário para configurá-lo mais adiante no tutorial.

## <a name="create-user-flows"></a>Criar fluxos de usuário

Um fluxo de usuário do Azure AD B2C define a experiência do usuário para uma tarefa de identidade. Por exemplo, inscrição, entrada, alteração de senhas e edição de perfis são fluxos de usuário comuns.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>Criar um fluxo de usuário de inscrição ou entrada

Para inscrever usuários para acesso e entrada no aplicativo de desktop, crie um **fluxo de usuário de inscrição ou entrada**.

1. Na página do portal do Azure AD B2C, selecione **Fluxos de usuário** e clique em **Novo fluxo de usuário**.
2. Na guia **Recomendado**, clique em **Inscrever-se e entrar**.

    Para configurar o fluxo de usuário, use as configurações a seguir:

    ![Adicione um fluxo de usuário de inscrição ou entrada](media/active-directory-b2c-tutorials-desktop-app/add-susi-user-flow.png)

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SiUpIn | Insira um **Nome** para o fluxo de usuário. O nome do fluxo de usuário é prefixado com **B2C_1_**. Use o nome do fluxo de usuário completo **B2C_1_SiUpIn** no código de exemplo. | 
    | **Provedores de Identidade** | Inscrição de email | O provedor de identidade usado para identificar o usuário exclusivamente. |

3.  Em **Atributos de usuário e declarações**, clique em **Mostrar mais** e selecione as seguintes configurações:

    ![Adicionar declarações e atributos de usuário](media/active-directory-b2c-tutorials-desktop-app/add-attributes-and-claims.png)

    | Coluna      | Valores sugeridos  | DESCRIÇÃO                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Coletar atributo** | Nome de exibição e CEP | Selecione os atributos a serem coletados do usuário durante a inscrição. |
    | **Declaração de retorno** | Nome de exibição, CEP, Usuário é novo, ID de objeto do usuário | Selecione as [declarações](../active-directory/develop/developer-glossary.md#claim) que você deseja incluir no [token de acesso](../active-directory/develop/developer-glossary.md#access-token). |

4. Clique em **OK**.

5. Clique em **Criar** para criar o seu fluxo de usuário. 

### <a name="create-a-profile-editing-user-flow"></a>Criar um fluxo de usuário de edição de perfil

Para permitir que os usuários redefinam suas informações de perfil de usuário por conta própria, crie um **fluxo de usuário de edição de perfil**.

1. Na página do portal do Azure AD B2C, selecione **Fluxo de usuário** e clique em **Novo fluxo de usuário**.
2. Na guia **Recomendado**, clique em **Inscrever-se e entrar**.

    Para configurar o fluxo de usuário, use as configurações a seguir:

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SiPe | Insira um **Nome** para o fluxo de usuário. O nome do fluxo de usuário é prefixado com **B2C_1_**. Use o nome do fluxo de usuário completo **B2C_1_SiPe** no código de exemplo. | 
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

## <a name="update-desktop-app-code"></a>Atualizar o código do aplicativo de desktop

Agora que registrou o aplicativo de desktop e criou os fluxos de usuário, você precisa configurar o aplicativo para usar o seu locatário do Azure AD B2C. Neste tutorial, você configura um aplicativo de desktop de exemplo. 

[Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [procure no repositório](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) ou clone o exemplo do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

O aplicativo de desktop WPF de exemplo demonstra como um aplicativo de desktop pode usar o Azure AD B2C para usuário inscrever-se, entrar e chamar uma API Web protegida.

Você precisa alterar o aplicativo para usar o registro do aplicativo em seu locatário e configurar os fluxos de usuário que você criou. 

Para alterar as configurações do aplicativo:

1. Abra a solução `active-directory-b2c-wpf` no Visual Studio.

2. No projeto `active-directory-b2c-wpf`, abra o arquivo **App.xaml.cs** e faça as seguintes atualizações:

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "The Application ID for your desktop app registered in your tenant";
    ```

3. Atualize a variável **PolicySignUpSignIn** com o nome do *fluxo de usuário de inscrição ou entrada* que você criou na etapa anterior. Lembre-se de incluir o prefixo *b2c_1_*.

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_SiUpIn";
    ```

## <a name="run-the-sample-desktop-application"></a>Executar o aplicativo de desktop de exemplo

Pressione **F5** para compilar e executar o aplicativo de desktop. 

O aplicativo de exemplo dá suporte a inscrição, entrada, edição de perfil e redefinição de senha. Este tutorial destaca como um usuário se inscreve para usar o aplicativo usando um endereço de email. Você pode explorar os outros cenários por conta própria.

### <a name="sign-up-using-an-email-address"></a>Criar conta usando um endereço de email

1. Clique no botão **Entrar** para inscrever-se como um usuário do aplicativo de desktop. Este usa o fluxo de usuário **B2C_1_SiUpIn** definido em uma etapa anterior.

2. O Azure AD B2C apresenta uma página de entrada com um link de inscrição. Como você ainda não tem uma conta, clique no link **Inscrever-se agora**. 

3. O fluxo de trabalho de inscrição apresenta uma página para coletar e verificar a identidade do usuário usando um endereço de email. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo de usuário.

    Use um endereço de email válido e valide-o usando o código de verificação. Defina uma senha. Insira valores para os atributos necessários. 

    ![Fluxo de trabalho de inscrição](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Clique em **Criar** para criar uma conta local no locatário do Azure AD B2C.

Agora o usuário pode usar seu endereço de email para entrar e usar o aplicativo de desktop.

> [!NOTE]
> Se você clicar no botão **Chamar API**, você receberá um erro "Não autorizado". Você recebe esse erro porque você está tentando acessar um recurso do locatário de demonstração. Como o token de acesso só é válido para seu locatário do Azure AD, a chamada à API não é autorizada. Continue com o próximo tutorial para criar uma API Web protegida para seu locatário. 

## <a name="clean-up-resources"></a>Limpar recursos

Você pode usar o seu locatário do Azure AD B2C se planeja experimentar outros tutoriais do Azure AD B2C. Quando não for mais necessário, você poderá [excluir o locatário do Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Próximas etapas

Nesse tutorial, você aprendeu a criar um locatário do Azure AD B2C, a criar fluxos de usuário e a atualizar o aplicativo desktop de exemplo para usar o seu locatário do Azure AD B2C. Continue para o próximo tutorial a fim de aprender a registrar, configurar e chamar uma API Web protegida a partir do seu aplicativo de desktop.

> [!div class="nextstepaction"]
> 
