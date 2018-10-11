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
ms.openlocfilehash: c2b86e79f0364ee84e01fee5e9837db5a6b618a2
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843381"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Adicionar as informações de registro do aplicativo ao seu aplicativo

Nesta etapa, você precisa configurar a URL de Redirecionamento das informações de registro do aplicativo e, em seguida, adicionar a ID do Aplicativo ao aplicativo JavaScript SPA.

### <a name="configure-redirect-url"></a>Configurar a URL de redirecionamento

Configure o campo `Redirect URL` com a URL da página index.html com base no servidor Web e, em seguida, clique em *Atualizar*.


> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Instruções do Visual Studio para obter a URL de redirecionamento
> Siga estas etapas para obter a URL de redirecionamento:
> 1.    No **Gerenciador de Soluções**, selecione o projeto e confira a janela **Propriedades**. Se não for exibida uma janela **Propriedades**, pressione **F4**.
> 2.    Copie o valor de **URL** para a área de transferência:<br/> ![Propriedades do projeto](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Cole o valor como uma **URL de redirecionamento** na parte superior dessa página e, em seguida, clique em **Atualizar**

<p/>

> #### <a name="setting-redirect-url-for-node"></a>Configurando a URL de redirecionamento para o Node
> Para o Node.js, você pode definir a porta do servidor Web no arquivo *server.js*. Este tutorial usa a porta 30662 para referência, mas você pode usar qualquer outra porta disponível. Siga as instruções abaixo para configurar uma URL de redirecionamento nas informações do registro de aplicativo:<br/>
> Defina `http://localhost:30662/` como uma **URL de redirecionamento** na parte superior dessa página ou use `http://localhost:[port]/` se você estiver usando uma porta TCP personalizada (em que *[port]* é o número da porta TCP personalizada) e, em seguida, clique em **Atualizar**

### <a name="configure-your-javascript-spa-application"></a>Configure seu aplicativo JavaScript SPA

1.  No arquivo `index.html` criado durante a instalação do projeto, adicione as informações de registro de aplicativo. Adicione o seguinte código na parte superior dentro das marcas `<script></script>` no corpo do arquivo `index.html`:

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
