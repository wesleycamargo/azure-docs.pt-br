---
title: Escolher tamanhos de VM em pools do Lote do Azure | Microsoft Docs
description: "Como escolher entre os tamanhos de VM disponíveis para nós de computação em pools de Lote do Azure"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: danlep
ms.openlocfilehash: addd1e9314a754b40cc5d49c0299f007580f512f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2018
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Escolher um tamanho de VM para nós de computação em um pool do Lote do Azure

Ao selecionar um tamanho de nó para um pool de Lote do Azure, você pode escolher dentre quase todos os tamanhos de VM disponíveis no Azure. O Azure oferece uma variedade de tamanhos para VMs do Linux e do Windows para diferentes cargas de trabalho. 

Há algumas exceções e limitações para escolher um tamanho de VM:
* Não há suporte no Lote para algumas famílias de VM ou tamanhos de VM. 
* Alguns tamanhos de VM são restritos e precisam ser especificamente habilitados antes de serem alocados.


## <a name="supported-vm-families-and-sizes"></a>Famílias e tamanhos de VM com suporte

### <a name="pools-in-virtual-machine-configuration"></a>Pools na configuração de Máquina Virtual

Os pools de lote na configuração da máquina virtual oferecem suporte a todos os tamanhos de VM ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)) *exceto* para:

| Família  | Tamanhos sem suporte  |
|---------|---------|
| Série A básica | Basic_A0 (A0) |
| Séria A | Standard_A0 |
| Série B | Todos |
| Série Fsv2 <sup>*</sup> | Todos |

<sup>*</sup>Os tamanhos nessa série estão em nossos planos para suporte futuro.

### <a name="pools-in-cloud-service-configuration"></a>Pools na configuração de Serviço de Nuvem

Os pools de lote na configuração do Serviço de Nuvem oferecem suporte a todos os [tamanhos de VM para Serviços de Nuvem](../cloud-services/cloud-services-sizes-specs.md) *exceto* para:

| Família  | Tamanhos sem suporte  |
|---------|---------|
| Séria A | ExtraSmall |
| Série Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="restricted-vm-families"></a>Famílias de VM restritas
As famílias de VM a seguir podem ser alocadas em pools de lote, mas você deve solicitar um aumento de cota específico (consulte [este artigo](batch-quota-limit.md#increase-a-quota)):
* Série NCv2
* Série NCv3
* Série ND

Esses tamanhos só podem ser usados em pools na configuração da Máquina Virtual.

## <a name="size-considerations"></a>Considerações de tamanhos

* **Requisitos do aplicativo** - considere as características e os requisitos dos aplicativos que você vai executar nos nós. Os aspectos como se o aplicativo tem multithread e quanta memória ele consome podem ajudar a determinar o tamanho do nó mais adequado e econômico. Para [cargas de trabalho de MPI](batch-mpi.md) de instâncias múltiplas ou aplicativos CUDA, considere tamanhos de VM de [HPC](../virtual-machines/linux/sizes-hpc.md) especializado ou [habilitado para GPU](../virtual-machines/linux/sizes-gpu.md), respectivamente. (Confira [Usar instâncias compatíveis com RDMA ou habilitadas para GPU em pools do Lote](batch-pool-compute-intensive-sizes.md).) 

* **Tarefas por nó** - Geralmente, você seleciona um tamanho de nó supondo que uma tarefa seja executada no nó por vez. No entanto, pode ser vantajoso ter várias tarefas (e, portanto, várias instâncias do aplicativo) [executadas em paralelo](batch-parallel-node-tasks.md) em nós de computação durante a execução do trabalho. Nesse caso, é comum escolher um tamanho multicore de nó para acomodar a demanda crescente de execução de tarefas paralelas.

* **Carregar níveis para tarefas diferentes** - Todos os nós em um pool têm o mesmo tamanho. Se você pretende executar aplicativos com diferentes requisitos de sistema e/ou níveis de carga, é recomendável usar pools separados. 

* **Disponibilidade de região** – Uma família ou tamanho de VM pode não estar disponível nas regiões onde você cria suas contas do Lote. Para verificar se um tamanho está disponível, consulte [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

* **Cotas** - As [cotas principais](batch-quota-limit.md#resource-quotas) na sua conta do Lote podem limitar o número de nós de um determinado tamanho que você pode adicionar a um pool do Lote. Para solicitar um aumento de cota, consulte [este artigo](batch-quota-limit.md#increase-a-quota). 

* **Configuração do pool** - Em geral, você tem mais opções de tamanho de VM quando você cria um pool na configuração da Máquina Virtual, em comparação com a configuração do serviço de nuvem.

## <a name="next-steps"></a>Próximas etapas

* Para uma visão geral detalhada do Lote, confira [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md).
* Para obter informações sobre tamanhos de VM de computação intensiva, consulte [Usar instâncias compatíveis com RDMA ou habilitadas para GPU em pools do Lote](batch-pool-compute-intensive-sizes.md). 


