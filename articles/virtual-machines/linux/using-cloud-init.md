---
title: Usar cloud-init para personalizar uma VM Linux | Microsoft Docs
description: "Como usar cloud-init para personalizar uma VM Linux durante a criação com a CLI 2.0 Preview do Azure"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/10/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 9864e4dfe3c0288e762f64a827e533ed9820ee64
ms.lasthandoff: 04/03/2017


---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation"></a>Usar cloud-init para personalizar uma VM Linux durante a criação
Este artigo mostra como criar um script cloud-init para definir o nome do host, atualizar pacotes instalados e gerenciar contas de usuários com a CLI 2.0 do Azure.  Os scripts cloud-init são chamados quando você cria uma VM usando a CLI do Azure.  Você também pode executar essas etapas com a [CLI do Azure 1.0](using-cloud-init-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-commands"></a>Comandos rápidos
Crie um script cloud-init.txt que define o nome do host, atualiza todos os pacotes e adiciona um usuário sudo ao Linux.

```sh
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```

Crie um grupo de recursos para iniciar VMs com [az group create](/cli/azure/group#create). O seguinte exemplo cria o grupo de recursos chamado `myResourceGroup`:

```azurecli
az group create --name myResourceGroup --location westus
```

Crie uma VM Linux com [az vm create](/cli/azure/vm#create) usando cloud-init para configurá-la durante a inicialização.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --custom-data cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado
### <a name="introduction"></a>Introdução
Quando você inicia uma nova VM do Linux, você obtém uma VM padrão do Linux sem nada personalizado nem pronta para suas necessidades. [inicialização de nuvem](https://cloudinit.readthedocs.org) é uma maneira padrão de inserir um script ou definições de configuração nessa VM do Linux como se ela estivesse sendo inicializada pela primeira vez.

No Azure, há três maneiras diferentes de fazer alterações em uma VM do Linux como se ela estivesse sendo implantada ou inicializada.

* Insira scripts usando a inicialização de nuvem.
* Insira scripts usando a [Extensão VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)do Azure.
* Um modelo do Azure usando a inicialização de nuvem.
* Um modelo do Azure usando [CustomScriptExtention](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para inserir scripts a qualquer momento após a inicialização:

* SSH para executar comandos diretamente
* Insira scripts usando a [Extensão VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)do Azure de forma obrigatória ou em um modelo do Azure
* Ferramentas de gerenciamento de configuração, como Ansible, Salt, Chef e Puppet.

> [!NOTE]
> : a Extensão VMAccess executa um script como raiz da mesma forma que com o uso do SSH.  No entanto, o uso a extensão de VM habilita vários recursos oferecidos pelo Azure que podem ser úteis, dependendo do cenário.
> 
> 

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Disponibilidade de inicialização de nuvem em aliases de imagem de criação rápida de uma VM do Azure:
| Alias | Editor | Oferta | SKU | Versão | inicialização de nuvem |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7,2 |mais recente |não |
| CoreOS |CoreOS |CoreOS |Estável |mais recente |sim |
| Debian |credativ |Debian |8 |mais recente |não |
| openSUSE |SUSE |openSUSE |13.2 |mais recente |não |
| RHEL |Redhat |RHEL |7,2 |mais recente |não |
| UbuntuLTS |Canônico |UbuntuServer |14.04.4-LTS |mais recente |sim |

A Microsoft está trabalhando com parceiros para incluir a inicialização de nuvem e trabalhar nas imagens que eles fornecem para o Azure.

## <a name="add-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Adicionar um script cloud-init à criação da VM com a CLI do Azure
Para iniciar um script de inicialização de nuvem ao criar uma VM no Azure, especifique o arquivo de inicialização de nuvem usando o comutador `--custom-data` da CLI do Azure.

Crie um grupo de recursos no qual iniciar as VMs.

Crie um grupo de recursos para iniciar VMs com [az group create](/cli/azure/group#create). O seguinte exemplo cria o grupo de recursos chamado `myResourceGroup`:

```azurecli
az group create --name myResourceGroup --location westus
```

Crie uma VM Linux com [az vm create](/cli/azure/vm#create) usando cloud-init para configurá-la durante a inicialização.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --custom-data cloud-init.txt
```

## <a name="create-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Criar um script cloud-init para definir o nome do host de uma VM Linux
Uma das configurações mais simples e mais importantes para qualquer VM do Linux seria o nome do host. Podemos defini-la com facilidade usando o cloud-init com este script.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Script de inicialização de nuvem de exemplo chamado `cloud_config_hostname.txt`.
```sh
#cloud-config
hostname: myservername
```

Durante a inicialização da VM, esse script de inicialização de nuvem define o nome do host como `myservername`. Crie uma VM Linux com [az vm create](/cli/azure/vm#create) usando cloud-init para configurá-la durante a inicialização.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --custom-data cloud-init.txt
```

Faça logon e verifique o nome do host das novas VMs.

```bash
ssh myVM
hostname
myservername
```

## <a name="create-a-cloud-init-script-to-update-linux"></a>Criar um script cloud-init para atualizar o Linux
Por segurança, é recomendável que a VM do Ubuntu seja atualizada na primeira inicialização.  Usando cloud-init, podemos fazer isso com o script a seguir, dependendo da distribuição do Linux que você esteja usando.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Script de inicialização de nuvem de exemplo `cloud_config_apt_upgrade.txt` para a família Debian
```sh
#cloud-config
apt_upgrade: true
```

Depois que o Linux for inicializado, todos os pacotes instalados serão atualizados por meio de `apt-get`. Crie uma VM Linux com [az vm create](/cli/azure/vm#create) usando cloud-init para configurá-la durante a inicialização.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --custom-data cloud_config_apt_upgrade.txt
```

Faça logon e verifique se todos os pacotes foram atualizados.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="create-a-cloud-init-script-to-add-a-user-to-linux"></a>Criar um script cloud-init para adicionar um usuário ao Linux
Uma das primeiras tarefas em qualquer nova VM do Linux é adicionar um usuário para você ou para evitar o uso de `root`. Chaves SSH são uma melhor prática de segurança e usabilidade e são adicionadas ao arquivo `~/.ssh/authorized_keys` com esse script de inicialização de nuvem.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Script de inicialização de nuvem de exemplo `cloud_config_add_users.txt` para a família Debian
```sh
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Depois que o Linux for inicializado, todos os usuários listados serão criados e adicionados ao grupo sudo. Crie uma VM Linux com [az vm create](/cli/azure/vm#create) usando cloud-init para configurá-la durante a inicialização.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --custom-data cloud_config_add_users.txt
```

Faça logon e verifique o usuário recém-criado.

```bash
ssh myVM
cat /etc/group
```

Saída

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Próximas etapas
A inicialização de nuvem está se tornando uma maneira padrão de modificar sua VM do Linux na inicialização. O Azure também tem extensões de VM, que permitem modificar a VM do Linux na inicialização ou durante sua execução. Por exemplo, você pode usar a VMAccessExtension do Azure para redefinir informações de SSH ou de usuário durante a execução da VM. Com a inicialização de nuvem, será necessária uma reinicialização para redefinir a senha.

[Sobre os recursos e extensões de máquina virtual](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Gerenciar usuários, SSH e verificar ou reparar discos em VMs do Linux do Azure usando a extensão VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


