---
title: Recursos vinculados e relacionados na galeria de blocos
description: "Saiba mais sobre recursos relacionados e vinculados que são exibidos na galeria de blocos do portal de visualização do Azure."
services: azure-portal
documentationcenter: 
author: adamabdelhamed
manager: wpickett
editor: 
ms.assetid: dba96d29-f518-49c8-bfd2-f14cecb44cbf
ms.service: azure-portal
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2015
ms.author: adamab
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: efa7bce26c2c4c153b083e0e34d689a11d27dd16
ms.lasthandoff: 03/06/2017


---
# <a name="related-and-linked-resources-in-the-tile-gallery"></a>Recursos vinculados e relacionados na galeria de blocos
A galeria de blocos permite que você encontre blocos para um determinado recurso e arraste-os para sua folha atual. Usando a galeria de blocos, você pode criar exibições de gerenciamento que abrangem os recursos. Para qualquer recurso especificado, os recursos relacionados incluem todos os recursos do grupo de recursos e todos os recursos vinculados para ou por meio do recurso.

## <a name="linked-resources-in-resource-manager"></a>Recursos vinculados no Resource Manager
A vinculação é um recurso do Resource Manager.  Ela permite que você declare relações entre recursos mesmo se eles não residirem no mesmo grupo de recursos. A vinculação não tem impacto sobre o tempo de execução de seus recursos, sem afetar a cobrança e sem afetar o acesso baseado em função.  Ela é simplesmente um mecanismo que você pode usar para representar as relações para que as ferramentas, como a galeria de blocos, possam fornecer uma experiência de gerenciamento avançada.  As ferramentas podem inspecionar os links usando a API de links e fornecer experiências de gerenciamento de relacionamento personalizadas também. 

## <a name="how-do-i-link-my-resources"></a>Como vinculo meus recursos?
Quando você criar recursos por meio do portal ou implantando um modelo por meio do Azure PowerShell ou CLI do Azure, os links são criados automaticamente para alguns recursos dependentes. Também é possível vincular recursos de forma programática usando a [API REST de Recursos Vinculados](/rest/api/resources/resourcelinks).

## <a name="next-steps"></a>Próximas etapas
* Se precisar de uma introdução à criação de modelos do Resource Manager, consulte [Criando modelos](../azure-resource-manager/resource-group-authoring-templates.md).
* Para entender mais sobre como trabalhar com grupos de recursos por meio do portal, consulte [Usando o portal do Azure para gerenciar os recursos do Azure](../azure-resource-manager/resource-group-portal.md).


