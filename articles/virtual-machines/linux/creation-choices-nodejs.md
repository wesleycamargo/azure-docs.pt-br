---
title: Diferentes maneiras de criar uma VM Linux com a CLI do Azure 1.0 | Microsoft Docs
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
ms.topic: 
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 1eb90d44797d66f3e09811918ce5a7f4ad4287c6
ms.contentlocale: pt-br
ms.lasthandoff: 07/14/2017

---
# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Diferentes maneiras de criar uma máquina virtual do Linux no Azure
Você tem a flexibilidade no Azure de criar uma máquina virtual Linux (VM) usando ferramentas e fluxos de trabalho confortáveis para você. Este artigo resume essas diferenças e exemplos para criar suas VMs do Linux.

## <a name="azure-cli"></a>CLI do Azure
Você pode criar VMs no Azure usando uma das seguintes versões da CLI:

- CLI 1.0 do Azure – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI do Azure 2.0](../windows/creation-choices.md) – nossa próxima geração de CLI para o modelo de implantação de gerenciamento de recursos

A CLI 1.0 do Azure está disponível entre plataformas por meio de um pacote npm, pacotes de distribuição fornecida ou contêineres de Docker. Você pode ler mais sobre [como instalar e configurar a CLI do Azure](../../cli-install-nodejs.md). Os tutoriais a seguir fornecem exemplos sobre como usar a CLI 1.0 do Azure. Leia cada artigo para obter mais detalhes sobre os comandos de início rápido da CLI mostrados:

* [Criar uma VM do Linux na CLI do Azure para desenvolvimento e teste](quick-create-cli-nodejs.md)
  
  * O exemplo a seguir cria uma VM CoreOS usando uma chave pública chamada *azure_id_rsa.pub*:
    
    ```azurecli
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
      --image-urn CoreOS
    ```
* [Criar uma VM do Linux protegida usando um modelo do Azure](create-ssh-secured-vm-from-template.md)
  
  * O exemplo a seguir cria uma VM usando um modelo armazenado no GitHub:
    
    ```azurecli
    azure group create --name myResourceGroup --location eastus 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```
* [Criar um ambiente Linux completo usando a CLI do Azure](create-cli-complete-nodejs.md)
  
  * Inclui a criação de um balanceador de carga e várias VMs em um conjunto de disponibilidade.
* [Adicionar um disco a uma VM do Linux](add-disk.md)
  
  * O exemplo a seguir adiciona um disco de *5* GB a uma VM existente chamada *myVM*:
    
    ```azurecli
    azure vm disk attach-new \
        --resource-group myResourceGroup \
        --vm-name myVM \
        --size-in-GB 5
    ```

## <a name="azure-portal"></a>Portal do Azure
O [portal do Azure](https://portal.azure.com) permite que você crie rapidamente uma VM porque não há nada para instalar em seu sistema. Use o portal do Azure para criar a VM:

* [Criar uma VM do Linux usando o portal do Azure](quick-create-portal.md) 

## <a name="operating-system-and-image-choices"></a>Sistema operacional e opções de imagem
Ao criar uma VM, você escolherá uma imagem com base no sistema operacional que deseja executar. A Azure e seus parceiros oferecem muitas imagens, algumas delas incluem aplicativos e ferramentas pré-instalados. Ou carregue uma de suas próprias imagens (consulte [a seção a seguir](#use-your-own-image)).

### <a name="azure-images"></a>Imagens do Azure
Use os `azure vm image` comandos da CLI para ver o que está disponível no editor, versão de distribuição e compilações.

Liste os editores disponíveis como a seguir:

```azurecli
azure vm image list-publishers --location eastus
```

Liste os produtos (ofertas) disponíveis para um determinado editor como a seguir:

```azurecli
azure vm image list-offers --location eastus --publisher Canonical
```

Liste os SKUs disponíveis (versões de distribuição) de uma determinada oferta como a seguir:

```azurecli
azure vm image list-skus --location eastus --publisher Canonical --offer UbuntuServer
```

Liste todas as imagens disponíveis para uma determinada versão como a seguir:

```azurecli
azure vm image list --location eastus --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Os comandos `azure vm quick-create` e `azure vm create` têm aliases que você pode usar para acessar rapidamente as distribuições mais comuns e suas versões mais recentes. Usar aliases geralmente é mais rápido do que especificar o editor, oferta, SKU e versão sempre que você cria uma VM:

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

* [Distribuições endossadas pelo Azure](endorsed-distros.md)
* [Informações para distribuições não endossadas](create-upload-generic.md)
* [Carregar e criar uma VM Linux com base em uma imagem de disco personalizada](upload-vhd.md)
* [Como capturar uma máquina virtual do Linux como um modelo do Resource Manager](capture-image.md).
  
  * Comandos de exemplo de início rápido para capturar uma VM existente:
    
    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --vm-name myVM
    azure vm generalize --resource-group myResourceGroup --vm-name myVM
    azure vm capture --resource-group myResourceGroup --vm-name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Próximas etapas
* Crie uma VM do Linux o [portal](quick-create-portal.md), com a [CLI](quick-create-cli.md) ou usando um [modelo do Azure Resource Manager](../windows/cli-deploy-templates.md).
* Depois de criar uma VM do Linux, [adicione um disco de dados](add-disk.md).
* Etapas rápidas para [redefinir uma senha ou chaves SSH e gerenciar os usuários](using-vmaccess-extension.md)


