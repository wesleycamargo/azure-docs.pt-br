---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3b596e5bad8202d88ea06c7eee114bec1063a35f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075669"
---
# <a name="enabling-azure-ultra-ssds"></a>Habilitando o SSDs ultra do Azure

O ultra SSD do Azure proporciona uma alta taxa de transferência, alta IOPS e armazenamento em disco de baixa latência e consistente para as VMs de IaaS do Azure. Essa nova oferta fornece o melhor em desempenho de linha, nos mesmos níveis de disponibilidade que nossas ofertas de discos atuais. Os benefícios adicionais do ultra SSD incluem a capacidade de alterar dinamicamente o desempenho do disco, junto com suas cargas de trabalho, sem a necessidade de reiniciar as máquinas virtuais. O ultra SSD é adequado a cargas de trabalho de grande volume de dados, como SAP HANA, bancos de dados de camada superior e cargas de trabalho de transações pesadas.

No momento, o ultra SSD está na versão prévia e é necessário [registrar-se](https://aka.ms/UltraSSDPreviewSignUp) para acessá-lo.

Após a aprovação, execute um dos seguintes comandos para determinar em qual zona no Leste dos EUA 2 implantar o ultradisco:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI: `az vm list-skus --resource-type disks --query “[?name==’UltraSSD_LRS’]”`

A resposta será semelhante à forma abaixo, em que X é a zona a ser usada para implantação no Leste dos EUA 2. X pode ser 1, 2 ou 3.

|ResourceType  |NOME  |Local padrão  |Zonas  |Restrição  |Recurso  |Value  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

Se não houve resposta do comando, significa que seu registro no recurso ainda está pendente ou não foi aprovado ainda.

Agora que você sabe em qual zona implantar, siga as etapas neste artigo para fazer sua primeira implantação de VMs com o ultra SSD.

## <a name="deploying-an-ultra-ssd"></a>Implantando um ultra SSD

Primeiro, determine o tamanho da VM a ser implantada. Como parte dessa versão prévia, há suporte apenas para as famílias de VM DsV3 e EsV3. Confira a segunda tabela neste [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) para obter mais detalhes sobre esses tamanhos de VM.
Consulte também o exemplo em [Criar uma VM com um ultra SSD múltiplo](https://aka.ms/UltraSSDTemplate), que mostra como criar uma VM com um ultra SSD múltiplo.

A seguir, são descritas as alterações novas/modificadas no modelo do Resource Manager: **apiVersion** para `Microsoft.Compute/virtualMachines` e `Microsoft.Compute/Disks` deve ser definido como `2018-06-01` (ou posterior).

Especifique o Sku do Disco UltraSSD_LRS, a capacidade do disco, a IOPS e a taxa de transferência em MBps para criar um ultradisco. A seguir há um exemplo que cria um disco com 1.024 GiB (GiB = 2^30 bytes), 80.000 IOPS e 1.200 MBps (MBps = 10^6 Bytes por segundo):

```json
"properties": {  
    "creationData": {  
    "createOption": "Empty"  
},  
"diskSizeGB": 1024,  
"diskIOPSReadWrite": 80000,  
"diskMBpsReadWrite": 1200,  
}
```

Adicione uma funcionalidade extra nas propriedades da VM para indicar seu ultra SSD habilitado (consulte o [exemplo](https://aka.ms/UltraSSDTemplate) para obter o modelo do Resource Manager completo):

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "properties": {
                    "hardwareProfile": {},
                    "additionalCapabilities" : {
                                    "ultraSSDEnabled" : "true"
                    },
                    "osProfile": {},
                    "storageProfile": {},
                    "networkProfile": {}
    }
}
```

Depois que a VM for provisionada, será possível particionar e formatar os discos de dados e configurá-los para suas cargas de trabalho.

## <a name="additional-ultra-ssd-scenarios"></a>Outros cenários do ultra SSD

- Durante a Criação da VM, o ultra SSD pode ser criado implicitamente também. No entanto, esses discos receberão um valor padrão de IOPS (500) e taxa de transferência (8 MiB/s).
- Outro ultra SSD pode ser anexado às VMs compatíveis.
- O ultra SSD dá suporte ao ajuste dos atributos de desempenho do disco (IOPS e taxa de transferência) durante a execução sem desanexar o disco da máquina virtual. Depois que uma operação de redimensionamento de desempenho do disco tiver sido emitida em um disco, poderá levar até uma hora para que a alteração entre em vigor efetivamente.
- O aumento da capacidade do disco requer que uma máquina virtual seja desalocada.

## <a name="next-steps"></a>Próximas etapas

Se desejar experimentar o novo tipo de disco e ainda não tiver inscrito na versão prévia, [solicite o acesso por meio desta pesquisa](https://aka.ms/UltraSSDPreviewSignUp).
