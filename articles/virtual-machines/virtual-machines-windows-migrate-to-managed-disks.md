---
title: Migrar VMs do Azure para o Managed Disks | Microsoft Docs
description: "Migre máquinas virtuais do Azure criadas com discos não gerenciados em contas de armazenamento a fim de usar o Managed Disks."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: e5f2eaa55e92c9a630533a5594e1a659cc2192f1
ms.openlocfilehash: 390195c8f07430e5fac6d53a77f6bb1aba53b197


---

# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrar VMs do Azure para o Managed Disks no Azure

O Azure Managed Disks elimina a necessidade de gerenciar [contas de armazenamento](../storage/storage-introduction.md) para VMs do Azure. Você só precisa especificar o tipo [Premium](../storage/storage-premium-storage-performance.md) ou [Standard](../storage/storage-standard-storage.md) e o tamanho do disco que você precisa, e o Azure criará e gerenciará o disco para você. 

Se você estiver usando a opção de armazenamento standard para seus discos, migre para o Premium Managed Disks e aproveite a velocidade e o desempenho desses discos. [Premium Managed Disks](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage-performance) é armazenado em SSD (Unidade de Estado Sólido), que fornece alto desempenho, suporte a disco de baixa latência para máquinas virtuais com cargas de trabalho de E/S intensas.

É possível migrar para o Managed Disks nos cenários a seguir:

| Migrar...                                            | Link da documentação                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| VMs em um conjunto de disponibilidade que usam discos não gerenciados para o Managed Disks   | [Converter VMs em um conjunto de disponibilidade a fim de usar discos gerenciados](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set)                                                                        |
| Discos não gerenciados Premium para discos gerenciados Premium   | [Converter as VMs do Azure existentes para discos gerenciados do mesmo tipo de armazenamento](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type)                                                                         |
| Discos não gerenciados Standard para discos gerenciados Standard | [Converter as VMs do Azure existentes para discos gerenciados do mesmo tipo de armazenamento](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type)                                                                         |
| Discos não gerenciados Standard para discos gerenciados Premium  | [Migrar VMs do Azure que usam discos não gerenciados padrão para o Premium Managed Disks](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#migrate-existing-azure-vms-using-standard-unmanaged-disks-to-premium-managed-disks)                            |
| Uma única VM do modo clássico para o modo do Resource Manager em discos gerenciados     | [Migrar uma única VM](virtual-machines-windows-migrate-single-classic-to-resource-manager.md)  | 
| Todas as VMs em uma vNet do modo clássico para o modo do Resource Manager em discos gerenciados     | [Migrar os recursos de IaaS do modo clássico para o modo do Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md) e [Converter uma VM de discos não gerenciados para discos gerenciados](virtual-machines-windows-convert-unmanaged-to-managed-disks.md) | 


## <a name="overview-of-managed-disks"></a>Visão Geral do Managed Disks

O Azure Managed Disks simplifica o gerenciamento do armazenamento, acabando com a necessidade de gerenciar as contas de armazenamento de forma separada.  Também é possível migrar suas VMs do Azure para o Managed Disks para aproveitar a melhor confiabilidade das VMs em um conjunto de disponibilidade. Isso garante um isolamento suficiente dos discos de VMs diferentes em um conjunto de disponibilidade a fim de evitar pontos de falhas. Ele coloca automaticamente os discos de diferentes VMs em um Conjunto de disponibilidade em unidades de escala (carimbos) de Armazenamentos diferentes, o que limita o impacto de falhas em uma unidade de escala de Armazenamento causadas por falhas de hardware e de software. Com base em suas necessidades, você pode escolher entre dois tipos de opções de armazenamento: 
 
- [Premium Managed Disks](../storage/storage-premium-storage.md) é uma mídia de armazenamento com base em SSD (Unidade de Estado Sólido), que fornece alto desempenho, suporte a disco de baixa latência para máquinas virtuais com cargas de trabalho de E/S intensas. Você pode tirar proveito da velocidade e do desempenho desses discos migrando o Premium Managed Disks.

- [Standard Managed Disks](../storage/storage-standard-storage.md) usa mídia de armazenamento com base em HDD (Unidade de Disco Rígido) e são apropriados para desenvolvimento/teste e outras cargas de trabalho de acesso infrequente menos sensíveis à variabilidade de desempenho. 


## <a name="plan-for-the-conversion-to-managed-disks"></a>Planejar-se para a conversão para Managed Disks

Esta seção ajuda você a tomar a melhor decisão sobre VM e tipos de disco.


## <a name="location"></a>Local padrão

Escolha um local onde o Azure Managed Disks estão disponíveis. Se você estiver mudando para o Premium Managed Disks, verifique também se o armazenamento Premium está disponível na região para onde você planeja mudar. Confira [Serviços do Azure por região](https://azure.microsoft.com/regions/#services) para obter informações atualizadas sobre as localizações disponíveis.

## <a name="vm-sizes"></a>Tamanhos de VM

Se você estiver migrando para o Premium Managed Disks, será necessário atualizar o tamanho da VM para um tamanho compatível com o Armazenamento Premium disponível na região onde a VM está localizada. Examine os tamanhos de VM que são compatíveis com o Armazenamento Premium. As especificações de tamanho de VM do Azure são listadas em [Tamanhos para máquinas virtuais](virtual-machines-windows-sizes.md).
Examine as características de desempenho das máquinas virtuais que funcionam com o Armazenamento Premium e escolha o tamanho de VM mais apropriado que melhor atende à sua carga de trabalho. Certifique-se de que há largura de banda suficiente disponível na sua VM para direcionar o tráfego de disco.

## <a name="disk-sizes"></a>Tamanhos do disco

**Premium Managed Disks**

Há três tipos de Premium Managed Disks que podem ser usados com a VM, e cada um tem IOPs e limites de taxa de transferência específicos. Leve em consideração esses limites ao escolher o tipo de disco Premium para sua VM com base nas necessidades de seu aplicativo em termos de capacidade, desempenho, escalabilidade e cargas de pico.

| Tipo de discos Premium  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| Tamanho do disco           | 128 GB            | 512 GB            | 1024 GB (1 TB)    |
| IOPS por disco       | 500               | 2.300              | 5.000              |
| Taxa de transferência por disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo |

**Standard Managed Disks**

Há cinco tipos de Standard Managed Disks que podem ser usados com sua VM. Cada um deles tem uma capacidade diferente, mas com os mesmos limites de taxa de transferência e IOPS. Escolha o tipo de Standard Managed Disks com base nas necessidades de capacidade de seu aplicativo.

| Tipo de disco Standard  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| Tamanho do disco           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   |
| IOPS por disco       | 500              | 500              | 500              | 500              | 500              |
| Taxa de transferência por disco | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo |

## <a name="disk-caching-policy"></a>Política de cache de disco 

**Premium Managed Disks**

Por padrão, a política de cache de disco é *Somente leitura* para todos os discos de dados Premium e *Leitura e gravação* para o disco de sistema operacional Premium anexado à VM. Esta definição de configuração é recomendável para atingir o desempenho ideal de leituras de entrada e saída dos seus aplicativos. Para discos de dados de gravação intensa ou somente gravação (como arquivos de log do SQL Server), desabilite o cache de disco para que possa obter o melhor desempenho do aplicativo.

## <a name="pricing"></a>Preços

Confira os [preços do Managed Disks](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). O preços do Premium Managed Disks são iguais aos do Premium Unmanaged Disks. No entanto, os preços do Standard Managed Disks são diferentes dos preços do Standard Unmanaged Disks.



## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Managed Disks](../storage/storage-managed-disks-overview.md)



<!--HONumber=Feb17_HO2-->


