---
title: Implantar um aplicativo do WordPress no Portal do Azure em cinco minutos | Microsoft Docs
description: "Saiba como é fácil executar aplicativos Web no Serviço de Aplicativo implantando um aplicativo do WordPress. Veja os resultados imediatamente."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/13/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 7ef5802866bf96859d3f44cdb58cbb412b08a700
ms.contentlocale: pt-br
ms.lasthandoff: 03/01/2017

---
# <a name="deploy-a-wordpress-app-in-the-azure-portal-in-five-minutes"></a>Implantar um aplicativo do WordPress no Portal do Azure em cinco minutos

Este tutorial mostra como implantar seu primeiro aplicativo Web do [WordPress](https://wordpress.org/) no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) em minutos.

![Site do WordPress](./media/app-service-web-get-started-php-portal/wpdashboard.png)

## <a name="prerequisites"></a>Pré-requisitos
Você precisa de uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Você pode [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/) sem uma conta do Azure. Crie um aplicativo inicial e brinque com ele por até uma hora: não é necessário cartão de crédito ou compromissos.
> 
> 

## <a name="deploy-the-wordpress-app"></a>Implantar o aplicativo do WordPress
1. Entre no [Portal do Azure](https://portal.azure.com).

2. Abra [https://portal.azure.com/#create/WordPress.WordPress](https://portal.azure.com/#create/WordPress.WordPress).

    Esse link é um atalho para configurar imediatamente um novo aplicativo do WordPress no Portal do Azure.

3. Em **Nome do aplicativo**, digite um nome do aplicativo Web. Você verá uma marca de seleção verde na caixa se o nome for exclusivo no domínio `azurewebsites.net`.
   
5. Em **Grupo de Recursos**, clique em **Criar novo** para criar um novo [grupo de recursos](../azure-resource-manager/resource-group-overview.md) e, em seguida, atribua um nome a ele.

6. Em **Provedor de Banco de Dados**, selecione **CleaDB**.

7. Clique em **Plano do Serviço de Aplicativo/Local** > **Criar Novo**. Configure o [Plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) conforme mostrado:

    - Em **Plano do Serviço de Aplicativo**, digite o nome desejado.
    - Em **Local**, escolha um local para hospedar seu plano.
    - Clique em **Tipo de preço** e selecione **F1 Gratuito** ou outra camada que atenda a você e, em seguida, clique em **Selecionar**.
    - Clique em **OK**.

8. Clique em **Banco de Dados** > **Criar Novo**. Configure o Banco de Dados SQL, conforme mostrado:

    - Em **Nome do Banco de Dados**, digite um nome de banco de dados. 
    - Em **Local**, escolha o mesmo local do Plano do Serviço de Aplicativo.
    - Clique em **Tipo de preço** e selecione **Mercúrio** ou outra camada que atenda a você e, em seguida, clique em **Selecionar**.
    - Clique em **Termos Legais** e clique em **Comprar**.
    - Clique em **OK**.

9. Clique em **Criar**.

    Agora o Azure cria seu aplicativo do WordPress com base em sua configuração. Você deve ver uma notificação **Implantação iniciada...**.

    ![Implantação iniciada – primeiro WordPress no Serviço de Aplicativo do Azure](./media/app-service-web-get-started-php-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-wordpress-web-app"></a>Inicie e gerencie seu aplicativo Web do WordPress

Quando o Azure concluir a implantação do aplicativo você verá outra notificação.

![Implantação bem-sucedida – primeiro WordPress no Serviço de Aplicativo do Azure](./media/app-service-web-get-started-php-portal/deployment-succeeded.png)

1. Clique na notificação. Se você não viu a notificação, poderá sempre acessá-la clicando no sino de notificação (![Sino de notificação – primeiro WordPress no Serviço de Aplicativo do Azure](./media/app-service-web-get-started-dotnet-portal/notification.png)).

    Agora você deve ver a [folha](../azure-resource-manager/resource-group-portal.md#manage-resources) de gerenciamento de seu aplicativo Web (*folha*: uma página de portal que se abre horizontalmente).

3. Na parte superior da página de Visão geral, clique em **Procurar**.
   
    ![Procurar – primeiro WordPress no Serviço de Aplicativo do Azure](./media/app-service-web-get-started-php-portal/browse.png)

    Agora você vê a página **Inicial** do WordPress. Configure a instalação do WordPress e comece a brincar com ele!

    ![Configuração do WordPress – primeiro WordPress no Serviço de Aplicativo do Azure](./media/app-service-web-get-started-php-portal/wordpress-config.png)
    
## <a name="next-steps"></a>Próximas etapas
* [Criar, configurar e implantar um aplicativo Web Laravel no Azure](app-service-web-php-get-started.md) – Aprenda as habilidades básicas que você precisa executar qualquer aplicativo Web de PHP no Azure, tais como:

    * Criar e configurar aplicativos no Azure do PowerShell/Bash.
    * Definir a versão do PHP.
    * Usar um arquivo de inicialização que não está no diretório raiz do aplicativo.
    * Habilite a automação do Composer.
    * Acessar variáveis de ambiente específicas.
    * Solucionar erros comuns.

* [Implantar seu código no Serviço de Aplicativo do Azure](web-sites-deploy.md) – Aprenda como implantar do FTP ou de repositórios de controle do código-fonte.
* [Adicionar funcionalidade ao seu primeiro aplicativo Web](app-service-web-get-started-2.md) – Leve seu aplicativo do Azure para o próximo patamar. Autenticar os usuários. Dimensione-o com base na demanda. Configure alguns alertas de desempenho. Tudo isso com apenas alguns cliques.

