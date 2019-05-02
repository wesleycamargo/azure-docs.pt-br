---
title: Tutorial – Permitir acesso a uma API Web do Node.js de um aplicativo da área de trabalho – Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como usar o Active Directory B2C para proteger uma API Web Node.js e chamá-la de um aplicativo de desktop .NET.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: ef48c0f78083217581594b481b15a74a49fef4f9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64715848"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Tutorial: Permitir acesso a uma API Web do Node.js de um aplicativo da área de trabalho usando o Azure Active Directory B2C

Este tutorial mostra como chamar um recurso da API Web Node.js protegida pelo Azure Active Directory (Azure AD) B2C de um aplicativo de desktop do Windows Presentation Foundation (WPF).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um aplicativo API Web
> * Configurar escopos para uma API Web
> * Conceder permissões à API Web
> * Atualizar o exemplo para usar o aplicativo

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas e os pré-requisitos no [Tutorial: Permitir autenticação de aplicativos da área de trabalho com contas usando o Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

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

Para chamar uma API Web protegida de um aplicativo, é necessário conceder permissões ao aplicativo para a API. No tutorial de pré-requisito, você criou um aplicativo Web no Azure AD B2C chamado *app1*. Use este aplicativo para chamar a API Web.

1. Selecione **Aplicativos** e, em seguida, selecione *nativeapp1*.
2. Selecione **Acesso à API** e, em seguida, selecione **Adicionar**.
3. No menu suspenso **Selecionar API**, selecione *webapi1*.
4. No menu suspenso **Selecionar Escopos**, selecione os escopos **Hello.Read** e **Hello.Write** definidos anteriormente.
5. Clique em **OK**.

Um usuário autentica-se com o Azure AD B2C para usar o aplicativo da área de trabalho do WPF. O aplicativo da área de trabalho obtém uma concessão de autorização do Azure AD B2C para acessar a API Web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a API Web está registrada e você tem escopos definidos, configure o código da API Web para usar o locatário do Azure AD B2C. Neste tutorial, configure um aplicativo Web Node.js de exemplo que pode ser baixado do GitHub. 

[Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) ou clone o aplicativo Web de exemplo do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
O exemplo de API Web Node.js usa a biblioteca de Passport.js para habilitar o Azure AD B2C a proteger chamadas à API. 

1. Abra o arquivo `index.js` .
2. Configure o exemplo com as informações de registro do locatário do Azure AD B2C. Alterar as seguintes linhas de código:

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Execute o exemplo

1. Inicie um prompt de comando de Node.js.
2. Altere o diretório que contém o exemplo de Node.js. Por exemplo `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Execute os seguintes comandos:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Executar o aplicativo da área de trabalho

1. Abra a solução **active-directory-b2c-wpf** no Visual Studio.
2. Pressione **F5** para executar o aplicativo de desktop.
3. Entre usando o endereço de email e a senha usada no [tutorial Autenticar usuários com o Azure Active Directory B2C em um aplicativo de desktop](active-directory-b2c-tutorials-desktop-app.md).
4. Clique no botão **Chamar API**. 

O aplicativo da área de trabalho faz uma solicitação para a API Web e obtém uma resposta com o nome de exibição do usuário conectado. Seu aplicativo da área de trabalho protegido está chamando a API Web protegida no seu locatário do Azure AD B2C.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Adicionar um aplicativo API Web
> * Configurar escopos para uma API Web
> * Conceder permissões à API Web
> * Atualizar o exemplo para usar o aplicativo

> [!div class="nextstepaction"]
> [Tutorial: Adicionar provedores de identidade aos aplicativos no Azure Active Directory B2C](tutorial-add-identity-providers.md)
