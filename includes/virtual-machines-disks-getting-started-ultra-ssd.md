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
ms.openlocfilehash: b28830ac2634ad2238d834238de83c9184bcd6f0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47010384"
---
# <a name="enabling-azure-ultra-ssds"></a>Habilitando os SSDs Ultra do Azure

O SSD Ultra do Azure proporciona alta taxa de transferência, alta IOPS e armazenamento em disco de baixa latência consistente para VMs de IaaS do Azure. Essa nova oferta fornece o melhor em desempenho de linha, nos mesmos níveis de disponibilidade que nossas ofertas de discos atuais. Os benefícios adicionais do SSD Ultra incluem a capacidade de alterar dinamicamente o desempenho do disco, juntamente com suas cargas de trabalho, sem a necessidade de reiniciar as máquinas virtuais. O SSD Ultra é adequado para cargas de trabalho de grande volume de dados, como SAP HANA, bancos de dados de camada superior e cargas de trabalho de transações pesadas.

No momento, os SSds Ultra estão em versão prévia e é necessário [registrar-se](https://aka.ms/UltraSSDPreviewSignUp) nela para acessá-los.

Após a aprovação, execute um dos seguintes comandos para determinar em qual zona no Leste dos EUA 2 você deseja implantar seu SSD Ultra:

PowerShell: `Get-AzureRmComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI: `az vm list-skus --resource-type disks --query “[?name==’UltraSSD_LRS’]”`

A resposta será semelhante à forma abaixo, em que X é a zona a ser usada para implantação no Leste dos EUA 2. X pode ser 1, 2 ou 3.

|ResourceType  |NOME  |Local padrão  |Zonas  |Restrição  |Recurso  |Valor  |
|---------|---------|---------|---------|---------|---------|---------|
|discos     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

Se não houve resposta do comando, significa que seu registro no recurso ainda está pendente ou não foi aprovado ainda.

Agora que você sabe em qual zona implantar, siga as etapas de implantação neste artigo para implantar suas primeiras VMs com discos SSD Ultra.

## <a name="deploying-an-ultra-ssd"></a>Implantando um SSD Ultra

Primeiro, determine o tamanho da VM a ser implantada. Como parte dessa versão prévia, há suporte apenas para as famílias de VM DsV3 e EsV3. Confira a segunda tabela neste [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) para obter mais detalhes sobre esses tamanhos de VM.
Confira também o [Create a VM with multiple Ultra SSD disks](https://aka.ms/UltraSSDTemplate) (Criar uma VM com vários discos SSD Ultra) de exemplo, que mostra como criar uma VM com vários discos SSD Ultra.

A seguir, são descritas as alterações novas/modificadas no modelo do Resource Manager: **apiVersion** para `Microsoft.Compute/virtualMachines` e `Microsoft.Compute/Disks` deve ser definido como `2018-06-01` (ou posterior).

Especifique o Sku do disco UltraSSD_LRS, a capacidade do disco, IOPS e taxa de transferência em MBps para criar um disco SSD Ultra. A seguir há um exemplo que cria um disco com 1.024 GiB (GiB = 2^30 bytes), 80.000 IOPS e 1.200 MBps (MBps = 10^6 Bytes por segundo):

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

Adicione uma funcionalidade adicional nas propriedades da VM para indicar seu SSD Ultra Habilitado (consulte o [exemplo](https://aka.ms/UltraSSDTemplate) para obter o modelo do Resource Manager completo):

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

## <a name="additional-ultra-ssd-scenarios"></a>Outros cenários de SSD Ultra

- Durante a criação da VM, os SSDs Ultra podem ser criados implicitamente também. No entanto, esses discos receberão um valor padrão de IOPS (500) e taxa de transferência (8 MiB/s).
- Outros SSDs Ultra podem ser anexados às VMs compatíveis com o SSD Ultra.
- O SSD Ultra dá suporte ao ajuste dos atributos de desempenho do disco (IOPS e taxa de transferência) em tempo de execução sem desanexar o disco da máquina virtual. Depois que uma operação de redimensionamento do desempenho do disco tiver sido emitida em um disco, poderá levar até uma hora para que a alteração entre em vigor efetivamente.
- O aumento da capacidade do disco requer que uma máquina virtual seja desalocada.

# <a name="next-steps"></a>Próximas etapas

Se desejar experimentar o novo tipo de disco e ainda não tiver inscrito na versão prévia, [solicite o acesso por meio desta pesquisa](https://aka.ms/UltraSSDPreviewSignUp).