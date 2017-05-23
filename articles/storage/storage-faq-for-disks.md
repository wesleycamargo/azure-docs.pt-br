---
title: Perguntas frequentes (FAQ) sobre Discos de VM IaaS do Azure | Microsoft Docs
description: "Perguntas frequentes sobre discos de VM IaaS do Azure e discos premium (gerenciados e não gerenciados)"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2017
ms.author: robinsh
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: af7d5b03e1490ed8d90021980f14c47281e8a655
ms.contentlocale: pt-br
ms.lasthandoff: 05/17/2017


---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Perguntas frequentes sobre Discos de VM IaaS do Azure e discos premium gerenciados e não gerenciados

Neste artigo, iremos consultar algumas das perguntas frequentes sobre Managed Disks e Armazenamento Premium.

## <a name="managed-disks"></a>Managed Disks

**O que é o Azure Managed Disks?**

Managed Disks é um recurso que simplifica o gerenciamento de disco para VMs IaaS do Azure manipulando o gerenciamento de conta de armazenamento para você. Para saber mais, confira [Visão Geral do Managed Disks](storage-managed-disks-overview.md).

**Se eu criar um disco gerenciado standard com base em um VHD existente que tinha 80 GB de tamanho, quanto isso custará?**

Um disco gerenciado standard criado com base em um VHD de 80 GB será tratado como o próximo tamanho de disco standard disponível, um disco S10. Você será cobrado de acordo com os preços do disco S10. Verifique a [página de preços](https://azure.microsoft.com/pricing/details/storage) para obter detalhes.

**Existem custos de transação de discos gerenciados standard?**

Sim, você é cobrado por cada transação. Verifique a [página de preços] (https://azure.microsoft.com/pricing/details/storage) para obter detalhes.

**Para um disco gerenciado standard, eu serei cobrado pelo tamanho real dos dados no disco ou pela capacidade provisionada do disco?**

Você é cobrado com base na capacidade provisionada do disco. Verifique a [página de preços](https://azure.microsoft.com/pricing/details/storage) para obter detalhes.

**O preço dos discos premium gerenciados é diferente do preço dos discos não gerenciados?**

O preço dos discos premium gerenciados é o mesmo que os discos premium não gerenciados.

**Posso alterar o tipo de conta de armazenamento (Standard/Premium) de meus discos gerenciados?**

Sim. Você pode alterar o tipo de conta de armazenamento de seus discos gerenciados usando o portal do Azure, PowerShell ou a CLI do Azure.

**É possível copiar ou exportar um disco gerenciado para uma conta de armazenamento privado?**

Sim, você pode exportar seus discos gerenciados usando o portal do Azure, PowerShell ou a CLI do Azure.

**Posso usar um arquivo VHD em uma conta de armazenamento do Azure para criar um disco gerenciado em assinaturas diferentes?**

Não.

**Posso usar um arquivo VHD em uma conta de armazenamento do Azure para criar um disco gerenciado em uma região diferente?**

Não.

**Haverá limitações de escala para clientes que usam discos gerenciados?**

O Managed Disks elimina os limites associados a contas de armazenamento. No entanto, o número de discos gerenciados por assinatura é limitado a 2000 por padrão. Isso pode ser aumentado telefonando para o suporte.

**Posso fazer um instantâneo incremental de um disco gerenciado?**

Não. O recurso de Instantâneo atual faz uma cópia completa de um disco gerenciado. No entanto, estamos planejando oferecer suporte a instantâneos incrementais no futuro.

**As VMs em um Conjunto de Disponibilidade podem consistir de uma combinação de discos gerenciados e não gerenciados?**

Não, as VMs em um Conjunto de Disponibilidade devem usar todos os discos gerenciados ou não gerenciados. Ao criar um Conjunto de Disponibilidade, você pode escolher qual tipo de discos que você deseja usar.

**O Managed Disks é a opção padrão no portal do Azure?**

Atualmente não, mas ele se tornará o padrão no futuro.


**É possível criar um disco gerenciado vazio?**

Sim, você pode criar um disco vazio. Um disco gerenciado pode ser criado independentemente de uma VM, ou seja, sem anexá-lo a uma VM.

**O que é a contagem de domínios de falha com suporte para Conjuntos de Disponibilidade usando o Managed Disks?**

A contagem de domínios de falha com suporte é 2 ou 3 para Conjuntos de Disponibilidade usando o Managed Disks dependendo da região na qual está localizado.

**Como é conta de armazenamento standard para a configuração de diagnóstico?**

Você configura uma conta de armazenamento privado para diagnóstico da VM. No futuro, planejamos alternar o diagnóstico para o Managed Disks.

**Que tipo de suporte RBAC temos para o Managed Disks?**

O Managed Disks oferece suporte a três funções principais padrão:

1.  Proprietário: pode gerenciar tudo, incluindo o acesso.

2.  Colaboradores: podem gerenciar tudo, exceto o acesso.

3.  Leitores: podem ver tudo, mas não podem fazer alterações.

**É possível copiar ou exportar um disco gerenciado para uma conta de armazenamento privado?**

Você pode obter uma assinatura de acesso compartilhado somente leitura URI (SAS) para o disco gerenciado e usá-la para copiar o conteúdo para uma conta de armazenamento privado ou armazenamento local.

**Posso criar uma cópia do meu disco gerenciado?**

Os clientes podem tirar um instantâneo de seus discos gerenciados e, em seguida, usar o instantâneo para criar outro disco gerenciado.

**Ainda há suporte para discos não gerenciados?**

Sim, há suporte para discos gerenciados e não gerenciados. No entanto, recomendamos que você comece a usar discos gerenciados para novas cargas de trabalho e migre suas cargas de trabalho atuais para o Managed Disks.

**Se eu criar um disco de 128 GB e, em seguida, aumentar o tamanho para 130 GB, serei cobrado pelo próximo tamanho de disco (512 GB)?**

Sim.

**Posso criar Managed Disks LRS, GRS e ZRS?**

O Azure Managed Disks atualmente suporta apenas o armazenamento LRS (localmente redundante).

**osso reduzir/diminuir o tamanho de meus Managed Disks?**
Não. Não há suporte para esse recurso no momento. 

**Posso alterar a propriedade de nome do computador ao usar um disco de SO especializado (sem Sysprep ou generalizado) para provisionar uma VM** Não. Não é possível atualizar a propriedade de nome do computador. A nova VM a herdará da VM pai que foi usada para criar o disco do sistema operacional. 

**Onde posso encontrar modelos do Azure Resource Manager de exemplo para criar VMs com Managed Disks**
* https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md
* https://github.com/chagarw/MDPP

## <a name="managed-disks-and-port-8443"></a>Managed Disks e porta 8443

**Por que os clientes têm que desbloquear o tráfego de saída na porta 8443 para VMs usando o Azure Managed Disks?**

O Agente de VM do Azure usa a porta 8443 para relatar o status de cada extensão de VM para a plataforma do Azure. Sem que essa porta seja desbloqueada, o agente de VM não conseguirá relatar o status de nenhuma extensão da VM. Para obter mais informações sobre o agente de VM, consulte [visão geral do Agente de Máquina Virtual do Azure](../virtual-machines/windows/agent-user-guide.md).

**O que acontece se uma VM é implantada com extensões e a porta não está desbloqueada?**

A implantação resultará em um erro. 

**O que acontece se uma VM for implantada sem extensões e a porta não estiver desbloqueada?**

Não haverá nenhum impacto na implantação. 

**O que acontece se uma extensão for instalada em uma VM que já está provisionada e em execução e a VM não tiver a porta 8443 desbloqueada?**

A extensão não será implantada com êxito. O status da extensão será desconhecido. 

**O que acontecerá se um modelo do Azure Resource Manager for usado para provisionar várias VMs com a porta 8443 bloqueada – uma VM com extensões e uma segunda VM dependente na primeira VM?**

A primeira VM será exibida como uma implantação com falha porque as extensões não foram implantadas com êxito. A segunda VM não será implantada. 

**Esse requisito de desbloqueio da porta se aplicará a todas as extensões da VM?**

Sim.

**As conexões de entrada e saída na porta 8443 precisam ser desbloqueadas?**

Não. Somente as conexões de saída na porta 8443 têm que ser desbloqueadas. 

**É necessário ter conexões de saída na porta 8443 que está sendo desbloqueada por todo o tempo de vida da VM?**

Sim.

**Ter essa porta desbloqueada afeta o desempenho da VM?**

Não.

**Há uma data estimada para esse problema ser resolvido para que não seja necessário desbloquear a porta 8443?**

Sim, no final de maio de 2017.

## <a name="premium-disks--both-managed-and-unmanaged"></a>Discos Premium – gerenciados e não gerenciados

**Se uma VM usa uma série de tamanho que oferece suporte ao armazenamento Premium, como um DSv2, é possível anexar discos de dados standard e premium?** 

Sim.

**É possível anexar discos de dados standard e premium a uma série de tamanho que não oferece suporte a armazenamento Premium, como as séries D, Dv2, G ou F?**

Não. Você só pode anexar discos de dados standard às VMs que não usam uma série de tamanho que oferece suporte ao armazenamento Premium.

**Se criar um disco de dados premium com base em um VHD existente que tinha 80 GB de tamanho, quanto isso custará?**

Um disco de dados premium criado com base em um VHD de 80 GB será tratado como o próximo tamanho de disco premium disponível, um disco P10. Você será cobrado de acordo com os preços do disco P10.

**Existem custos de transação ao usar o Armazenamento Premium?**

Há um custo fixo para cada tamanho de disco que vem provisionado com limites específicos de IOPS e taxa de transferência. Os outros custos são largura de banda de saída e recurso de instantâneos, caso aplicável. Verifique a [página de preços](https://azure.microsoft.com/pricing/details/storage) para obter detalhes.

**Quais são os limites de IOPS e taxa de transferência que posso obter do cache de disco?**

Os limites combinados para cache e SSD local para um item da série DS são 4000 IOPS por núcleo e 33 MB por segundo por núcleo. A série GS oferece 5000 IOPS por núcleo e 50 MB por segundo por núcleo.

**O SSD local tem suporte para VMs do Managed Disks?**

O SSD local é um armazenamento temporário fornecido com uma VM de discos gerenciados. Não há custo adicional para esse armazenamento temporário. É recomendável que você não use esse SSD local para armazenar os dados do aplicativo, pois eles não são persistidos no armazenamento de Blobs do Azure.

**Há alguma repercussão do uso de CORTE em Discos Premium?**

Não há nenhuma desvantagem em usar CORTE nos Discos do Azure Premium ou Standard.

## <a name="what-if-my-question-isnt-answered-here"></a>E se dúvida não foi respondida aqui?

Se sua pergunta não estiver listada aqui, fale conosco e nós ajudaremos a encontrar uma resposta. Você pode publicar uma pergunta no final deste artigo, nos comentários ou no [Fórum do armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata) do MSDN para entrar em contato com a equipe de Armazenamento do Azure e outros membros da comunidade sobre este artigo.

Para fazer uma solicitação de recurso, envie suas solicitações e ideias para o [Fórum de comentários do Armazenamento do Azure](https://feedback.azure.com/forums/217298-storage).

