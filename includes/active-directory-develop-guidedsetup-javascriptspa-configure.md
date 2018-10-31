---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: b3d46e10facdef26b36c910a5c23b40a415a2894
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988389"
---
## <a name="register-your-application"></a>Registre seu aplicativo

Existem várias maneiras de registrar um aplicativo. Selecione a opção que melhor atenda às suas necessidades:
* [Modo expresso - Use o início rápido do SPA para configurar o aplicativo](#option-1-register-your-application-express-mode)
* [Modo avançado - configure manualmente as configurações do aplicativo](#option-2-register-your-application-advanced-mode)

### <a name="option-1-register-your-application-express-mode"></a>Opção 1: Registrar seu aplicativo (modo Expresso)

1. Faça login no [registro do aplicativo do portal do Azure (visualização)](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) para registrar um aplicativo.
1. Na página **Registrar um aplicativo**, insira um nome para o seu aplicativo.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
1. Quando terminar, selecione **Registro**.
1. Siga as instruções de início rápido para baixar e configurar automaticamente seu novo aplicativo para você em um clique.

### <a name="option-2-register-your-application-advanced-mode"></a>Opção 2: Registrar seu aplicativo (modo Avançado)

1. Faça login no [portal do Azure](https://portal.azure.com/) para registrar um aplicativo.
1. Se sua conta fornecer acesso a mais de um inquilino, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
1. No painel de navegação à esquerda, selecione o serviço do **Active Directory do Azure** e, em seguida, selecione **Registros do aplicativo (Visualizar)> Novo registro**.
1. Quando a página **Registrar um aplicativo** aparecer, insira um nome para o seu aplicativo.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
1. Na seção **Redirect URI**, selecione a plataforma **Web** e defina o valor para o URL do aplicativo com base em seu servidor da web. Veja as seções abaixo para obter instruções de como definir e obter a URL de redirecionamento no Visual Studio e no Node.
1. Quando terminar, selecione **Registro**.
1. Na página **Visão geral** do aplicativo, anote o valor do **ID do aplicativo (cliente)**.
1. Este início rápido requer que o [fluxo de concessão implícito](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md) seja ativado. No painel de navegação à esquerda do aplicativo registrado, selecione **Autenticação**.
1. Em **Configurações avançadas**, sob **concessão implícita**, ative os **tokens de ID** e **acesse as caixas de seleção**. Os tokens de ID e tokens de acesso são necessários, pois esse aplicativo precisa fazer login de usuários e chamar uma API.
1. Clique em **Salvar**.

> #### <a name="setting-the-redirect-url-for-node"></a>Definir a URL de redirecionamento para nó
> Para o Node.js, você pode definir a porta do servidor Web no arquivo *server.js*. Este tutorial usa a porta 30662 para referência, mas você pode usar qualquer outra porta disponível. Siga as instruções abaixo para configurar uma URL de redirecionamento nas informações do registro de aplicativo:<br/>
> - Volte para o *Registro do aplicativo* e defina `http://localhost:30662/` como `Redirect URL`, ou use `http://localhost:[port]/` se estiver usando uma porta TCP personalizada (onde *[porta]* é o personalizado Número da porta TCP).

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Instruções do Visual Studio para obter a URL de redirecionamento
> Siga estas etapas para obter a URL de redirecionamento:
> 1. No **Gerenciador de Soluções**, selecione o projeto e confira a janela **Propriedades**. Se não for exibida uma janela **Propriedades**, pressione **F4**.
> 2. Copie o valor de **URL** para a área de transferência:<br/> ![Propriedades do projeto](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Volte para o *Registro do aplicativo* e defina o valor como um **URL de redirecionamento**.

#### <a name="configure-your-javascript-spa"></a>Configurar o JavaScript SPA

1. No arquivo `index.html` criado durante a configuração do projeto, inclua as informações de registro do aplicativo. Adicione o seguinte código na parte superior dentro das marcas `<script></script>` no corpo do arquivo `index.html`:

    ```javascript
    var applicationConfig = {
        clientID: "[Enter the application Id here]",
        graphScopes: ["user.read"],
        graphEndpoint: "https://graph.microsoft.com/v1.0/me"
    };
    ```

<ol start="2">
<li>
Substitua <code>Enter the application Id here</code> pelo ID do aplicativo que você acabou de registrar.
</li>
</ol>
