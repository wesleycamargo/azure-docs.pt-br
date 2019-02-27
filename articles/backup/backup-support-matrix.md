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
ms.openlocfilehash: ff4ee1d88bd13e647d0f6218d7e9c9b2c57a5a01
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429563"
---
# <a name="azure-backup-support-matrix"></a>Matriz de suporte do Backup do Azure

Use o [serviço de Backup do Azure](backup-overview.md) para fazer backup de dados na nuvem do Microsoft Azure. Este artigo resume as configurações e limitações de suporte geral para cenários e implantações do Backup do Azure.

Outras matrizes de suporte estão disponíveis:

[Matriz de suporte](backup-support-matrix-iaas.md) para backup de VM do Azure [Matriz de suporte](backup-support-matrix-mabs-dpm.md) para backup usando o DPM do System Center/MABS (Servidor de Backup do Microsoft Azure) [Matriz de suporte](backup-support-matrix-mars-agent.md) para backup usando o agente MARS (Serviços de Recuperação do Microsoft Azure)

## <a name="vault-support"></a>Suporte de cofre

O Backup do Azure usa cofres dos Serviços de Recuperação para orquestrar e gerenciar backups e armazenar os dados de backup.

**Configuração** | **Detalhes**
--- | ---
**Cofres na assinatura** | Até 500 cofres dos Serviços de Recuperação em uma assinatura única.
**Computadores em um cofre** | Até 1.000 VMs do Azure em um único cofre.<br/><br/> Até 50 servidores MABS podem ser registrados em um único cofre.
**Fontes de dados no armazenamento do cofre** | Máximo de 54.400 GB. Não há nenhum limite para backups de VM do Azure.
**Backups no cofre** | VMs do Azure: uma vez por dia<br/><br/>Computadores protegidos por DPM/MABS: duas vezes ao dia<br/><br/> Backup de computadores diretamente usando o agente MARS: três vezes ao dia. 
**Backups entre cofres** | O backup é dentro de uma região.<br/><br/> Você precisa de um cofre em cada região do Azure que contenha VMs que você deseja fazer backup. Você não pode fazer backup em uma região diferente. 
**Mover o cofre** | Você pode [mover cofres](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) entre assinaturas ou entre grupos de recursos na mesma assinatura.
**Mover dados entre cofres** | Não há suporte para a movimentação de dados de backup entre cofres.
**Modificar o tipo de armazenamento do cofre** | Modifique o tipo de replicação de armazenamento (GRS/LRS) de um cofre antes que os backups sejam armazenados. Após o início dos backups no cofre, o tipo de replicação não poderá ser modificado.



## <a name="on-premises-backup-support"></a>Suporte de backup local

Veja a seguir os itens compatíveis, caso deseje fazer backup de computadores locais.

**Computador** | **Backup de** | **Localidade** | **Recursos**
--- | --- | --- | ---
**Backup direto de computador Windows com o agente MARS** | Arquivos, pastas, estado do sistema | Fazer backup no cofre dos Serviços de Recuperação | Backup três vezes por dia.<br/><br/> Sem backup com reconhecimento de aplicativo.<br/><br/> Restaurar arquivo, pasta e volume.
**Backup direto de computador Linux com o agente MARS** | Sem suporte para backup.
**Backup para DPM** | Arquivos, pastas, volumes, estado do sistema e dados de aplicativo. | Fazer backup em armazenamento local do DPM. Em seguida, o DPM faz backup no cofre. | Instantâneos com reconhecimento de aplicativo<br/><br/> Granularidade completa de backup e recuperação.<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware).<br/><br/>. Sem suporte para o Oracle.
**Backup no MABS** | Arquivos, pastas, volumes, estado do sistema e dados de aplicativo. | Fazer backup no armazenamento local do MABS. Em seguida, o MABS faz backup no cofre. | Instantâneos com reconhecimento de aplicativo<br/><br/> Granularidade completa de backup e recuperação.<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware).<br/><br/>. Sem suporte para o Oracle.


## <a name="azure-vm-backup-support"></a>Suporte de backup de VM do Azure

### <a name="azure-vm-limits"></a>Limites de VM do Azure

**Limite** | **Detalhes**
--- | ---
Discos de dados de VM do Azure | Limite de 16.
Tamanho do disco de dados de VM do Azure | O disco individual pode ter até 4.095 GB.


### <a name="azure-vm-backup-options"></a>Opções de backup de VM do Azure

Veja a seguir os itens compatíveis, caso deseje fazer backup de VMs do Azure.

**Computador** | **Backup de** | **Localidade** | **Recursos**
--- | --- | --- | ---
**Backup VM do Azure usando a extensão de VM** | Toda a VM | Backup em cofre | Extensão instalada quando você habilita o backup de uma VM.<br/><br/> Backup uma vez por dia.<br/><br/> Backup com reconhecimento de aplicativo para VMs do Windows; backup consistente com arquivo para VMs do Linux. Configure a consistência com aplicativo para computadores Linux usando scripts personalizados.<br/><br/> Restaure a VM/o disco.<br/><br/> Não é possível fazer backup de uma VM do Azure em uma localização local.
**Backup de VM do Azure usando o agente MARS** | Arquivos, pastas | Backup em cofre | Backup três vezes por dia.<br/><br/> O agente MARS poderá executar juntamente com a extensão de VM se você quiser fazer backup de arquivos/pastas específicos em vez de toda a VM.
**VM do Azure com o DPM** | Arquivos, pastas, volumes, estado do sistema e dados de aplicativo. | Fazer backup em armazenamento local de VM do Azure executando o DPM. Em seguida, o DPM faz backup no cofre. | Instantâneos com reconhecimento de aplicativo<br/><br/> Granularidade completa de backup e recuperação.<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware).<br/><br/>. Sem suporte para o Oracle.
**VM do Azure com o MABS** | Arquivos, pastas, volumes, estado do sistema e dados de aplicativo. | Backup em armazenamento local de VM do Azure executando o MABS. Em seguida, o MABS faz backup no cofre. | Instantâneos com reconhecimento de aplicativo<br/><br/> Granularidade completa de backup e recuperação.<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware).<br/><br/> Sem suporte para o Oracle.





## <a name="linux-backup-support"></a>Suporte de backup do Linux

Veja a seguir os itens compatíveis, caso deseje fazer backup de computadores Linux.

**Backup** | **Linux (endossado pelo Azure)**
--- | ---
**Backup direto de computador local executando o Linux**. |  Não. O agente do MARS só pode ser instalado em computadores Windows.
**Fazer backup de VM do Azure executando Linux (usando a extensão do agente)** | Backup consistente com aplicativo usando [scripts personalizados](backup-azure-linux-app-consistent.md).<br/><br/> Recuperação em nível de arquivo.<br/><br/> Faça a restauração com a criação de uma VM de um ponto de recuperação ou um disco.
**Fazer backup local ou em VM do Azure executando Linux usando o DPM** | Backup consistente de arquivos das VMs Convidadas Linux no Hyper-V e VMWare<br/><br/> Restauração da VM do Hyper-V e VMs Convidadas Linux do VMWare</br></br> Backup consistente com o arquivo não disponível para VMs do Azure
**Fazer backup de computador/VM do Azure local executando Linux usando o MABS** | Backup consistente de arquivos das VMs Convidadas Linux no Hyper-V e VMWare<br/><br/> Restauração de VM do Hyper-V e VMs Convidadas Linux do VMWare</br></br> Backup consistente com arquivo não disponível para VMs do Azure.

## <a name="daylight-saving-support"></a>Suporte de horário de verão

O Backup do Azure não oferece suporte ao ajuste automático do relógio para alterações de horário de verão para backups de VMs do Azure. Modifique as políticas de backup manualmente, conforme necessário.


## <a name="disk-deduplication-support"></a>Suporte de eliminação de duplicação de disco

O suporte de eliminação de duplicação de disco ocorre da seguinte maneira:
- Há suporte local para a eliminação de duplicação de disco quando o DPM ou o MABS é usado para fazer backup de VMs do Hyper-V que executam o Windows. O Windows Server faz a eliminação de duplicação de dados (em nível do host) em VHDs (discos rígidos virtuais) conectados como armazenamento de backup às máquinas virtuais.
- Não há suporte para a eliminação de duplicação no Azure para qualquer componente de Backup. Quando o System Center DPM e o Servidor de Backup são implantados no Azure, os discos de armazenamento anexados à VM não podem ser submetidos à eliminação de duplicação.


## <a name="securityencryption-support"></a>Suporte à segurança/criptografia

O Backup do Azure dá suporte à criptografia para dados em trânsito e em repouso:

Tráfego de rede para o Azure:
- O tráfego de backup dos servidores para o cofre dos Serviços de Recuperação é criptografado usando a criptografia AES 256.
- Os dados de backup são enviados por um link HTTPS seguro.
- Os dados de backup são armazenados no cofre dos Serviços de Recuperação na forma criptografada.
- Só você tem a senha para desbloquear esses dados. A Microsoft não pode descriptografar os dados de backup em momento algum.
    > [!WARNING]
    > Depois de configurar o cofre, só você terá acesso à chave de criptografia. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave for perdida, a Microsoft não poderá recuperar os dados de backup.
Segurança dos dados:
- Ao fazer backup de VMs do Azure, você precisa configurar a criptografia *dentro* da máquina virtual.
- O Backup do Azure dá suporte a Azure Disk Encryption, que usa o BitLocker em máquinas virtuais Windows e **dm-crypt** em máquinas virtuais Linux.
- No back-end, o Backup do Azure usa [criptografia do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md), que protege dados em repouso.


**Computador** | **Em trânsito** | **Em repouso**
--- | --- | ---
Computadores Windows locais sem o DPM/MABS | ![Sim][green] | ![Sim][green]
VMs do Azure | ![Sim][green] | ![Sim][green]
Locais/VMs do Azure com o DPM | ![Sim][green] | ![Sim][green]
Locais/VMs do Azure com o MABS | ![Sim][green] | ![Sim][green]



## <a name="compression-support"></a>Suporte à compactação

O backup dá suporte à compactação do tráfego de backup, conforme resumido na tabela abaixo. 

- Para VMs do Azure, a extensão de VM lê os dados diretamente da conta de armazenamento do Azure na rede de armazenamento; portanto, não é necessário compactar esse tráfego.
- Se estiver usando o DPM ou o MABS, você poderá compactar os dados antes de serem copiados em backup no DPM/MABS, para economizar largura de banda.

**Computador** | **Compactar no DPM/MABS (TCP)** | **Compactar (HTTPS) no cofre**
--- | --- | ---
**Backup direto em computadores locais do Windows** | ND | Sim
**Fazer backup de VMs do Azure usando a extensão de VM** | ND | ND
**Fazer backup em computadores locais/Azure usando o MABS/DPM | ![Sim][green] | ![Sim][green]



## <a name="retention-limits"></a>Limites de retenção

**Configuração** | **Limites**
--- | ---
Número máximo de pontos de recuperação por instância protegida (computador/carga de trabalho) | 9999
Tempo máximo de expiração para um ponto de recuperação | Sem limite
Frequência máxima de backup para o DPM/MABS | A cada 15 minutos para o SQL Server<br/><br/> Uma vez por hora para outras cargas de trabalho.
Frequência máxima de backup para o cofre | Computadores Windows locais/VMs do Azure que executam o MARS: três por dia<br/><br/> DPM/MABS: Dois por dia<br/><br/> Backup de VM do Azure: Uma vez por dia
Retenção do ponto de recuperação | Diária, semanal, mensal, anual.
Período de retenção máximo | Depende da frequência de backup.
Pontos de recuperação no disco do DPM/MABS | 64 para servidores de arquivos, 448 para servidores de aplicativos.<br/><br/> Os pontos de recuperação de fita são ilimitados para o DPM local.

## <a name="next-steps"></a>Próximas etapas

- [Examinar matriz de suporte](backup-support-matrix-iaas.md) para backup de VM do Azure.


[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
