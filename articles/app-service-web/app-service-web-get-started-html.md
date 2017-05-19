---
title: "Criar um aplicativo Web HTML estático no Azure em cinco minutos | Microsoft Docs"
description: "Saiba como é fácil executar aplicativos Web no Serviço de Aplicativo implantando um aplicativo de exemplo."
services: app-service\web
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/08/2017
ms.author: riande
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 895906e1ab4bc50093ed3b18f043c3dd515ca054
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-static-html-web-app-in-azure-in-five-minutes"></a>Criar um aplicativo Web HTML estático no Azure em cinco minutos

Este início rápido orienta você sobre como implantar um site básico de HTML e CSS no Azure. Você executará o aplicativo usando um [Plano do Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview) e criará um aplicativo Web nele usando a [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli). Agora, use o git para implantar o aplicativo no Azure. Demora cerca de cinco minutos para concluir o tutorial depois que os pré-requisitos forem instalados.

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar essa amostra, baixe e instale os seguintes componentes:

- [Git](https://git-scm.com/)
- [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Baixar o exemplo

Em uma janela do terminal, clone o repositório de aplicativos de exemplo em seu computador local:

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="view-the-html"></a>Exibir o HTML

Navegue para o diretório que contém o HTML de exemplo. Abra o arquivo *index.html* em seu navegador.

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser.png)

[!INCLUDE [login-to-azure](../../includes/login-to-azure.md)] 
[!INCLUDE [configure-deployment-user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [app-service-web-quickstart1](../../includes/app-service-web-quickstart1.md)] 

Crie um [aplicativo Web](app-service-web-overview.md) no Plano do Serviço de Aplicativo `quickStartPlan`. O aplicativo Web fornece um espaço de hospedagem para seu código e fornece uma URL para exibir o aplicativo implantado.

[!INCLUDE [app-service-web-quickstart2](../../includes/app-service-web-quickstart2.md)] 

A página está sendo executada como um aplicativo Web do Serviço de Aplicativo do Azure:

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="update-and-redeploy-the-app"></a>Atualizar o aplicativo e reimplantar

Abra o arquivo *index.html* . Faça uma alteração à marcação. Por exemplo, altere `Hello world!` para `Hello Azure!`

Confirme suas alterações no Git e, em seguida, envie as alterações de código por push para o Azure.

```bash
git commit -am "updated HTML"
git push azure master
```

Depois que a implantação for concluída, atualize seu navegador para ver as alterações.

[!INCLUDE [manage-azure-web-app](../../includes/manage-azure-web-app.md)]


[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

- Explore os [scripts da CLI dos aplicativos Web](app-service-cli-samples.md) de exemplo.
- Saiba como [mapear um nome de domínio personalizado](app-service-web-tutorial-custom-domain.md), por exemplo, contoso.com, para um [aplicativo do Serviço de Aplicativo](app-service-web-tutorial-custom-domain.md).
