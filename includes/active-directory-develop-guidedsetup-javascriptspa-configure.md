---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 387adcdf8bdabf90bc1e691a7a8ec9ae0a8e90dc
ms.sourcegitcommit: abeefca6cd5ca01c3e0b281832212aceff08bf3e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "64993278"
---
## <a name="register-your-application"></a>Registre seu aplicativo

1. Faça login no [Portal do Azure](https://portal.azure.com/) para registrar um aplicativo.
1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
1. Navegue até a página [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) da plataforma de identidade da Microsoft para desenvolvedores.
1. Quando a página **Registrar um aplicativo** aparecer, insira um nome para o seu aplicativo.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
1. Na seção **Redirect URI**, selecione a plataforma **Web** e defina o valor para o URL do aplicativo com base em seu servidor da web. Veja as seções abaixo para obter instruções de como definir e obter a URL de redirecionamento no Visual Studio e no Node.
1. Ao terminar, selecione **Registrar**.
1. Na página **Visão geral** do aplicativo, anote o valor de **ID do aplicativo (cliente)**.
1. Este início rápido requer que o [fluxo de concessão implícita](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md) seja ativado. No painel de navegação à esquerda do aplicativo registrado, selecione **Autenticação**.
1. Em **Configurações avançadas**, sob **Concessão implícita**, marque as caixas de seleção **Tokens de ID** e **Tokens de Acesso**. Os tokens de ID e tokens de acesso são necessários, pois esse aplicativo precisa fazer logon dos usuários e chamar uma API.
1. Clique em **Salvar**.

> #### <a name="setting-the-redirect-url-for-node"></a>Definir a URL de redirecionamento para nó
> Para o Node.js, você pode definir a porta do servidor Web no arquivo *server.js*. Este tutorial usa a porta 30662 para referência, mas você pode usar qualquer outra porta disponível. Siga as instruções abaixo para configurar uma URL de redirecionamento nas informações do registro de aplicativo:<br/>
> - Volte para o *Registro do aplicativo* e defina `http://localhost:30662/` como `Redirect URL`, ou use `http://localhost:[port]/` se estiver usando uma porta TCP personalizada (onde *[porta]* é o personalizado Número da porta TCP).

<p>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Instruções do Visual Studio para obter a URL de redirecionamento
> Siga estas etapas para obter a URL de redirecionamento:
> 1. No **Gerenciador de Soluções**, selecione o projeto e confira a janela **Propriedades**. Se não for exibida uma janela **Propriedades**, pressione **F4**.
> 2. Copie o valor de **URL** para a área de transferência:<br/> ![Propriedades do projeto](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Volte para o *Registro do aplicativo* e defina o valor como um **URL de redirecionamento**.

#### <a name="configure-your-javascript-spa"></a>Configurar o JavaScript SPA

1. No arquivo `index.html` criado durante a configuração do projeto, inclua as informações de registro do aplicativo. Adicione o seguinte código na parte superior dentro das marcas `<script></script>` no corpo do arquivo `index.html`:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here",
            authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Em que:
    - `Enter_the_Application_Id_here` - é a **ID do aplicativo (cliente)** que você registrou.
    - `Enter_the_Tenant_Info_Here` - é definido como uma das seguintes opções:
       - Se o seu aplicativo for compatível com **Contas neste diretório organizacional**, substitua esse valor pelo **ID do locatário** ou **Nome do locatário** (por exemplo, Contoso.microsoft.com)
       - Se seu aplicativo dá suporte a **Contas em qualquer diretório organizacional**, substitua esse valor por `organizations`
       - Se seu aplicativo for compatível com **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**, substitua esse valor por `common`. Para restringir a compatibilidade *Somente a conta pessoais da Microsoft*, substitua esse valor por `consumers`.
