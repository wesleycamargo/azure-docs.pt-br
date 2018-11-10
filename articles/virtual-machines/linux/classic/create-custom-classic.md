---
title: Criar uma VM clássica do Linux usando a CLI clássica do Azure | Microsoft Docs
description: Saiba como criar uma máquina virtual do Linux com a CLI Clássica do Azure usando o modelo de implantação Clássico
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: d8e469289f72fe892ea7c3da99972e6326c75eb9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242529"
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-classic-cli"></a>Como criar uma VM Clássica do Linux com a CLI Clássica do Azure
> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para a versão do Resource Manager, consulte [aqui](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Este tópico descreve como criar uma VM (máquina virtual) do Linux com a CLI Clássica do Azure usando o modelo de implantação Clássico. Vamos usar uma imagem do Linux das **IMAGENS** disponíveis no Azure. Os comandos da CLI Clássica do Azure oferecem as seguintes opções de configuração, entre outras:

* Conectar a VM a uma rede virtual
* Adicionar a VM a um serviço de nuvem existente
* Adicionar a VM a uma conta de armazenamento existente
* Adicionar a VM a um conjunto de disponibilidade ou local.

> [!IMPORTANT]
> Se você quiser que sua VM use uma rede virtual para que possa se conectar às suas máquinas virtuais diretamente pelo nome do host ou estabelecer conexões entre locais, especifique a rede virtual ao criar a VM. Uma máquina virtual poderá ser configurada para ingressar em uma rede virtual somente quando você criar a VM. Para mais detalhes sobre redes virtuais, consulte [Visão geral da rede virtual do Azure](https://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Como criar uma VM do Linux usando o modelo de implantação Clássico
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]

