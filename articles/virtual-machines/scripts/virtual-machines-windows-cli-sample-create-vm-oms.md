---
title: Exemplo de script da CLI do Azure - Criar uma VM do Windows Server 2016 com monitoramento de OMS | Microsoft Docs
description: Exemplo de script da CLI do Azure - Criar uma VM do Windows Server 2016 com o monitoramento do OMS
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: a172d73732354d31d717d8e2f3a5c5c43cbbd6dc
ms.openlocfilehash: 3c479ed2450927a4e7c6cd07250873c556735b8b
ms.lasthandoff: 03/01/2017

---

# <a name="monitor-a-vm-with-operations-management-suite"></a>Monitorar uma VM com o Operations Management Suite

Esse script cria uma Máquina Virtual do Azure, instala o agente do OMS (Operations Management Suite) e registra o sistema com um espaço de trabalho do OMS. Depois que o script tiver sido executado, a máquina virtual ficará visível no console do OMS.

Antes de executar esse script, certifique-se de que uma conexão com o Azure foi criada usando o comando `az login`. Além disso, você deve alterar a variável $AdminPassword no início do script para atender aos requisitos exclusivos de complexidade da senha.

Este exemplo funciona em um Shell Bash. Para opções sobre como executar scripts da CLI do Azure no Windows, veja [Execução da CLI do Azure no Windows](../virtual-machines-windows-cli-options.md).

## <a name="create-vm-sample-with-operations-management-suite"></a>Criar um exemplo de VM com o Operations Management Suite

[!code-azurecli[principal](../../../cli_scripts/virtual-machine/create-vm-monitor-oms/create-windows-vm-monitor-oms.sh "Criação rápida de VM")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Após a execução do exemplo de script, o comando a seguir pode ser usado para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Cria a máquina virtual e a conecta a placa de rede, a rede virtual, a sub-rede e o NSG. Este comando também especifica a imagem de máquina virtual a ser usada e as credenciais administrativas.  |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | Executa uma extensão de VM em uma máquina virtual. Nesse caso, a extensão do agente Operations Management Suite é usada para instalar o agente do OMS e para registrar a VM em um espaço de trabalho do OMS. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script CLI máquinas virtuais adicionais podem ser encontrados na [documentação da VM do Windows do Azure](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

