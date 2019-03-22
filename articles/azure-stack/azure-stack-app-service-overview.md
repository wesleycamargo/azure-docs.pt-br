---
title: O serviço de aplicativo na visão geral do Azure Stack | Microsoft Docs
description: Visão geral do serviço de aplicativo no Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: ba2a322cdbcf929bef586f9f35ec2dc394f7af53
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778511"
---
# <a name="app-service-on-azure-stack-overview"></a>Serviço de Aplicativo na visão geral do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Serviço de aplicativo do Azure no Azure Stack é uma oferta de plataforma-como um serviço (PaaS) do Microsoft Azure está disponível para o Azure Stack. O serviço permite aos clientes internos ou externos criar a web, API e o Azure Functions aplicativos para qualquer plataforma ou dispositivo. Eles podem integrar seus aplicativos a aplicativos locais e automatizar os processos de negócios. Os operadores de nuvem do Azure Stack podem executar os aplicativos cliente em VMs (máquinas virtuais) totalmente gerenciadas, com sua escolha de recursos compartilhados de VM ou VMs dedicadas.

O serviço de aplicativo do Azure permite que você automatize processos de negócios e hospedar APIs de nuvem. Como um único serviço integrado, o serviço de aplicativo do Azure permite que você combine vários componentes, como sites, APIs REST e processos de negócios, em uma única solução.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Por que oferece o serviço de aplicativo do Azure no Azure Stack?

Veja alguns recursos importantes do Serviço de Aplicativo:

- **Várias linguagens e estruturas**: Serviço de aplicativo tem suporte de primeira classe para ASP.NET, Node. js, Java, PHP e Python. Você também pode executar o Windows PowerShell e outros scripts ou executáveis nas VMs do serviço de aplicativo.
- **Otimização de DevOps**: Configure a implantação e integração contínua com GitHub, Git local ou o BitBucket. Você pode promover as atualizações por meio de teste e ambientes de preparo e gerenciar seus aplicativos no serviço de aplicativo usando o Azure PowerShell ou a interface de linha de comando de plataforma cruzada (CLI).
- **Integração do Visual Studio**: Ferramentas dedicadas no Visual Studio simplificam o trabalho de criação e implantação de aplicativos.

## <a name="app-types-in-app-service"></a>Tipos de aplicativo no Serviço de Aplicativo

O serviço de aplicativo oferece vários tipos de aplicativo, cada um deles se destina a hospedar uma carga de trabalho específica:

- [Aplicativos Web](../app-service/overview.md) para hospedar sites e aplicativos web.
- [Aplicativos de API](../app-service/overview.md) para hospedar APIs REST.
- Funções do Azure para hospedar cargas de trabalho sem servidor controlada por eventos.

A palavra *aplicativo* refere-se aos recursos de hospedagem dedicados à execução de uma carga de trabalho. Levando *aplicativo web* como exemplo, você provavelmente já está acostumado a pensar de um aplicativo web, como o código de aplicativo e recursos de computação que juntos oferecem a funcionalidade a um navegador. No serviço de aplicativo, um aplicativo web é o recurso de computação do Azure Stack fornece para hospedar o código do aplicativo.

Seu aplicativo pode ser composto de vários aplicativos de serviço de aplicativo de tipos diferentes. Por exemplo, se seu aplicativo é composto de um front-end da web e um REST back-end API, você pode:

- Implantar ambos (front-end e API) em um único aplicativo Web
- Implantar seu código de front-end em um aplicativo Web e seu código de back-end em um aplicativo de API.

   [![Visão geral do serviço de aplicativo com os dados de monitoramento](media/azure-stack-app-service-overview/image01.png "visão geral do serviço de aplicativo com os dados de monitoramento")](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>O que é um plano do Serviço de Aplicativo?

O provedor de recursos do serviço de aplicativo usa o mesmo código que usa o serviço de aplicativo do Azure e, portanto, compartilha alguns conceitos comuns. No serviço de aplicativo, o contêiner de preços para aplicativos é chamado de *plano do serviço de aplicativo*. Representa o conjunto de máquinas virtuais dedicadas usado para manter seus aplicativos. Dentro de uma determinada assinatura, você pode ter vários planos de serviço de aplicativo.

No Azure, há trabalhadores dedicados e compartilhados. Um trabalhador compartilhado oferece suporte à hospedagem de aplicativo de multilocatário de alta densidade, e há apenas um conjunto de trabalhadores compartilhados. Servidores dedicados são usados por apenas um locatário e vêm em três tamanhos: pequeno, médio e grande. As necessidades dos clientes em locais sempre não podem ser descritas usando esses termos. No serviço de aplicativo no Azure Stack, os administradores do provedor de recursos podem definir as camadas de trabalhador que desejam tornar disponíveis. Com base em suas necessidades exclusivas de hospedagem, você pode definir vários conjuntos de trabalhadores compartilhados ou conjuntos diferentes de trabalhadores dedicados. Usando essas definições de camada de trabalho, eles podem, em seguida, definir sua própria SKUs de preços.

## <a name="portal-features"></a>Recursos do Portal

Serviço de aplicativo no Azure Stack usa a mesma interface de usuário que usa o serviço de aplicativo do Azure. O mesmo acontece com o back-end. No entanto, alguns recursos são desativados no Azure Stack. As expectativas específicas do Azure ou serviços que exigem esses recursos não estão disponíveis atualmente no Azure Stack.

## <a name="next-steps"></a>Próximas etapas

- [Antes de começar com o serviço de aplicativo no Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [Instalar o provedor de recursos do serviço de aplicativo](azure-stack-app-service-deploy.md)

Você também pode experimentar outras [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md), como o [provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md) e o [provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md).
