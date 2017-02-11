---
title: "Implantando aplicativos no Serviço de Aplicativo do Azure"
description: "Saiba como implantar aplicativos para o trabalho do Serviço de Aplicativo"
keywords: "serviço de aplicativo do azure, serviço de aplicativo, implantando, implantação"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: wpickett
editor: 
ms.assetid: de12cd6e-e124-4e48-90bc-c3a3801305da
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2778057e772fcba7e6021ac11ec5bdcf3720a703


---
# <a name="azure-app-service-deployment-overview"></a>Visão geral da implantação do Serviço de Aplicativo do Azure
O Serviço de Aplicativo do Azure fornece um recurso avançado e integrado definido para dar suporte à criação de fluxos de trabalho de implantação flexível e poderosa. A implantação de aplicativos pode aproveitar opções que incluem publicação de controle do código-fonte local ou integração contínua, WebDeploy e FTP. O método recomendado para implantação do aplicativo de produção é a alternância do slot de implantação. Os slots de implantação representam ambientes de preparo e integração associados a aplicativos de produção. Os slots de implantação podem ser configurados e depender de tráfego da Web para validação, e o tráfego pode ser alternado sob demanda para implantação na produção sem a necessidade de tempo de inatividade e aquecimento automatizado. As etapas de um fluxo de trabalho de implantação podem ser automatizadas facilmente por meio de produtos de gerenciamento de versão, como o Visual Studio Release Management. Isso é útil para coordenar com outros recursos da solução (por exemplo, armazenamento de dados), recorrência e replicação em várias unidades de implantação. 

[!INCLUDE [app-service-blueprint-deployment](../../includes/app-service-blueprint-deployment.md)]




<!--HONumber=Nov16_HO3-->


