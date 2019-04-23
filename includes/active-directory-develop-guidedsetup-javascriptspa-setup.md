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
ms.openlocfilehash: da2477b19327273fe922ac81f909233cb4ef8f06
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59503013"
---
## <a name="setting-up-your-web-server-or-project"></a>Configuração do servidor Web ou do projeto

> Prefere baixar este projeto de exemplo?
> - [Baixar os arquivos de projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) para um servidor Web local, como Node
>
> ou o
> - [Baixar o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)
>
> Em seguida, pule para a [Etapa de configuração](#register-your-application) para configurar o exemplo de código antes de executá-lo.

## <a name="prerequisites"></a>Pré-requisitos
Um servidor Web local como [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) ou a integração do IIS Express com o [Visual Studio 2017](https://www.visualstudio.com/downloads/) é necessário para executar este tutorial.

Se for usar o Node.js para executar o projeto, instale um IDE, como o [Visual Studio Code](https://code.visualstudio.com/download), para editar os arquivos de projeto.

As instruções neste guia são baseadas no Node.js e no Visual Studio 2017, mas fique à vontade para usar qualquer outro ambiente de desenvolvimento ou servidor Web.

## <a name="create-your-project"></a>Criar seu projeto

> ### <a name="option-1-node-other-web-servers"></a>Opção 1: Node/outros servidores Web
> Verifique se você instalou o [Node.js](https://nodejs.org/en/download/) e siga a etapa abaixo:
> - Crie uma pasta para hospedar o aplicativo.

<p><!-- -->

> ### <a name="option-2-visual-studio"></a>Opção 2: Visual Studio
> Se você estiver usando o Visual Studio e estiver criando um novo projeto, siga as etapas abaixo para criar uma nova solução do Visual Studio:
> 1.    No Visual Studio:  **Arquivo > Novo > Projeto**
> 2.    Em **Visual C#\Web**, selecione **Aplicativo Web ASP.NET (.NET Framework)**
> 3.    Insira um nome para seu aplicativo e selecione **OK**
> 4.    Em **Novo aplicativo Web ASP.NET**, selecione **Vazio**

## <a name="create-your-single-page-applications-ui"></a>Criar a interface do usuário do aplicativo de página única
1. Crie um arquivo `index.html` para seu JavaScript SPA. Se estiver usando o Visual Studio, selecione o projeto (pasta raiz do projeto), clique com o botão direito do mouse e selecione: **Adicionar > Novo Item > Página HTML** e dê a ele o nome index.html.

2. Adicione o seguinte código à sua página:
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.4/js/msal.js"></script>
       <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```
