---
title: Tutorial – Gerenciar discos do Azure com a CLI do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a usar a CLI 2.0 do Azure para criar e gerenciar discos do Azure para máquinas virtuais
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 889facbf9612f2462a10c886a428ac052becefd8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704480"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli-20"></a>Tutorial – Gerenciar discos do Azure com a CLI 2.0 do Azure

Máquinas virtuais (VMs) do Azure usam discos para armazenar o sistema operacional, aplicativos e dados. Ao criar uma VM, é importante escolher um tamanho de disco e a configuração apropriada para a carga de trabalho esperada. Este tutorial mostra como implantar e gerenciar os discos de VM. Você saberá mais sobre:

> [!div class="checklist"]
> * Discos de sistema operacional e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar os discos de dados
> * Redimensionamento de discos
> * Instantâneos de disco

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli).

## <a name="default-azure-disks"></a>Discos padrão do Azure

Quando uma máquina virtual do Azure é criada, dois discos são automaticamente anexados à máquina virtual.

**Disco do sistema operacional** - Os discos do sistema operacional podem ser dimensionados para até 2 TB e hospedar o sistema operacional das máquinas virtuais. O disco do sistema operacional é rotulado */dev/sda* por padrão. A configuração de cache do disco do SO é otimizada para desempenho do SO. Devido a essa configuração, o disco do sistema operacional **não deve** ser usado para aplicativos ou dados. Para aplicativos e dados, utilize um disco de dados, que é detalhado posteriormente neste tutorial.

**Disco temporário** - Discos temporários utilizam uma unidade de estado sólido localizada no mesmo host do Azure que a máquina virtual. Os discos temporários são altamente eficazes e podem ser usados para operações como o processamento de dados temporário. No entanto, se a VM for movida para um novo host, todos os dados armazenados em um disco temporário serão removidos. O tamanho do disco temporário é determinado pelo tamanho da máquina virtual. Os discos temporários são rotulados */dev/sdb* e têm um ponto de montagem de */mnt*.

### <a name="temporary-disk-sizes"></a>Tamanhos do disco temporário

| Tipo | Tamanhos comuns | Tamanho máximo do disco temporário (GiB) |
|----|----|----|
| [Propósito geral](sizes-general.md) | Série A, B e D | 1600 |
| [Computação otimizada](sizes-compute.md) | Série F | 576 |
| [Memória otimizada](sizes-memory.md) | Série D, E, G e M | 6144 |
| [Armazenamento otimizado](sizes-storage.md) | Série L | 5630 |
| [GPU](sizes-gpu.md) | Série N | 1440 |
| [Alto desempenho](sizes-hpc.md) | Séries A e H | 2000 |

## <a name="azure-data-disks"></a>Discos de dados do Azure

Para instalar aplicativos e dados de armazenamento, outros discos de dados podem ser adicionados. Os discos de dados devem ser usados em qualquer situação onde o armazenamento de dados durável e responsivo é desejado. Cada disco de dados tem uma capacidade máxima de 4 TB. O tamanho da máquina virtual determina quantos discos de dados podem ser anexados a uma VM. Para cada vCPU da VM, podem ser anexados dois discos de dados.

### <a name="max-data-disks-per-vm"></a>Máximo de discos de dados por VM

| Tipo | Tamanho da VM | Máximo de discos de dados por VM |
|----|----|----|
| [Propósito geral](sizes-general.md) | Série A, B e D | 64 |
| [Computação otimizada](sizes-compute.md) | Série F | 64 |
| [Memória otimizada](../virtual-machines-windows-sizes-memory.md) | Série D, E e G | 64 |
| [Armazenamento otimizado](../virtual-machines-windows-sizes-storage.md) | Série L | 64 |
| [GPU](sizes-gpu.md) | Série N | 64 |
| [Alto desempenho](sizes-hpc.md) | Série A e H | 64 |

## <a name="vm-disk-types"></a>Tipos de disco da máquina virtual

O Azure fornece dois tipos de disco.

### <a name="standard-disk"></a>Disco Standard

Armazenamento padrão é apoiado por HDDs e oferece armazenamento econômico e eficaz. Os discos Standard são ideais para uma carga de trabalho econômica de desenvolvimento e teste.

### <a name="premium-disk"></a>Disco Premium

Os discos Premium são apoiados por disco de baixa latência e alto desempenho baseado em SSD. Perfeitos para VMs que executam carga de trabalho de produção. O Armazenamento Premium dá suporte às VMs das séries DS, DSv2, GS e FS. Ao escolher o tamanho de um disco, o valor é arredondado para o tipo seguinte. Por exemplo, se o tamanho do disco for menor que 128 GB, o tipo de disco é P10. Se o tamanho do disco está entre 129 e 512 GB, o tamanho é P20. Acima de 512 GB, o tamanho é um P30.

### <a name="premium-disk-performance"></a>Desempenho do disco Premium

|Tipo de disco de armazenamento Premium | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Tamanho do disco (arredondado) | 32 GB | 64 GB | 128 GB | 512 GB | 1.024 GB (1 TB) | 2,048 GB (2 TB) | 4,095 GB (4 TB) |
| IOPS máxima por disco | 120 | 240 | 500 | 2.300 | 5.000 | 7.500 | 7.500 |
Taxa de transferência por disco | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

Embora a tabela acima identifique a IOPS máxima por disco, um nível mais alto de desempenho pode ser obtido com a distribuição de vários discos de dados. Por exemplo, uma VM Standard_GS5 pode atingir o máximo de 80.000 IOPS. Para obter informações detalhadas sobre o máximo de IOPS por VM, veja [Tamanhos da VM Linux](sizes.md).

## <a name="create-and-attach-disks"></a>Criar e anexar discos

Discos de dados podem ser criados e anexados no momento da criação de VM ou a uma VM existente.

### <a name="attach-disk-at-vm-creation"></a>Anexar disco na criação da VM

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create).

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Crie uma máquina virtual com o comando [az vm create](/cli/azure/vm#az-vm-create). O exemplo a seguir cria uma VM chamada *myVM*, adiciona uma conta de usuário chamada *azureuser* e gera as chaves SSH, caso ainda não existam. O `--datadisk-sizes-gb` argumento é utilizado para especificar que um disco adicional deve ser criado e anexado à máquina virtual. Para criar e anexar mais de um disco, utilize uma lista delimitada por espaço dos valores de tamanho de disco. No exemplo a seguir, uma VM é criada com dois discos de dados, ambos os 128 GB. Como os tamanhos de disco são 128 GB, esses discos são configurados como P10, que fornecem o máximo de 500 IOPS por disco.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Anexar disco à máquina virtual existente

Para criar e anexar um novo disco a uma máquina virtual existente, utilize o comando [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach). O exemplo a seguir cria um disco premium de 128 gigabytes de tamanho e anexa-o à VM criada na última etapa.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --disk myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Preparar discos de dados

Depois que um disco é anexado à máquina virtual, o sistema operacional precisa ser configurado para usar o disco. O exemplo a seguir mostra como configurar um disco manualmente. Esse processo também pode ser automatizado utilizando a inicialização por nuvem, que é abordada em um [tutorial posterior](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Configuração manual

Crie uma conexão SSH com a máquina virtual. Substitua o endereço IP de exemplo pelo endereço IP público de sua máquina virtual.

```azurecli-interactive
ssh azureuser@52.174.34.95
```

Particione o disco com `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Grave um sistema de arquivos na partição utilizando o comando `mkfs`.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Monte o novo disco para que ele seja acessível no sistema operacional.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

O disco agora pode ser acessado por meio do ponto de montagem *datadrive*, que pode ser verificado ao executar o comando `df -h`.

```bash
df -h
```

A saída mostra a nova unidade montada em */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Para garantir que a unidade seja remontada após uma reinicialização, ela deve ser adicionada ao arquivo */etc/fstab*. Para fazer isso, obtenha o UUID do disco com o `blkid` utilitário.

```bash
sudo -i blkid
```

A saída exibe o UUID da unidade, `/dev/sdc1` nesse caso.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Adicione uma linha semelhante à seguinte para o arquivo */etc/fstab*.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail   1  2
```

Agora que o disco foi configurado, feche a sessão SSH.

```bash
exit
```

## <a name="resize-vm-disk"></a>Redimensionar o disco da máquina virtual

Após a implantação de uma máquina virtual, o disco do sistema operacional ou quaisquer discos de dados anexados podem aumentar de tamanho. Aumentar o tamanho de um disco é útil quando é você precisa de mais espaço de armazenamento ou um nível mais alto de desempenho (tais como P10, P20 e P30). Discos não podem ser reduzidos.

Antes de aumentar o tamanho de um disco, é necessário ter a ID ou o nome do disco. Utilize o comando [az disk list](/cli/azure/disk#az-disk-list) para retornar todos os discos em um grupo de recursos. Anote o nome do disco que você deseja redimensionar.

```azurecli-interactive
az disk list \
    --resource-group myResourceGroupDisk \
    --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
    --output table
```

A máquina vitual deve ser desalocada. Utilize o comando [az vm deallocate](/cli/azure/vm#az-vm-deallocate) para parar e desalocar a máquina virtual.

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupDisk --name myVM
```

Utilize o comando [az disk update](/cli/azure/vm/disk#az-vm-disk-update) para redimensionar o disco. Este exemplo redimensiona um disco chamado *myDataDisk* para 1 terabyte.

```azurecli-interactive
az disk update --name myDataDisk --resource-group myResourceGroupDisk --size-gb 1023
```

Concluída a operação de redimensionamento, inicie a máquina virtual.

```azurecli-interactive
az vm start --resource-group myResourceGroupDisk --name myVM
```

Se o disco do sistema operacional for redimensionado, a partição é expandida automaticamente. Se você redimensionar um disco de dados, as partições atuais precisam ser expandidas no sistema operacional das máquinas virtuais.

## <a name="snapshot-azure-disks"></a>Discos padrão do Azure

Ao criar um instantâneo do disco, o Azure cria uma cópia do disco de apenas leitura, de um ponto no tempo. Instantâneos de máquina virtual do Azure são úteis para salvar rapidamente o estado de uma máquina virtual antes de fazer alterações de configuração. No caso das alterações de configuração se mostrarem indesejadas, o estado da máquina virtual pode ser restaurado usando o instantâneo. Quando uma máquina virtual tem mais de um disco, um instantâneo é tirado de cada disco independentemente dos outros. Para fazer backups consistentes de aplicativos, considere parar a máquina virtual antes de tirar instantâneos de disco. Como alternativa, utilize o [serviço de Backup do Azure](/azure/backup/), que permite realizar backups automáticos, enquanto a máquina virtual está em execução.

### <a name="create-snapshot"></a>Como criar um instantâneo

Antes de criar um instantâneo de disco da máquina virtual, você deve ter a ID ou o nome do disco. Utilize o comando [az vm show](/cli/azure/vm#az-vm-show) para obter a ID do disco. Neste exemplo, a ID do disco é armazenada em uma variável e utilizada em uma etapa posterior.

```azurecli-interactive
osdiskid=$(az vm show -g myResourceGroupDisk -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Agora que você tem a ID do disco da máquina virtual, o comando a seguir criará o instantâneo do disco.

```azurcli
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Como criar o disco a partir de um instantâneo

Esse instantâneo pode ser convertido em um disco, que pode ser usado para recriar a máquina virtual.

```azurecli-interactive
az disk create --resource-group myResourceGroupDisk --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Como restaurar a máquina virtual a partir de um instantâneo

Para demonstrar a recuperação da máquina virtual, exclua a máquina virtual existente.

```azurecli-interactive
az vm delete --resource-group myResourceGroupDisk --name myVM
```

Crie uma nova máquina virtual a partir do disco de instantâneo.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Reanexar um disco de dados

Todos os discos de dados precisam ser anexados novamente à máquina virtual.

Primeiro, utilize o comando [az disk list](/cli/azure/disk#az-disk-list) para encontrar o nome do disco de dados. Este exemplo coloca o nome do disco em uma variável chamada *datadisk*, que será usada na próxima etapa.

```azurecli-interactive
datadisk=$(az disk list -g myResourceGroupDisk --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Use o comando [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) para anexar o disco.

```azurecli-interactive
az vm disk attach –g myResourceGroupDisk –-vm-name myVM –-disk $datadisk
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos de discos da VM como:

> [!div class="checklist"]
> * Discos de sistema operacional e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar os discos de dados
> * Redimensionamento de discos
> * Instantâneos de disco

Vá para o próximo tutorial para saber como automatizar a configuração da máquina virtual.

> [!div class="nextstepaction"]
> [Automatizar a configuração da VM](./tutorial-automate-vm-deployment.md)
