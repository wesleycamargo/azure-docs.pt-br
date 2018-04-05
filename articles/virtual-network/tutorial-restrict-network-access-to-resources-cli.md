---
title: Restringir o acesso à rede para recursos PaaS - CLI do Azure | Microsoft Docs
description: Saiba como limitar e restringir o acesso à rede para os recursos do Azure, como Armazenamento do Microsoft Azure e Banco de Dados SQL do Azure, com pontos de extremidade de serviço de rede virtual usando a CLI do Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 5c0c6a802c931b71f5be8b01c610cf0810b0b4d1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Restrinja o acesso à rede a recursos de PaaS com pontos de extremidade de serviço de rede virtual usando a CLI do Azure

Os pontos de extremidade de serviço de rede virtual permitem limitar o acesso à rede a alguns recursos de serviço do Azure para uma sub-rede da rede virtual. Você também pode remover o acesso à internet para os recursos. Os pontos de extremidade de serviço fornecem conexão direta de sua rede virtual para os serviços do Azure com suporte, permitindo que você use o espaço de endereço privado da sua rede virtual para acessar os serviços do Azure. O tráfego destinado aos recursos do Azure por meio de pontos de extremidade de serviço sempre ficam na rede de backbone do Microsoft Azure. Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar uma rede virtual com uma sub-rede
> * Adicionar uma sub-rede e habilitar um ponto de extremidade de serviço
> * Criar um recurso do Azure e permitir o acesso à rede para ele de apenas uma sub-rede
> * Implantar uma máquina virtual (VM) para cada sub-rede
> * Confirmar o acesso a um recurso a partir de uma sub-rede
> * A confirmação do acesso é negada para um recurso a partir de uma sub-rede e da internet

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este início rápido exigirá a execução da CLI do Azure versão 2.0.28 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Antes de criar uma rede virtual, será necessário criar um grupo de recursos para a rede virtual e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Crie uma rede virtual com uma sub-rede com [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Habilitar um ponto de extremidade de serviço 

Você pode habilitar pontos de extremidade de serviço apenas para serviços que oferecem suporte a pontos de extremidade de serviço. Exiba os serviços habilitados para ponto de extremidade de serviço em um local do Azure com [az network vnet list-endpoint-services](/cli/azure/network/vnet#az_network_vnet_list_endpoint_services). O exemplo a seguir retorna uma lista de serviços habilitados para ponto de extremidade de serviço disponíveis na região *eastus*. A lista de serviços retornados aumentará ao longo do tempo, visto que mais serviços do Azure se tornam habilitados para ponto de extremidade de serviço.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Crie uma sub-rede adicional na rede virtual com [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Neste exemplo, um ponto de extremidade de serviço para *Microsoft.Storage* é criado para a sub-rede: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-to-and-from-subnet"></a>Restringir o acesso à rede para a e da sub-rede

Crie um grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). O seguinte exemplo cria um grupo de segurança de rede chamado *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Associe o grupo de segurança de rede à sub-rede *Privada* com [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). O exemplo a seguir associa o grupo de segurança de rede *myNsgPrivate* à sub-rede *Privada*:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Crie regras de segurança com [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). A regra que segue permite o acesso de saída para os endereços IP públicos atribuídos ao serviço de Armazenamento do Microsoft Azure: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"

Each network security group contains several [default security rules](security-overview.md#default-security-rules). The rule that follows overrides a default security rule that allows outbound access to all public IP addresses. The `destination-address-prefix "Internet"` option denies outbound access to all public IP addresses. The previous rule overrides this rule, due to its higher priority, which allows access to the public IP addresses of Azure Storage.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"

The following rule allows SSH traffic inbound to the subnet from anywhere. The rule overrides a default security rule that denies all inbound traffic from the internet. SSH is allowed to the subnet so that connectivity can be tested in a later step.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>Restringir o acesso à rede a um recurso

As etapas necessárias para restringir o acesso à rede a recursos criados por meio de serviços do Azure habilitados para pontos de extremidade de serviço variam de acordo com os serviços. Consulte a documentação de serviços individuais para etapas específicas de cada serviço. O restante deste artigo inclui etapas para restringir o acesso à rede de uma conta de Armazenamento do Microsoft Azure como um exemplo.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de Armazenamento do Azure com [az storage account create](/cli/azure/storage/account#az_storage_account_create). Substitua `<replace-with-your-unique-storage-account-name>` por um nome que seja exclusivo em todos os locais do Azure, entre 3 a 24 caracteres de comprimento, usando apenas números e letras minúsculas.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Depois de criar a conta de armazenamento, recupere a cadeia de conexão para a conta de armazenamento em uma variável com [az storage account show-connection-string](/cli/azure/storage/account#az_storage_account_show_connection_string). A cadeia de conexão é usada para criar um compartilhamento de arquivos em uma etapa posterior.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Exiba o conteúdo da variável e observe o valor para **AccountKey** retornado na saída, porque ele é usado em uma etapa posterior.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Criar um compartilhamento de arquivos na conta de armazenamento

Crie um compartilhamento de arquivos na conta de armazenamento com [az storage share create](/cli/azure/storage/share#az_storage_share_create). Em uma etapa posterior, esse compartilhamento de arquivos é montado para confirmar o acesso à rede para ele.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Negar todo acesso à rede para uma conta de armazenamento

Por padrão, as contas de armazenamento aceitam conexões de rede de clientes em qualquer rede. Para limitar o acesso às redes selecionadas, altere a ação padrão para *Negar* com [az storage account update](/cli/azure/storage/account#az_storage_account_update). Depois que o acesso à rede for negado, a conta de armazenamento não estará acessível em nenhuma rede.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Habilitar o acesso à rede de uma sub-rede

Permita o acesso à rede para a conta de armazenamento a partir da sub-rede *Privada* com [az storage account network-rule add](/cli/azure/storage/account/network-rule#az_storage_account_network_rule_add).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Para testar o acesso à rede para uma conta de armazenamento, implante uma VM para cada sub-rede.

### <a name="create-the-first-virtual-machine"></a>Criar a primeira máquina virtual

Crie uma VM na sub-rede *Pública* com [az vm create](/cli/azure/vm#az_vm_create). Se as chaves SSH ainda não existirem em uma localização de chave padrão, o comando criará. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

A VM demora alguns minutos para criar. Quando a VM estiver criada, a CLI do Azure mostra informações semelhantes ao exemplo a seguir: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Anote o **publicIpAddress** na saída retornada. Esse endereço será usado para acessar a VM da internet em uma etapa posterior.

### <a name="create-the-second-virtual-machine"></a>Criar uma segunda máquina virtual

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

A VM demora alguns minutos para criar. Após a criação, anote o **publicIpAddress** na saída retornada. Esse endereço será usado para acessar a VM da internet em uma etapa posterior.

## <a name="confirm-access-to-storage-account"></a>Confirmar acesso à conta de armazenamento

SSH para a VM *myVmPrivate*. Substitua *<publicIpAddress>* pelo endereço IP público de sua VM *myVmPrivate*.

```bash 
ssh <publicIpAddress>
```

Crie uma pasta para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Monte o compartilhamento de arquivos do Azure para o diretório que você criou. Antes de executar o comando a seguir, substitua `<storage-account-name>` pelo nome da conta e `<storage-account-key>` com a chave que você recuperou em [Criar uma conta de armazenamento](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Você receberá o aviso `user@myVmPrivate:~$`. O compartilhamento de arquivos do Azure montado com êxito para */mnt/Retention/ MyAzureFileShare*.

Confirme se a VM não tem nenhuma conectividade de saída com nenhum outro endereço IP público:

```bash
ping bing.com -c 4
```

Você não recebe nenhuma resposta porque o grupo de segurança de rede associado à sub-rede *Privada* não permite acesso de saída para endereços IP públicos que não sejam os endereços atribuídos ao serviço de Armazenamento do Microsoft Azure.

Saia da sessão SSH para a VM *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Confirmar que o acesso está negado para a conta de armazenamento

Use o comando a seguir para criar uma sessão SSH com a VM *myVmPublic*. Substitua `<publicIpAddress>` pelo endereço IP público de sua VM *myVmPublic*: 

```bash 
ssh <publicIpAddress>
```

Crie um diretório para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Tente montar o compartilhamento de arquivos do Azure para o diretório que você criou. Este tutorial presume que você implantou a versão mais recente do Ubuntu. Se você estiver usando versões anteriores do Ubuntu, consulte [Montar no Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter instruções adicionais sobre como montar compartilhamentos de arquivos. Antes de executar o comando a seguir, substitua `<storage-account-name>` pelo nome da conta e `<storage-account-key>` com a chave que você recuperou em [Criar uma conta de armazenamento](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Acesso negado, e você recebe um erro `mount error(13): Permission denied`, porque a VM *myVmPublic* é implantada na sub-rede *Pública*. A sub-rede *Pública* não tem um ponto de extremidade de serviço habilitado para Armazenamento do Microsoft Azure, e a conta de armazenamento só permite o acesso à rede a partir da sub-rede *Privada*, não da sub-rede *Pública*.

Saia da sessão SSH para a VM *myVmPublic*.

Em seu computador, tente exibir os compartilhamentos em sua conta de armazenamento com [az storage share list](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Substitua `<account-name>` e `<account-key>` pelo nome da conta de armazenamento e a chave de [Criar uma conta de armazenamento](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Acesso negado, e você recebe um erro *Esta solicitação não está autorizada a executar esta operação*, porque o computador não está na sub-rede *Privada* da rede virtual *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, use [az group delete](/cli/azure#az_group_delete) para remover o grupo de recursos e todos os recursos que ele contém.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou um ponto de extremidade de serviço para uma sub-rede de rede virtual. Você aprendeu que os pontos de extremidade de serviço podem ser habilitados para os recursos implantados com vários serviços do Azure. Você criou uma conta de Armazenamento do Microsoft Azure e limitou o acesso à rede para a conta de armazenamento, restringindo apenas aos recursos em uma sub-rede de rede virtual. Antes de criar pontos de extremidade de serviço em redes virtuais de produção, é recomendável que você se familiarize completamente com os [pontos de extremidade de serviço](virtual-network-service-endpoints-overview.md).

Se tiver várias redes virtuais em sua conta, você poderá conectar duas redes virtuais em conjunto para que os recursos em cada rede virtual possam se comunicar uns com os outros. Avance para o próximo tutorial para saber como conectar redes virtuais.

> [!div class="nextstepaction"]
> [Conectar redes virtuais](./tutorial-connect-virtual-networks-cli.md)
