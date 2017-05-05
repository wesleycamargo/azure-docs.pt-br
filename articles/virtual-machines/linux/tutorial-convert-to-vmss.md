---
title: Converter uma VM do Azure em um conjunto de dimensionamento | Microsoft Docs
description: "Crie e implante um conjunto de dimensionamento de máquinas virtuais Linux do Azure com a CLI do Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 8d3376d2791b1349298db618d475ce5573083702
ms.lasthandoff: 04/07/2017

---

# <a name="convert-an-existing-azure-virtual-machine-to-a-scale-set"></a>Converter uma máquina virtual existente do Azure em um conjunto de dimensionamento

Este tutorial mostra como usar a CLI 2.0 do Azure para converter uma máquina virtual em um conjunto de dimensionamento de máquinas virtuais. Você também aprenderá a automatizar a configuração das máquinas virtuais no conjunto de dimensionamento. Para obter mais informações sobre como instalar a CLI 2.0 do Azure, consulte [Introdução à CLI 2.0 do Azure](/cli/azure/get-started-with-azure-cli.md). Para obter mais informações sobre conjuntos de dimensionamento, confira [Conjuntos de Dimensionamento de Máquina Virtual](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

## <a name="step-1---deprovision-the-vm"></a>Etapa 1 – Desprovisionar a VM

Use SSH para conectar-se à VM.

Desprovisione a VM usando o agente de VM do Azure para excluir arquivos e dados. Para obter uma visão geral detalhada do cancelamento do provisionamento, consulte [Capturar uma máquina virtual Linux](capture-image.md).

```bash
sudo waagent -deprovision+user -force
exit
```

## <a name="step-2---capture-an-image-of-the-vm"></a>Etapa 2 – Capturar uma imagem da VM

Para obter uma visão geral detalhada da captura, consulte [Capturar uma máquina virtual Linux](capture-image.md).

Desaloque a VM com [az vm deallocate](/cli/azure/vm#deallocate):

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Generalize a VM com [az vm generalize](/cli/azure/vm#generalize):

```azurecli
az vm generalize --resource-group myResourceGroup --name myVM
```

Crie uma imagem de recurso da VM com [az image create](/cli/azure/image#create):

```azurecli
az image create --resource-group myResourceGroup --name myImage --source myVM
```

## <a name="step-3---create-the-scale-set"></a>Etapa 3 – Criar o conjunto de dimensionamento

Obtenha a **ID** da imagem.

```azurecli
az image show --resource-group myResourceGroup --name myImage --query id
```

```json
"/subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage"
```

Crie uma VM com base no recurso de imagem com [az vmss create](/cli/azure/vmss#create):

```azurecli
az vmss create --resource-group myResourceGroup --name myScaleSet --image /subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage --upgrade-policy-mode automatic --vm-sku Standard_DS1_v2 --data-disk-sizes-gb 10 --admin-username azureuser --generate-ssh-keys
```

Esse comando também anexou um disco de dados de 10 GB. Tenha em mente que, dependendo do tamanho de VM escolhido (usamos **Standard_DS1_v2**), o número de discos de dados permitido é diferente. Para obter mais informações, consulte os [tamanhos de máquina virtual](sizes.md).

Depois que o conjunto de dimensionamento for concluído, conecte-se a ele. Obtenha uma lista de endereços IP das instâncias para o SSH com [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info):

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

```json
[
  "52.183.00.000:50000",
  "52.183.00.000:50003"
]
```

Agora você pode se conectar à instância de máquina virtual para inicializar o disco de dados

```bash
ssh -i ~/.ssh/id_rsa.pub -p 50000 azureuser@52.183.00.000
```

## <a name="step-4---initialize-the-data-disk"></a>Etapa 4 – inicializar o disco de dados

Enquanto estiver conectado à máquina virtual, particione o disco com `fdisk`:

```bash
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | sudo fdisk /dev/sdc
```

Grave um sistema de arquivos na partição com o comando `mkfs`:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Monte o novo disco para que ele seja acessível no sistema operacional:

```bash
sudo mkdir /datadrive ; sudo mount /dev/sdc1 /datadrive
```

O disco agora pode ser acessado por meio do ponto de montagem da unidade de dados, que pode ser verificado com `ls /datadrive/`.

Encerre a sessão do SSH.


## <a name="step-5---configure-firewall"></a>Etapa 5 – Configurar o firewall

Abra um espaço no firewall para o servidor Web hospedado pelo conjunto de dimensionamento. Quando o conjunto de dimensionamento foi criado, um balanceador de carga também foi criado e você o usou para aplicar **SSH** às máquinas virtuais individuais. Para abrir uma porta, você precisa de duas informações, que podem ser obtidas com a CLI do Azure.

* **Pool de endereços IP de front-end**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query frontendIpConfigurations[0].name`

* **Pool de endereços IP de back-end**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query backendAddressPools[0].name`

Com esses dois nomes, você pode abrir a porta **80**.

```azurecli
az network lb rule create --backend-pool-name myScaleSetLBBEPool --backend-port 80 --frontend-ip-name loadBalancerFrontEnd --frontend-port 80 --name webserver --protocol tcp --resource-group myResourceGroup --lb-name myScaleSetLB
```


## <a name="step-6---automate-configuration"></a>Etapa 6 – Automatizar a configuração

O disco de dados precisa ser configurado em cada instância de máquina virtual. Podemos automatizar a configuração da máquina virtual com a extensão **CustomScript**.

Primeiro, crie um script *.sh* que inclui os comandos de formato de disco.

```sh
#!/bin/bash

# Setup the data disk
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | fdisk /dev/sdc
fdisk /dev/sdc
mkfs -t ext4 /dev/sdc1
mkdir /datadrive
mount /dev/sdc1 /datadrive

exit 0
```

Em seguida, carregue o arquivo de script no local em que a extensão **CustomScript** pode acessá-lo. Uma cópia está disponível [aqui](https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4).

Crie um arquivo local chamado **settings.json** e coloque o bloco JSON a seguir nele. A propriedade `flieUris` deve ser definida no local em que o arquivo de script foi carregado.

```json
{
  "fileUris": ["https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4/raw/3ac6e385010ac675e23ce583ce27b1a752f1b482/prep-vmss.sh"],
  "commandToExecute": "bash prep-vmss.sh" 
}
```

Implante esse comando no conjunto de dimensionamento com a extensão **CustomScript**, referenciando o arquivo **settings.json** que acabamos de criar.

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

Essa extensão é executada automaticamente em todas as instâncias e em todas as instâncias criadas posteriormente pelo dimensionamento.

## <a name="step-7---configure-autoscale-rules"></a>Etapa 7 – Configurar regras de dimensionamento automático

Atualmente, as regras de dimensionamento automático não podem ser definidas na CLI do Azure. Use o [portal do Azure](https://portal.azure.com) para configurar o dimensionamento automático.

## <a name="step-8---management-tasks"></a>Etapa 8 – Tarefas de gerenciamento

Durante todo o ciclo de vida do conjunto de dimensionamento, você poderá precisar executar uma ou mais tarefas de gerenciamento. Além disso, talvez você deseje criar scripts que automatizam várias tarefas do ciclo de vida e a CLI do Azure fornece uma maneira rápida de realizar essas tarefas. Estas são algumas tarefas comuns.

### <a name="get-connection-info"></a>Obter informações de conexão

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

### <a name="set-instance-count-manual-scale"></a>Definir a contagem de instâncias (escala manual)

```azurecli
az vmss scale --resource-group myResourceGroup --name myScaleSet --new-capacity 4
```

### <a name="delete-resource-group"></a>Excluir grupo de recursos

Excluir um grupo de recursos exclui todos os recursos contidos nele.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre alguns dos recursos do conjunto de dimensionamento de máquinas virtuais apresentados neste tutorial, consulte as seguintes informações:

- [Visão geral dos conjuntos de dimensionamento de máquinas virtuais do Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Visão geral do Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
- [Controlar o fluxo de tráfego de rede com grupos de segurança de rede](../../virtual-network/virtual-networks-nsg.md)
