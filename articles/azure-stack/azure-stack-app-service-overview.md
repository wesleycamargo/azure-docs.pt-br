---
title: "Visão geral do serviço de aplicativo: pilha do Azure | Microsoft Docs"
description: "Visão geral do serviço de aplicativo na pilha do Azure"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: anwestg
ms.openlocfilehash: fd2d355b2556faddb06acf2998b54ffcc9aa7919
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="app-service-on-azure-stack-overview"></a>Serviço de Aplicativo na visão geral do Azure Stack

Serviço de aplicativo do Azure na pilha do Azure é uma oferta de plataforma como um-serviço (PaaS) do Microsoft Azure disponíveis para a pilha do Azure. O serviço permite que seus clientes - internos ou externos - criar web API e funções do Azure aplicativos para qualquer plataforma ou dispositivo. Eles podem integrar seus aplicativos com aplicativos locais e automatizar seus processos de negócios. Operadores de nuvem do Azure pilha podem executar aplicativos de cliente em totalmente gerenciadas máquinas de virtuais (VMs), com sua opção de VMs dedicadas ou recursos compartilhados de VM.

Serviço de aplicativo do Azure inclui recursos para automatizar processos de negócios e hospedagem de APIs de nuvem. Como um único serviço integrado, o serviço de aplicativo do Azure permite compor vários componentes - sites da Web, APIs RESTful e processos de negócios – em uma única solução.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Por que oferece o serviço de aplicativo do Azure na pilha do Azure?

Veja alguns recursos importantes do Serviço de Aplicativo:
- **Vários idiomas e estruturas**: serviço de aplicativo tem suporte de primeira classe para ASP.NET, Node.js, Java, PHP e Python. Você também pode executar o Windows PowerShell e outros scripts ou executáveis em VMs do serviço de aplicativo.
- **Otimização de DevOps**: configurar a integração contínua e implantação com GitHub, local Git ou BitBucket. Promova as atualizações por meio de teste e ambientes de preparo. Gerencie seus aplicativos no serviço de aplicativo usando o Azure PowerShell ou a interface de linha de comando de plataforma cruzada (CLI).
- **Integração do Visual Studio**: dedicadas ferramentas no Visual Studio simplificar o trabalho de criação e implantação de aplicativos.

## <a name="app-types-in-app-service"></a>Tipos de aplicativo no Serviço de Aplicativo

Serviço de aplicativo oferece vários tipos de aplicativo, cada um deles se destina a hospedar uma carga de trabalho específica:

- [Aplicativos Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) para hospedar sites e aplicativos web.
- [Aplicativos de API](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform) para hospedagem de APIs RESTful.
- Funções do Azure para hospedar as cargas de trabalho sem servidor acionados por eventos.

O aplicativo word aqui refere-se aos recursos de hospedagem dedicados à execução de uma carga de trabalho. Usando "aplicativo Web" como exemplo, você provavelmente já está acostumado a pensar em um aplicativo Web como os recursos de computação e o código do aplicativo que, juntos, fornecem funcionalidade a um navegador. Mas, no serviço de aplicativo, um aplicativo web é os recursos de computação pilha do Azure fornece para hospedar o código do aplicativo.

Seu aplicativo pode ser composto de vários aplicativos de serviço de aplicativo de tipos diferentes. Por exemplo, se seu aplicativo é composto de um front-end da web e uma API RESTful volta terminar, você pode:
- Implantar ambos (front-end e API) em um único aplicativo Web
- Implantar seu código de front-end em um aplicativo Web e seu código de back-end em um aplicativo de API.

   ![](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>O que é um plano do Serviço de Aplicativo?

O provedor de recursos do serviço de aplicativo usa o mesmo código que usa o serviço de aplicativo do Azure. Como resultado, alguns conceitos comuns valem descrevendo. No serviço de aplicativo, o contêiner de preços para aplicativos é chamado o plano de serviço de aplicativo. Representa o conjunto de máquinas virtuais dedicadas usadas para manter seus aplicativos. Dentro de uma determinada assinatura, você pode ter vários planos de serviço de aplicativo.

No Azure, há trabalhadores dedicados e compartilhados. Um trabalho compartilhado oferece suporte à hospedagem de aplicativo multilocatário de alta densidade, e há apenas um conjunto de trabalhadores compartilhados. Servidores dedicados são usados por apenas um locatário e vêm em três tamanhos: pequeno, médio e grande. As necessidades dos clientes no local sempre não podem ser descritas usando esses termos. No serviço de aplicativo na pilha do Azure, os administradores de provedor de recursos podem definir as camadas de trabalhador que desejam tornar disponíveis. Com base em suas necessidades exclusivas de hospedagem, você pode definir vários conjuntos de trabalhadores compartilhados ou conjuntos diferentes de trabalhadores dedicados. Usando as definições da camada de trabalho, eles podem definir seus próprios preços SKUs.

## <a name="portal-features"></a>Recursos do Portal

Serviço de aplicativo na pilha do Azure usa a mesma interface do usuário que usa o serviço de aplicativo do Azure, como acontece com o back-end. Alguns recursos são desabilitados e não são funcionais na pilha do Azure. As expectativas específicas do Azure ou serviços que exigem a esses recursos ainda não disponíveis na pilha do Azure.

## <a name="next-steps"></a>Próximas etapas


- [Antes de iniciar o serviço de aplicativo na pilha do Azure](azure-stack-app-service-before-you-get-started.md)
- [Instalar o provedor de recursos do serviço de aplicativo](azure-stack-app-service-deploy.md)

Você também pode experimentar outros [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md), como o [provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md) e [provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md).
