---
title: Capturar uma VM Linux com a CLI 2.0 do Azure | Microsoft Docs
description: "Como capturar e generalizar uma imagem de uma VM (máquina virtual) do Azure baseada no Linux usando discos gerenciados criados com a CLI 2.0 do Azure"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8433f326f04d0585e538695c419e451323c7869b
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-generalize-and-capture-a-linux-virtual-machine"></a>Como generalizar e capturar uma máquina virtual Linux
Para reutilizar as máquinas virtuais (VMs) implantadas e configuradas no Azure, você captura uma imagem da máquina virtual. O processo também envolve generalizar a VM para remover informações pessoais da conta antes de implantar novas VMs a partir da imagem. Este artigo detalha como capturar a imagem de uma VM com a CLI 2.0 do Azure para uma VM usando o Azure Managed Disks. Esses discos são tratados pela plataforma do Azure e não exigem nenhuma preparação ou local para armazenamento. Para saber mais, veja [Visão geral dos Azure Managed Disks](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Este artigo detalha como capturar uma VM Linux com a CLI 2.0 do Azure. Você também pode executar essas etapas com a [CLI do Azure 1.0](capture-image-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!TIP]
> Se quiser criar uma cópia de sua VM Linux existente com seu estado especializado para depuração ou backup, confira [Criar uma cópia de uma máquina virtual do Linux em execução no Azure](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se você quiser carregar um VHD do Linux na VM local, confira [Carregar e criar uma VM do Linux por meio da imagem de disco personalizada](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  


## <a name="before-you-begin"></a>Antes de começar
Verifique se os seguintes pré-requisitos foram atendidos:

* **VM do Azure criada no modelo de implantação do Gerenciador de Recursos** - se você ainda não criou uma VM do Linux, pode usar o [portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), a [CLI do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [modelos do Resource Manager](cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Configure a VM conforme necessário. Por exemplo, [adicionar discos de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), aplicar atualizações e instalar aplicativos. 

Também é preciso ter a [CLI 2.0 do Azure](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando [az login](/cli/azure/#login).

## <a name="quick-commands"></a>Comandos rápidos
Se você precisar realizar rapidamente a tarefa, a seção a seguir detalha a base de dados de comandos para capturar uma imagem de uma VM Linux no Azure. Mais informações detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, começando [aqui](#detailed-steps). Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem `myResourceGroup`, `myVM` e `myImage`.

1. Desprovisione a VM de origem:

    ```bash
    ssh ops@myvm.westus.cloudapp.azure.com
    sudo waagent -deprovision+user -force
    exit
    ```

2. Desaloque a VM com [az vm deallocate](/cli/azure/vm#deallocate):

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Generalize a VM com [az vm generalize](/cli/azure/vm#generalize):
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

4. Crie uma imagem de recurso da VM com [az image create](/cli/azure/image#create):
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```

5. Crie uma máquina virtual do seu recurso de imagem com [az vm create](/cli/azure/vm#create):

    ```azurecli
    az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="detailed-steps"></a>Etapas detalhadas
Nas etapas a seguir, desprovisione uma VM existente, desaloque e generalize o recurso de máquina virtual e então crie uma imagem. Você pode usar essa imagem para criar VMs em qualquer grupo de recursos dentro da sua assinatura. Esse processo fornece aos [Azure Managed Disks](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) uma vantagem sobre os discos não gerenciados. Com discos não gerenciados, você cria uma cópia de blob do disco rígido virtual (VHD) subjacente e eles então ficam limitados à criação de VMs na mesma conta de armazenamento do blob VHD copiado. Com os discos gerenciados, você pode criar um recurso de imagem que pode ser implantado em sua assinatura inteira.

## <a name="step-1-remove-the-azure-linux-agent"></a>Etapa 1: remover o agente Linux do Azure
Para preparar a VM para a generalização, você desprovisiona a máquina virtual usando o agente de VM do Azure para excluir arquivos e dados. Use o comando **waagent** com o parâmetro **deprovision** na VM Linux de destino. Para saber mais, confira o [Guia do usuário do agente Linux para o Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

1. Conecte-se à VM do Linux usando um cliente SSH.
2. Na janela SSH, digite o seguinte comando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Execute o comando apenas em uma VM que você pretende capturar como uma imagem. Ele não garante que a imagem esteja sem nenhuma informação confidencial ou seja adequada para redistribuição.
 
3. Digite **y** para continuar. Você pode adicionar o parâmetro **-force** para evitar essa etapa de confirmação.
4. Após a conclusão do comando, digite **sair**. Esta etapa fecha o cliente SSH.

## <a name="step-2-capture-the-vm"></a>Etapa 2: Capturar a VM
Use a CLI 2.0 do Azure para generalizar e capturar a VM. Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem **myResourceGroup**, **myVnet** e **myVM**.

1. Desaloque a VM desprovisionada com [az vm deallocate](/cli//azure/vm#deallocate). O seguinte exemplo desaloca a VM `myVM` no grupo de recursos chamado `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Generalize a VM com [az vm generalize](/cli//azure/vm#generalize). O exemplo a seguir generaliza a VM `myVM` no grupo de recursos denominado `myResourceGroup`:
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

3. Agora crie uma imagem do recurso da VM com [az image create](/cli//azure/image#create). O exemplo a seguir cria uma imagem `myImage` no grupo de recursos denominado `myResourceGroup` usando o recurso de máquina virtual denominado `myVM`:
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```
   
   > [!NOTE]
   > A imagem é criada no mesmo grupo de recursos da VM de origem. Você pode criar VMs em qualquer grupo de recursos em sua assinatura desde esta imagem. De uma perspectiva de gerenciamento, você poderá criar um grupo de recursos específicos para seus recursos de máquina virtual e imagens.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Etapa 3: criar uma VM com base na imagem capturada
Criar uma máquina virtual usando a imagem criada com [az vm create](/cli/azure/vm#create). O exemplo a seguir cria uma VM `myVMDeployed` da imagem denominada `myImage`:

```azurecli
az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```

Com os discos gerenciados, você pode criar VMs de uma imagem em qualquer grupo de recursos em sua assinatura. Esse comportamento é uma alteração de discos não gerenciados em que você só pode criar VMs na mesma conta de armazenamento que seu VHD de origem. Para criar uma máquina virtual em um grupo de recursos diferente do da imagem, especifique a ID de recurso completa para a imagem. Use [az image list](/cli/azure/image#list) para exibir uma lista de imagens. A saída deverá ser semelhante ao seguinte exemplo:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

O exemplo a seguir usa **az vm create** para criar uma máquina virtual em um grupo de recursos diferente do da imagem de origem, especificando a ID de recurso da imagem:

```azurecli
az vm create --resource-group myOtherResourceGroup --name myOtherVMDeployed 
    --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage"
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```


### <a name="verify-the-deployment"></a>Verificar a implantação
Agora, use o SSH na máquina virtual que você criou para verificar a implantação e começar a usar a nova VM. Para se conectar via SSH, localize o endereço IP ou o FQDN da VM com [az vm show](/cli/azure/vm#show):

```azurecli
az vm show --resource-group myResourceGroup --name myVM --show-details
```

## <a name="next-steps"></a>Próximas etapas
Você pode criar várias máquinas virtuais da sua imagem de VM de origem. Se você precisar fazer alterações em sua imagem: 

- Crie uma VM usando sua imagem.
- Faça quaisquer atualizações ou alterações de configuração.
- Siga as etapas novamente para desprovisionar, desalocar, generalizar e criar uma imagem.
- Use essa nova imagem para implantações futuras. Se desejar, exclua a imagem original.

Para saber mais sobre como gerenciar suas VMs com a CLI, confira [CLI 2.0 do Azure](/cli/azure/overview).

