---
title: Exemplos de CLI do Azure | Microsoft Docs
description: Exemplos de CLI do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 5a73094c90b2a7400a664a827e07d5ada2184952
ms.openlocfilehash: 372f6413903fbf7344871a136ffcddcadfc81782
ms.lasthandoff: 02/27/2017


---
# <a name="azure-virtual-machine-cli-samples"></a>Exemplos de CLI da Máquina Virtual do Azure

A tabela a seguir inclui links para bash scripts criados usando a CLI do Azure.

| | |
|---|---|
|**Criar máquinas virtuais**||
| [Criar uma máquina virtual](./scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual Linux com configuração mínima. |
| [Criar uma máquina virtual totalmente configurada](./scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um grupo de recursos, a máquina virtual e todos os recursos relacionados.|
| [Criar máquinas virtuais altamente disponíveis](./scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria várias máquinas virtuais em uma configuração altamente disponíveis e de balanceamento de carga. |
| [Criar uma máquina virtual com o Docker habilitado](./scripts/virtual-machines-linux-cli-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual, configura essa VM como um host Docker e executa um contêiner NGINX. |
| [Criar uma máquina virtual e executar o script de configuração](./scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e usa a extensão de Script do Azure personalizado para instalar o NGINX. |
| [Criar uma máquina virtual com WordPress instalado](./scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e usa a extensão de Script do Azure personalizado para instalar o WordPress. |
|**Máquinas virtuais de rede**||
| [Proteger o tráfego de rede entre as máquinas virtuais](./scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria duas máquinas virtuais, todos os recursos relacionados e grupos de segurança de rede (NSG) internos e externos. |
|**Monitorar máquinas virtuais**||
| [Monitorar uma VM com o Operations Management Suite](./scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual, instala o agente do Operations Management Suite e registra a VM em um espaço de trabalho do OMS.  |
|**Solucionar problemas de máquinas virtuais**||
| [Solucionar problemas de um disco de sistema operacional de VMs](./scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Monta o disco do sistema operacional de uma máquina virtual como um disco de dados em uma segunda máquina virtual. |
| | |

