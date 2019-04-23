---
title: Importar VMs de outro laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como importar as máquinas virtuais de outro laboratório para o laboratório atual no Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: cb4a3ec9be82957b4c0366ec232f1147c52d0251
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60148765"
---
# <a name="import-vms-from-another-lab-in-azure-devtest-labs"></a>Importar VMs de outro laboratório no Azure DevTest Labs
O serviço Azure DevTest Labs melhora significativamente o gerenciamento de VMs (máquinas virtuais) para atividades de desenvolvimento e teste. Ele permite que você mova uma VM de um laboratório para outro conforme os requisitos da equipe ou da infraestrutura se alteram. Aqui estão alguns cenários comuns em que pode ser necessário fazer isso:

- Uma pessoa da equipe passa para outro grupo dentro da empresa e deseja levar as VMs de desenvolvimento para o laboratório da nova equipe.
- O grupo atingiu a cota no nível da assinatura e deseja dividir as equipes em várias assinaturas.
- A empresa planeja passar para o Express Route (ou para alguma outra nova topologia de rede) e a equipe quer mover VMs para usar essa nova infraestrutura.

## <a name="solution-and-constraints"></a>Solução e restrições
O Azure DevTest Labs permite que um proprietário do laboratório importe as VMs de um laboratório de origem para um laboratório de destino. O proprietário do laboratório pode, opcionalmente, fornecer um novo nome para a VM de destino no processo. O processo de importação inclui todas as dependências como discos, agendamentos, configurações de rede, etc. O processo demora um pouco e é afetado pelo número/tamanho dos discos anexados ao computador de origem e pela distância até o destino (por exemplo, da região Leste dos EUA para a região Sudeste Asiático). Quando o processo de importação é concluído, a VM de origem permanece desligada e a nova fica em execução no laboratório de destino.

Há duas restrições de chave a serem consideradas ao planejar a importação de VMs para outro laboratório:

- Há suporte para a importação de VMs entre assinaturas e entre regiões, mas as assinaturas precisam ser associadas ao mesmo locatário do Azure Active Directory.
- As VMs não podem estar em um estado requisitável no laboratório de origem.

Além disso, para poder importar uma VM de um laboratório para outro, você precisa ser o proprietário da VM no laboratório de origem e o proprietário do laboratório de destino.

## <a name="steps-to-import-a-vm-from-another-lab"></a>Etapas para importar uma VM de outro laboratório
No momento, você pode importar uma VM de um laboratório para outro apenas usando o Azure PowerShell e a API REST.

### <a name="use-powershell"></a>Usar o PowerShell
Baixe o arquivo de script do PowerShell ImportVirtualMachines.ps1 do [repositório Git do Azure DevTest Lab](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines) em sua unidade local.

#### <a name="import-a-single-vm"></a>Importar uma única VM
Execute o script ImportVirtualMachines.ps1 para importar uma única VM de um laboratório de origem para um laboratório de destino. Você pode especificar um novo nome para a VM que está sendo copiada usando o parâmetro DestinationVirtualMachineName.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -SourceVirtualMachineName "<Name of the machine. Optional. If not specified, all VMs are copied>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VM is copied>" `
                            -DestinationVirtualMachineName "<New name for the VM. Optional>"
```


#### <a name="importing-all-vms"></a>Importando todas as VMs
Ao executar o script ImportVirtualMachines.ps1, se você não especificar uma VM no laboratório de origem, o script importará todas as VMs no laboratório de origem para o laboratório de destino.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VMs are copied>"
```

### <a name="use-rest-api"></a>Usar a API REST
Invoque a API REST em relação ao laboratório de destino e passe o laboratório de origem, a assinatura e as informações da VM como parâmetros, conforme mostrado no exemplo a seguir:

```json
POST https://management.azure.com/subscriptions/<ID of the target/destination subscription>/resourceGroups/<Name of the resource group that contains the destination lab>/providers/Microsoft.DevTestLab/labs/<Name of the lab to which the VMs are copied>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<ID of the subscription that contains the source VM>/resourcegroups/<Name of the resource group that contains the source lab>/providers/microsoft.devtestlab/labs/<Name of the lab that contains the source VM>/virtualmachines/MyVm",
   destinationVirtualMachineName: "MyVmNew"
}
```

## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre o redimensionamento de uma VM, confira [Redimensionar uma VM](devtest-lab-resize-vm.md).
- Para obter informações sobre como reimplantar uma VM, confira [Reimplantar uma VM](devtest-lab-redeploy-vm.md).
