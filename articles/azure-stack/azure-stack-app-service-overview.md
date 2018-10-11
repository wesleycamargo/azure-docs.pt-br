---
title: 'Visão geral do serviço de aplicativo: o Azure Stack | Microsoft Docs'
description: Visão geral do serviço de aplicativo no Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: cf2d65e7e2927aee99e533ea0bca0818f3ab98f6
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079150"
---
# <a name="app-service-on-azure-stack-overview"></a>Serviço de Aplicativo na visão geral do Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Serviço de aplicativo do Azure no Azure Stack é uma oferta de plataforma-como um serviço (PaaS) do Microsoft Azure está disponível para o Azure Stack. O serviço permite que seus clientes - internos ou externos - criar a web, API e o Azure Functions aplicativos para qualquer plataforma ou dispositivo. Eles podem integrar seus aplicativos com aplicativos locais e automatizar seus processos de negócios. Operadores de nuvem do Azure Stack podem executar aplicativos de cliente em totalmente gerenciadas VMs (máquinas virtuais), com sua escolha de recursos compartilhados de VM ou VMs dedicadas.

O serviço de aplicativo do Azure permite que você automatize processos empresariais e hospedagem de APIs de nuvem. Como um único serviço integrado, o serviço de aplicativo do Azure permite que você combine vários componentes – sites, APIs RESTful e processos de negócios – em uma única solução.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Por que oferece o serviço de aplicativo do Azure no Azure Stack?

Veja alguns recursos importantes do Serviço de Aplicativo:

- **Várias linguagens e estruturas**: serviço de aplicativo tem suporte de primeira classe para ASP.NET, Node. js, Java, PHP e Python. Você também pode executar o Windows PowerShell e outros scripts ou executáveis nas VMs do serviço de aplicativo.
- **Otimização de DevOps**: configurar a implantação e integração contínua com GitHub, Git local ou o BitBucket. Promova atualizações por meio de teste e ambientes de preparo. Gerencie seus aplicativos no serviço de aplicativo usando o Azure PowerShell ou a interface de linha de comando de plataforma cruzada (CLI).
- **Integração do Visual Studio**: ferramentas dedicadas no Visual Studio simplificam o trabalho de criação e implantação de aplicativos.

## <a name="app-types-in-app-service"></a>Tipos de aplicativo no Serviço de Aplicativo

O serviço de aplicativo oferece vários tipos de aplicativo, cada um deles se destina a hospedar uma carga de trabalho específica:

- [Aplicativos Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) para hospedar sites e aplicativos web.
- [Aplicativos de API](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform) para hospedar APIs RESTful.
- Funções do Azure para hospedar cargas de trabalho sem servidor controlada por eventos.

O aplicativo do word refere-se aos recursos de hospedagem dedicados à execução de uma carga de trabalho. Usando "aplicativo Web" como exemplo, você provavelmente já está acostumado a pensar em um aplicativo Web como os recursos de computação e o código do aplicativo que, juntos, fornecem funcionalidade a um navegador. Mas, no serviço de aplicativo um aplicativo web é os recursos de computação do Azure Stack fornece para hospedar o código do aplicativo.

Seu aplicativo pode ser composto de vários aplicativos de serviço de aplicativo de tipos diferentes. Por exemplo, se seu aplicativo é composto de um front-end da web e o término de volta uma API RESTful, você pode:

- Implantar ambos (front-end e API) em um único aplicativo Web
- Implantar seu código de front-end em um aplicativo Web e seu código de back-end em um aplicativo de API.

   ![Visão geral do serviço de aplicativo com os dados de monitoramento](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>O que é um plano do Serviço de Aplicativo?

O provedor de recursos do serviço de aplicativo usa o mesmo código que usa o serviço de aplicativo do Azure. Como resultado, alguns conceitos comuns valem descrevendo. No serviço de aplicativo, o contêiner de preços para aplicativos é chamado o plano do serviço de aplicativo. Representa o conjunto de máquinas virtuais dedicadas usado para manter seus aplicativos. Dentro de uma determinada assinatura, você pode ter vários planos de serviço de aplicativo.

No Azure, há trabalhadores dedicados e compartilhados. Um trabalhador compartilhado oferece suporte à hospedagem de aplicativo de multilocatário de alta densidade, e há apenas um conjunto de trabalhadores compartilhados. Servidores dedicados são usados por apenas um locatário e vêm em três tamanhos: pequeno, médio e grande. As necessidades dos clientes em locais sempre não podem ser descritas usando esses termos. No serviço de aplicativo no Azure Stack, os administradores do provedor de recursos podem definir as camadas de trabalhador que desejam tornar disponíveis. Com base em suas necessidades exclusivas de hospedagem, você pode definir vários conjuntos de trabalhadores compartilhados ou conjuntos diferentes de trabalhadores dedicados. Usando essas definições de camada de trabalho, eles podem, em seguida, definir sua própria SKUs de preços.

## <a name="portal-features"></a>Recursos do Portal

Serviço de aplicativo no Azure Stack usa a mesma interface do usuário que usa o serviço de aplicativo do Azure, o mesmo acontece com o back-end. No entanto, alguns recursos são desabilitados e não são funcionais no Azure Stack. As expectativas específicas do Azure ou serviços que exigem esses recursos não estão disponíveis atualmente no Azure Stack.

## <a name="next-steps"></a>Próximas etapas

- [Antes de começar com o serviço de aplicativo no Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [Instalar o provedor de recursos do serviço de aplicativo](azure-stack-app-service-deploy.md)

Você também pode experimentar outras [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md), como o [provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md) e o [provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md).
