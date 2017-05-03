---
title: "Redes Virtuais do Azure e Máquinas Virtuais do Linux | Microsoft Docs"
description: "Tutorial – Gerenciar redes virtuais do Azure e Máquinas Virtuais do Linux com a CLI do Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: c1494009c126c4715caf7972a0f59cffdaba3a7f
ms.lasthandoff: 04/22/2017

---

# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>Gerenciar redes virtuais do Azure e Máquinas Virtuais do Linux com a CLI do Azure

Neste tutorial, você aprenderá a criar várias VMs (máquinas virtuais) em uma rede virtual e configurar a conectividade de rede entre elas. Quando estiver concluída, uma VM "front-end" poderá ser acessada pela Internet na porta 22 para SSH e na porta 80 para conexões HTTP. Uma VM "back-end" com um Banco de Dados MySQL será isolada e somente poderá ser acessada da VM front-end na porta 3306.

As etapas deste tutorial podem ser concluídas usando o módulo mais recente da [CLI do Azure 2.0](/cli/azure/install-azure-cli).

## <a name="create-vm-and-vnet"></a>Criar VM e VNet

Uma Rede Virtual do Azure (VNet) é uma representação da sua própria rede na nuvem. Uma VNet é um isolamento lógico da nuvem do Azure dedicada à sua assinatura. Em uma VNet, você deve encontrar sub-redes, regras de conectividade para essas sub-redes e conexões das VMs para as sub-redes. A CLI do Azure facilita a tarefa de criar todos os recursos relacionados à rede de que você precisa para dar suporte ao acesso às VMs. 

Antes de criar os outros recursos do Azure, será necessário criar um grupo de recursos com az group create. O exemplo a seguir cria um grupo de recursos denominado `myRGNetwork` no local `westus`:

```azurecli
az group create --name myRGNetwork --location westus
```

Quando você cria uma máquina virtual usando a CLI do Azure, os recursos de rede necessários são criados automaticamente ao mesmo tempo. Crie `myFrontendVM` e seus recursos de rede de suporte com [az vm create](https://docs.microsoft.com/cli/azure/vm#create):

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Depois que a VM for criada, anote o endereço IP público. Esse endereço é usado nas etapas posteriores deste tutorial:

```bash
{
  "fqdns": "",
  "id": "/subscriptions/{id}/resourceGroups/myRGNetwork/providers/Microsoft.Compute/virtualMachines/myFrontendVM",
  "location": "westus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myRGNetwork"
}
```

Estes recursos de rede foram criados:

- **myFrontendVMNSG** – o Grupo de Segurança de Rede que protege o tráfego de entrada para `myFrontendVM`.
- **myVMPublicIP** – o endereço IP público que permite acesso à Internet para `myFrontendVM`.
- **myVMVMNic** – o adaptador de rede virtual que fornece conectividade de rede para `myFrontendVM`.
- **myVMVNET** – rede virtual à qual `myFrontendVM` está conectado.

## <a name="install-web-server"></a>Instalar servidor Web

Crie uma conexão SSH com `myFrontendVM`. Substitua o endereço IP de exemplo pelo endereço IP público da VM:

```bash
ssh 40.68.254.142
```

Execute os comandos a seguir para instalar o NGINX:

```bash
sudo apt-get -y update && sudo apt-get -y install nginx
```

Feche a sessão SSH:

```bash
exit
```

## <a name="manage-internet-traffic"></a>Gerenciar o tráfego de Internet

Um NSG (Grupo de Segurança de Rede) contém uma lista de regras de segurança que permitem ou negam o tráfego de rede para recursos conectados a uma VNet. Os NSGs podem ser associados a sub-redes ou NICs individuais anexadas às VMs. Abrir ou fechar o acesso às VMs por meio de portas é feito usando regras de NSG. Quando você criou o `myFrontendVM`, a porta 22 de entrada foi aberta automaticamente para conectividade SSH.

Abra a porta 80 em `myFrontendVM` com [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port):

```azurecli
az vm open-port --port 80 --resource-group myRGNetwork --name myFrontendVM
```

Agora você pode usar o endereço IP público para navegar até a VM para ver o site do NGINX.

![Site padrão NGINX](./media/quick-create-cli/nginx.png)

## <a name="manage-internal-traffic"></a>Gerenciar tráfego interno

A comunicação interna de VMs também pode ser configurada usando um NSG. Nesta seção, você aprenderá a criar uma sub-rede adicional na rede e atribuir um NSG a ela para permitir uma conexão de `myFrontendVM` para `myBackendVM` na porta 3306. A sub-rede é então atribuída à VM quando ela é criada.

Adicione um novo Grupo de Segurança de Rede chamado `myBackendNSG` com [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create). 

```azurecli
az network nsg create \
 --resource-group myRGNetwork \
 --name myBackendNSG
```

Configure uma porta para habilitar `myFrontendVM` e `myBackendVM` para se comunicarem um com o outro na rede virtual. Adicione uma regra NSG que permita tráfego para `myBackendSubnet` somente de `myVMSubnet` com `az network rule create`:

```azurecli
az network nsg rule create \
 --resource-group myRGNetwork \
 --nsg-name myBackendNSG \
 --name com-rule \
 --access Allow \
 --protocol Tcp \
 --direction Inbound \
 --priority 100 \
 --source-address-prefix 10.0.0.0/24 \
 --source-port-range "*" \
 --destination-address-prefix "*" \
 --destination-port-range 3306
```

## <a name="add-back-end-subnet"></a>Adicionar sub-rede back-end

Uma sub-rede é um recurso filho de uma VNet e ajuda a definir segmentos de espaços de endereços em um bloco CIDR, usando prefixos de endereços IP. As NICs podem ser adicionadas a sub-redes e conectadas a VMs, fornecendo conectividade para diversas cargas de trabalho.

Adicione `myBackEndSubnet` a `myFrontendVMVNet` com [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#create):

```azurecli
az network vnet subnet create \
 --address-prefix 10.0.1.0/24 \
 --name myBackendSubnet \
 --resource-group myRGNetwork \
 --vnet-name myFrontendVMVNET \
 --network-security-group myBackendNSG
```

## <a name="create-back-end-vm"></a>Criar VM back-end

Criar `myBackendVM` usando `myBackendSubnet` com `az vm create`:

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --subnet myBackendSubnet \
  --vnet-name myFrontendVMVNET \
  --public-ip-address ""

```

## <a name="install-database"></a>Instalar banco de dados

Para este tutorial, você deve copiar a chave privada da VM de desenvolvimento para `myFrontendVM`. Em um ambiente de produção, é recomendável criar chaves específicas para uso em VMs em vez de usar --generate-ssh-keys ao criar as VMs. 

A VM back-end destina-se a não ser acessada publicamente. Nesta seção, você aprenderá a usar o SSH para fazer logon em `myFrontendVM` e, em seguida, a usar o SSH para fazer logon na `myBackendVM` da `myFrontendVM`.

Substitua o endereço IP de exemplo pelo endereço IP público da `myFrontendVM`:

```bash
scp ~/.ssh/id_rsa 40.68.254.142:~/.ssh/id_rsa
```

Crie uma conexão SSH com `myFrontendVM`. Substitua o endereço IP de exemplo pelo endereço IP público da `myFrontendVM`:

```bash
ssh 40.68.254.142
```

Da `myFrontendVM`, conecte-se à `myBackendVM`:

```bash
ssh myBackendVM
```

Execute o comando a seguir para instalar o MySQL:

```bash
sudo apt-get -y install mysql-server
```

Siga as instruções para configurar o MySQL.

Feche as sessões SSH:

```bash
exit
```

O MySQL é instalado para mostrar como um aplicativo pode ser instalado em `myBackendVM`, ele não é realmente usado neste tutorial.
