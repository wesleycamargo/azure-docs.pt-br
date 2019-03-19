---
title: Matriz de suporte do Backup do Azure
description: Fornece um resumo de configurações compatíveis e limitações do serviço de Backup do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 51bd4b935b32bea20d3f5de0b8cda62dfdbf07b8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57898713"
---
# <a name="azure-backup-support-matrix"></a>Matriz de suporte do Backup do Azure

Você pode usar [Backup do Azure](backup-overview.md) para fazer backup de dados para a plataforma de nuvem do Microsoft Azure. Este artigo resume as configurações gerais de suporte e limitações para cenários de Backup do Azure e implantações.

Outras matrizes de suporte estão disponíveis:

- Matriz de suporte para [backup de máquina virtual do Azure (VM)](backup-support-matrix-iaas.md)
- Matriz de suporte para backup usando [System Center Data Protection Manager (DPM) / Microsoft Azure Backup MABS (servidor)](backup-support-matrix-mabs-dpm.md)
- Matriz de suporte para backup usando o [agente do Microsoft Azure Recovery Services (MARS)](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>Suporte de cofre

O Backup do Azure usa cofres de serviços de recuperação para orquestrar e gerenciar backups. Ele também usa cofres para armazenar dados de backup. 

A tabela a seguir descreve os recursos dos cofres dos serviços de recuperação:

**Recurso** | **Detalhes**
--- | ---
**Cofres na assinatura** | Até 500 cofres dos Serviços de Recuperação em uma assinatura única.
**Computadores em um cofre** | Até 1.000 VMs do Azure em um único cofre.<br/><br/> Até 50 servidores MABS podem ser registrados em um único cofre.
**Fontes de dados no armazenamento do cofre** | Máximo 54,400 GB. Não há nenhum limite para backups de VM do Azure.
**Backups no cofre** | **VMs do Azure:** Uma vez por dia.<br/><br/>**Máquinas protegidas pelo MABS/DPM:** Duas vezes ao dia.<br/><br/> **Backup de máquinas diretamente, usando o agente do MARS:** Três vezes ao dia. 
**Backups entre cofres** | O backup é dentro de uma região.<br/><br/> Você precisa de um cofre em cada região do Azure que contenha VMs que você deseja fazer backup. Você não pode fazer backup em uma região diferente. 
**Mover cofres** | Você pode [mover cofres](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) entre assinaturas ou entre grupos de recursos na mesma assinatura.
**Mover dados entre cofres** | Movimentação de dados de backup entre cofres não é suportado.
**Modificar o tipo de armazenamento do cofre** | Você pode modificar o tipo de replicação de armazenamento (armazenamento com redundância geográfica ou armazenamento com redundância local) para um cofre, antes que os backups são armazenados. Após o início dos backups no cofre, o tipo de replicação não poderá ser modificado.

## <a name="on-premises-backup-support"></a>Suporte de backup local

Aqui está o que tem suporte se você quiser fazer backup de máquinas locais:

**Computador** | **O que é feito backup** | **Localidade** | **Recursos**
--- | --- | --- | ---
**Backup direto de computador Windows com o agente MARS** | Arquivos, pastas, estado do sistema | Fazer backup no cofre dos serviços de recuperação. | Fazer backup de três vezes por dia<br/><br/> Nenhum backup de reconhecimento de aplicativo<br/><br/> Restaurar o arquivo, pasta, volume
**Backup direto de computador Linux com o agente MARS** | Backup sem suporte
**Fazer backup no DPM** | Arquivos, pastas, volumes, estado do sistema, dados de aplicativo | Fazer backup em armazenamento local do DPM. Em seguida, o DPM faz backup no cofre. | Instantâneos com reconhecimento de aplicativo<br/><br/> Granularidade completa para backup e recuperação<br/><br/> Linux com suporte para VMs (Hyper-V/VMware)<br/><br/> Oracle não tem suportado
**Fazer backup no MABS** | Arquivos, pastas, volumes, estado do sistema, dados de aplicativo | Fazer backup no armazenamento local do MABS. Em seguida, o MABS faz backup no cofre. | Instantâneos com reconhecimento de aplicativo<br/><br/> Granularidade completa para backup e recuperação<br/><br/> Linux com suporte para VMs (Hyper-V/VMware)<br/><br/> Oracle não tem suportado

## <a name="azure-vm-backup-support"></a>Suporte de backup de VM do Azure

### <a name="azure-vm-limits"></a>Limites de VM do Azure

**Limite** | **Detalhes**
--- | ---
**Discos de dados VM do Azure** | Limite de 16
**Tamanho do disco de dados VM do Azure** | Discos individuais podem ser até 4.095 GB

### <a name="azure-vm-backup-options"></a>Opções de backup de VM do Azure

Aqui está o que tem suporte se você quiser fazer backup de VMs do Azure:

**Computador** | **O que é feito backup** | **Localidade** | **Recursos**
--- | --- | --- | ---
**Backup VM do Azure usando a extensão de VM** | Toda a VM | Fazer backup no cofre. | Extensão instalada quando você habilita o backup de uma VM.<br/><br/> Fazer backup de uma vez por dia.<br/><br/> Reconhecimento de aplicativo de backup para VMs do Windows; consistente com o arquivo de backup para VMs Linux. Você pode configurar a consistência do aplicativo para computadores Linux usando scripts personalizados.<br/><br/> Restaure a VM ou disco.<br/><br/> Não é possível fazer backup de uma VM do Azure para um caminho local.
**Backup VM do Azure usando o agente MARS** | Arquivos, pastas | Fazer backup no cofre. | Fazer backup de três vezes ao dia.<br/><br/> Se você quiser fazer backup de arquivos específicos ou pastas em vez de toda a VM, o agente do MARS pode executar junto com a extensão de VM.
**VM do Azure com o DPM** | Arquivos, pastas, volumes, estado do sistema, dados de aplicativo | Fazer backup no armazenamento local da VM do Azure que está executando o DPM. Em seguida, o DPM faz backup no cofre. | Instantâneos de reconhecimento de aplicativo.<br/><br/> Granularidade completa de backup e recuperação.<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware).<br/><br/> Sem suporte para o Oracle.
**VM do Azure com o MABS** | Arquivos, pastas, volumes, estado do sistema, dados de aplicativo | Fazer backup no armazenamento local da VM do Azure que está executando o MABS. Em seguida, o MABS faz backup no cofre. | Instantâneos de reconhecimento de aplicativo.<br/><br/> Granularidade completa de backup e recuperação.<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware).<br/><br/> Sem suporte para o Oracle.

## <a name="linux-backup-support"></a>Suporte de backup do Linux

Aqui está o que tem suporte se você quiser fazer backup de máquinas do Linux:

**Tipo de backup** | **Linux (endossado pelo Azure)**
--- | ---
**Backup direto da máquina local que está executando o Linux** | Sem suporte. O agente do MARS pode ser instalado apenas em computadores Windows.
**Usando a extensão do agente para fazer backup de VM do Azure que está executando o Linux** | Backup consistente com o aplicativo por meio [scripts personalizados](backup-azure-linux-app-consistent.md).<br/><br/> Recuperação em nível de arquivo.<br/><br/> Faça a restauração com a criação de uma VM de um ponto de recuperação ou um disco.
**Usando o DPM para fazer backup no local ou VM do Azure que está executando o Linux** | Backup consistente com o arquivo de VMs convidadas Linux no Hyper-V e VMWare.<br/><br/> Restauração de VM do Hyper-V e VMWare VMs de convidado do Linux.<br/><br/> Backup consistente com o arquivo não está disponível para VM do Azure.
**Usando o MABS para fazer backup do computador local ou VM do Azure que está executando o Linux** | Backup consistente com o arquivo de VMs convidadas Linux no Hyper-V e VMWare.<br/><br/> Restauração de VM do Hyper-V e VMs convidadas Linux do VMWare.<br/><br/> Backup consistente com arquivo não disponível para VMs do Azure.

## <a name="daylight-saving-time-support"></a>Suporte de horário de verão

O Backup do Azure não dá suporte a ajuste automático do relógio para horário de verão para backups de VM do Azure. Modifique as políticas de backup manualmente, conforme necessário.

## <a name="disk-deduplication-support"></a>Suporte de eliminação de duplicação de disco

O suporte de eliminação de duplicação de disco ocorre da seguinte maneira:

- Eliminação de duplicação de disco é local com suporte quando você usar o DPM ou MABs para fazer backup de VMs Hyper-V que estão executando o Windows. Windows Server executa a eliminação de duplicação de dados (no nível do host) em discos de rígidos virtuais (VHDs) que estão anexados à VM como armazenamento de backup.
- Não há suporte para a eliminação de duplicação no Azure para qualquer componente de Backup. Quando o DPM e MABS é implantado no Azure, os discos de armazenamento anexados à VM não podem ser a eliminação de duplicação.

## <a name="security-and-encryption-support"></a>Suporte de segurança e criptografia

O Backup do Azure dá suporte à criptografia para dados em trânsito e em repouso.

### <a name="network-traffic-to-azure"></a>Tráfego de rede para o Azure

- Tráfego de backup de servidores no cofre dos serviços de recuperação é criptografado usando Advanced Encryption Standard 256.
- Os dados de backup são enviados por um link HTTPS seguro.
- Dados de backup são armazenados no cofre de serviços de recuperação de forma criptografada.
- Só você tem a senha para desbloquear esses dados. A Microsoft não pode descriptografar os dados de backup em momento algum.

    > [!WARNING]
    > Depois de configurar o cofre, só você terá acesso à chave de criptografia. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave for perdida, a Microsoft não poderá recuperar os dados de backup.

### <a name="data-security"></a>Segurança de dados

- Quando você estiver fazendo backup de VMs do Azure, você precisa configurar a criptografia *dentro do* a máquina virtual.
- O Backup do Azure dá suporte a Azure Disk Encryption, que usa o BitLocker em máquinas virtuais Windows e **dm-crypt** em máquinas virtuais Linux.
- No back-end, o Backup do Azure usa [criptografia do serviço de armazenamento do Azure](../storage/common/storage-service-encryption.md), qual protege dados em repouso.

**Computador** | **Em trânsito** | **Em repouso**
--- | --- | ---
**As máquinas do Windows sem MABS/DPM no local** | ![Sim][green] | ![Sim][green]
**VMs do Azure** | ![Sim][green] | ![Sim][green]
**As máquinas do Windows locais ou máquinas virtuais do Azure com o DPM** | ![Sim][green] | ![Sim][green]
**As máquinas do Windows locais ou VMs do Azure com o MABS** | ![Sim][green] | ![Sim][green]

## <a name="compression-support"></a>Suporte à compactação

Backup oferece suporte a compactação do tráfego de backup, conforme resumido na tabela a seguir.

- Para VMs do Azure, a extensão de VM lê os dados diretamente da conta de armazenamento do Azure na rede de armazenamento, portanto, não é necessário compactar esse tráfego.
- Se você estiver usando o DPM ou MABS, você pode economizar largura de banda ao compactar os dados antes que ele é feito backup.

**Computador** | **Compactar no DPM/MABS (TCP)** | **Compactar ao cofre (HTTPS)**
--- | --- | ---
**Backup direto em computadores locais do Windows** | ND | ![Sim][green]
**Backup de VMs do Azure usando a extensão de VM** | ND | ND
**Backup em local/Azure máquinas usando o MABS/DPM** | ![Sim][green] | ![Sim][green]

## <a name="retention-limits"></a>Limites de retenção

**Configuração** | **Limites**
--- | ---
**Máximo de pontos de recuperação por instância protegida (computador ou carga de trabalho)** | 9,999
**Tempo de expiração máximo para um ponto de recuperação** | Sem limite
**Frequência de backup máxima para MABS/DPM** | A cada 15 minutos para o SQL Server<br/><br/> Uma vez por hora para outras cargas de trabalho
**Frequência de backup máxima para o cofre** | **Local máquinas do Windows ou VMs do Azure executando o MARS:** Três por dia<br/><br/> **MABS/DPM:** Dois por dia<br/><br/> **Backup VM do Azure:** Uma por dia
**Retenção do ponto de recuperação** | Diária, semanal, mensal, anual
**Período de retenção máximo** | Depende da frequência de backup
**Pontos de recuperação no disco do MABS/DPM** | 64 para servidores de arquivos; 448 para servidores de aplicativo <br/><br/>Pontos de recuperação de fita ilimitado para o DPM no local

## <a name="next-steps"></a>Próximas etapas

- [Examinar matriz de suporte](backup-support-matrix-iaas.md) para backup de VM do Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
