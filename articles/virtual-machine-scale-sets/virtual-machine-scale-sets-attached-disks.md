---
title: Discos de dados conectados a conjuntos de dimensionamento de máquina Virtual do Azure | Microsoft Docs
description: Saiba como usar discos de dados anexados com conjuntos de escala de máquina virtual
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: ec11a2d66530129fb61d97681e6882b887c8654c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Discos de dados conectados e conjuntos de dimensionamento de máquina virtual do Azure
Para expandir o armazenamento disponível, os [conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/) do Azure oferecem suporte a instâncias de VM com discos de dados anexados. É possível anexar discos de dados ao criar o conjunto de dimensionamento ou em um já existente.

> [!NOTE]
>  Ao criar um conjunto de dimensionamento com discos de dados anexado, você precisa montar e formatar os discos de dentro de uma VM para usá-los (assim como para VMs autônomas do Azure). Uma maneira conveniente de concluir esse processo é usar uma Extensão de script personalizado que chama um script para particionar e formatar todos os discos de dados em uma VM. Para obter exemplos disso, consulte [CLI do Azure 2.0](tutorial-use-disks-cli.md#prepare-the-data-disks) e [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Criar e gerenciar discos em um conjunto de dimensionamento
Para obter informações detalhadas sobre como criar um conjunto de dimensionamento com discos de dados anexado, preparar e formatar ou adicionar e remover discos de dados, consulte um dos seguintes tutoriais:

- [CLI 2.0 do Azure](tutorial-use-disks-cli.md)
- [PowerShell do Azure](tutorial-use-disks-powershell.md)

O restante deste artigo descreve os casos de uso específico, como clusters do Service Fabric que requerem discos de dados ou anexar discos de dados existente com conteúdo para um conjunto de dimensionamento.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Criar um cluster do Service Fabric com discos de dados anexados
Cada [tipo de nó](../service-fabric/service-fabric-cluster-nodetypes.md) em um cluster do [Service Fabric](/azure/service-fabric) sendo executado no Azure é respaldado por um conjunto de dimensionamento de máquinas virtuais.  Usando um modelo do Azure Resource Manager, é possível anexar discos de dados para os conjuntos de dimensionamento que compõem o cluster do Service Fabric. É possível usar um [modelo existente](https://github.com/Azure-Samples/service-fabric-cluster-templates) como um ponto de partida. No modelo, inclua uma seção _dataDisks_ no _storageProfile_ dos recursos _Microsoft.Compute/virtualMachineScaleSets_ e implante o modelo. O exemplo a seguir anexa um disco de dados de 128 GB:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

É possível particionar, formatar e montar os discos de dados automaticamente quando o cluster for implantado.  Adicione uma extensão de script personalizada para o _extensionProfile_ do _virtualMachineProfile_ dos conjuntos de dimensionamento.

Para preparar os discos de dados automaticamente em um cluster do Windows, adicione o seguinte:

```json
{
    "name": "customScript",    
    "properties": {    
        "publisher": "Microsoft.Compute",    
        "type": "CustomScriptExtension",    
        "typeHandlerVersion": "1.8",    
        "autoUpgradeMinorVersion": true,    
        "settings": {    
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Para preparar os discos de dados automaticamente em um cluster do Linux, adicione o seguinte:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>Adicionar discos de dados preenchidos previamente a um conjunto de dimensionamento existente 
> Quando você adiciona discos a um modelo do conjunto de dimensionamento existente, por padrão, o disco sempre será criado vazio. Este cenário também inclui novas instâncias criadas pelo conjunto de dimensionamento. Esse comportamento ocorre porque a definição do conjunto de dimensionamento tem um disco de dados vazio. Para criar unidades de dados preenchidas previamente para um modelo do conjunto de dimensionamento existente, você pode escolher qualquer uma das duas opções:

* copie os dados da VM de instância 0 para o(s) disco(s) de dados em outras VMs executando um script personalizado.
* Crie uma imagem gerenciada com o disco do SO mais o disco de dados (com os dados necessários) e crie um novo conjunto de dimensionamento com a imagem. Dessa forma, cada nova VM criada tem um disco de dados fornecido na definição do conjunto de dimensionamento. Como essa definição faz referência a uma imagem com um disco de dados que tem dados personalizados, todas as máquinas virtuais no conjunto de dimensionamento terão essas alterações.

> A maneira de criar uma imagem personalizada pode ser encontrada aqui: [Criar uma imagem gerenciada de uma VM generalizada no Azure](/azure/virtual-machines/windows/capture-image-resource/) 

> O usuário precisa capturar a VM de instância 0 que tem os dados necessários, em seguida, usar esse vhd para a definição da imagem.


## <a name="additional-notes"></a>Observações adicionais
O suporte a discos Gerenciados pelo Azure e a discos de dados anexados do conjunto de dimensionamento está disponível na versão [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) ou posterior da API Microsoft.Compute.

A implementação inicial do suporte de disco anexado para conjuntos de escala, você não pode anexar ou desanexar discos de dados de VMs individuais em um conjunto de escala.

Suporte do portal do Azure para discos de dados anexados em conjuntos de escala é limitada inicialmente. Dependendo dos seus requisitos, você pode usar modelos do Azure, CLI, PowerShell, SDKs e API REST para gerenciar os discos anexados.


