---
title: Implantar um aplicativo Web do Umbraco no Portal do Azure em cinco minutos | Microsoft Docs
description: "Saiba como é fácil executar os aplicativos Web no Serviço de Aplicativo implantando um aplicativo ASP.NET de exemplo. Veja os resultados imediatamente."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/10/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: fa3f31cdd708729071876ffad707bea70567da83
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-an-umbraco-web-app-in-the-azure-portal-in-five-minutes"></a>Implantar um aplicativo Web do Umbraco no Portal do Azure em cinco minutos

Este tutorial ajuda você a implantar um aplicativo Web do [Umbraco](https://our.umbraco.org/) no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) em alguns minutos.

![Aplicativo do Umbraco](./media/app-service-web-get-started-dotnet-portal/defaultpage.png)

## <a name="prerequisites"></a>Pré-requisitos
Você precisa de uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Você pode [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/) sem uma conta do Azure. Crie um aplicativo inicial e brinque com ele por até uma hora: não é necessário cartão de crédito ou compromissos.
> 
> 

## <a name="deploy-the-aspnet-app"></a>Implantar o aplicativo ASP.NET
1. Entre no [Portal do Azure](https://portal.azure.com).

2. Abra [https://portal.azure.com/#create/umbracoorg.UmbracoCMS](https://portal.azure.com/#create/umbracoorg.UmbracoCMS).

    Esse link é um atalho para configurar imediatamente um novo aplicativo do Umbraco no Portal do Azure.

3. Em **Nome do aplicativo**, digite um nome do aplicativo Web. Você verá uma marca de seleção verde na caixa se o nome for exclusivo no domínio `azurewebsites.net`.
   
5. Em **Grupo de Recursos**, clique em **Criar novo** para criar um novo [grupo de recursos](../azure-resource-manager/resource-group-overview.md) e, em seguida, atribua um nome a ele.

7. Clique em **Plano do Serviço de Aplicativo/Local** > **Criar Novo**. Configure o [Plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) conforme mostrado:

    - Em **Plano do Serviço de Aplicativo**, digite o nome desejado.
    - Em **Local**, escolha um local para hospedar seu plano.
    - Clique em **Tipo de preço** e selecione **F1 Gratuito** ou outra camada que atenda a você e, em seguida, clique em **Selecionar**.
    - Clique em **OK**.

    A configuração do CMS do Umbraco agora deve ser semelhante a seguinte captura de tela:

    ![Configuração em andamento – primeiro Umbraco no Serviço de Aplicativo do Azure](./media/app-service-web-get-started-dotnet-portal/configure-in-progress.png)

12. Clique em **Banco de Dados SQL** > **Criar um novo banco de dados**. Configure o Banco de Dados SQL, conforme mostrado:

    - Em **Nome**, digite um nome, como **myDB**.
    - Clique em **Tipo de preço** e selecione **F Gratuito** ou outra camada que atenda a você e, em seguida, clique em **Selecionar**.
    - Clique em **Servidor de destino** > **Criar um novo servidor**. Configure o servidor de banco de dados conforme mostrado:

        - Em **Nome do servidor**, digite um nome do servidor. Você verá uma marca de seleção verde na caixa se o nome for exclusivo no domínio `.database.windows.net`.
        - Em **Logon de administrador do servidor**, digite o nome de usuário administrador desejado.
        - Em **Senha** e **Confirmar senha**, digite a senha desejada.
        - Em Local, selecione o mesmo local que você usar para o aplicativo Web.
        - Verifique se **Permitir que os serviços do Azure acessem o servidor** está selecionado.
        - Clique em **Selecionar**.
    
    - Clique em **Selecionar**.

13. Clique em **Configurações do aplicativo Web**, especifique o nome de usuário e a senha do banco de dados e clique em **OK**.

    A configuração do CMS do Umbraco agora deve ser semelhante a seguinte captura de tela:

    ![Configuração concluída – primeiro Umbraco no Serviço de Aplicativo do Azure](./media/app-service-web-get-started-dotnet-portal/configure-complete.png)

14. Clique em **Criar**.
    
    Agora o Azure cria seu aplicativo do Umbraco com base em sua configuração. Você deve ver uma notificação **Implantação iniciada...**.

    ![Implantação bem-sucedida – primeiro Umbraco no Serviço de Aplicativo do Azure](./media/app-service-web-get-started-dotnet-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-umrbaco-web-app"></a>Iniciar e gerenciar seu aplicativo Web Umbraco

Quando o Azure concluir a implantação do aplicativo você verá outra notificação.

![Implantação bem-sucedida – primeiro Umbraco no Serviço de Aplicativo do Azure](./media/app-service-web-get-started-dotnet-portal/deployment-succeeded.png)

1. Clique na notificação. Se você não viu a notificação, poderá sempre acessá-la clicando no sino de notificação (![Sino de notificação – primeiro Umbraco no Serviço de Aplicativo do Azure](./media/app-service-web-get-started-dotnet-portal/notification.png)).

    Agora você deve ver a [folha](../azure-resource-manager/resource-group-portal.md#manage-resources) de gerenciamento de seu aplicativo Web (*folha*: uma página de portal que se abre horizontalmente).

3. Na parte superior da página de Visão geral, clique em **Procurar**.
   
    ![Procurar – primeiro Umbraco no Serviço de Aplicativo do Azure](./media/app-service-web-get-started-dotnet-portal/browse.png)

    Agora você vê a página **Inicial** do Umbraco. Configure a instalação do Umbraco e comece a brincar com ele!

    ![Configuração do Umbraco – primeiro Umbraco no Serviço de Aplicativo do Azure](./media/app-service-web-get-started-dotnet-portal/umbraco-config.png)
    
## <a name="next-steps"></a>Próximas etapas
* [Implantar um aplicativo Web ASP.NET no Serviço de Aplicativo do Azure usando o Visual Studio](web-sites-dotnet-get-started.md) – Aprenda a criar um novo aplicativo Web do Azure no Visual Studio, usando qualquer um dos modelos de aplicativos incluídos.
* [Implantar seu código no Serviço de Aplicativo do Azure](web-sites-deploy.md) – Aprenda como implantar do FTP ou de repositórios de controle do código-fonte.
* [Adicionar funcionalidade ao seu primeiro aplicativo Web](app-service-web-get-started-2.md) – Leve seu aplicativo do Azure para o próximo patamar. Autenticar os usuários. Dimensione-o com base na demanda. Configure alguns alertas de desempenho. Tudo isso com apenas alguns cliques.

