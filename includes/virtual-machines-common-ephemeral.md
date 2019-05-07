---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: d7737f73ee4eb9ae9dc8c4845020b7543a5b3495
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159159"
---
Efêmeros discos do sistema operacional são criados no armazenamento local da máquina Virtual (VM) e não são persistidos no armazenamento do Azure remoto. Discos de SO efêmeros funcionam bem para cargas de trabalho sem monitoração de estado, em que os aplicativos são tolerantes a falhas VM individuais, mas estiver mais preocupado com o tempo necessário para implantações em grande escala ou o tempo para refazer a imagem de instâncias de VM individuais. Também é adequado para aplicativos implantados usando o modelo de implantação clássico, para mover para o modelo de implantação do Resource Manager. Com o disco do SO efêmero, você deve observar menor latência de leitura/gravação para o disco do sistema operacional e a recriação de imagem de VM mais rápida. Além disso, o disco do SO efêmero é gratuito, você incorrerá em nenhum custo de armazenamento para o disco do sistema operacional. 
 
Os principais recursos dos discos efêmeros são: 
- Eles podem ser usados com imagens do Marketplace e imagens personalizadas.
- Você pode implantar imagens de VM até o tamanho do Cache de VM.
- Capacidade de redefinir rapidamente ou refazer a imagem de suas VMs para o estado original de inicialização.  
- Menor latência de tempo de execução semelhante a um disco temporário. 
- Sem custos para o disco do sistema operacional. 
 
 
Principais diferenças entre os discos do sistema operacional persistentes e efêmeras:

|                             | Disco do sistema operacional persistente                          | Disco do SO Efêmero                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Limite de tamanho do disco do sistema operacional      | 2 TiB                                                                                        | Cache de tamanho para o tamanho da VM ou 2TiB, o que for menor - [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), e [GS](../articles/virtual-machines/linux/sizes-memory.md)              |
| Tamanhos VM com suporte          | Todos                                                                                          | DSv1, DSv2, DSv3, Esv2, Fs, FsV2, GS, M                                               |
| Suporte ao tipo de disco           | Disco do sistema operacional gerenciado e não gerenciado                                                                | Somente disco do sistema operacional gerenciado                                                               |
| Suporte de regiões              | Todas as regiões                                                                                  | Todas as regiões                              |
| Persistência de dados            | Dados de disco do sistema operacional gravados em disco do sistema operacional são armazenados no armazenamento do Azure                                  | Dados gravados no disco do sistema operacional são armazenados no armazenamento de VM local e não são persistidos no armazenamento do Azure. |
| Estado parada desalocada      | As VMs e instâncias de conjunto de dimensionamento podem ser interrompida e desalocada e reiniciadas do estado parada desalocada | Máquinas virtuais e instâncias de conjunto de escala não podem ser interrompida e desalocada                                  |
| Suporte de disco do sistema operacional especializado | Sim                                                                                          | Não                                                                                  |
| Redimensionamento do disco do sistema operacional              | Durante a criação da VM e depois a VM é interrompida e desalocada com suporte                                | Suporte durante a criação da VM apenas                                                  |
| Redimensionar para um novo tamanho VM   | Os dados de disco do sistema operacional são preservados                                                                    | Dados no disco do SO são excluídos, o sistema operacional for novamente provisionado                                      |

## <a name="register-for-the-preview"></a>Registre-se para a versão prévia


Se registre para a visualização de discos efêmeros do sistema operacional usando a versão mais recente do CLI do Azure ou Azure PowerShell.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzRmProviderFeature –FeatureName LocalDiffDiskPreview
```

Para verificar se você está registrado para a versão prévia:

```azurepowershell-interactive
Get-AzRmProviderFeature –FeatureName LocalDiffDiskPreview
```

### <a name="cli"></a>CLI

```azurecli-interactive
az provider register --namespace Microsoft.Compute
az feature register --namespace Microsoft.Compute --name LocalDiffDiskPreview
```

Para verificar se você está registrado para a versão prévia:
 
```azurecli-interactive
az provider show –namespace ‘Microsoft.Compute’
```


## <a name="scale-set-deployment"></a>Implantação do conjunto de dimensionamento  
O processo para criar um conjunto de dimensionamento que usa um disco do SO efêmero é adicionar o `diffDiskSettings` propriedade para o `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` tipo de recurso no modelo. Além disso, a política de cache deve ser definida como `ReadOnly` para o disco do SO efêmero. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-deployment"></a>Implantação da VM 
Você pode implantar uma VM com um disco do SO efêmero usando um modelo. O processo para criar uma VM que usa discos de SO efêmeros é adicionar o `diffDiskSettings` propriedade ao tipo de recurso Compute/virtualmachines no modelo. Além disso, a política de cache deve ser definida como `ReadOnly` para o disco do SO efêmero. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Refazer a imagem de uma VM usando REST
Atualmente, o único método para refazer a imagem de uma instância de máquina Virtual com o disco do SO efêmero é por meio do usando a API REST. Para conjuntos de dimensionamento, refazer a imagem já está disponível por meio do Powershell, CLI e o portal.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: O que é o tamanho dos discos do sistema operacional local?**

R: Para a visualização, daremos suporte a plataforma e/ou imagens até o tamanho do cache VM, onde todas as leituras/gravações no disco do sistema operacional será locais no mesmo nó que a máquina Virtual. 

**P: O disco do SO efêmero pode ser redimensionado?**

R: Não, uma vez que o disco do SO efêmero é provisionado, o disco do sistema operacional não pode ser redimensionado. 

**P: Pode anexar um disco gerenciado a uma VM efêmero?**

R: Sim, você pode anexar um disco de dados gerenciado a uma VM que usa um disco do SO efêmero. 

**P: Todos os tamanhos VM terá suporte para discos do SO efêmeros?**

R: Não, todos os tamanhos de VM de armazenamento Premium são compatíveis (DS, ES, FS, GS e M), exceto a série B, N-series e os tamanhos da série H.  
 
**P: O disco do SO efêmero pode ser aplicado a máquinas virtuais existentes e conjuntos de dimensionamento?**

R: Não, efêmero só pode ser usado durante a VM de disco do sistema operacional e criação do conjunto de dimensionamento. 

**P: Você pode misturar efêmeros e normais discos do sistema operacional em um conjunto de dimensionamento?**

R: Não, você não pode ter uma combinação de instâncias de disco do SO efêmeras e persistentes dentro do mesmo conjunto de dimensionamento. 

**P: Pode o disco do SO efêmero ser criado usando o Powershell ou CLI?**

R: Sim, você pode criar VMs com disco do SO efêmero usando REST, modelos, PowerShell e CLI.

**P: Quais recursos não são compatíveis com o disco do SO efêmero?**

R: Os discos efêmeros não dão suporte:
- Captura de imagens VM
- Instantâneos de disco 
- Azure Disk Encryption 
- Serviço de Backup do Azure
- Azure Site Recovery  
- Troca de disco do sistema operacional 
