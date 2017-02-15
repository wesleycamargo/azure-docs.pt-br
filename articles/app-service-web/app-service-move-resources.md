---
title: Mover os recursos do aplicativo Web para outro grupo de recursos
description: "Descreve os cenários em que você pode mover aplicativos Web e Serviços de Aplicativos de um grupo de recursos para outro."
services: app-service
documentationcenter: 
author: ZainRizvi
manager: wpickett
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2016
ms.author: zarizvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 08b8953b91a87a131f70309e41be3a9ce0123299


---
# <a name="supported-move-configurations"></a>Configurações de movimentação com suporte
Você pode mover os recursos de aplicativo Web do Azure usando a [API de recursos de movimentação ARM](../resource-group-move-resources.md).

Atualmente, os aplicativos Web do Azure dão suporte para os seguintes cenários de movimentação:

* Mover todo o conteúdo de um grupo de recursos (aplicativos Web, planos de serviços de aplicativos e certificados) para outro grupo de recursos 
  * Observação: o grupo de recursos de destino não pode conter nenhum recurso Microsoft.Web nesse cenário
* Mover aplicativos Web individuais para um grupo de recursos diferente, enquanto ainda os hospeda em seu plano de serviço de aplicativo atual (o plano de serviço de aplicativo permanece no grupo de recursos antigo)




<!--HONumber=Nov16_HO3-->


