<properties
   pageTitle="Como marcar uma máquina virtual do Linux | Microsoft Azure"
   description="Aprenda a marcar uma máquina virtual do Linux criada no Azure usando o modelo de implantação do Resource Manager."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="dkshir;memccror"/>

# Como marcar uma máquina virtual do Linux no Azure

Este artigo descreve as diferentes maneiras de marcar uma máquina virtual do Linux no Azure por meio do Azure Resource Manager. As marcas são pares de chave/valor definidos pelo usuário que podem ser colocados diretamente em um recurso ou grupo de recursos. Atualmente, o Azure oferece suporte a até 15 marcas por recurso e grupo de recursos. As marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente. Observe que as marcas tem suporte apenas a recursos criados por meio do Gerenciador de Recursos do Azure. Se quiser marcar uma máquina virtual do Windows, consulte [How to tag a Windows virtual machine in Azure (Como marcar uma máquina virtual Windows no Azure)](virtual-machines-windows-tag.md).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## Marcando com a CLI do Azure

A marcação também tem suporte de recursos que já foram criados por meio da CLI do Azure. Para começar, configure seu [ambiente da CLI do Azure][]. Faça logon na sua assinatura usando a CLI do Azure e alterne para o modo ARM. É possível exibir todas as propriedades de uma determinada Máquina Virtual, incluindo as marcas, usando este comando:

        azure vm show -g MyResourceGroup -n MyVM

Ao contrário do PowerShell, se você estiver adicionando marcas a um recurso que já contenha marcas, não será necessário especificar todas as marcas, antigas ou novas, antes de usar o comando `azure vm set`. Em vez disso, esse comando permite acrescentar uma marca ao recurso. Para adicionar uma nova marca de VM usando a CLI do Azure, use o comando `azure vm set` juntamente com o parâmetro de marca **-t**:

        azure vm set -g MyResourceGroup -n MyVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Para remover todas as marcas, use o parâmetro **–T** no comando `azure vm set`.

        azure vm set – g MyResourceGroup –n MyVM -T


Agora que aplicamos marcas aos nossos recursos por meio do PowerShell, da CLI do Azure e do Portal, vamos examinar os detalhes de uso para ver as marcas no portal de cobrança.




## Próximas etapas

* Para saber mais sobre como marcar seus recursos do Azure, veja [Visão geral do Azure Resource Manager][] e [Usando marcas para organizar os recursos do Azure][].
* Para ver como as marcas podem ajudá-lo a gerenciar seu uso de recursos do Azure, veja [Entenda sua fatura do Microsoft Azure][] e [Obtenha informações sobre o consumo de recursos do Microsoft Azure][].





[ambiente da CLI do Azure]: ./xplat-cli-azure-resource-manager.md
[Visão geral do Azure Resource Manager]: ../resource-group-overview.md
[Usando marcas para organizar os recursos do Azure]: ../resource-group-using-tags.md
[Entenda sua fatura do Microsoft Azure]: ../billing-understand-your-bill.md
[Obtenha informações sobre o consumo de recursos do Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_0330_2016-->