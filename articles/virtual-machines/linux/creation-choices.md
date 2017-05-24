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
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 756711abb014829971af126c5cb60c12e79c920e
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Modos diferentes para criar uma VM Linux
Você tem a flexibilidade no Azure de criar uma máquina virtual Linux (VM) usando ferramentas e fluxos de trabalho confortáveis para você. Este artigo resume essas diferenças e exemplos para criar suas VMs Linux, incluindo a CLI do Azure 2.0. Você também pode exibir opções de criação incluindo a [CLI 1.0 do Azure](creation-choices-nodejs.md).

A [CLI do Azure 2.0](/cli/azure/install-az-cli2) está disponível entre plataformas por meio de um pacote npm, pacotes fornecidos por distribuição ou por contêiner do Docker. Instale o build mais adequado ao seu ambiente e faça logon em uma conta do Azure usando [az login](/cli/azure/#login)

* [Criar uma VM Linux com a CLI 2.0 do Azure](quick-create-cli.md)
  
  * Crie um grupo de recursos com [az group create](/cli/azure/group#create) chamado *myResourceGroup*: 
   
    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```
    
  * Crie uma VM com [az vm create](/cli/azure/vm#create) chamada *myVM* usando a imagem mais recente de *UbuntuLTS* e gere chaves SSH se elas já existirem em *~/.ssh*:

    ```azurecli
    az vm create \
        --resource-group myResourceGroup \
        --name myVM \
        --image UbuntuLTS \
        --generate-ssh-keys
    ```

* [Criar uma VM do Linux com um modelo do Azure](create-ssh-secured-vm-from-template.md)
  
  * O exemplo a seguir usa [az group deployment create](/cli/azure/group/deployment#create) para criar uma VM com um modelo armazenado no GitHub:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
* [Criar uma VM do Linux e personalizá-la com cloud-init](tutorial-automate-vm-deployment.md)

* [Criar um aplicativo de alta disponibilidade e com balanceamento de carga em várias VMs Linux](tutorial-load-balancer.md)


## <a name="azure-portal"></a>Portal do Azure
O [portal do Azure](https://portal.azure.com) permite que você crie rapidamente uma VM porque não há nada para instalar em seu sistema. Use o portal do Azure para criar a VM:

* [Criar uma VM do Linux usando o portal do Azure](quick-create-portal.md) 


## <a name="operating-system-and-image-choices"></a>Sistema operacional e opções de imagem
Ao criar uma VM, você escolherá uma imagem com base no sistema operacional que deseja executar. A Azure e seus parceiros oferecem muitas imagens, algumas delas incluem aplicativos e ferramentas pré-instalados. Ou carregue uma de suas próprias imagens (consulte [a seção a seguir](#use-your-own-image)).

### <a name="azure-images"></a>Imagens do Azure
Use os comandos [az vm image](/cli/azure/vm/image) da CLI para ver o que está disponível no editor, versão de distribuição e compilações.

Liste os editores disponíveis:

```azurecli
az vm image list-publishers --location eastus
```

Listar produtos (ofertas) disponíveis para determinado editor:

```azurecli
az vm image list-offers --publisher Canonical --location eastus
```

Listar SKUs disponíveis (versões de distribuição) de determinada oferta:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location eastus
```

Liste todas as imagens disponíveis para determinada versão:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location eastus
```

Para obter mais exemplos de navegação e uso das imagens disponíveis, consulte [Navegar e selecionar imagens da máquina virtual do Azure com a CLI do Azure](cli-ps-findimage.md).

O comando [az vm create](/cli/azure/vm#create) tem aliases que você pode usar para acessar rapidamente as distribuições mais comuns e suas versões mais recentes. Usar aliases geralmente é mais rápido do que especificar o editor, oferta, SKU e versão sempre que você cria uma VM:

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
Se você precisar de personalizações específicas, poderá usar uma imagem com base em uma VM existente do Azure capturando essa VM. Você também pode carregar uma imagem criada no local. Para obter mais informações sobre as distribuições com suporte e como usar suas próprias imagens, confira os artigos a seguir:

* [Distribuições endossadas pelo Azure](endorsed-distros.md)
* [Informações para distribuições não endossadas](create-upload-generic.md)
* [Como criar uma imagem de uma VM existente do Azure](tutorial-custom-images.md).
  
  * Comandos de exemplo de início rápido para criar uma imagem de uma VM do Azure existente:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm image create --resource-group myResourceGroup --source myVM --name myImage
    ```

## <a name="next-steps"></a>Próximas etapas
* Crie uma VM do Linux com a [CLI](quick-create-cli.md), por meio do [portal](quick-create-portal.md) ou usando um [modelo do Azure Resource Manager](../windows/cli-deploy-templates.md).
* Depois de criar uma VM do Linux, [saiba mais sobre discos e armazenamento do Azure](tutorial-manage-disks.md).
* Etapas rápidas para [redefinir uma senha ou chaves SSH e gerenciar os usuários](using-vmaccess-extension.md).

