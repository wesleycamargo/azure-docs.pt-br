---
title: "Início Rápido do Azure – Criar CLI de VM do Windows | Microsoft Docs"
description: "Aprenda rapidamente criar máquinas virtuais Windows com a CLI do Azure."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: b55478409d87ea1dd138c246ae05530df5e4ce2e
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-cli"></a>Criar uma máquina virtual do Windows com a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia detalha o uso da CLI do Azure para implantar uma máquina virtual executando o Windows Server 2016. Depois que a implantação for concluída, nos conectamos ao servidor e instalamos o IIS.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este tutorial requer a CLI do Azure, versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar atualizar, confira [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Criar máquina virtual

Crie uma VM com [az vm create](/cli/azure/vm#create). 

O exemplo a seguir cria uma VM chamada *myVM*. Este exemplo usa o *azureuser* para um nome de usuário administrativo e *myPassword12* como a senha. Atualize esses valores para algo apropriado para seu ambiente. Esses valores são necessários durante a criação de uma conexão com a máquina virtual.

```azurecli
az vm create `
  --resource-group myResourceGroup `
  --name myVM --image win2016datacenter `
  --admin-username azureuser `
  --admin-password myPassword12
```

Quando a VM tiver sido criada, a CLI do Azure mostra informações semelhantes ao exemplo a seguir. Anote `publicIpAaddress`. Esse endereço é usado para acessar a VM.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>Abra a porta 80 para tráfego da Web 

Por padrão, somente as conexões de RDP são permitidas em máquinas virtuais Windows implantadas no Azure. Se essa VM for se transformar em um servidor Web, você precisará abrir a porta 80 na Internet. Use o comando [az vm open-port](/cli/azure/vm#open-port) para abrir a porta desejada.  
 
 ```azurecli 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```


## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Use o seguinte comando para criar uma sessão de área de trabalho remota com a máquina virtual. Substitua o endereço IP pelo endereço IP público de sua máquina virtual. Quando solicitado, insira as credenciais usadas ao criar a máquina virtual.

```bash 
mstsc /v:<Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Instalar o IIS usando o PowerShell

Agora que você fez logon na VM do Azure, pode usar uma única linha do PowerShell para instalar o IIS e habilitar a regra de firewall local para permitir o tráfego da Web. Abra um promt do PowerShell e execute o seguinte comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Exibir a página de boas-vindas do IIS

Com o IIS instalado e a porta 80 que agora está aberta na sua VM da Internet, você pode usar um navegador da Web de sua escolha para exibir a página de boas-vindas do IIS padrão. Certifique-se de usar o endereço IP público que você documentou acima para visitar a página padrão. 

![Site do IIS padrão](./media/quick-create-powershell/default-iis-website.png) 
## <a name="delete-virtual-machine"></a>Excluir máquina virtual

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina virtual simples, uma regra de grupo de segurança de rede e instalou um servidor Web. Para saber mais sobre máquinas virtuais do Azure, continue o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Windows Azure](./tutorial-manage-vm.md)

