---
title: Como marcar uma máquina virtual do Linux | Microsoft Docs
description: Aprenda a marcar uma máquina virtual do Linux criada no Azure usando o modelo de implantação do Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror

---
# Como marcar uma máquina virtual do Linux no Azure
Este artigo descreve as diferentes maneiras de marcar uma máquina virtual do Linux no Azure por meio do modelo de implantação do Resource Manager. As marcas são pares de chave/valor definidos pelo usuário que podem ser colocados diretamente em um recurso ou grupo de recursos. Atualmente, o Azure oferece suporte a até 15 marcas por recurso e grupo de recursos. As marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente. Observe que as marcas tem suporte apenas para recursos criados por meio do modelo de implantação do Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## Marcando com a CLI do Azure
Para começar, [instale e configure a CLI do Azure](../xplat-cli-azure-resource-manager.md) e verifique se você está no modo do Resource Manager (`azure config mode arm`).

É possível exibir todas as propriedades de uma determinada Máquina Virtual, incluindo as marcas, usando este comando:

        azure vm show -g MyResourceGroup -n MyTestVM

Para adicionar uma nova marca de VM usando a CLI do Azure, use o comando `azure vm set` juntamente com o parâmetro de marca **-t**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Para remover todas as marcas, use o parâmetro **–T** no comando `azure vm set`.

        azure vm set – g MyResourceGroup –n MyTestVM -T


Agora que aplicamos marcas aos nossos recursos por meio do CLI do Azure e do Portal, vamos examinar os detalhes de uso para ver as marcas no portal de cobrança.

[!INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## Próximas etapas
* Para saber mais sobre como marcar seus recursos do Azure, consulte [Visão geral do Azure Resource Manager][Visão geral do Azure Resource Manager] e [Usando marcas para organizar os Recursos do Azure][Usando marcas para organizar os Recursos do Azure].
* Para ver como as marcas podem ajudá-lo a gerenciar seu uso de recursos do Azure, consulte [Noções básicas de sua fatura do Azure][Noções básicas de sua fatura do Azure] e [Obtenha informações sobre o consumo de recursos do Microsoft Azure][Obtenha informações sobre o consumo de recursos do Microsoft Azure].

[Azure CLI environment]: ./xplat-cli-azure-resource-manager.md
[Visão geral do Azure Resource Manager]: ../resource-group-overview.md
[Usando marcas para organizar os Recursos do Azure]: ../resource-group-using-tags.md
[Noções básicas de sua fatura do Azure]: ../billing-understand-your-bill.md
[Obtenha informações sobre o consumo de recursos do Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_0706_2016-->