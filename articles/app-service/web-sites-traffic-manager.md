---
title: "Controlando o tráfego de aplicativos Web do Azure com o Gerenciador de Tráfego do Azure"
description: "Este artigo fornece informações resumidas sobre o Gerenciador de Tráfego do Azure, já que ele está relacionado a aplicativos Web do Azure."
services: app-service\web
documentationcenter: 
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.openlocfilehash: 93645aa5765d533b45fe2266f061ad61c0bf45d7
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2017
---
# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Controlando o tráfego de aplicativos Web do Azure com o Gerenciador de Tráfego do Azure
> [!NOTE]
> Este artigo apresenta informações resumidas sobre o Gerenciador de Tráfego do Microsoft Azure no que se refere a Aplicativos Web do Azure. Mais informações sobre o próprio Gerenciador de Tráfego do Azure podem ser encontradas visitando-se os links ao final deste artigo.
> 
> 

## <a name="introduction"></a>Introdução
Você pode usar o Gerenciador de Tráfego do Azure para controlar como as solicitações de clientes Web são distribuídas aos aplicativos Web no Serviço de Aplicativo do Azure. Quando os pontos de extremidade do aplicativo Web são adicionados a um perfil do Gerenciador de Tráfego do Azure, o Gerenciador de Tráfego do Azure acompanha o status de seus aplicativos Web (em execução, parado ou excluído) para decidir quais desses pontos de extremidade devem receber tráfego.

## <a name="routing-methods"></a>Métodos de roteamento
O Gerenciador de Tráfego do Azure usa três métodos diferentes de roteamento. Esses métodos são descritos na lista a seguir, já que pertencem a aplicativos Web do Azure.

* **[Prioridade](#priority):** use um aplicativo Web primário para todo o tráfego e forneça backups caso o backup primário ou os aplicativos Web de backup não estejam disponíveis.
* **[Ponderada](#weighted):** distribua tráfego em um conjunto de aplicativos Web, seja uniformemente ou acordo com pesos, o que você definir.
* **[Desempenho](#performance):** quando você tiver aplicativos Web em diferentes localizações geográficas, use o aplicativo Web "mais próximo" em termos da menor latência de rede.
* **[Geográfico](#geographic):** direcionar os usuários para aplicativos Web específicos com base em qual localização geográfica a consulta DNS se origina. 

Para obter mais informações, consulte [Métodos de roteamento do Gerenciador de Tráfego](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="web-apps-and-traffic-manager-profiles"></a>Aplicativos Web e perfis do Gerenciador de Tráfego
Para configurar o controle do tráfego de aplicativos Web, crie um perfil no Gerenciador de Tráfego do Azure que use um dos três métodos de balanceamento de carga descritos anteriormente e, em seguida, adicione os pontos de extremidade (neste caso, aplicativos Web) para os quais você deseja controlar o tráfego até o perfil. O status do aplicativo Web (em execução, parado ou excluído) é comunicado regularmente ao perfil para que o Gerenciador de Tráfego do Azure possa direcioná-lo de acordo.

Ao usar o Gerenciador de Tráfego do Azure com o Azure, tenha em mente os seguintes pontos:

* Para implantações apenas de aplicativos Web na mesma região, Aplicativos Web já oferece funcionalidade de failover e rodízio independentemente do modo do aplicativo Web.
* Para implantações na mesma região usando Aplicativos Web juntamente com outro serviço de nuvem do Azure, você pode combinar ambos os tipos de pontos de extremidade para habilitar cenários híbridos.
* Você só pode especificar um ponto de extremidade de aplicativo Web por região em um perfil. Quando você seleciona um aplicativo Web como um ponto de extremidade para uma região, os aplicativos Web restantes nessa região ficam indisponíveis para seleção para esse perfil.
* Os pontos de extremidade de aplicativo Web que você especificar no perfil do Gerenciador de Tráfego do Azure serão exibidos na seção **Nomes de Domínio** da página Configurar do aplicativo Web no perfil, mas não serão configuráveis lá.
* Depois que você adicionar um aplicativo Web a um perfil, a **URL do Site** no Painel da página do portal do aplicativo Web exibirá a URL do domínio personalizado do aplicativo Web se você tiver configurado uma. Caso contrário, exibe a URL do perfil do Gerenciador de Tráfego (por exemplo, `contoso.trafficmgr.com`). O nome de domínio direto do aplicativo Web e a URL do Gerenciador de Tráfego estarão visíveis na página Configurar do aplicativo Web na seção **Nomes de Domínio**.
* Os nomes de domínio personalizados funcionam conforme esperado, mas, além de adicioná-los a seus aplicativos Web, você também deve configurar o mapa DNS para apontar para a URL do Gerenciador de Tráfego. Para obter informações sobre como configurar um domínio personalizado para um aplicativo Web do Azure, consulte [Mapear um nome de domínio personalizado para um site do Azure](app-service-web-tutorial-custom-domain.md).
* Você só pode adicionar aplicativos Web que estejam no modo padrão ou premium para um perfil do Gerenciador de Tráfego do Azure.

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral conceitual e técnica do Gerenciador de Tráfego do Azure, consulte [Visão geral do Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md).

Para saber mais sobre como usar o Gerenciador de Tráfego com aplicativos Web, veja as postagens de blog [Using Azure Traffic Manager with Azure Web Sites](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) (Usando o Gerenciador de Tráfego do Azure com sites do Azure) e [Azure Traffic Manager can now integrate with Azure Web Sites](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/) (O Gerenciador de Tráfego do Azure agora pode se integrar a sites do Azure).

