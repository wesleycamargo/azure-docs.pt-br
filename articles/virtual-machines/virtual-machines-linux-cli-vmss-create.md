<properties
	pageTitle="O que são conjuntos de escala de VM? | Microsoft Azure"
	description="Saiba mais sobre os conjuntos de escala de VM."
	keywords="máquina virtual linux, conjuntos de dimensionamento de máquina virtual" 
	services="virtual-machines-linux"
	documentationCenter=""
	authors="gatneil"
	manager="madhana"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machine-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2016"
	ms.author="gatneil"/>

# O que são conjuntos de escala de máquina virtual?

Conjuntos de Escala de Máquina Virtual permitem gerenciar diversas Máquinas Virtuais como um conjunto. Em um alto nível, conjuntos de escala têm os seguintes prós e contras:

Prós:

1. Alta disponibilidade. Cada conjunto de escala coloca suas VMs em um Conjunto de Disponibilidade com 5 FDs (Domínios de Falha) e 5 UDs (Domínios de Atualização) para garantir a disponibilidade (para obter mais informações sobre UDs e de FDs, consulte [disponibilidade da VM](./virtual-machines-linux-manage-availability.md)). 
2. Fácil integração com o Balanceador de Carga do Azure e o Gateway de Aplicativo.
3. Fácil integração com Autoescala do Azure.
4. Simplifica implantação, gerenciamento e limpeza de VMs.
5. Suporte a tipos comuns do Windows e Linux, bem como imagens personalizadas.

Contras:

1. Não é possível anexar discos de dados para instâncias de VM em um conjunto de escala. Em vez disso, deve-se usar Armazenamento de Blobs, Arquivos do Azure, Tabelas do Azure ou outras soluções de armazenamento.

## Criação rápida usando a CLI do Azure

[AZURE.INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## Próximas etapas

Para obter informações gerais, confira a [página de aterrissagem principal para conjuntos de escala](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Para obter mais documentação, confira a [página de documentação principal para conjuntos de escala](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Para modelos do Resource Manager de exemplo usando conjuntos de escala, procure por "vmss" no [repositório github de Modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates).

<!---HONumber=AcomDC_0413_2016-->