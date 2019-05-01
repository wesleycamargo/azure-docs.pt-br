---
title: Histórico de versão do Azure Site Recovery implantação Planejador
description: Correções de diferentes versões de Planejador de implantação do Site Recovery conhecidas e limitações conhecidas, juntamente com suas datas de lançamento.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 04/24/2019
ms.author: dapatil
ms.openlocfilehash: 2edf7ce3be1402a497ceab5b826a89ee43c5c39b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927366"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Histórico de versão do Azure Site Recovery implantação Planejador

Este artigo fornece um histórico de todas as versões do Planejador de implantação do Azure Site Recovery, juntamente com as correções, conhecidos limitações em cada um e suas datas de lançamento.

## <a name="version-24"></a>Versão 2.4

**Data de lançamento: 17 de abril de 2019**

**Correções:**

- Aperfeiçoada a compatibilidade de sistema operacional, especificamente ao tratamento de erros com base em localização.
- Adicionado VMs com até 20 Mbps de dados de taxa (variação) de alteração para a lista de verificação de compatibilidade.
- Mensagens de erro aprimoradas
  - Adicionado suporte para o vCenter 6.7.
  - Adicionado suporte para a estação de trabalho do Windows Server 2019 e Red Hat Enterprise Linux (RHEL).



## <a name="version-23"></a>Versão 2.3

**Data de lançamento: 3 de dezembro de 2018**

**Correções:**

- Corrigido um problema que impediu o Planejador de implantação de gerar um relatório com o local de destino fornecido e a assinatura.

## <a name="version-22"></a>Versão 2.2 

**Data de lançamento: 25 de abril de 2018**

**Correções:**

- Operações de GetVMList:
  - Corrigido um problema que causava GetVMList falhar se a pasta especificada não existe. Ele agora cria o diretório padrão, ou cria o diretório especificado no parâmetro de arquivo de saída.
  - Adicionadas que mais detalhadas para GetVMList razões da falha.
- Adicionadas informações de tipo VM como uma coluna na planilha de VMs compatível do relatório do Planejador de implantação.
- Hyper-V para recuperação de desastre do Azure:
  - Discos de VMs excluídas com compartilhado VHDs e passagem de criação de perfil. A operação Startprofiling mostra a lista de VMs excluídas no console do.
  - Adicionado VMs com mais de 64 discos à lista de VMs incompatíveis.
  - Atualizada a replicação inicial (IR) e o fator de compactação de replicação (DR) de delta.
  - Adicionado suporte limitado para armazenamento SMB.

## <a name="version-21"></a>Versão 2.1

**Data de lançamento: 3 de janeiro de 2018**

**Correções:**

- Atualizado o relatório do Excel.
- Bugs corrigidos na operação GetThroughput.
- Opção adicional para limitar o número de VMs para criar o perfil ou gerar o relatório. O limite padrão é 1.000 VMs.
- VMware para recuperação de desastre do Azure:
  - Corrigido um problema de VM do Windows Server 2016 vai para a tabela incompatível. 
  - Mensagens atualizadas de compatibilidade para VMs do Windows de Interface de Firmware extensível (EFI).
- Atualizado o VMware no Azure e Hyper-V para Azure, VM variação de dados de limite por VM. 
- Confiabilidade aprimorada de análise de arquivo de lista VM.

## <a name="version-201"></a>Versão 2.0.1

**Data de lançamento: 7 de dezembro de 2017**

**Correções:**

- Adicionada uma recomendação para otimizar a largura de banda de rede.

## <a name="version-20"></a>Versão 2.0

**Data de lançamento: 28 de novembro de 2017**

**Correções:**

- Adicionado suporte para Hyper-V para recuperação de desastre do Azure.
- Calculadora de custos adicionais.
- Adicionado OS verificação de versão do VMware para recuperação de desastre do Azure para determinar se a VM é compatível ou incompatível para a proteção. A ferramenta usa a cadeia de caracteres de versão de sistema operacional que é retornada pelo servidor do vCenter para a VM. É a versão do sistema operacional convidado que o usuário selecionou ao criar a VM no VMware.

**Limitações conhecidas:**

- Para Hyper-V para recuperação de desastre do Azure, a VM com o nome que contém os caracteres, como: `,`, `"`, `[`, `]`, e ``` ` ``` não são suportados. Se a criação de perfil, geração de relatórios falharão ou terá um resultado incorreto.
- Para o VMware para recuperação de desastre do Azure, VM com o nome que contenha vírgulas não é suportado. Se a criação de perfil, falha de geração de relatórios ou terá um resultado incorreto.

## <a name="version-131"></a>Versão 1.3.1

**Data de lançamento: 19 de julho de 2017** 

**Correções:**

- Adicionado suporte para discos grandes (> 1TB) na geração de relatórios. Agora você pode usar o Planejador de implantação para planejar a replicação para máquinas virtuais com tamanhos de disco maiores que 1 TB (até 4095 GB).
Leia mais sobre [Suporte a discos grandes no Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Versão 1.3

**Data de lançamento: 9 de maio de 2017**

**Correções:**

- Adicionado suporte para o disco gerenciado na geração de relatórios. O número de VMs que pode ser colocado em uma única conta de armazenamento é calculado com base em se o disco gerenciado está selecionado para Failover/Failover de teste.

## <a name="version-12"></a>versão 1.2

**Data de lançamento: 7 de abril de 2017**

**Correções:**

- Adição de inicialização (BIOS ou EFI) verificações de tipo para cada VM determinar se a VM é compatível ou incompatível para a proteção.
- Sistema operacional adicionadas informações de tipo para cada máquina virtual nas VMs compatíveis e nas planilhas de VMs incompatíveis.
- Adicionado suporte para a operação GetThroughput para as regiões do US Government e China Microsoft Azure.
- Algumas outras verificações de pré-requisitos foram adicionadas para vCenter e Servidor ESXi.
- Corrigido um problema de relatório incorreto sendo gerado quando as configurações de localidade são definidas como inglês.

## <a name="version-11"></a>Versão 1.1

**Data de lançamento: 9 de março de 2017**

**Correções:**

- Corrigido um problema que impediu a criação de perfil de VMs quando houver duas ou mais VMs com o mesmo nome ou endereço IP em vários hosts de ESXi do vCenter.
- Corrigido um problema que causou a copiar e pesquisar desabilitada para as VMs compatíveis e incompatíveis planilhas de VMs.

## <a name="version-10"></a>Versão 1.0

**Data de lançamento: 23 de fevereiro de 2017**

**Limitações conhecidas:**

- Dá suporte somente para VMware para cenários de recuperação de desastre do Azure. Para o Hyper-V para cenários de recuperação de desastre do Azure, use o [ferramenta de Planejador de capacidade do Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Não dá suporte a operação GetThroughput para as regiões do US Government e China Microsoft Azure.
- O perfil de cann't ferramenta VMs se o servidor do vCenter tiver duas ou mais VMs com o mesmo nome ou endereço IP em vários hosts ESXi.
Nesta versão, a ferramenta ignora a criação de perfil para nomes de VM ou endereços IP duplicados em VMListFile. A solução alternativa é criar um perfil das VMs usando um host ESXi em vez do servidor vCenter. Certifique-se de executar uma instância de cada host ESXi.
