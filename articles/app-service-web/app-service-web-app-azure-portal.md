---
title: "Referência para navegar no portal do Azure"
description: "Conheça as diferentes experiências de usuário para aplicativos Web do Serviço de Aplicativo entre o portal de gerenciamento e o Portal do Azure"
services: app-service
documentationcenter: 
author: jaime-espinosa
manager: wpickett
editor: jimbe
ms.assetid: 0cc6a3cc-bd89-4a96-9177-d25f6fb737bb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: jaime-espinosa
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 00617f4d508c843000d69c02464a1bef36320a8e


---
# <a name="reference-for-navigating-the-azure-portal"></a>Referência para navegar no portal do Azure
Os Sites da Web do Azure agora são chamados de [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714). Estamos atualizando toda a nossa documentação para refletir essa alteração no nome e para fornecer instruções para o Portal do Azure. Até que esse processo seja concluído, você poderá usar este documento como um guia para trabalhar com Aplicativos Web no Portal do Azure.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="the-future-of-the-azure-classic-portal"></a>O futuro do Portal Clássico do Azure
Você notará as alterações de identidade visual no Portal Clássico do Azure, mas esse portal está sendo substituído pelo Portal do Azure. Como o portal clássico está sendo desativado, o foco para o novo desenvolvimento está mudando para o Portal do Azure. Todos os novos recursos futuros para Aplicativos Web serão incluídos no Portal do Azure. Comece a usar o Portal do Azure para aproveitar os recursos mais recentes que os aplicativos Web têm a oferecer.

## <a name="layout-differences-between-the-azure-classic-portal-and-azure-portal"></a>Diferenças de layout entre o portal clássico e o Portal Clássico do Azure
No portal clássico, todos os serviços do Azure são listados no lado esquerdo. A navegação no portal antigo segue uma estrutura de árvore, em que você pode começar no serviço e navegar para cada elemento. Essa estrutura funciona bem para o gerenciamento de componentes independentes. No entanto, os aplicativos criados no Azure são uma coleção de serviços interconectados, e essa estrutura de árvore não é ideal para trabalhar com coleções de serviços. 

O Portal do Azure facilita a compilação de aplicativos de ponta a ponta com componentes de vários serviços. O portal é organizado como *jornadas*. Uma *jornada* é uma série de *folhas*, que são contêineres para os diferentes componentes. Por exemplo, a configuração do dimensionamento automático para um aplicativo Web é uma *jornada* que leva várias folhas, conforme mostrado no exemplo a seguir: a folha **site** (o título dessa folha ainda não foi atualizado para usar a nova terminologia), a folha **Configurações** e a folha **Escalar horizontalmente**. No exemplo, o dimensionamento automático está sendo configurado para depender do uso da CPU. Portanto, há também uma folha de **Percentual de CPU**. Os componentes dentro das *folhas* são chamados de *partes*, que se parecem com blocos. 

![](./media/app-service-web-app-azure-portal/AutoScaling.png)

## <a name="navigation-example-create-a-web-app"></a>Exemplo de navegação: criar um aplicativo Web
A criação de novos aplicativos Web é muito fácil. A imagem a seguir mostra o portal clássico e o portal lado a lado para demonstrar que não houve muitas mudanças no número de etapas necessárias para colocar um aplicativo Web em funcionamento. 

![](./media/app-service-web-app-azure-portal/CreateWebApp.png)

No portal, você pode escolher entre os tipos mais comuns de aplicativos Web, incluindo aplicativos de galerias populares, como o WordPress. Para obter uma lista completa de aplicativos disponíveis, visite o [Azure Marketplace].

Ao criar um aplicativo Web, você especifica a URL, o plano do Serviço de Aplicativo e o local no portal, como faria no portal clássico. 

![](./media/app-service-web-app-azure-portal/CreateWebAppSettings.png)

Além disso, o portal permite definir outras configurações comuns. Por exemplo, [grupos de recursos](../azure-resource-manager/resource-group-overview.md) tornam simples ver e gerenciar recursos relacionados do Azure. 

## <a name="navigation-example-settings-and-features"></a>Exemplo de navegação: configurações e recursos
Todas as configurações e recursos agora são agrupados logicamente em uma única folha, da qual você pode navegar.

![](./media/app-service-web-app-azure-portal/WebAppSettings.png)

Por exemplo, você pode criar domínios personalizados clicando em **Domínios personalizados e SSL** na folha **Configurações**.

![](./media/app-service-web-app-azure-portal/ConfigureWebApp.png)

Para configurar um alerta de monitoramento, clique em **Solicitações e erros** e em **Adicionar Alerta**.

![](./media/app-service-web-app-azure-portal/Monitoring.png)

Para habilitar o diagnóstico, clique em **Logs de diagnóstico** na folha **Configurações**.

![](./media/app-service-web-app-azure-portal/Diagnostics.png)

Para definir configurações de aplicativo, clique em **Configurações do aplicativo** na folha **Configurações**. 

![](./media/app-service-web-app-azure-portal/AppSettingsPreview.png)

Com exceção do nome da marca, alguns itens no portal foram renomeados ou agrupados de modo diferente para que seja mais fácil encontrá-los. Por exemplo, veja a seguir uma captura de tela da página correspondente de configurações de aplicativo (**Configurar**) no portal clássico.

![](./media/app-service-web-app-azure-portal/AppSettings.png)

## <a name="more-resources"></a>Mais Recursos
[Azure Portal]: https://portal.azure.com
[Azure Marketplace]: /marketplace/

> [!NOTE]
> Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## <a name="whats-changed"></a>O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)




<!--HONumber=Jan17_HO3-->


