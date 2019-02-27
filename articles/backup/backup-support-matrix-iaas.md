---
title: Matriz de suporte de Backup do Azure para backup de VM do Azure
description: Fornece um resumo de configurações compatíveis e limitações ao fazer backup de VMs do Azure com o serviço de Backup do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: b99d6285942bafe5467827c30b5ba2e42094fdf3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56431050"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matriz de suporte para backup de VM do Azure
Você pode usar o [serviço de Backup do Azure](backup-overview.md) para fazer backup de cargas de trabalho e computadores locais e VMs do Azure. Este artigo resume as configurações de suporte e limitações ao fazer backup de máquinas virtuais (VMs) do Azure com o Backup do Azure.

Outras matrizes de suporte:

- [Matriz de suporte geral](backup-support-matrix.md) para o Backup do Azure
- [Matriz de suporte](backup-support-matrix-mabs-dpm.md) para o servidor de Backup do Microsoft Azure e backup do DOM
- [Matriz de suporte](backup-support-matrix-mars-agent.md) para backup com o agente MARS



## <a name="supported-scenarios"></a>Cenários com suporte

Veja como fazer backup e restaurar as VMs do Azure com o serviço de Backup do Azure.


**Cenário** | **Backup** | **Agente** |**Restore** 
--- | --- | --- | ---
**Backup direto de VMs do Azure** | Restaurar toda a VM  | Nenhum agente é necessário na VM do Azure. O Backup do Azure instala e usa uma extensão de backup no [agente de VM do Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) em execução na VM. | Restaure da seguinte maneira:<br/><br/> - **Crie uma VM básica**. Isso é útil se a VM não tiver nenhuma configuração especial, como vários endereços IP.<br/><br/> - **Restaure o disco da VM**. Restaure o disco. Depois anexe-o a uma VM existente ou crie uma nova VM desde o disco usando o PowerShell.<br/><br/> - **Substitua o disco da VM**. Se existir uma VM que usa discos gerenciados (não criptografados), você pode restaurar um disco e usá-lo para substituir um disco existente na VM.<br/><br/> - **Restaure arquivos/pastas específicas**. Você pode restaurar os arquivos/pastas de uma VM, em vez da VM inteira.  
**Backup direto de VMs do Azure (somente no Windows)** | Fazer backup de arquivos/pastas/volumes específicos | Instale o [agente MARS (Serviços de Recuperação do Microsoft Azure)](backup-azure-file-folder-backup-faq.md).<br/><br/> Você pode executar o agente do MARS com a extensão de backup para o agente de VM do Azure para fazer backup da VM no nível de arquivo/pasta. | Restaure arquivos/pastas específicos.
**Fazer backup de VM do Azure no servidor de backup** |  Faça o backup de arquivos/pastas/volumes; arquivos de estado do sistema/bare metal; dados de aplicativos do System Center DPM ou do Servidor de Backup do Microsoft Azure (servidor MAB).<br/><br/> Em seguida, o DPM/MABS faz o backup no cofre | Instale o agente de proteção do MABS/DPM na VM. O agente de MARS é instalado no DPM/MABS.| Restaure arquivos/pastas/volumes; arquivos de estado do sistema/bare metal; dados de aplicativos. 

Saiba mais sobre o backup usando um servidor de backup (backup-architecture.md#architecture-back-up-to-dpmmabs) e os [requisitos de suporte](backup-support-matrix-mabs-dpm.md).


## <a name="supported-backup-actions"></a>Ações de backup compatíveis

**Ação** | **Suporte**
--- | ---
Habilitar o backup ao criar uma VM do Windows Azure | Com suporte para:  Windows Server 2016 (Datacenter/Datacenter Core); Windows Server 2012 R2 DataCenter; Windows Server 2008 R2 SP1
Habilitar backup ao criar uma VM do Linux | Com suporte para:<br/><br/> - Servidor Ubuntu: 1710, 1704, 1604 (LTS), 1404 (LTS)<br/><br/> - Red Hat: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> - SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3<br/><br/> - Debian: 8, 9<br/><br/> - CentOS: 6.9, 7.3<br/><br/> Oracle Linux: 6.7, 6.8, 6.9, 7.2, 7.3
Fazer backup de uma VM que esteja desligada/offline/buscando VM |  Com suporte.<br/><br/> O instantâneo só é consistente com a falha e não com o aplicativo.
Backup de discos após a migração para discos gerenciados. |  Com suporte.<br/><br/> O backup continuará a funcionar. Nenhuma ação é necessária.
Backup de discos gerenciados depois de habilitar o bloqueio de grupo de recursos | Sem suporte.<br/><br/> Backup não consegue excluir os pontos mais antigos do recurso e os backups começam a falhar ao atingir o limite máximo de pontos de restauração.
Modificar a política de backup de uma VM |  Com suporte.<br/><br/> O backup da VM é feito usando as configurações de retenção e agendamento da nova política. Se as configurações de retenção forem estendidas, os pontos de recuperação existentes serão marcados e mantidos. Se forem reduzidas, os pontos de recuperação existentes serão removidos no próximo trabalho de limpeza e eventualmente excluídos.
Cancelar um trabalho de backup | Tem suporte durante o processo de instantâneo.<br/><br/> Não tem suporte quando o instantâneo está em transferência para o cofre. 
Backup da VM para uma região ou uma assinatura diferentes |  Sem suporte.
Backups por dia (com a extensão de VM do Azure) | Um backup agendado por dia.<br/><br/> É possível fazer até quatro backups sob demanda por dia.
Backups por dia (por meio do agente MARS) | Três backups agendados por dia. 
Backups por dia (por meio do DPM/MABS) | Dois backups agendados por dia.
Backup mensal/anual   | Não tem suporte durante o backup com a extensão de VM do Azure. Só há suporte para diários e semanais.<br/><br/> É possível configurar a política para manter backups diários/semanais por um período de retenção mensal/anual.
Ajuste automático de relógio | Sem suporte.<br/><br/> O Backup do Azure não faz ajuste automático do relógio para alterações de horário de verão na conta ao fazer o backup da VM.<br/><br/>  Modifique a política manualmente, se necessário.
[Recursos de segurança para backups híbridos](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  Não há suporte para desabilitar recursos.



## <a name="operating-system-support-windows"></a>Suporte do sistema operacional (Windows)

A tabela resume os sistemas operacionais com suporte ao fazer backup de VMs do Windows Azure.

**Cenário** | **Suporte SO** 
--- | ---
Fazer backup com a extensão do agente de VM do Azure | Windows Client: Sem suporte<br/><br/> Windows Server: Windows Server 2008 R2 ou posterior 
Backup com o agente MARS | Sistemas operacionais com [suporte](backup-support-matrix-mars-agent.md#support-for-direct-backups).
Backup por meio do DPM/MABS | Sistemas operacionais compatíveis para backup com [MABS](backup-mabs-protection-matrix.md) e [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

## <a name="support-for-linux-backup"></a>Suporte de backup do Linux

Veja a seguir os itens compatíveis, caso deseje fazer backup de computadores Linux.

**Ação** | **Suporte**
--- | --- 
Backup de VMs do Azure no Linux com o agente de VM do Azure para Linux | Backup consistente de arquivos.<br/><br/> Backup consistente com aplicativo usando [scripts personalizados](backup-azure-linux-app-consistent.md).<br/><br/> Durante a restauração você pode criar uma nova VM, restaurar um disco e usá-lo para criar uma VM, ou restaurar um disco e usá-lo para substituir um disco em uma VM existente. Você também pode restaurar arquivos e pastas individuais.
Backup de VMs do Azure no Linux com o agente MARS | Sem suporte.<br/><br/> O agente do MARS só pode ser instalado em computadores Windows.
Backup de VMs do Azure no Linux com DPM/MABS | Sem suporte.

## <a name="operating-system-support-linux"></a>Suporte do sistema operacional (Linux)

Para backups do Linux na VM do Azure, o Backup do Azure oferece suporte para a [lista de distribuições do Linux endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Observe que:

- O Backup do Azure não dá suporte ao principal sistema operacional Linux.
- O Backup do Azure não dá suporte a sistemas operacionais de 32 bits.
- Outras distribuições personalizadas do Linux devem funcionar, contanto que o [agente de VM para Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) esteja disponível na VM com suporte para Python.



## <a name="backup-frequency-and-retention"></a>Frequência e retenção de backup

**Configuração** | **Limites** 
--- | --- 
Número máximo de pontos de recuperação por instância protegida (computador/carga de trabalho) | 9999
Tempo máximo de expiração para um ponto de recuperação | Sem limite
Frequência máxima de backup para o cofre (extensão da VM do Azure) | Uma vez por dia
Frequência máxima de backup para o cofre (Agente MARS) | Três backups por dia.
Frequência máxima de backup para o DPM/MABS | A cada 15 minutos para o SQL Server<br/><br/> Uma vez por hora para outras cargas de trabalho.
Retenção do ponto de recuperação | Diária, semanal, mensal, anual.
Período de retenção máximo | Depende da frequência de backup.
Pontos de recuperação no disco do DPM/MABS | 64 para servidores de arquivos, 448 para servidores de aplicativos.<br/><br/> Os pontos de recuperação de fita são ilimitados para o DPM local.


## <a name="supported-restore-methods"></a>Métodos de restauração compatíveis

**Método de restauração** | **Detalhes**
--- | ---
**Criar uma nova VM** | Você pode criar uma VM durante o processo de restauração. <br/><br/> Essa opção proporciona uma VM básica em funcionamento. Você pode especificar o nome da VM, grupo de recursos, rede virtual, sub-rede e armazenamento.  
**Restaurar um disco** | Você pode restaurar um disco e usá-lo para criar uma VM<br/><br/> Ao selecionar essa opção, o Backup do Azure copia dados do cofre para a conta de armazenamento que você selecionar. O trabalho de restauração gera um modelo. É possível baixar e usar o modelo para especificar as configurações personalizadas e criar uma VM.<br/><br/> Essa opção permite especificar mais configurações que a opção anterior para criar uma VM.<br/><br/>
**Substituir um disco existente** | Você pode restaurar um disco e usá-lo para substituir um disco que esteja atualmente em uma máquina virtual.
**Restaurar arquivos** | É possível recuperar arquivos de um ponto de recuperação selecionado. Baixe um script para montar o disco da VM a partir do ponto de recuperação. Em seguida, percorra os volumes do disco para localizar os arquivos/pastas que você deseja recuperar e desmonte o disco ao terminar.

## <a name="support-for-file-level-restore"></a>Suporte para restauração no nível de arquivos

**Restore** | **Com suporte**
--- | --- 
Restaurar arquivos entre sistemas operacionais | Você pode restaurar os arquivos de qualquer máquina que tenha o mesmo sistema operacional (ou compatível) que a VM do backup. Veja a [Tabela de sistemas operacionais compatíveis](backup-azure-restore-files-from-vm.md#system-requirements).
Restaurar arquivos em VMs clássicas | Sem suporte. 
Restaurar arquivos de VMs criptografadas | Sem suporte.
Restaurar arquivos de contas de armazenamento restritas à rede | Sem suporte.
Restaurar arquivos em máquinas virtuais usando Espaços de Armazenamento do Windows | Não há suporte para restaurar a mesma VM.<br/><br/> Em vez disso, restaure os arquivos em uma VM compatível.
Restaurar arquivos em VM Linux usando matrizes LVM/Raid | Não há suporte para restaurar a mesma VM.<br/><br/> Restaure em uma VM compatível.
Restaurar arquivos com configurações de rede especiais | Não há suporte para restaurar a mesma VM. <br/><br/> Restaure em uma VM compatível.

## <a name="support-for-vm-management"></a>Suporte para gerenciamento de VM

A tabela resume o suporte para backup durante as tarefas de gerenciamento de VM, como adicionar ou substituir os discos da VM.

**Restore** | **Com suporte** 
--- | --- 
Restaure em região/assinatura/zona. | Sem suporte 
Restaurar uma VM existente | Use a opção para substituir disco.
Restaurar o disco com a conta de armazenamento habilitada com a Criptografia do Serviço de Armazenamento do Azure (SSE) | Sem suporte.<br/><br/> Restaure em uma conta que não esteja habilitado com SSE.
Restaurar as contas de armazenamento mistas | Sem suporte.<br/><br/> Com base no tipo de conta de armazenamento, todos os discos restaurados serão premium ou standard e não mistos. 
Restaurar para conta de armazenamento usando o ZRS | Sem suporte
Restaurar VM diretamente em um conjunto de disponibilidade | Para discos gerenciados, você pode restaurar o disco e usar a opção de conjunto de disponibilidade do modelo.<br/><br/> Sem suporte para discos não gerenciados. Para discos não gerenciados, restaure o disco e crie uma VM no conjunto de disponibilidade.
Restaurar o backup de VMs não gerenciadas após a atualização para gerenciadas |  Com suporte.<br/><br/> Você pode restaurar discos e depois criar uma VM gerenciada.
Restaurar uma VM em um ponto de restauração antes de migrar a VM para discos gerenciados |  Com suporte.<br/><br/> Restaure os discos não gerenciados (padrão), converta os discos restaurados para discos gerenciados e crie uma VM com discos gerenciados.
Restaurar VM que foi excluída. |  Com suporte.<br/><br/> Você pode restaurar a VM usando um ponto de recuperação.
A restauração de uma VM DC (controladora de domínio) que faça parte de uma configuração com vários DCs por meio do portal | Com suporte, se você restaurar o disco e criar uma VM usando o PowerShell.
Restaurar a VM em uma rede virtual diferente |   Com suporte.<br/><br/> A rede virtual deve estar na mesma assinatura e região.




## <a name="vm-compute-support"></a>Suporte de VM de computação

**Computação** | **Suporte** 
--- | --- 
Tamanho da VM |   Qualquer tamanho de VM do Azure com, no mínimo, 2 núcleos de CPU e 1 GB de RAM.<br/><br/> [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 
Backup de VMs em [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets) |  Com suporte.<br/><br/> Você não pode restaurar uma VM em um conjunto disponível usando a opção para criar rapidamente uma VM. Em vez disso, ao restaurar a VM, você precisa restaurar o disco e usá-lo para implantar uma VM, ou restaurar um disco e usá-lo para substituir um disco existente. 
Backup de VMs em [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview) |  Sem suporte.  
Backup de VMs implantadas com [benefício de uso híbrido (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) |  Com suporte.
Backup de VMs implantadas em um [conjunto de dimensionamento](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |   Sem suporte   
Backup de VMs implantadas a partir do [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publicado pela Microsoft, produtos de terceiros) |    Com suporte.<br/><br/> A VM também deve estar em execução em um sistema operacional com suporte.<br/><br/> Ao recuperar arquivos na VM, você pode restaurar apenas em um sistema operacional compatível (não um SO anterior ou posterior).
Backup de VMs implantadas desde uma imagem personalizada (terceiros) |     Com suporte.<br/><br/> A VM também deve estar em execução em um sistema operacional com suporte.<br/><br/> Ao recuperar arquivos na VM, você pode restaurar apenas em um sistema operacional compatível (não um SO anterior ou posterior).
Backup de VMs migradas para o Azure   |  Com suporte.<br/><br/> Para fazer backup da VM, o agente da VM deve estar instalado no computador migrado. 



## <a name="vm-storage-support"></a>Suporte ao armazenamento de VM

**Componente** | **Suporte**
--- | ---
Discos de dados de VM do Azure | Faça o backup de uma VM com 16 discos de dados ou menos.
Tamanho do disco de dados | O disco individual pode ter até 4.095 GB.<br/><br/> Se você estiver executando a versão mais recente do backup da VM do Azure (chamado de Restauração Instantânea), discos com tamanho de até 4TB têm suporte. [Saiba mais](backup-instant-restore-capability.md).
Tipo de armazenamento | HDD Standard, SSD Standard e SSD Premium <br/><br/> O SSD Standard tem suporte se você estiver executando a versão mais recente do backup da VM do Azure (chamado de Restauração Instantânea). [Saiba mais](backup-instant-restore-capability.md).
Discos gerenciados | Com suporte
Discos criptografados |  Com suporte.<br/><br/> É possível fazer o backup das VMs do Azure habilitadas com o Azure Disk Encryption (ADE) (com ou sem o aplicativo Azure AD).<br/><br/> As VMs criptografadas não podem ser recuperadas no nível de arquivo/pasta. Você precisa recuperar a VM inteira.<br/><br/> Você pode habilitar a criptografia em VMs que já estão protegidos pelo Backup do Azure.
Discos com o Acelerador de Gravação habilitado | Sem suporte.<br/><br/> Se você estiver executando a versão mais recente do backup de VM do Azure (conhecido como [Restauração Instantânea](backup-instant-restore-capability.md)), pode excluir discos com o Acelerador de Gravação habilitado a partir do backup.
Fazer backup de discos com eliminação de duplicação | Sem suporte.
Adicionar o disco à VM protegida |  Com suporte.
Redimensionar o disco em uma VM protegida |  Com suporte.

## <a name="vm-network-support"></a>Suporte de rede de VM


**Componente** | **Suporte**
--- | ---
Número de NICs | Até o número máximo de NICs com suporte em um tamanho específico de VM do Azure.<br/><br/> As NICs são criadas quando a VM é criada durante o processo de restauração.<br/><br/> O número de NICs na VM restaurada espelha o número de NICs na VM quando você habilitou a proteção. Remover as NICs depois de habilitar não afeta a contagem.
Balanceador de carga interno/externo |    Com suporte. <br/></br> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) sobre a restauração de VMs com configurações de rede especiais.
Múltiplos endereços IP reservados |   Com suporte. <br/></br> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) sobre a restauração de VMs com configurações de rede especiais.
VMs com múltiplos adaptadores de rede  |  Com suporte. <br/></br> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) sobre a restauração de VMs com configurações de rede especiais.
VMs com endereços IP públicos    |  Com suporte.<br/><br/> Você precisa associar um endereço IP público existente à NIC, ou criar um endereço e associá-lo à NIC após executar a restauração.
NSG (Grupo de Segurança de Rede) na NIC/sub-rede. |    Com suporte.
Endereço IP Reservado (estático) |  Sem suporte.<br/><br/> Você não pode fazer backup de uma VM com um endereço IP reservado e sem ponto de extremidade definido.
Endereço IP dinâmico |     Com suporte.<br/><br/> Se a NIC na VM de origem usa o endereçamento IP dinâmico, por padrão, a NIC na VM restaurada também usará.
Gerenciador de Tráfego | Com suporte<br/><br/>. Se a VM de backup estiver no Gerenciador de Tráfego, você precisará adicionar manualmente a VM restaurada no mesmo Gerenciador de Tráfego. 
DNS do Azure |  Com suporte.
DNS Personalizado |     Com suporte.
Conectividade de saída por meio do proxy HTTP |  Com suporte.<br/><br/> Não há suporte para proxy autenticado. 
Ponto de extremidade de serviço VNet  |  Com suporte.<br/><br/> As configurações de conta de armazenamento de rede virtual e firewall devem permitir o acesso de Todas as Redes. 



## <a name="vm-securityencryption-support"></a>Suporte à segurança/criptografia de VM

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



## <a name="vm-compression-support"></a>Suporte à compactação de VM

O backup dá suporte à compactação do tráfego de backup, conforme resumido na tabela abaixo. Observe que:

- Para VMs do Azure, a extensão de VM lê os dados diretamente da conta de armazenamento do Azure na rede de armazenamento; portanto, não é necessário compactar esse tráfego.
- Se estiver usando o DPM ou o MABS, você poderá compactar os dados antes de serem copiados em backup no DPM/MABS, para economizar largura de banda. 

**Computador** | **Compactar no DPM/MABS (TCP)** | **Compactar (HTTPS) no cofre**
--- | --- | ---
Computadores Windows locais sem o DPM/MABS | ND | Sim
VMs do Azure | ND | ND
Locais/VMs do Azure com o DPM | ![Sim][green] | ![Sim][green]
Locais/VMs do Azure com o MABS | ![Sim][green] | ![Sim][green]




## <a name="next-steps"></a>Próximas etapas

- [Fazer backup de VMs do Azure](backup-azure-arm-vms-prepare.md)
- [Fazer backup direto de computadores Windows](tutorial-backup-windows-server-to-azure.md), sem um servidor de backup.
- [Configurar o MABS](backup-azure-microsoft-azure-backup.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no MABS.
- [Configurar o DPM](backup-azure-dpm-introduction.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

