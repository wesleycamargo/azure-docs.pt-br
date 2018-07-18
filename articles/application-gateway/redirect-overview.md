---
title: Visão geral de redirecionamento do Gateway de Aplicativo do Azure
description: Saiba mais sobre a capacidade de redirecionamento do Gateway de Aplicativo do Azure
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/19/2018
ms.author: amsriva
ms.openlocfilehash: 65c631ca9beb5eab5d8fe2b7e71daa0cf3b768fa
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204370"
---
# <a name="application-gateway-redirect-overview"></a>Visão geral do redirecionamento do Gateway de Aplicativo

É um cenário comum para muitos aplicativos da Web dar suporte a redirecionamento automático de HTTP para HTTPS para garantir que todas as comunicações entre o aplicativo e seus usuários ocorram em um caminho criptografado. No passado, os clientes usaram técnicas como a criação de um pool de back-end dedicado cujo único propósito é redirecionar as solicitações recebidas de HTTP para HTTPS.

O Gateway de Aplicativo agora dá suporte à capacidade de redirecionar tráfego no Gateway. Isso simplifica a configuração do aplicativo, otimiza o uso de recursos e dá suporte a novos cenários de redirecionamento, incluindo redirecionamento global e baseado no caminho. O suporte a redirecionamento do Gateway de Aplicativo não está limitado apenas a redirecionamento HTTP -> HTTPS. Esse é um mecanismo de redirecionamento genérico, que permite o redirecionamento do tráfego recebido em um ouvinte para outro ouvinte no Gateway de Aplicativo. Também há suporte para o redirecionamento do site externo.

O suporte a redirecionamento do Gateway de Aplicativo oferece os seguintes recursos:

-  **Redirecionamento Global**

   Redirecionamento global de um ouvinte para outro ouvinte no gateway. Isso habilita o redirecionamento de HTTP para HTTPS em um site.
- **Redirecionamento baseado em caminho**

   Esse tipo de redirecionamento permite o redirecionamento de HTTP para HTTPS apenas em uma área específica do site, por exemplo, uma área de carrinho de compras é denotada por /cart/*.
- **Redirecionamento para um site externo**

![redirecionamento](./media/redirect-overview/redirect.png)

Com essa alteração, os clientes precisam criar um novo objeto de configuração de redirecionamento, que especifica o ouvinte de destino ou o site externo para o qual o redirecionamento é desejado. O elemento de configuração também dá suporte a opções para habilitar o acréscimo da cadeia de consulta e do caminho de URI à URL redirecionada. Você também podem escolher se o redirecionamento é um redirecionamento temporário (código de status HTTP 302) ou permanente (código de status HTTP 301). Depois de criada, essa configuração de redirecionamento é anexada ao ouvinte de origem por meio de uma nova regra. Ao usar uma regra básica, a configuração de redirecionamento é associada a um ouvinte de origem e é um redirecionamento global. Quando uma regra de caminho é usada, a configuração de redirecionamento é definida no mapa de caminho de URL. Então ela se aplica somente a área de caminho específico de um site.

### <a name="next-steps"></a>Próximas etapas

[Como configurar o redirecionamento de URL em um gateway de aplicativo](tutorial-url-redirect-powershell.md)
