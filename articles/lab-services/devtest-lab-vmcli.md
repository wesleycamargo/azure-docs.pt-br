---
title: Criar e gerenciar máquinas virtuais em DevTest Labs com a CLI do Azure | Microsoft Docs
description: Saiba como usar o Azure DevTest Labs para criar e gerenciar máquinas virtuais com a CLI do Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: ccf9b08856fcc652e3ad4b2b31587d43d7ef9cca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995935"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Criar e gerenciar máquinas virtuais com DevTest Labs usando a CLI do Azure
Este guia rápido ajudará você a criar, iniciar, conectar, atualizar e limpar um computador de desenvolvimento em seu laboratório. 

Antes de começar:

* Se um laboratório não tiver sido criado, as instruções poderão ser encontradas [aqui](devtest-lab-create-lab.md).

* [Instale a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Para iniciar, execute az login para criar uma conexão com o Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Criar e verificar a máquina virtual 
Crie uma máquina virtual de uma imagem do marketplace com autenticação ssh.
```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```
> [!NOTE]
> Coloque o nome do **grupo de recursos do laboratório** no parâmetro --resource-group.
>

Se você quiser criar uma máquina virtual usando uma fórmula, use o parâmetro --formula em [az lab vm create](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create).


Verifique se a VM está disponível.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Iniciar e conectar-se à máquina virtual
Inicie uma VM.
```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```
> [!NOTE]
> Coloque o nome do **grupo de recursos do laboratório** no parâmetro --resource-group.
>

Conectar-se a uma VM: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) ou [Área de Trabalho Remota](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Atualizar a máquina virtual
Aplique artefatos a uma VM.
```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

Lista de artefatos disponíveis no laboratório.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>Parar e excluir a máquina virtual    
Pare uma VM.
```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Exclua uma VM.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
