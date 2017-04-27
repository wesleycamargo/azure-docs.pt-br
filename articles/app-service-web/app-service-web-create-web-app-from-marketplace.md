---
title: Criar um aplicativo Web no Azure Marketplace | Microsoft Docs
description: Saiba como criar um novo aplicativo Web WordPress no Azure Marketplace usando o Portal do Azure.
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 972a296d-f927-470b-8534-0f2cb9eac223
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/25/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: a04c7129cd2e16c129f3e4b8e8e40f76ff37114d
ms.lasthandoff: 01/20/2017


---
# <a name="create-a-web-app-from-the-azure-marketplace"></a>Criar um aplicativo Web do Azure Marketplace
<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

O Azure Marketplace disponibiliza uma ampla gama de aplicativos Web populares desenvolvidos pela Microsoft, por outras empresas e por iniciativas de software livre. Por exemplo, WordPress, Umbraco CMS, Drupal, etc. Os aplicativos Web são criados em uma grande variedade de estruturas populares, como [PHP] neste exemplo do WordPress, [.NET], [Node.js], [Java] e [Python], para citar alguns exemplos. Para criar um aplicativo Web do Azure Marketplace, o único software necessário será o navegador que você usa para o [Portal do Azure].

Neste tutorial, você aprenderá a:

* Localize e crie o aplicativo Web no Serviço de Aplicativo do Azure baseado em um modelo do Azure Marketplace.
* Defina as configurações do Serviço de Aplicativo do Azure para o novo aplicativo Web.
* Inicie e gerencie seu aplicativo Web.

Para fins deste tutorial, você implantará um site de blog do WordPress no Azure Marketplace. Quando você tiver concluído as etapas neste tutorial, terá seu próprio site do WordPress em execução na nuvem.

![Painel do aplicativo Web WordPress de exemplo][WordPressDashboard1]

O site do WordPress implantado neste tutorial usa o MySQL para o banco de dados. Se você deseja usar o Banco de Dados SQL como banco de dados, confira [Projeto Nami], que também está disponível no Azure Marketplace.

> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [ativar os benefícios de assinante do Visual Studio][activate] ou [inscrever-se em uma avaliação gratuita][free trial].
> 
> Se você quiser ter uma introdução ao Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo]. Lá, você poderá criar imediatamente um aplicativo Web de curta duração inicial no Serviço de Aplicativo, sem exigência de cartão de crédito e sem compromissos.
> 
> 

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>Localizar e Criar um Aplicativo Web no Serviço de Aplicativo do Azure
1. Faça logon no [Portal do Azure].
2. Clique em **Novo**.
   
    ![Criar um novo recurso do Azure][MarketplaceStart]
3. Procure **WordPress** e clique em **WordPress**. (Se você desejar usar o Banco de Dados SQL em vez de MySQL, procure **Project Nami**.)
   
    ![Procurar o WordPress no Marketplace][MarketplaceSearch]
4. Depois de ler a descrição do aplicativo WordPress, clique em **Criar**.
   
    ![Criar um aplicativo Web WordPress][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>Definir as Configurações do Serviço de Aplicativo do Azure para o Novo Aplicativo Web
1. Depois de criar um novo aplicativo Web, a folha de configurações do WordPress será exibida, que você usará para concluir as etapas a seguir:
   
    ![Definir configurações do aplicativo Web WordPress][ConfigStart]
2. Digite um nome para o aplicativo Web na caixa **aplicativo Web** .
   
    Esse nome deve ser exclusivo no domínio azurewebsites.net porque a URL do aplicativo Web será *{nome}*.azurewebsites.net. Se o nome inserido não for exclusivo, um ponto de exclamação vermelho aparecerá na caixa de texto.
   
    ![Configurar o nome do aplicativo Web WordPress][ConfigAppName]
3. Se você tiver mais de uma assinatura, escolha a que deseja usar.
   
    ![Configurar a assinatura para o aplicativo Web][ConfigSubscription]
4. Selecione um **Grupo de Recursos** ou crie um novo.
   
    Para saber mais sobre os grupos de recursos, confira [Visão geral do Azure Resource Manager][ResourceGroups].
   
    ![Configurar o grupo de recursos para o aplicativo Web][ConfigResourceGroup]
5. Selecione um **Plano/Local do Serviço de Aplicativo** ou crie um novo.
   
    Para saber mais sobre os planos do Serviço de Aplicativo, veja [Visão geral dos planos do Serviço de Aplicativo do Azure][AzureAppServicePlans].
   
    ![Configurar o plano de serviço para o aplicativo Web][ConfigServicePlan]
6. Clique em **Banco de Dados** e, em seguida, na folha **Novo Banco de Dados MySQL**, forneça os valores necessários para configurar o banco de dados MySQL.
   
    a. Digite um novo nome ou mantenha o nome padrão.
   
    b. Deixe o **Tipo de Banco de Dados** definido como **Compartilhado**.
   
    c. Escolha o mesmo local que o que você escolheu para o aplicativo Web.
   
    d. Escolha uma camada de preços. **Mercury** - que é gratuito, com conexões e espaço em disco mínimos - é adequado para este tutorial.
   
    e. Na folha **Novo Banco de Dados MySQL**, aceite os termos legais e clique em **OK**.
   
    ![Definir as configurações de banco de dados para o aplicativo Web.][ConfigDatabase]
7. Na folha **WordPress**, aceite os termos legais e, em seguida, clique em **Criar**.
   
    ![Concluir as configurações do aplicativo Web e clicar em OK][ConfigFinished]
   
    O Serviço de Aplicativo do Azure cria o aplicativo Web, geralmente em menos de um minuto. Você pode observar o progresso clicando no ícone de sino na parte superior da página do portal.
   
    ![Indicador de progresso][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Inicie e gerencie seu aplicativo Web do WordPress
1. Quando a criação de aplicativos Web for concluída, navegue no Portal do Azure para o grupo de recursos no qual você criou o aplicativo e você poderá ver o aplicativo Web e o banco de dados.
   
    O recurso adicional com o ícone de lâmpada é o [Application Insights][ApplicationInsights], que fornece serviços de monitoramento para seu aplicativo Web.
2. Na folha **Grupo de recursos** , clique na linha de aplicativo Web.
   
    ![Selecionar o aplicativo Web WordPress][WordPressSelect]
3. Na folha do aplicativo Web, clique em **Procurar**.
   
    ![Navegar até o aplicativo Web WordPress][WordPressBrowse]
4. Se for solicitado o idioma para o blog do WordPress, selecione o idioma desejado e clique em **Continuar**.
   
    ![Configurar o idioma para seu aplicativo Web WordPress][WordPressLanguage]
5. Na página de **Boas-vindas** do WordPress, insira as informações de configuração exigidas pelo WordPress e, em seguida, clique em **Instalar WordPress**.
   
    ![Definir as configurações de seu aplicativo Web WordPress][WordPressConfigure]
6. Faça logon usando as credenciais que você criou na página de **Boas-vindas** .  
7. A página Painel do site será aberta e exibirá as informações que você forneceu.    
   
    ![Exibir o painel do WordPress][WordPressDashboard2]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você viu como criar e implantar um aplicativo Web de exemplo no Azure Marketplace.

Para obter mais informações sobre como trabalhar com aplicativos Web do Serviço de Aplicativo, consulte os links no lado esquerdo da página (para janelas de navegador amplas) ou na parte superior da página (para janelas de navegador estreitas).

Para obter mais informações sobre o desenvolvimento de aplicativos Web do WordPress no Azure, veja [Desenvolvendo o WordPress no Serviço de Aplicativo do Azure][WordPressOnAzure].

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[Experimentar o Serviço de Aplicativo]: https://azure.microsoft.com/try/app-service/
[ResourceGroups]: ../azure-resource-manager/resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Portal do Azure]: https://portal.azure.com/
[Projeto Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png

