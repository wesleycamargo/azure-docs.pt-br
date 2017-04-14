---
title: Diferentes maneiras de criar uma VM do Linux no Azure | Microsoft Azure
description: "Aprenda as diferentes maneiras de criar uma máquina virtual do Linux no Azure, incluindo links para ferramentas e tutoriais para cada método."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d7ff1317cdf6ccfe6b4e5035878fc4e096fcc0f9
ms.lasthandoff: 04/03/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Modos diferentes para criar uma VM Linux
Você tem a flexibilidade no Azure de criar uma máquina virtual Linux (VM) usando ferramentas e fluxos de trabalho confortáveis para você. Este artigo resume essas diferenças e exemplos para criar suas VMs Linux, incluindo a CLI do Azure 2.0. Você também pode exibir opções de criação incluindo a [CLI 1.0 do Azure](creation-choices-nodejs.md).

A [CLI do Azure 2.0](/cli/azure/install-az-cli2) está disponível entre plataformas por meio de um pacote npm, pacotes fornecidos por distribuição ou por contêiner do Docker. Instale o build mais adequado ao seu ambiente e faça logon em uma conta do Azure usando [az login](/cli/azure/#login)

Os exemplos a seguir usam a CLI do Azure 2.0. Leia cada artigo para obter mais detalhes sobre os comandos mostrados. Você também pode encontrar exemplos nas opções de criação do Linux usando a [CLI do Azure 1.0](creation-choices-nodejs.md).

* [Criar uma VM Linux usando a CLI do Azure 2.0](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Este exemplo usa [az group create](/cli/azure/group#create) para criar um grupo de recursos denominado `myResourceGroup`: 
-    
    ```azurecli
    az group create --name myResourceGroup --location westus
    ```
    
  * Este exemplo usa [az vm create](/cli/azure/vm#create) para criar uma VM chamada `myVM` usando a imagem mais recente do Debian com os Discos Gerenciados do Azure e uma chave pública denominada `id_rsa.pub`:

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
     --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  –-new --size-gb 5    --public-ip-address-dns-name myPublicDNS \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

    * Se você deseja usar discos não gerenciados, adicione o sinalizador `--use-unmanaged-disks` ao comando acima. Uma conta de armazenamento é criada para você. Para saber mais, veja [Visão geral dos Azure Managed Disks](../../storage/storage-managed-disks-overview.md).

* [Criar uma VM do Linux protegida usando um modelo do Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * O exemplo a seguir usa [az group deployment create](/cli/azure/group/deployment#create) para criar uma máquina virtual usando um modelo armazenado no GitHub:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Criar um ambiente Linux completo usando a CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Inclui a criação de um balanceador de carga e várias VMs em um conjunto de disponibilidade.

* [Adicionar um disco a uma VM do Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * O exemplo a seguir usa [az vm disk attach-new](/cli/azure/vm/disk#attach-new) para adicionar um disco gerenciado de 50 Gb a uma máquina virtual existente chamada `myVM`:
  
    ```azurecli
    az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  \
    –-new --size-gb 50
    ```

## <a name="azure-portal"></a>Portal do Azure
O [portal do Azure](https://portal.azure.com) permite que você crie rapidamente uma VM porque não há nada para instalar em seu sistema. Use o portal do Azure para criar a VM:

* [Criar uma VM do Linux usando o portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Anexar um disco usando o portal do Azure](../windows/attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Sistema operacional e opções de imagem
Ao criar uma VM, você escolherá uma imagem com base no sistema operacional que deseja executar. A Azure e seus parceiros oferecem muitas imagens, algumas delas incluem aplicativos e ferramentas pré-instalados. Ou carregue uma de suas próprias imagens (consulte [a seção a seguir](#use-your-own-image)).

### <a name="azure-images"></a>Imagens do Azure
Use os comandos [az vm image](/cli/azure/vm/image) da CLI para ver o que está disponível no editor, versão de distribuição e compilações.

Liste os editores disponíveis:

```azurecli
az vm image list-publishers --location WestUS
```

Listar produtos (ofertas) disponíveis para determinado editor:

```azurecli
az vm image list-offers --publisher Canonical --location WestUS
```

Listar SKUs disponíveis (versões de distribuição) de determinada oferta:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location WestUS
```

Liste todas as imagens disponíveis para determinada versão:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location WestUS
```

Para obter mais exemplos de navegação e uso das imagens disponíveis, consulte [Navegar e selecionar imagens da máquina virtual do Azure com a CLI do Azure](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

O comando **az vm create** tem aliases que você pode usar para acessar rapidamente as distribuições mais comuns e suas versões mais recentes. Usar aliases geralmente é mais rápido do que especificar o editor, oferta, SKU e versão sempre que você cria uma VM:

| Alias | Editor | Oferta | SKU | Versão |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7,2 |mais recente |
| CoreOS |CoreOS |CoreOS |Estável |mais recente |
| Debian |credativ |Debian |8 |mais recente |
| openSUSE |SUSE |openSUSE |13.2 |mais recente |
| RHEL |Redhat |RHEL |7,2 |mais recente |
| SLES |SLES |SLES |12-SP1 |mais recente |
| UbuntuLTS |Canônico |UbuntuServer |14.04.4-LTS |mais recente |

### <a name="use-your-own-image"></a>Usar sua própria imagem
Se você precisar de personalizações específicas, poderá usar uma imagem com base em uma VM existente do Azure *capturando* essa VM. Você também pode carregar uma imagem criada no local. Para obter mais informações sobre as distribuições com suporte e como usar suas próprias imagens, confira os artigos a seguir:

* [Distribuições endossadas pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Informações para distribuições não endossadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Como capturar uma máquina virtual do Linux como um modelo do Resource Manager](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Comandos de exemplo **az vm** de início rápido para capturar uma VM existente usando discos não gerenciados:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm capture --resource-group myResourceGroup --name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Próximas etapas
* Crie uma VM do Linux o [portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), com a [CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou usando um [modelo do Azure Resource Manager](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Depois de criar uma VM do Linux, [adicione um disco de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Etapas rápidas para [redefinir uma senha ou chaves SSH e gerenciar os usuários](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

