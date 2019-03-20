---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: 09b4f94db3464943a8367bfb3ca89f9a88446193
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553258"
---
Você pode anexar um número de discos de dados para uma máquina virtual do Azure. Com base nas metas de escalabilidade e desempenho para discos de dados da VM, você pode determinar o número e tipo de disco que você precisa para atender aos seus requisitos de capacidade e desempenho.

> [!IMPORTANT]
> Para obter o desempenho ideal, limite a quantidade de discos altamente utilizados anexados à máquina virtual para evitar possíveis limitações. Se todos os discos conectados não são altamente utilizados ao mesmo tempo, a máquina virtual pode dar suporte a um número maior de discos.

* **Discos gerenciados do Azure:** 

> | Recurso | Limite padrão | Limite máximo |
> | --- | --- | --- |
> | Standard Managed Disks | 10.000 | 50.000 |
> | Discos gerenciados SSD Standard | 10.000 | 50.000 |
> | Discos gerenciados Premium | 10.000 | 50.000 |
> | Standard_LRS instantâneos | 10.000 | 50.000 |
> | Standard_ZRS instantâneos | 10.000 | 50.000 |
> | Imagem gerenciada | 10.000 | 50.000 |

* **Para contas de armazenamento padrão:** Uma conta de armazenamento Standard tem uma taxa de solicitação total máxima de 20.000 IOPS. O IOPS total em todos os discos de máquina virtual em uma conta de armazenamento padrão não deve exceder esse limite.
  
    Basicamente, você pode calcular o número de discos altamente utilizados compatíveis com uma conta de armazenamento Standard com base no limite de taxa de solicitação. Por exemplo, para uma VM de camada básica, o número máximo de discos altamente utilizados é aproximadamente 66, que é 20.000/300 IOPS por disco. O número máximo de discos altamente utilizados para uma VM de camada Standard é de aproximadamente 40, que é 20.000/500 IOPS por disco. 

* **Para contas de armazenamento Premium:** Uma conta de armazenamento Premium tem uma taxa de transferência total máxima de 50 Gbps. A taxa de transferência total de todos os discos da VM não deve exceder esse limite.

