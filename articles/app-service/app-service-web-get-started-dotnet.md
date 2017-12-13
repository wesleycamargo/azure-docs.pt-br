---
title: Como criar um aplicativo Web ASP.NET Core no Azure | Microsoft Docs
description: "Saiba como executar aplicativos Web no Serviço de Aplicativo do Azure com a implantação do aplicativo Web do ASP.NET padrão."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/14/2017
ms.author: cephalin
ms.custom: mvc, devcenter
ms.openlocfilehash: 47f8fd97ad3be833e4f86f4bda57b140ce73c5de
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2017
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Criar um aplicativo Web ASP.NET Core no Azure

Os [aplicativos Web do Azure](app-service-web-overview.md) fornecem um serviço de hospedagem na Web altamente escalonável,com aplicação automática de patches.  Este guia de início rápido mostra como implantar seu primeiro aplicativo Web ASP.NET Core em aplicativos Web do Azure. Quando terminar, você terá um grupo de recursos que consiste em um plano do Serviço de Aplicativo e um aplicativo Web do Azure com um aplicativo Web implantado.

> [!NOTE]
> Se você quiser saber como criar e implantar um aplicativo Web do ASP.NET Framework, o artigo está disponível [aqui](app-service-web-get-started-dotnet-framework.md). 
>

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Instale o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> com as cargas de trabalho a seguir:
    - **Desenvolvimento Web e do ASP.NET**
    - **Desenvolvimento do Azure**

    ![ASP.NET, desenvolvimento Web e desenvolvimento do Azure (na Web e na nuvem)](media/app-service-web-tutorial-dotnet-sqldatabase/workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-aspnet-core-web-app"></a>Criar um aplicativo Web ASP.NET Core

No Visual Studio, crie um projeto selecionando **Arquivo > Novo > Projeto**. 

Na caixa de diálogo **Novo Projeto**, selecione **Visual C# > Web > Aplicativo Web ASP.NET Core**.

Nomeie o aplicativo como _myFirstAzureWebApp_ e clique em **OK**.
   
![Caixa de diálogo Novo Projeto](./media/app-service-web-get-started-dotnet/new-project.png)

Você pode implantar qualquer tipo de aplicativo Web ASP.NET Core no Azure. Para o início rápido, selecione o modelo **Aplicativo Web** e verifique se a autenticação está definida como **Sem Autenticação**.
      
Selecione **OK**.

![Caixa de diálogo Novo Projeto ASP .NET](./media/app-service-web-get-started-dotnet/razor-pages-aspnet-dialog.png)

Após a criação do projeto do ASP.NET Core, a página de boas-vinda do ASP.NET Core será exibida fornecendo vários links para recursos que o ajudarão a começar. 

![Página de boas-vindas](./media/app-service-web-get-started-dotnet/aspnet-core-welcome-page.png)

No menu, selecione **Depurar > Iniciar sem depuração** para executar o aplicativo Web localmente.

![Executar o aplicativo localmente](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="publish-to-azure"></a>Publicar no Azure

No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **myFirstAzureWebApp** e selecione **Publicar**.

![Publicar no Gerenciador de Soluções](./media/app-service-web-get-started-dotnet/right-click-publish.png)

Verifique se o **Serviço de Aplicativo do Microsoft Azure** está selecionado e clique em **Publicar**.

![Publicar na página de visão geral do projeto](./media/app-service-web-get-started-dotnet/publish-to-app-service.png)

Isso abre a caixa de diálogo **Criar Serviço de Aplicativo**, que ajuda a criar todos os recursos do Azure necessários para executar o aplicativo Web ASP.NET Core no Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Na caixa de diálogo **Criar Serviço de Aplicativo**, selecione **Adicionar uma conta** e entre com sua assinatura do Azure. Se você já estiver conectado, selecione a conta que contém a assinatura desejada na lista suspensa.

> [!NOTE]
> Se você já estiver conectado, não selecione **Criar** ainda.
>
>
   
![Entrar no Azure](./media/app-service-web-get-started-dotnet/sign-in-azure.png)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Ao lado de **Grupo de recursos**, selecione **Novo**.

Nomeie o grupo de recursos **myResourceGroup** e selecione **Ok**.

## <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Ao lado de **Plano do Serviço de Aplicativo**, selecione **Novo**. 

Na caixa de diálogo **Configurar Plano do Serviço de Aplicativo**, use as configurações na tabela de acordo com a captura de tela.

![Criar plano de Serviço de Aplicativo](./media/app-service-web-get-started-dotnet/configure-app-service-plan.png)

| Configuração | Valor sugerido | Descrição |
|-|-|-|
|Plano do Serviço de Aplicativo| myAppServicePlan | O nome do plano do Serviço de Aplicativo. |
| Local | Europa Ocidental | O datacenter onde o aplicativo Web está hospedado. |
| Tamanho | Grátis | O [Tipo de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) determina os recursos de hospedagem. |

Selecione **OK**.

## <a name="create-and-publish-the-web-app"></a>Publicar e publicar o aplicativo Web

Em **Nome do Aplicativo Web**, digite um nome exclusivo do aplicativo (os caracteres válidos são `a-z`, `0-9`, e `-`), ou aceite o nome exclusivo gerado automaticamente. A URL do aplicativo Web é `http://<app_name>.azurewebsites.net`, onde `<app_name>` é o nome do aplicativo Web.

Clique em **Criar** para começar a criar os recursos do Azure.

![Configurar o nome do aplicativo Web](./media/app-service-web-get-started-dotnet/web-app-name.png)

Após a conclusão do assistente, ele publicará o aplicativo Web ASP.NET Core no Azure e, em seguida, iniciará o aplicativo no navegador padrão.

![Aplicativo Web ASP.NET publicado no Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

O nome do aplicativo Web especificado na [etapa criar e publicar](#create-and-publish-the-web-app) é usado como o prefixo de URL no formato `http://<app_name>.azurewebsites.net`.

Parabéns, seu aplicativo Web ASP.NET Core está em execução no Serviço de Aplicativo do Azure.

## <a name="update-the-app-and-redeploy"></a>Atualizar o aplicativo e reimplantar

No **Gerenciador de Soluções**, abra _Pages/Index.cshtml_.

Encontre o rótulo HTML `<div id="myCarousel" class="carousel slide" data-ride="carousel" data-interval="6000">` próximo à parte superior e substitua o elemento inteiro pelo seguinte código:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Para implantar novamente no Azure, clique com o botão direito do mouse no projeto **myFirstAzureWebApp**, no **Gerenciador de Soluções** e selecione **Publicar**.

Na página de publicação, selecione **Publicar**.

Quando a publicação está concluída, o Visual Studio inicia um navegador para a URL do aplicativo Web.

![Aplicativo Web ASP.NET atualizado no Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-web-app"></a>Gestão do aplicativo web do Azure

Acesse o <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerenciar o aplicativo Web.

No menu à esquerda, selecione **Serviços de Aplicativos** e, em seguida, selecione o nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/app-service-web-get-started-dotnet/access-portal.png)

A página Visão Geral do seu aplicativo Web é exibida. Aqui você pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. 

![Folha Serviço de Aplicativo no portal do Azure](./media/app-service-web-get-started-dotnet/web-app-blade.png)

O menu à esquerda fornece páginas diferentes para configurar seu aplicativo. 

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [ASP.NET com o Banco de dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)
