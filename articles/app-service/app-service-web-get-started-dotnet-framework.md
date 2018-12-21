---
title: Criar um aplicativo Web ASP.NET Framework do C# – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como executar aplicativos Web no Serviço de Aplicativo do Azure com a implantação do aplicativo Web do ASP.NET no C# padrão.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5f32ed74086b26f91f3ab24cd619bb117d497012
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53251259"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Criar um aplicativo Web do ASP.NET Framework no Azure

Os [aplicativos Web do Azure](app-service-web-overview.md) fornecem um serviço de hospedagem na Web altamente escalonável,com aplicação automática de patches.  Este guia de início rápido mostra como implantar seu primeiro aplicativo web ASP.NET em aplicativos Web do Azure. Quando terminar, você terá um grupo de recursos que consiste em um plano do Serviço de Aplicativo e um aplicativo Web do Azure com um aplicativo Web implantado.

![](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, instale o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> com a carga de trabalho do **ASP.NET e de desenvolvimento para a Web**.

Se você já instalou o Visual Studio 2017:

- Instale as atualizações mais recentes no Visual Studio, clicando em **Ajuda** > **Verificar Atualizações**.
- Adicione a carga de trabalho clicando em **Ferramentas** > **Obter Ferramentas e Recursos**.

## <a name="create-an-aspnet-web-app"></a>Criar um aplicativo Web ASP .NET

No Visual Studio, crie um projeto selecionando **Arquivo > Novo > Projeto**. 

Na caixa de diálogo **Novo Projeto**, clique em **Visual C# > Web > Aplicativo Web ASP.NET (.NET Framework)**.

Nomeie o aplicativo como _myFirstAzureWebApp_ e clique em **OK**.
   
![Caixa de diálogo Novo Projeto](./media/app-service-web-get-started-dotnet-framework/new-project.png)

Você pode implantar qualquer tipo de aplicativo Web ASP.NET no Azure. Para este início rapido, selecione o modelo **MVC** e verifique se a autenticação está definida para **Sem Autenticação**.
      
Selecione **OK**.

![Caixa de diálogo Novo Projeto ASP .NET](./media/app-service-web-get-started-dotnet-framework/select-mvc-template.png)

No menu, selecione **Depurar > Iniciar sem depuração** para executar o aplicativo Web localmente.

![Executar o aplicativo localmente](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## <a name="launch-the-publish-wizard"></a>Iniciar assistente de publicação

No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **myFirstAzureWebApp** e selecione **Publicar**.

![Publicar no Gerenciador de Soluções](./media/app-service-web-get-started-dotnet-framework/solution-explorer-publish.png)

O assistente de publicação é iniciado automaticamente. Selecione **Serviço de Aplicativo** > **Publicar** para abrir a caixa de diálogo **Criar Serviço de Aplicativo**.

![Publicar na página de visão geral do projeto](./media/app-service-web-get-started-dotnet-framework/publish-to-app-service.png)

## <a name="sign-in-to-azure"></a>Entrar no Azure

Na caixa de diálogo **Criar Serviço de Aplicativo**, selecione **Adicionar uma conta** e entre com sua assinatura do Azure. Se você já estiver conectado, selecione a conta que contém a assinatura desejada na lista suspensa.

> [!NOTE]
> Se você já estiver conectado, não selecione **Criar** ainda.
>
>
   
![Entrar no Azure](./media/app-service-web-get-started-dotnet-framework/sign-in-azure.png)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Ao lado de **Grupo de recursos**, selecione **Novo**.

Nomeie o grupo de recursos **myResourceGroup** e selecione **Ok**.

## <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Ao lado de **Plano de Hospedagem**, selecione **Novo**. 

Na caixa de diálogo **Configurar Plano de Hospedagem**, use as configurações na tabela de acordo com a captura de tela.

![Criar plano de Serviço de Aplicativo](./media/app-service-web-get-started-dotnet-framework/configure-app-service-plan.png)

| Configuração | Valor sugerido | DESCRIÇÃO |
|-|-|-|
|Plano do Serviço de Aplicativo| myAppServicePlan | O nome do plano do Serviço de Aplicativo. |
| Local padrão | Europa Ocidental | O datacenter onde o aplicativo Web está hospedado. |
| Tamanho | Grátis | O [Tipo de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) determina os recursos de hospedagem. |

Selecione **OK**.

## <a name="create-and-publish-the-web-app"></a>Publicar e publicar o aplicativo Web

Em **Nome do Aplicativo**, digite um nome exclusivo do aplicativo (os caracteres válidos são `a-z`, `0-9` e `-`) ou aceite o nome exclusivo gerado automaticamente. A URL do aplicativo Web é `http://<app_name>.azurewebsites.net`, em que `<app_name>` é o nome do aplicativo.

Clique em **Criar** para começar a criar os recursos do Azure.

![Configurar nome do aplicativo](./media/app-service-web-get-started-dotnet-framework/web-app-name.png)

Depois que o assistente é concluído, ele publica o aplicativo Web ASP.NET no Azure e, em seguida, inicia o aplicativo no navegador padrão.

![Aplicativo Web ASP.NET publicado no Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

O nome do aplicativo especificado na [etapa para criar e publicar](#create-and-publish-the-web-app) é usado como o prefixo de URL no formato `http://<app_name>.azurewebsites.net`.

Parabéns, seu primeiro aplicativo Web ASP.NET está em execução no Serviço de Aplicativo do Azure.

## <a name="update-the-app-and-redeploy"></a>Atualizar o aplicativo e reimplantar

No **Gerenciador de Soluções**, abra _Views\Home\Index.cshtml_.

Encontre o rótulo HTML `<div class="jumbotron">` próximo à parte superior e substitua o elemento inteiro pelo seguinte código:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Para implantar novamente no Azure, clique com o botão direito do mouse no projeto **myFirstAzureWebApp**, no **Gerenciador de Soluções** e selecione **Publicar**.

Na página de publicação, selecione **Publicar**.
![Página de resumo da publicação do Visual Studio](./media/app-service-web-get-started-dotnet-framework/publish-summary-page.png)

Quando a publicação está concluída, o Visual Studio inicia um navegador para a URL do aplicativo Web.

![Aplicativo Web ASP.NET atualizado no Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-web-app"></a>Gestão do aplicativo web do Azure

Acesse o <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerenciar o aplicativo Web.

No menu à esquerda, selecione **Serviços de Aplicativos** e, em seguida, selecione o nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/app-service-web-get-started-dotnet-framework/access-portal.png)

A página Visão Geral do seu aplicativo Web é exibida. Aqui você pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. 

![Folha Serviço de Aplicativo no portal do Azure](./media/app-service-web-get-started-dotnet-framework/web-app-blade.png)

O menu à esquerda fornece páginas diferentes para configurar seu aplicativo. 

## <a name="video"></a>Vídeo

Assista ao vídeo para ver este início rápido em ação e, depois, execute as etapas para publicar seu primeiro aplicativo .NET no Azure.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-for-NET-Developers/Create-a-NET-app-in-Azure-Quickstart/player]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [ASP.NET com o Banco de dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)
