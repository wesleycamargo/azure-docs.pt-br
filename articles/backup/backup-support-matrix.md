---
title: Matriz de suporte do Backup do Azure
description: Fornece um resumo de configurações compatíveis e limitações do serviço de Backup do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b1fa723863e6485e977e075986c3779efed1e689
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360612"
---
# <a name="azure-backup-support-matrix"></a>Matriz de suporte do Backup do Azure

Use o [serviço de Backup do Azure](backup-overview.md) para fazer backup de dados na nuvem do Microsoft Azure. Este artigo resume as configurações compatíveis e as limitações para cenários e implantações do Backup do Azure.

## <a name="vault-support"></a>Suporte de cofre

O Backup do Azure usa cofres dos Serviços de Recuperação para orquestrar e gerenciar backups e armazenar os dados de backup.

**Configuração** | **Detalhes**
--- | ---
Número de cofres | Até 500 cofres dos Serviços de Recuperação em uma assinatura única.
Computadores em um cofre | Até 1.000 VMs do Azure em um único cofre.<br/><br/> Até 50 computadores locais que executam o agente do Backup do Azure (agente do MABS [Serviços de Recuperação do Microsoft Azure]) podem ser registrados em um único cofre.
Fonte de dados no armazenamento do cofre | Máximo de 54.400 GB. Não há nenhum limite para backups de VM do Azure.
Backups no cofre | VMs do Azure: uma vez por dia; computadores protegidos pelo DPM/MABS: duas vezes por dia; computadores copiados em backup diretamente usando o agente do MARS: três vezes por dia.  
Mover o cofre | Você pode mover os cofres dos Serviços de Recuperação de backup entre assinaturas e grupos de recursos. [Saiba mais](backup-azure-move-recovery-services-vault.md).
Mover dados entre cofres | Não há suporte para a movimentação de dados de backup entre cofres.
Tipo de replicação de armazenamento | Modifique o tipo de replicação de armazenamento (GRS/LRS) de um cofre antes que os backups sejam armazenados. Após o início dos backups no cofre, o tipo de replicação não poderá ser modificado.



## <a name="on-premises-backup-support"></a>Suporte de backup local 

Veja a seguir os itens compatíveis, caso deseje fazer backup de computadores locais.

**Computador** | **Localidade** | **Fazer backup** | **Recursos**
--- | --- | --- | ---
**Físico/virtual do Windows (sem servidor de backup)** | Arquivos, pastas, estado do sistema | Copiados em backup no cofre dos Serviços de Recuperação | Backup três vezes por dia.<br/><br/> Sem backup com reconhecimento de aplicativo.<br/><br/> Restaurar arquivo, pasta e volume.
**Físico/virtual do Linux (sem servidor de backup)** | Sem suporte para backup.
**Físico/virtual com o DPM** | Arquivos, pastas, volumes, estado do sistema e dados de aplicativo. | Copiados em backup no DPM (em um disco anexado localmente ao servidor DPM ou em fita).<br/><br/> Em seguida, o DPM faz backup no cofre. | Instantâneos com reconhecimento de aplicativo<br/><br/> Granularidade completa de backup e recuperação.<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware).<br/><br/>. Sem suporte para o Oracle.
**Físico/virtual com o MABS** | Arquivos, pastas, volumes, estado do sistema e dados de aplicativo. | Copiados em backup no MABS (em um disco anexado localmente ao servidor MABS). Sem suporte para fita<br/><br/> Em seguida, o MABS faz backup no cofre. | Instantâneos com reconhecimento de aplicativo<br/><br/> Granularidade completa de backup e recuperação.<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware).<br/><br/>. Sem suporte para o Oracle.


## <a name="azure-vms"></a>VMs do Azure

### <a name="azure-vm-limits"></a>Limites de VM do Azure

**Limite** | **Detalhes**
--- | ---
Discos de dados de VM do Azure | Limite de 16.
Tamanho do disco de dados de VM do Azure | O disco individual pode ter até 4.095 GB.


### <a name="azure-vm-backup-options"></a>Opções de backup de VM do Azure

Veja a seguir os itens compatíveis, caso deseje fazer backup de VMs do Azure.

**Computador** | **Localidade** | **Fazer backup** | **Recursos**
--- | --- | --- | ---
**VMs do Azure (sem servidor de backup)** | Arquivos, pastas, estado do sistema | Copiados em backup no cofre. | Backup uma vez por dia.<br/><br/> Backup com reconhecimento de aplicativo para VMs do Windows; backup consistente com arquivo para VMs do Linux. Configure a consistência com aplicativo para computadores Linux usando scripts personalizados.<br/><br/> Restaure a VM/o disco.<br/><br/> Não é possível fazer backup de uma VM do Azure em uma localização local.
**VM do Azure com o DPM** | Arquivos, pastas, volumes, estado do sistema e dados de aplicativo. | Copiados em backup no DPM em execução no Azure (em um disco anexado localmente ao servidor DPM). Sem suporte para fita.<br/><br/> Em seguida, o DPM faz backup no cofre. | Instantâneos com reconhecimento de aplicativo<br/><br/> Granularidade completa de backup e recuperação.<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware).<br/><br/>. Sem suporte para o Oracle.
**VM do Azure com o MABS** | Arquivos, pastas, volumes, estado do sistema e dados de aplicativo. | Copiados em backup no MABS em execução no Azure (em um disco anexado localmente ao servidor MABS). Sem suporte para fita<br/><br/> Em seguida, o MABS faz backup no cofre. | Instantâneos com reconhecimento de aplicativo<br/><br/> Granularidade completa de backup e recuperação.<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware).<br/><br/>. Sem suporte para o Oracle.





## <a name="linux-backup-support"></a>Suporte de backup do Linux

Veja a seguir os itens compatíveis, caso deseje fazer backup de computadores Linux.

**Backup** | **Linux (endossado pelo Azure)**
--- | --- 
**Computador Linux local (sem o DPM nem o MABS)**. |  Não. O agente do MARS só pode ser instalado em computadores Windows. 
**VM do Azure (sem o DPM nem o MABS)** | Backup consistente com aplicativo usando [scripts personalizados](backup-azure-linux-app-consistent.md).<br/><br/> Recuperação em nível de arquivo.<br/><br/> Faça a restauração com a criação de uma VM de um ponto de recuperação ou um disco.
**Computador local/VM do Azure com o DPM** | Backup consistente de arquivos das VMs Convidadas Linux no Hyper-V e VMWare<br/><br/> Restauração da VM do Hyper-V e VMs Convidadas Linux do VMWare</br></br> Backup consistente com o arquivo não disponível para VMs do Azure
**Computador local/VM do Azure com o MABS** | Backup consistente de arquivos das VMs Convidadas Linux no Hyper-V e VMWare<br/><br/> Restauração de VM do Hyper-V e VMs Convidadas Linux do VMWare</br></br> Backup consistente com arquivo não disponível para VMs do Azure.

## <a name="disk-support"></a>Suporte de disco

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
Computadores Windows locais sem o DPM/MABS | ![SIM][green] | ![SIM][green]
VMs do Azure | ![SIM][green] | ![SIM][green] 
Locais/VMs do Azure com o DPM | ![SIM][green] | ![SIM][green]
Locais/VMs do Azure com o MABS | ![SIM][green] | ![SIM][green]



## <a name="compression-support"></a>Suporte à compactação

O backup dá suporte à compactação do tráfego de backup, conforme resumido na tabela abaixo. Observe que:

- Para VMs do Azure, a extensão de VM lê os dados diretamente da conta de armazenamento do Azure na rede de armazenamento; portanto, não é necessário compactar esse tráfego.
- Se estiver usando o DPM ou o MABS, você poderá compactar os dados antes de serem copiados em backup no DPM/MABS, para economizar largura de banda. 

**Computador** | **Compactar no DPM/MABS (TCP)** | **Compactar (HTTPS) no cofre**
--- | --- | ---
Computadores Windows locais sem o DPM/MABS | ND | SIM
VMs do Azure | ND | ND
Locais/VMs do Azure com o DPM | ![SIM][green] | ![SIM][green]
Locais/VMs do Azure com o MABS | ![SIM][green] | ![SIM][green]



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

- [Fazer backup de VMs do Azure](backup-azure-arm-vms-prepare.md)
- [Fazer backup de computadores Windows diretamente](tutorial-backup-windows-server-to-azure.md), sem um servidor de backup.
- [Configurar o MABS](backup-azure-microsoft-azure-backup.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no MABS.
- [Configurar o DPM](backup-azure-dpm-introduction.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
