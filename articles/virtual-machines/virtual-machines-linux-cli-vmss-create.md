---
title: Criar Conjuntos de Dimensionamento de VMs do Azure com a CLI do Azure | Microsoft Docs
description: Saiba mais sobre os conjuntos de escala de VM.
keywords: "máquina virtual linux, conjuntos de dimensionamento de máquina virtual"
services: virtual-machines-linux
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba1aedb6-49cb-4546-8b8b-da97aba8e42d
ms.service: virtual-machine-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/24/2016
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 5b8950619da3f42bc3c92443a7b9e9f2a97067f9
ms.openlocfilehash: 8d274127a4e1947e42b5544c3c1f62c4e48dcee9


---
# <a name="what-are-virtual-machine-scale-sets"></a>O que são conjuntos de escala de máquina virtual?
Conjuntos de Escala de Máquina Virtual permitem gerenciar diversas Máquinas Virtuais como um conjunto. Em um alto nível, conjuntos de escala têm os seguintes prós e contras:

Prós:

1. Alta disponibilidade. Cada conjunto de dimensionamento coloca suas VMs em um Conjunto de disponibilidade com 5 FDs (Domínios de Falha) e 5 UDs (Domínios de Atualização) para garantir a disponibilidade (para obter mais informações sobre UDs e de FDs, consulte [Disponibilidade da VM](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 
2. Fácil integração com o Balanceador de Carga do Azure e o Gateway de Aplicativo.
3. Fácil integração com Autoescala do Azure.
4. Simplifica implantação, gerenciamento e limpeza de VMs.
5. Suporte a tipos comuns do Windows e Linux, bem como imagens personalizadas.

Contras:

1. Não é possível anexar discos de dados para instâncias de VM em um conjunto de escala. Em vez disso, deve-se usar Armazenamento de Blobs, Arquivos do Azure, Tabelas do Azure ou outras soluções de armazenamento.

## <a name="quick-create-using-azure-cli"></a>Criação rápida usando a CLI do Azure
[!INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## <a name="next-steps"></a>Próximas etapas
Para obter informações gerais, confira a [página de aterrissagem principal para conjuntos de escala](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Para obter mais documentação, confira a [página de documentação principal para conjuntos de escala](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Para modelos do Resource Manager de exemplo usando conjuntos de escala, procure por "vmss" no [repositório github de Modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates).




<!--HONumber=Jan17_HO4-->


