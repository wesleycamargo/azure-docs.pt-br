---
title: Mover um aplicativo Web do Azure para outro grupo de recursos | Microsoft Docs
description: "Descreve os cenários em que você pode mover aplicativos Web e Serviços de Aplicativos de um grupo de recursos para outro."
services: app-service
documentationcenter: 
author: ZainRizvi
manager: erikre
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: zarizvi
ms.openlocfilehash: 4d5d6540dd0b51a4f172649a1fd9a2343efeceee
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2017
---
# <a name="move-an-azure-web-app-to-another-resource-group"></a>Mover um aplicativo Web do Azure para outro grupo de recursos
Você pode mover um aplicativo Web e/ou os recursos relacionados a ele a outro grupo de recursos na mesma assinatura ou para um grupo de recursos em uma assinatura diferente. Isso é feito como parte do gerenciamento de recursos padrão no Azure. Para obter mais informações, consulte, confira [Mover recursos do Azure para um novo grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md).

## <a name="limitations-when-moving-within-the-same-subscription"></a>Limitações ao mover dentro da mesma assinatura

Ao mover um aplicativo Web _dentro da mesma assinatura_, você não pode mover os certificados SSL carregados. No entanto, você pode mover um aplicativo Web para o novo grupo de recursos sem mover o respectivo certificado SSL carregado, mantendo a funcionalidade SSL do aplicativo. 

Se você deseja mover o certificado SSL com o aplicativo Web, siga estas etapas:

1.  Exclua o certificado carregado do aplicativo Web.
2.  Mova o aplicativo Web.
3.  Carregue o certificado no aplicativo Web movido.

## <a name="limitations-when-moving-across-subscriptions"></a>Limitações ao mover entre assinaturas

Ao mover um aplicativo Web _entre assinaturas_, as seguintes limitações se aplicam:

- O grupo de recursos de destino não pode ter nenhum recurso de Serviço de Aplicativo existente. Os recursos de Serviço de Aplicativo incluem:
    - Aplicativos Web
    - Planos do Serviço de Aplicativo
    - Certificados SSL importados ou carregados
    - Ambientes de Serviço de Aplicativo
- Todos os recursos de Serviço de Aplicativo no grupo de recursos devem ser movidos juntos.
- Recursos do Serviço de Aplicativo podem ser movidos apenas no grupo de recursos no qual eles foram originalmente criados. Se um recurso do Serviço de Aplicativo não estiver mais em seu grupo de recursos original, ele deverá ser movido de volta para esse grupo de recursos original primeiro e, em seguida, poderá ser movido entre assinaturas. 
