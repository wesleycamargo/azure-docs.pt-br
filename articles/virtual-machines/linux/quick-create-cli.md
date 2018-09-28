---
title: Guia de início rápido - Criar uma VM Linux com a CLI do Azure | Microsoft Docs
description: Neste início rápido, você aprende a usar a CLI do Azure para criar uma máquina virtual Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 87a36e027515319c4bdfeaa559f55fd6e5a1c75b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958519"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>Guia de início rápido: crie uma máquina virtual do Linux com a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este início rápido mostra como usar a CLI do Azure para implantar uma máquina virtual (VM) do Linux no Azure que executa o Ubuntu. Para ver a VM em ação, você habilita o SSH na VM e instala o servidor Web do NGINX.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este início rápido exigirá a execução da CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Criar máquina virtual

Crie uma VM com o comando [az vm create](/cli/azure/vm#az_vm_create).

O exemplo a seguir cria uma VM denominada *myVM*, adiciona uma conta de usuário chamada *azureuser* e gera chaves SSH, se elas ainda não existirem em um local de chave padrão (*~/.ssh*). Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A criação da VM e dos recursos de suporte demora alguns minutos. O seguinte exemplo de saída mostra que a operação de criação de VM foi bem-sucedida.

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Observe a sua própria `publicIpAddress` na saída da sua VM. Este endereço é usado para acessar a VM na próxima etapa.

## <a name="open-port-80-for-web-traffic"></a>Abra a porta 80 para tráfego da Web

Por padrão, somente conexões de SSH são abertas quando você criar uma VM do Linux no Azure. Use [az vm open-port](/cli/azure/vm#az_vm_open_port) para abrir a porta TCP 80 para uso com o servidor web NGINX:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

SSH para sua VM, como de costume. Substitua **publicIpAddress** pelo endereço IP público da VM como observado na saída anterior da sua VM:

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Instalar servidor Web

Para ver a VM em ação, instale o servidor Web do NGINX. Para atualizar fontes de pacote e instalar o último pacote do NGINX, execute os seguintes comandos na sessão do SSH:

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Quando terminar, `exit` a sessão do SSH.

## <a name="view-the-web-server-in-action"></a>Ver o servidor web em ação

Com o NGINX instalado e a porta 80 agora aberta na VM pela Internet, use um navegador da Web de sua escolha para exibir a página inicial padrão do NGINX. Use o endereço IP público da VM obtido em uma etapa anterior. O seguinte exemplo mostra o site padrão do NGINX:

![Site padrão NGINX](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos, a VM e todos os recursos relacionados. Tenha certeza que você saiu da sessão SSH para sua VM e então exclua os recursos da seguinte maneira:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina virtual simples, abriu uma porta de rede para o tráfego da Web e instalou um servidor Web básico. Para saber mais sobre máquinas virtuais do Azure, continue o tutorial para VMs do Linux.


> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Linux Azure](./tutorial-manage-vm.md)
