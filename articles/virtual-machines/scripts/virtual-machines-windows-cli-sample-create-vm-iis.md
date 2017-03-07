---
title: Exemplo de Script da CLI do Azure - Instalar o IIS | Microsoft Docs
description: Exemplo de Script da CLI do Azure - Instalar o IIS
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/28/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: a172d73732354d31d717d8e2f3a5c5c43cbbd6dc
ms.openlocfilehash: 1c0d2de77acc4696cb0e274e14591a2925454dd6
ms.lasthandoff: 03/01/2017

---

# <a name="quick-create-a-virtual-machine-with-the-azure-cli"></a>Criação rápida de uma máquina virtual com a CLI do Azure

Este script cria uma Máquina Virtual Azure com o Windows Server 2016 e, em seguida, usa a Extensão de Script Personalizado de Máquina Virtual do Azure para instalar o IIS. Após a execução do script, o site padrão do IIS pode ser contatado no endereço IP público da máquina virtual.

Antes de executar esse script, certifique-se de que uma conexão com o Azure foi criada usando o comando `az login`. Além disso, você deve alterar a variável $AdminPassword no início do script para atender aos requisitos exclusivos de complexidade da senha.

Este exemplo funciona em um Shell Bash. Para opções sobre como executar scripts da CLI do Azure no Windows, veja [Execução da CLI do Azure no Windows](../virtual-machines-windows-cli-options.md).

## <a name="create-vm-sample"></a>Criar exemplo de VM

```azurecli
#!/bin/bash

# Update for your admin password
AdminPassword=ChangeYourAdminPassword1

# Create a resource group.
az group create --name myResourceGroup --location westeurope

# Create a virtual machine. 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password $AdminPassword

# Open port 80 to allow web traffic to host.
az vm open-port --port 80 --resource-group myResourceGroup --name myVM 

# Use CustomScript extension to install Apache.
az vm extension set \
  --publisher Microsoft.Compute \
  --version 1.8 \
  --name CustomScriptExtension \
  --vm-name myVM \
  --resource-group myResourceGroup \
  --settings '{"commandToExecute":"powershell.exe Install-WindowsFeature -Name Web-Server"}'
```

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
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Cria a máquina virtual e a conecta à placa de rede, a rede virtual, à sub-rede e ao grupo de segurança de rede. Este comando também especifica a imagem da máquina virtual a ser usada e as credenciais administrativas.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) | Cria uma regra de grupo de segurança de rede para permitir o tráfego de entrada. Neste exemplo, a porta 80 está aberta para tráfego HTTP. |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | Adiciona e executa uma extensão da máquina virtual a uma VM. Neste exemplo, a extensão de script personalizado é usada para instalar o IIS.|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script CLI máquinas virtuais adicionais podem ser encontrados na [documentação da VM do Windows do Azure](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

