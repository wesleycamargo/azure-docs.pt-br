---
title: Exemplo de Script CLI do Azure - montar o disco do sistema operacional | Microsoft Docs
description: Exemplo de Script CLI do Azure - montar o disco do sistema operacional
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
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: cdd0a11e872d81dfbb35c7a0cbfa2e1f7234dc08
ms.lasthandoff: 03/21/2017

---

# <a name="troubleshoot-a-vms-operating-system-disk"></a>Solucionar problemas de um disco de sistema operacional de VMs

Esse script monta o disco do sistema operacional de uma máquina virtual com falha ou um problema como um disco de dados para uma segunda máquina virtual. Isso pode ser útil ao solucionar problemas de disco problemas ou recuperação de dados. 

Se necessário, instale a CLI do Azure usando a instrução encontrada no [guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e, em seguida, execute `az login` para criar uma conexão com o Azure. Além disso, você precisa de uma máquina virtual existente. Atualize o nome e o grupo de recursos da máquina virtual existente na amostra de script.

Este exemplo funciona em um shell Bash. Para opções sobre como executar scripts da CLI do Azure no cliente Windows, veja [Execução da CLI do Azure no Windows](../virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Criação rápida de VM")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm#show) | Retorne a lista de máquinas virtuais. Nesse caso, a opção de consulta é usada para retornar o disco de sistema operacional da máquina virtual. Este valor é adicionado a um nome de variável 'uri'. |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm#delete) | Exclui uma máquina virtual. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Cria uma máquina virtual.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#attach) | Anexa um disco a uma máquina virtual. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#list-ip-addresses) | Retorna os endereços IP de uma máquina virtual. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI de máquina virtual adicionais podem ser encontrados na [documentação da VM Linux do Azure](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

