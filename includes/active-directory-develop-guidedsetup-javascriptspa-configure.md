---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 07aac49e7aed7c95863a2058a9de3d1e8f2cd1ad
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060433"
---
## <a name="register-your-application"></a>Registre seu aplicativo

Há várias maneiras de criar um aplicativo, selecione um deles:

### <a name="option-1-register-your-application-express-mode"></a>Opção 1: Registrar seu aplicativo (modo Expresso)
Agora você precisa registrar seu aplicativo no *Portal de Registro de Aplicativos da Microsoft*:

1.  Registre seu aplicativo por meio do [Portal de registro de aplicativo da Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure).
2.  Insira um nome para o aplicativo e seu email.
3.  Verifique se a opção **Instalação Guiada** está marcada.
4.  Siga as instruções para obter a ID do aplicativo e colá-la no código.

### <a name="option-2-register-your-application-advanced-mode"></a>Opção 2: Registrar seu aplicativo (modo Avançado)

1. Acesse o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar um aplicativo.
2. Insira um nome para o aplicativo e seu email.
3. Verifique se a opção **Instalação Guiada** está desmarcada.
4.  Clique em **Adicionar Plataforma** e escolha **Web**.
5. Adicione a **URL de redirecionamento** que corresponde à URL do aplicativo com base em seu servidor Web. Veja as seções abaixo para obter instruções de como definir e obter a URL de redirecionamento no Visual Studio e no Node.
6. Clique em **Salvar**.

> #### <a name="setting-redirect-url-for-node"></a>Configurando a URL de redirecionamento para o Node
> Para o Node.js, você pode definir a porta do servidor Web no arquivo *server.js*. Este tutorial usa a porta 30662 para referência, mas fique à vontade para usar qualquer porta disponível. Em qualquer caso, siga as instruções abaixo para configurar uma URL de redirecionamento nas informações de registro do aplicativo:<br/>
> - Volte para o *Portal de Registro de Aplicativos* e defina `http://localhost:30662/` como um `Redirect URL` ou use `http://localhost:[port]/` se estiver usando uma porta TCP personalizada (em que *[porta]* é o número da porta TCP personalizada) e clique em 'Salvar'

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Instruções do Visual Studio para obter a URL de redirecionamento
> Siga estas etapas para obter a URL de redirecionamento:
> 1.    No **Gerenciador de Soluções**, selecione o projeto e confira a janela **Propriedades**. Se não for exibida uma janela **Propriedades**, pressione **F4**.
> 2.    Copie o valor de **URL** para a área de transferência:<br/> ![Propriedades do projeto](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Volte para o *Portal de Registro de Aplicativo* e cole o valor como uma **URL de redirecionamento** e selecione **Salvar**


#### <a name="configure-your-javascript-spa"></a>Configurar o JavaScript SPA

1.  No arquivo `index.html` criado durante a instalação do projeto, adicione as informações de registro de aplicativo. Adicione o seguinte código na parte superior dentro das marcas `<script></script>` no corpo do arquivo `index.html`:

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
<ol start="3">
<li>
Substitua <code>Enter the application Id here</code> pela ID do aplicativo que você acabou de registrar.
</li>
</ol>
