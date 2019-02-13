---
title: Planejamento para o Azure Stack de capacidade de armazenamento | Microsoft Docs
description: Saiba mais sobre armazenamento planejamento de capacidade para implantações do Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 09/18/2018
ms.openlocfilehash: 29244c20bb4bbad8077788abbc29e6267f701d2e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176333"
---
# <a name="azure-stack-storage-capacity-planning"></a>Planejamento de capacidade de armazenamento de pilha do Azure
As seções a seguir fornecem a capacidade de armazenamento do Azure Stack informações de planejamento para ajudar no planejamento de necessidades de armazenamento de soluções.

## <a name="uses-and-organization-of-storage-capacity"></a>Usos e organização de capacidade de armazenamento
A configuração hiperconvergente do Azure Stack permite o compartilhamento de dispositivos de armazenamento físico. São três divisões principais do armazenamento disponível entre a infraestrutura de armazenamento temporário de máquinas virtuais do locatário e o backup de blobs, tabelas e filas dos serviços de armazenamento do Azure consistente (ACS) de armazenamento.

## <a name="spaces-direct-cache-and-capacity-tiers"></a>Cache e os níveis de capacidade de espaços diretos
Não há capacidade de armazenamento usada para o sistema operacional, registro em log local, despejos de memória e outro armazenamento temporário de infra-estrutura precisa. Essa capacidade de armazenamento local é separar (dispositivos e capacidade) de dispositivos de armazenamento colocados sob gerenciamento da configuração de espaços de armazenamento diretos. O restante dos dispositivos de armazenamento é colocado em um único pool de capacidade de armazenamento, independentemente do número de servidores na unidade de escala. Esses dispositivos são de dois tipos: O cache e capacidade.  Os dispositivos de Cache são apenas – Cache. Espaços diretos consumirá esses dispositivos para write-back e cache de leitura. As capacidades desses dispositivos de Cache, embora usado, não serão confirmadas à capacidade formatada, "visível" dos formatada-discos virtuais. Os dispositivos de capacidade são usados para essa finalidade e fornecem o "local de residência" dos dados gerenciados por espaços de armazenamento.

Toda a capacidade de armazenamento é alocada e gerenciada diretamente pela infraestrutura do Azure Stack. O operador precisa tomar decisões sobre a configuração, a alocação, ou lidar com opções quando se trata de uma expansão de capacidade. Essas decisões de design foram feitas para alinhar com os requisitos da solução e são automatizados durante a instalação/implantação inicial ou durante a expansão da capacidade. Detalhes sobre a resiliência, capacidade reservada para recompilações e outros detalhes foram consideradas como parte do design. 

Operadores podem escolher entre um todos os flash ou uma configuração de armazenamento híbrido:

![Planejamento de capacidade de armazenamento do Azure](media/azure-stack-capacity-planning/storage.png)

Na configuração de todos os flash, o cache é NVMe com uma opção de SSD SATA ou NVMe para capacidade. Na configuração híbrida, o cache é uma escolha entre NVMe e SATA SSD, enquanto a capacidade é a unidade de disco rígido.

Um breve resumo dos espaços de armazenamento diretos e configuração de armazenamento do Azure Stack é o seguinte:
- Um Pool de espaços de armazenamento por unidade de escala (todos os dispositivos de armazenamento são configurados com um único pool)
- Discos virtuais são criados como um espelho de cópia três para melhor desempenho e resiliência
- Cada disco virtual é formatado como um sistema de arquivos ReFS
- Capacidade do disco virtual é calculada e alocada em uma maneira para deixar o valor do dispositivo de uma capacidade de capacidade de dados não alocada no pool. Isso é o equivalente de uma unidade de capacidade por servidor.
- Cada sistema de arquivos ReFS terá o BitLocker habilitado para criptografia de dados em repouso. 

Os-discos virtuais criados automaticamente e suas capacidades são da seguinte maneira:

|NOME|Cálculo de capacidade|DESCRIÇÃO|
|-----|-----|-----|
|Dispositivo de inicialização/local|Mínimo de 340 GB<sup>1</sup>|Armazenamento de servidor individuais para VMs de infraestrutura "local" e imagens do sistema operacional|
|Infraestrutura|3.5 TB|Todos os usos de infraestrutura do Azure Stack|
|VmTemp|Veja abaixo<sup>2</sup>|Máquinas virtuais de locatário tem um disco temporário conectado e que os dados são armazenados nesses discos virtuais|
|ACS|Veja abaixo <sup>3</sup>|Capacidade de armazenamento consistente do Azure para blobs, tabelas e filas de serviço|

<sup>1</sup> capacidade de armazenamento mínimo necessária do parceiro de solução do Azure Stack.

<sup>2</sup> o tamanho de disco virtual usado para discos temporários de máquina Virtual de locatário é calculado como uma proporção da memória física do servidor. Conforme observado nas tabelas a seguir para os tamanhos de VM IaaS do Azure, o disco temporário é uma proporção da memória física atribuída à máquina virtual. A alocação feita para o armazenamento de "disco temporário" no Azure Stack será feita de uma maneira que captura a maioria dos casos de uso, mas talvez não consiga atender a todas as necessidades de armazenamento de disco temporário. A proporção escolhida é uma compensação entre a disponibilizar o armazenamento temporário enquanto não consome a maioria da capacidade de armazenamento da solução para apenas a capacidade de disco temporário. Um disco de armazenamento temporário é criado por servidor em que a unidade de escala. A capacidade do armazenamento temporário não ultrapassem 10% da capacidade de armazenamento geral disponível no pool de armazenamento da unidade de escala. O cálculo é algo parecido com o exemplo a seguir:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> os-discos virtuais criados para uso pelo ACS são uma divisão simple da capacidade restante. Conforme observado, todos os-discos virtuais são um espelho de três vias e não está alocada a partir de uma capacidade das unidades de capacidade para cada servidor. Os vários-discos virtuais enumerados acima são alocados primeiro e a capacidade restante, em seguida, é usada para o ACS de discos virtuais.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre a planilha de planejamento de capacidade do Azure Stack](capacity-planning-spreadsheet.md)
