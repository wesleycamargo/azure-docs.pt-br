---
title: Tutorial – Habilitar autenticação um aplicativo de página única – Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como usar o Azure Active Directory B2C para fornecer o logon do usuário para um aplicativo de página única (JavaScript).
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 9541d635ff69444459470cf1e486568a58af0a1e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730167"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Tutorial: Habilitar autenticação em um aplicativo de página única usando o Azure Active Directory B2C

Este tutorial mostra como usar o Azure AD (Azure Active Directory) B2C para inscrever e conectar usuários em um SPA (aplicativo de página única). O Azure AD B2C permite que seus aplicativos se autentiquem com contas sociais, corporativas e do Azure Active Directory usando protocolos padrão abertos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Atualizar o aplicativo no Azure AD B2C
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo de usuário

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Crie fluxos de usuário](tutorial-create-user-flows.md) para habilitar experiências de usuário em seu aplicativo. 
* Instalar o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com a carga de trabalho **ASP.NET e desenvolvimento Web**.
* Instale o [SDK do .NET Core 2.0.0](https://www.microsoft.com/net/core) ou posterior
* Instale o [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Atualizar o aplicativo

No tutorial concluído como parte dos pré-requisitos, você adicionou um aplicativo Web no Azure AD B2C. Para habilitar a comunicação com o exemplo no tutorial, é necessário adicionar um URI de redirecionamento ao aplicativo no Azure AD B2C.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Aplicativos** e, em seguida, selecione o aplicativo *webapp1*.
5. Em **URL de resposta**, adicione `http://localhost:6420`.
6. Clique em **Salvar**.
7. Na página de propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo Web.
8. Selecione **Chaves**, **Gerar chave** e selecione **Salvar**. Registre a chave que você usará ao configurar o aplicativo Web.

## <a name="configure-the-sample"></a>Configurar o exemplo

Neste tutorial, você deve configurar um exemplo que pode ser baixado do GitHub. O exemplo demonstra como um aplicativo de página única pode usar o Azure AD B2C para um usuário inscrever-se, entrar e chamar uma API Web protegida.

[Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clone o exemplo do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

Para alterar as configurações:

1. Abra o arquivo `index.html` no exemplo.
2. Configure o exemplo com a ID do aplicativo e a chave que você registrou anteriormente. Altere as linhas de código a seguir substituindo os valores pelos nomes do diretório e de APIs:

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID>',
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
    };
    ```

    O nome do fluxo de usuário usado neste tutorial é **B2C_1_signupsignin1**. Se você estiver usando um nome de fluxo de usuário diferente, use o nome de fluxo de usuário no valor `authority`.

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

O exemplo dá suporte à inscrição, conexão, edição de perfil e redefinição de senha. Este tutorial destaca como um usuário se inscreve usando um endereço de email.

### <a name="sign-up-using-an-email-address"></a>Criar conta usando um endereço de email

1. Clique em **Logon** para inscrever-se como um usuário do aplicativo. Este usa o fluxo de usuário **B2C_1_signupsignin1** definido em uma etapa anterior.
2. O Azure AD B2C apresenta uma página de entrada com um link de inscrição. Como você ainda não tem uma conta, clique no link **Inscrever-se agora**. 
3. O fluxo de trabalho de inscrição apresenta uma página para coletar e verificar a identidade do usuário usando um endereço de email. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo de usuário.

    Use um endereço de email válido e valide-o usando o código de verificação. Defina uma senha. Insira valores para os atributos necessários. 

    ![Fluxo de trabalho de inscrição](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Clique em **Criar** para criar uma conta local no diretório do Azure AD B2C.

Agora o usuário pode usar seu endereço de email para entrar e usar o aplicativo SPA.

> [!NOTE]
> Após o logon, o aplicativo exibirá um erro de "permissões insuficientes". Você recebe esse erro porque você está tentando acessar um recurso do diretório de demonstração. Uma vez que seu token de acesso só é válido para seu diretório do Azure AD, a chamada à API não é autorizada. Continue com o próximo tutorial para criar uma API Web protegida para seu diretório.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Atualizar o aplicativo no Azure AD B2C
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo de usuário

> [!div class="nextstepaction"]
> [Tutorial: Permitir acesso a uma API Web ASP.NET Core de um aplicativo de página única usando o Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
