---
title: Fornecer configurações de pós-implantação por meio de extensões – Azure | Microsoft Docs
description: Saiba como usar extensões de modelo do Azure Resource Manager para fornecer configurações de pós-implantação.
services: azure-resource-manager
documentationcenter: na
author: mumian
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: eb46966c3a28b3fa4c2b23668109b7c5d23a609b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60390861"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Fornecer configurações de pós-implantação usando extensões

As extensões de modelo são pequenos aplicativos que fornecem tarefas de configuração e automação pós-implantação e tarefas de automação nos recursos do Azure. Mais popular são as extensões de máquina virtual. Consulte [Recursos e extensões de máquina Virtual para Windows](../virtual-machines/extensions/features-windows.md), e [recursos e extensões de máquina Virtual para Linux](../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Extensões

As extensões existentes são:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight clusters/extensions](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters/extensions)
- [Microsoft.Sql servers/databases/extensions](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Para descobrir as extensões disponíveis, navegue até a [referência de modelo](https://docs.microsoft.com/azure/templates/). Em **Filtrar por título**, insira **extensão**.

Para saber como usar essas extensões, consulte:

- [Tutorial: Implantar extensões de máquina virtual com modelos do Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).
- [Tutorial: Importar arquivos BACPAC do SQL com modelos do Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Tutorial: Implantar extensões de máquina virtual com modelos do Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md)
