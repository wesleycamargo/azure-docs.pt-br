---
title: Criar um conjunto de dimensionamento do Azure que use VMs de baixa prioridade (versão prévia) | Microsoft Docs
description: Saiba como criar conjuntos de dimensionamento de máquinas virtuais do Azure que usem VMs de baixa prioridade para economizar nos custos
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: memccror
ms.openlocfilehash: 9e4970ecc538caab537281931b89bfd57d994cfa
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>VMs de baixa prioridade em conjuntos de dimensionamento (versão prévia)

O uso de VMs de baixa prioridade em conjuntos de dimensionamento permite tirar proveito da capacidade inutilizada com uma economia significativa. A qualquer momento que o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá VMs de baixa prioridade. Portanto, as VMs de baixa prioridade são ótimas para cargas de trabalho que podem lidar com interrupções, como trabalhos de processamento de carga, ambientes de desenvolvimento/teste, grandes cargas de trabalho de computação e etc.

A quantidade de capacidade disponível pode variar com base no tamanho, região, hora do dia e etc. Ao implantar VMs de baixa prioridade em conjuntos de dimensionamento, o Azure alocará as VMs se houver capacidade disponível, mas não há SLA para essas VMs. Um conjunto de dimensionamento de baixa prioridade é implantado em um domínio de falha único e não oferece garantias de alta disponibilidade.

> [!NOTE]
> Os conjuntos de dimensionamento de baixa prioridade estão em versão prévia e prontos para seus cenários de desenvolvimento e teste. 

## <a name="eviction-policy"></a>Política de remoção

Quando suas VMs de conjunto de dimensionamento de baixa prioridade são removidas, elas serão movidas para o estado Interrompido (desalocado) por padrão. Com essa política de remoção, você pode reimplantar instâncias removidas, mas não há nenhuma garantia de que a alocação terá êxito. As VMs paradas afetarão sua cota de instância do conjunto de dimensionamento e você será cobrado pelos discos subjacentes. 

Se você quiser que suas VMs em seu conjunto de dimensionamento de baixa prioridade sejam excluídas quando forem removidas, você pode definir a política de remoção para excluir no [Modelo do Azure Resource Manager](#use-azure-resource-manager-templates). Com a política de remoção definida para excluir, você pode criar novas VMs, aumentando a propriedade de contagem de instância do conjunto de dimensionamento. As VMs removidas são excluídas junto com seus discos subjacentes e, portanto, você não será cobrado pelo armazenamento. Você também pode usar o recurso de dimensionamento automático dos conjuntos de dimensionamento para tentar e compensar automaticamente as VMs removidas, mas não há nenhuma garantia de que a alocação terá êxito. É recomendável que você só use o recurso de dimensionamento automático em conjuntos de dimensionamento de baixa prioridade quando você definir a política de remoção para excluir para evitar que o custo de seus discos atinjam os limites de cota. 

> [!NOTE]
> Durante a versão prévia, você poderá definir sua política de remoção usando [Modelos do Azure Resource Manager](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Implantação de VMs de baixa prioridade em conjuntos de dimensionamento

Para implantar VMs de baixa prioridade em conjuntos de dimensionamento, você poderá definir o novo sinalizador *Prioridade* como *Baixa*. Todas as VMs no conjunto de dimensionamento serão definidas como baixa prioridade. Para criar um conjunto de dimensionamento com VMs de baixa prioridade, use um dos seguintes métodos:
- [CLI 2.0 do Azure](#use-the-azure-cli-20)
- [PowerShell do Azure](#use-azure-powershell)
- [Modelos do Gerenciador de Recursos do Azure](#use-azure-resource-manager-templates)

## <a name="use-the-azure-cli-20"></a>Usar a CLI 2.0 do Azure

O processo para criar um conjunto de dimensionamento com VMs de baixa prioridade é o mesmo detalhado no [artigo de introdução](virtual-machine-scale-sets-create-cli.md). Basta adicionar o parâmetro '--Priority' à chamada da CLI e defini-lo como *Baixa* conforme mostrado no exemplo a seguir:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure

O processo para criar um conjunto de dimensionamento com VMs de baixa prioridade é o mesmo detalhado no [artigo de introdução](virtual-machine-scale-sets-create-powershell.md).
Basta adicionar o parâmetro '-Priority' a [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) e defini-lo como *Baixa* conforme mostrado no exemplo a seguir:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Usar Modelos do Azure Resource Manager

O processo para criar um conjunto de dimensionamento que use VMs de baixa prioridade é o mesmo detalhado no artigo de introdução para [Linux](virtual-machine-scale-sets-create-template-linux.md) ou [Windows](virtual-machine-scale-sets-create-template-windows.md). Adicione a propriedade ‘priority’ ao tipo de recurso *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* em seu modelo e especifique *Baixa* como valor. Certifique-se de usar a versão *2017-10-30-preview* da API ou superior. 

Para definir a política de remoção para excluir, adicione o parâmetro 'evictionPolicy' e defina-o como *excluir*.

O exemplo a seguir cria uma conjunto de dimensionamento de baixa prioridade do Linux chamado *myScaleSet* em *Centro-Oeste dos EUA*, que irá *excluir* as VMs no conjunto de dimensionamento durante a remoção:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
       "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
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
## <a name="next-steps"></a>Próximas etapas
Agora que você criou um conjunto de dimensionamento com VMs de baixa prioridade, tente implantar nosso [modelo de dimensionamento automático com baixa prioridade](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Confira a [página de preços de conjunto de dimensionamento de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) para obter detalhes sobre preços.