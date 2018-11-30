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
ms.openlocfilehash: 9a9f3cd2668f0e78441548592f38ade8ddc0fe46
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52330686"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Adicionar as informações de registro do aplicativo ao aplicativo

Nesta etapa, você precisa configurar a URL de Redirecionamento das informações de registro do aplicativo e, em seguida, adicionar a ID do Aplicativo ao aplicativo JavaScript SPA.

### <a name="configure-redirect-url"></a>Configurar a URL de redirecionamento

Configure o campo `Redirect URL` com a URL da página index.html com base no servidor Web e, em seguida, clique em *Atualizar*.

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Instruções do Visual Studio para obter a URL de redirecionamento
> Siga estas etapas para obter a URL de redirecionamento:
> 1. No **Gerenciador de Soluções**, selecione o projeto e confira a janela **Propriedades**. Se não for exibida uma janela **Propriedades**, pressione **F4**.
> 2. Copie o valor de **URL** para a área de transferência:<br/> ![Propriedades do projeto](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Cole o valor como uma **URL de redirecionamento** na parte superior dessa página e, em seguida, selecione **Atualizar**.

<p/>

> #### <a name="setting-redirect-url-for-node"></a>Configurando a URL de redirecionamento para o Node
> Para o Node.js, você pode definir a porta do servidor Web no arquivo *server.js*. Este tutorial usa a porta 30662 para referência, mas você pode usar qualquer outra porta disponível. Siga as instruções abaixo para configurar uma URL de redirecionamento nas informações do registro de aplicativo:<br/>
> Defina `http://localhost:30662/` como uma **URL de redirecionamento** na parte superior dessa página ou use `http://localhost:[port]/` se você estiver usando uma porta TCP personalizada (em que *[port]* é o número da porta TCP personalizada) e, em seguida, clique em **Atualizar**

### <a name="configure-your-javascript-spa-application"></a>Configure seu aplicativo JavaScript SPA

1. No arquivo `index.html` criado durante a configuração do projeto, inclua as informações de registro do aplicativo. Adicione o seguinte código na parte superior dentro das marcas `<script></script>` no corpo do arquivo `index.html`:

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/common",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
