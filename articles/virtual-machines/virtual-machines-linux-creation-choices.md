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
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 42ee74ac250e6594616652157fe85a9088f4021a
ms.openlocfilehash: 23862762fcf0939ce84859fdae0274421c0bb5fe


---
# <a name="different-ways-to-create-a-linux-vm-including-the-azure-cli-20-preview"></a>Diferentes maneiras de criar uma VM do Linux incluindo a CLI do Azure 2.0 (Visualização)
Você tem a flexibilidade no Azure de criar uma máquina virtual Linux (VM) usando ferramentas e fluxos de trabalho confortáveis para você. Este artigo resume essas diferenças e exemplos para criar suas VMs do Linux.

## <a name="azure-cli"></a>CLI do Azure
Você pode criar VMs no Azure usando uma das seguintes versões da CLI:

- [CLI do Azure 1.0](virtual-machines-linux-creation-choices-nodejs.md) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos
- CLI 2.0 do Azure (Visualização) – nossa CLI da próxima geração para o modelo de implantação de gerenciamento de recursos (este artigo)

A [CLI do Azure 2.0 (Visualização)](/cli/azure/install-az-cli2) está disponível entre plataformas por meio de pacote npm, pacotes de distribuição fornecida ou contêiner do Docker. Instale o build mais adequado ao seu ambiente e faça logon em uma conta do Azure usando [az login](/cli/azure/#login)

Os exemplos a seguir usam a CLI do Azure 2.0 (Visualização). Leia cada artigo para obter mais detalhes sobre os comandos mostrados. Você também pode encontrar exemplos nas opções de criação do Linux usando a [CLI do Azure 1.0](virtual-machines-linux-creation-choices-nodejs.md).

* [Criar uma VM do Linux usando a CLI do Azure 2.0 (Visualização)](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Este exemplo usa [az group create](/cli/azure/group#create) para criar um grupo de recursos denominado `myResourceGroup`: 
    
    ```azurecli
    az group create --name myResourceGroup --location westus
    ```
    
  * Este exemplo usa [az vm create](/cli/azure/vm#create) para criar uma VM chamada `myVM` usando a imagem mais recente do Debian com os Discos Gerenciados do Azure e uma chave pública denominada `id_rsa.pub`:

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --public-ip-address-dns-name myPublicDNS \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

    * Se você deseja usar discos não gerenciados, adicione o sinalizador `--use-unmanaged-disks` ao comando acima. Uma conta de armazenamento é criada para você. Para saber mais, veja [Visão geral dos Azure Managed Disks](../storage/storage-managed-disks-overview.md).

* [Criar uma VM do Linux protegida usando um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * O exemplo a seguir usa [az group deployment create](/cli/azure/group/deployment#create) para criar uma máquina virtual usando um modelo armazenado no GitHub:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Criar um ambiente Linux completo usando a CLI do Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Inclui a criação de um balanceador de carga e várias VMs em um conjunto de disponibilidade.

* [Adicionar um disco a uma VM do Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * O exemplo a seguir usa [az vm disk attach-new](/cli/azure/vm/disk#attach-new) para adicionar um disco de 5 Gb não gerenciado chamado `myDataDisk.vhd` para uma máquina virtual existente chamada `myVM`:
  
    ```azurecli
    az vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
      --disk-size 5 --vhd https://mystorageaccount.blob.core.windows.net/vhds/myDataDisk.vhd
    ```

## <a name="azure-portal"></a>Portal do Azure
O [portal do Azure](https://portal.azure.com) permite que você crie rapidamente uma VM porque não há nada para instalar em seu sistema. Use o portal do Azure para criar a VM:

* [Criar uma VM do Linux usando o portal do Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Anexar um disco usando o portal do Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

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
az vm image list-offers --publisher-name Canonical --location WestUS
```

Listar SKUs disponíveis (versões de distribuição) de determinada oferta:

```azurecli
az vm image list-skus --publisher-name Canonical --offer UbuntuServer --location WestUS
```

Liste todas as imagens disponíveis para determinada versão:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location WestUS
```

Para obter mais exemplos de navegação e uso das imagens disponíveis, consulte [Navegar e selecionar imagens da máquina virtual do Azure com a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

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

* [Distribuições endossadas pelo Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Informações para distribuições não endossadas](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Como capturar uma máquina virtual do Linux como um modelo do Resource Manager](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Comandos de exemplo **az vm** de início rápido para capturar uma VM existente usando discos não gerenciados:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm capture --resource-group myResourceGroup --name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Próximas etapas
* Crie uma VM do Linux o [portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), com a [CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou usando um [modelo do Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Depois de criar uma VM do Linux, [adicione um disco de dados](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Etapas rápidas para [redefinir uma senha ou chaves SSH e gerenciar os usuários](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Feb17_HO2-->


