---
title: Matriz de suporte para o servidor de Backup do Microsoft Azure e System Center DPM
description: Este artigo resume o suporte de Backup do Azure quando você usa o Servidor de Backup do Microsoft Azure ou o System Center DPM para fazer backup de recursos de VM do Azure e no local.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 74b6d953939a569a3240c9d64134d143dc4f179e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56431020"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-serversystem-center-dpm"></a>Matriz de suporte para backup com o servidor de Backup do Microsoft Azure e System Center DPM

Você pode usar o [serviço de Backup do Azure](backup-overview.md) para fazer backup de cargas de trabalho e computadores locais e VMs do Azure. Este artigo resume as configurações de suporte e as limitações para fazer backup de máquinas usando o Servidor de Backup do Microsoft Azure (MABS) ou o System Center Data Protection Manager (DPM) e o Backup do Azure.

## <a name="about-mabsdpm"></a>Sobre o MABS/DPM

O [System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) é uma solução empresarial que configura, facilita e gerencia o backup e a recuperação de dados e de máquinas de empresa. Ele faz parte do conjunto [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) de produtos.


O MABS (Servidor de Backup do Microsoft Azure) é um produto de servidor que pode ser usado para fazer backup de servidores físicos locais, VMs (máquinas virtuais) e aplicativos em execução neles e nas VMs do Azure.

- O MABS se baseia no DPM (System Center Data Protection Manager) e fornece uma funcionalidade semelhante com algumas diferenças:
    - Não é necessária a licença do System Center para executar o MABS.
    - Tanto o MABS como o Azure do DPM fornecem armazenamento de backup de longo prazo. Além disso, o DPM permite fazer backup de dados para armazenamento de longo prazo em fita. O MABS não possui essa funcionalidade.
- Você pode baixar o servidor MABS no [Centro de Download da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=57520). Ele pode ser executado no local ou em uma VM do Azure no Azure.

O DPM e o MABS são compatíveis com backup de uma ampla variedade de aplicativos e sistemas operacionais de clientes e servidores. Eles fornecem vários cenários de backup:
    - É possível fazer backup no nível do computador com o backup de estado do sistema ou bare-metal.
    - É possível fazer backup de arquivos, compartilhamentos, pastas e volumes específicos.
    - É possível fazer backup de aplicativos específicos usando as configurações otimizadas de reconhecimento de aplicativo.

## <a name="mabsdpm-backup"></a>Fazer backup no MABS/DPM

Fazer backup usando o MABS/DPM e o Backup do Azure funciona da seguinte maneira:

1. O agente de proteção do MABS/DPM instalado em cada computador será submetido a backup. 
2. São feitos backup das máquinas/aplicativos no armazenamento local no MABS/DPM.
3. O Agente do MARS (Serviços de Recuperação do Microsoft Azure) é instalado no servidor MARS/DPM.
4. O agente do MARS faz backup dos discos do MABS/DPM para um cofre dos Serviços de Recuperação de backup no Azure usando o Backup do Azure.

Para mais informações:
- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura do MABS.
- [Revise](backup-support-matrix-mars-agent.md) o que é compatível com o agente do MARS.

## <a name="supported-scenarios"></a>Cenários com suporte 

**Cenário** | **Agente** | **Localidade**
--- | --- | ---
**Fazer backup das cargas de trabalho/computadores locais** | O agente de proteção do DPM/MABS é executado nos computadores que você deseja fazer backup<br/><br/> O agente do MARS no servidor MABS/DPM | O MABS/DPM deve ser executado no local
**Fazer backup de VMs/cargas de trabalho do Azure** | O agente de proteção do MABS/DPM no computador protegido<br/><br/> O agente do MARS no MABS/DPM | O MABS/DPM deve ser executado na VM do Azure.

## <a name="supported-deployments"></a>Implantações com suporte

O MABS/DPM pode ser implantado como resumido na tabela.

**Implantação** | **Suporte** | **Detalhes**
--- | --- | ---
**Implantado localmente** | Servidor físico<br/><br/>VM do Hyper-V<br/><br/> VM do VMware | Se o MABS/DPM for instalado como uma VM do VMware, só será possível fazer backup das VMs do VMware e das cargas de trabalho executadas nessas VMs.
**Implantado como uma VM do Azure Stack** | Somente no MABS | O DPM não pode ser usado para fazer backup das VMs do Azure Stack.
**Implantado como uma VM do Azure** | Protege as VMs do Azure e as cargas de trabalho executadas nessas VMs. | O MABS/DPM em execução no Azure não pode fazer backup de máquinas locais.


## <a name="supported-mabs-and-dpm-operating-systems"></a>Sistemas operacionais compatíveis com MABS e DPM

O Backup do Azure pode fazer backup do MABS/DPM executando qualquer um dos seguintes sistemas operacionais. Os sistemas operacionais devem estar executando os pacotes e as atualizações de serviços mais recentes.

**Cenário** | **MABS/DPM** 
--- | --- 
**MABS na VM do Azure** | Windows Server 2012 R2<br/><br/> Windows 2016 Datacenter<br/><br/> Windows 2019 Datacenter<br/><br/> É recomendável que você comece com uma imagem do marketplace.<br/><br/> Mínimo Standard A2 com 2 núcleos e 3.5 GB de RAM. 
**DPM na VM do Azure** | System Center 2012 R2 com atualização 3 ou posterior.<br/><br/> Sistema de operacional Windows conforme [exigido pelo System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> É recomendável que você comece com uma imagem do marketplace.<br/><br/> Mínimo Standard A2 com 2 núcleos e 3.5 GB de RAM. 
**O MABS no local** | Sistemas operacionais de 64 bits compatíveis:<br/><br/> – MABS v3 em diante: Windows Server 2019 (Standard, Datacenter, Essentials) <br/><br/> – MABS v2 em diante: Windows Server 2016 (Standard, Datacenter, Essentials)<br/><br/> Todas as versões do MABS: Windows Server 2012 R2/2012 (Standard, Datacenter, Foundation)<br/><br/>Todas as versões do MABS: Windows Storage Server 2012 R2/2012 (Standard/Workgroup)
**O DPM no local** | Servidor físico/VM do Hyper-V: System Center 2012 SP1 ou posterior.<br/><br/> VM do VMware: System Center 2012 R2 com atualização 5 ou posterior. 



## <a name="management-support"></a>Suporte de gerenciamento
**Problema** | **Detalhes**
--- | ---
**Instalação** | O MABS/DPM deve ser instalado em um computador de finalidade única.<br/><br/> Não instale o MABS/DPM no controlador de domínio, no computador com a instalação da função de Servidor de Aplicativos, no computador executando o Exchange Server ou System Center Operations Manager, ou no nó de cluster.<br/><br/> [Revise](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server) todos os requisitos de sistema do DPM.
**Domínio** | O servidor MABS/DPM deve ser associado a um domínio. Instale primeiro e, em seguida, associe o MABS/DPM em um domínio. Não é possível mover o MABS/DPM para um novo domínio após a implantação.
**Armazenamento** | O MBS (armazenamento de backup moderno) é compatível com o DPM 2016/MABS v2 em diante. Ele não está disponível para o MABS v1.
**Atualização do MABS** | Você pode instalar diretamente o MABS v3 ou atualizar o MABS v2 para o MABS v3. [Saiba mais](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Mover o MABS** | Se você estiver usando o MBS, é possível mover o MABS para um novo servidor mantendo o armazenamento.<br/><br/> O servidor deve ter o mesmo nome que o original. Você não pode alterar o nome se quiser manter o mesmo pool de armazenamento e usar o mesmo banco de dados do MABS para armazenar os pontos de recuperação de dados.<br/><br/> Você precisará de um backup do banco de dados MABS pois precisará restaurá-lo.




## <a name="mabs-support-on-azure-stack"></a>Compatibilidade do MABS no Azure Stack

Você pode implantar o MABS em uma VM do Azure Stack para poder gerenciar o backup de VMs do Azure Stack e as cargas de trabalho em um único local.

**Componente** | **Detalhes**
--- | --- 
**MABS na VM do Azure Stack** | Pelo menos o tamanho A2. É recomendável que você comece com uma imagem do Windows Server 2012 R2 ou Windows Server 2016 no Azure Marketplace.<br/><br/> Você não deve instalar mais nada na VM do MABS.
**Armazenamento do MABS** | Use uma conta de armazenamento separada para VM do MAB. O agente do MARS em execução no MABS precisa de armazenamento temporário para um local de cache e para manter a restauração de dados da nuvem.
**Pool de armazenamento do MABS** | O tamanho do pool de armazenamento do MABS é determinado pelo número e tamanho dos discos anexados à VM do MABS. Cada tamanho de VM do Azure Stack tem um número máximo de discos. Por exemplo, A2 é para quatro discos.
**Retenção do MABS** | Você não deve reter os dados de backups nos discos do MABS local por mais de cinco dias.
**Escalar verticalmente o MABS** | Para escalar verticalmente sua implantação, você pode aumentar o tamanho da VM do MABS. Por exemplo, da série A para D.<br/><br/> Você também pode garantir o descarregamento de dados com backup para o Azure regularmente e, se necessário, é possível implantar servidores adicionais do MABS.
.NET Framework no MABS | A VM do MABS precisa do .NET Framework 3.3 SP1 ou superior instalado.
**Domínio do MABS** | A VM do MABS deve ser associada a um domínio. Um usuário de domínio com privilégios de administrador deve instalar o MABS na VM.
**Backup de dados da VM do Azure Stack** | Você pode fazer backup de arquivos, pastas e aplicativos.
**Compatibilidade de backup** | Esses sistemas operacionais são compatíveis com as VMs que você deseja fazer backup:<br/><br/> – Canal Semestral do Windows Server (Datacenter/Enterprise/Standard)<br/><br/> Windows Server 2016/2012 R2/2012/2008 R2.
**Compatibilidade do SQL para VMs do Azure Stack** | Fazer backup do SQL Server 2016/2014/2012 SP1.<br/><br/> Fazer backup e recuperar os bancos de dados.
**Compatibilidade do SharePoint para VMs do Azure Stack** | SharePoint 2016/2013/2010.<br/><br/> Fazer backup e recuperar o farm, o banco de dados, o front-end e o servidor web.
**Requisitos de rede para backups de VMs** | Todas as VMs na carga de trabalho do Azure Stack devem estar na mesma Rede virtual e pertencer à mesma assinatura.


## <a name="dpmmabs-networking-support"></a>Suporte às redes MABS/DPM

### <a name="url-access"></a>Acesso à URL

O servidor MABS/DPM precisa de acesso a estas URLs:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Conectividade do MABS/DPM no Backup do Azure

É necessária conectividade com o serviço de Backup do Azure para os backups funcionarem corretamente e a assinatura do Azure deve estar ativa. A tabela a seguir mostra o comportamento se essas duas coisas não ocorrerem.

**MABS no Azure** | **Assinatura** | **Backup/restauração** 
--- | --- | --- 
Conectado | Ativo | Backup em disco do MABS/DPM<br/><br/> Backup no Azure<br/><br/> Restaurar do disco<br/><br/> Restaurar do Azure
Conectado | Expirado/desprovisionado | Nenhum backup em disco ou no Azure.<br/><br/> Se a assinatura estiver expirada, você pode restaurar do disco ou do Azure.<br/><br/> Se a assinatura estiver descomissionada, você não pode restaurar do disco ou do Azure. Os pontos de recuperação do Azure são excluídos.
Sem conectividade por mais de 15 dias | Ativo | Nenhum backup em disco ou no Azure.<br/><br/> Você pode restaurar do disco ou do Azure.
Sem conectividade por mais de 15 dias | Expirado/desprovisionado | Nenhum backup em disco ou no Azure.<br/><br/> Se a assinatura estiver expirada, você pode restaurar do disco ou do Azure.<br/><br/> Se a assinatura estiver descomissionada, você não pode restaurar do disco ou do Azure. Os pontos de recuperação do Azure são excluídos.



## <a name="dpmmabs-storage-support"></a>Compatível com armazenamento do MABS/DPM

Os dados de backup no MABS/DPM são armazenados no armazenamento de disco local. 

**Armazenamento** | **Detalhes**
--- | ---
**MBS** | O MBS (armazenamento de backup moderno) é compatível com o DPM 2016/MABS v2 em diante. Ele não está disponível para o MABS v1.
**Armazenamento MABS na VM do Azure** | Os dados são armazenados em discos do Azure anexados à VM do MABS/DPM e gerenciados no MABS/DPM. O número de discos que podem ser usados para o pool de armazenamento do MABS/DPM é limitado pelo tamanho da VM.<br/><br/> VM A2: 4 discos; VM A3: 8 discos; VM A4: 16 discos, com um tamanho máximo de 1 TB para cada disco. Isso determina o pool de armazenamento de backup total disponível.<br/><br/> A quantidade de dados que você pode fazer backup depende do número e do tamanho dos discos anexados.
**Retenção de dados do MABS em VM do Azure** | É recomendável manter os dados de um dia no disco do Azure do MABS/DPM e fazer backup do MABS/DPM no cofre para uma retenção mais longa. Assim, você pode proteger uma quantidade maior de dados descarregando-os no Backup do Azure.


### <a name="modern-backup-storage-mbs"></a>Armazenamento de backup moderno (MBS)
No DPM 2016/MABS v2 (executando o Windows Server 2016) e versões posteriores, você pode aproveitar o armazenamento de backup moderno (MBS).

- Os backups do MBS são armazenados no disco ReFS (Sistema de Arquivos Resiliente).
- O MBS usa clonagem de blocos ReFS para fazer backup mais rápido e usar de forma mais eficiente o espaço de armazenamento.
- Ao adicionar volumes ao pool de armazenamento local do MABS/DPM, configure-os com letras de unidade. Em seguida, é possível configurar o armazenamento de carga de trabalho em diferentes volumes.
- Ao criar grupos de proteção para fazer backup dos dados do MABS/DPM, é possível selecionar a unidade que você deseja usar. Por exemplo, você pode armazenar backups do SQL ou de outras cargas de trabalho IOPS alta em uma unidade de alto desempenho e as cargas de trabalho cujo backup é feito com menos frequência em uma unidade de desempenho inferior.


## <a name="supported-backups-to-mabs"></a>Backups compatíveis com o MABS

A tabela a seguir resume o que pode ser feito backup no MABS das máquinas locais e VMs do Azure.


**Backup** | **Versões** | **MABS** | **Detalhes**
--- | --- | --- | ---
**Windows 10, 8.1, 8, 7**<br/><br/>(32/64 bits) | MABS v3, v2, V1 | Configuração local | Volume/compartilhamento/pasta/arquivo<br/><br/> Compatíveis com volumes com eliminação de duplicação<br/><br/> Os volumes devem ter no mínimo 1 GB e NTFS.
**Windows Server 2016 (Datacenter, Standard, não Nano)**<br/><br/> 64/32 bits | MABS v3, v2 | VM local/Azure.| Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal<br/><br/> Compatíveis com volumes com eliminação de duplicação.
**Windows Server 2012 R2 (Datacenter e Standard)**<br/><br/> 64/32 bits | MABS v3, v2, v1 | VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo<br/><br/> Compatíveis com volumes com eliminação de duplicação.
**Windows Server 2012 com SP1 (Datacenter e Standard)**<br/><br/> 64/32 bits | MABS v3, v2, v1<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) deve ser instalado. | VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo<br/><br/> Compatíveis com volumes com eliminação de duplicação.
**Windows Server 2012 (Datacenter e Standard)**<br/><br/> 64/32 bits | MABS v1 | VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo<br/><br/> Compatíveis com volumes com eliminação de duplicação.
**Windows 2008 R2 com SP1 (Standard e Enterprise)**<br/><br/> 64/32 bits | Suporte para MABS v3, v2, v1.<br/><br/> O [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) deve ser instalado. | VM local/Azure. |   **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo<br/><br/> Compatíveis com volumes com eliminação de duplicação.
**Windows 2008 R2 (Standard e Enterprise)**<br/><br/> 64/32 bits | MABS v1. No MABS v2/v3, o sistema operacional deve estar executando o SP1. | VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo<br/><br/> Compatíveis com volumes com eliminação de duplicação.
**Windows Server 2008 com SP2**<br/><br/> 64/32 bits | MABS v1, v2, v3 | Somente o v1 é compatível quando o MABS é implantado como um computador físico local ou VM do Hyper-V.<br/><br/> As versões 1, 2 e 3 do MABS são compatíveis quando o MABS é implantado como uma VM do VMware.<br/><br/> Não é compatível com o MABS em execução na VM do Azure | Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.
**Windows Storage Server 2008** | MABS v1, v2, v3 | MABS como servidor físico local/VM do Hyper-V | Não é compatível com o MABS em execução na VM do Azure. | Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.
**SQL Server 2017** | MABS v3 | VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs.
**SQL Server 2016/2016 com SP1** | MABS v3, v2 | VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3, v2, v1 | VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3, v2, V1 | No local. | Fazer backup de servidor autônomo do Exchange, banco de dados no DAG<br/><br/> Recupere a caixa de correio, o banco de dados da caixa de correio no DAG.<br/><br/> Não é compatível com ReFS.<br/><br/> Fazer backup de clusters de discos não compartilhados.<br/><br/> Fazer backup do Exchange Server configurado para replicação contínua.
**SharePoint 2016**<br/><br/> **Sharepoint 2013**<br/><br/> **SharePoint 2010** | MABS v3, v2, v1 | VM local/Azure | Fazer backup de farm, servidor web front-end.<br/><br/> Recupere o farm, banco de dados, aplicativo Web, arquivo ou item de lista, pesquisa do SharePoint, servidor web front-end.<br/><br/> Você não pode fazer backup de um farm usando o AlwaysOn nos bancos de dados de conteúdo.
**Hyper-V no Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (com SP1)** | MABS v3, v2, v1 | Configuração local | **Agente do MABS no host Hyper-V**: Fazer backup de VMs inteiras e arquivos de dados do host. Fazer backup das VMs com armazenamento local, das VMs em cluster com armazenamento CSV, das VMs com armazenamento de servidor de arquivos SMB.<br/><br/> **Agente do MABS na VM convidada**: Fazer backup de cargas de trabalho em execução na VM. CSVs.<br/><br/> **Recuperação**: VM, recuperação em nível de item de VHD/volume/pastas/arquivos.<br/><br/> **VMs Linux**: Fazer backup com o Hyper-V em execução no Windows Server 2012 R2 em diante. A recuperação de VMs do Linux é para o computador inteiro.
**VMs do VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2, v1.<br/><br/> O MABS v1 precisa do pacote cumulativo de atualização 1) | Configuração local | Fazer backup de VMs do VMware no armazenamento CSVs, NFS e SAN.<br/><br/> Recuperar toda a VM.<br/><br/> Backup do Windows/Linux.<br/><br/> Recuperação em nível de item de pasta/arquivos somente para VMs do Windows.<br/><br/> Não é compatível com vApps de VMware.<br/><br/> A recuperação de VMs do Linux é para o computador inteiro.



## <a name="supported-backups-to-dpm"></a>Backups compatíveis com o DPM

A tabela a seguir resume o que pode ser feito backup no DPM das máquinas locais e VMs do Azure.



**Backup** | **DPM** | **Detalhes**
--- | --- | --- 
**Windows 10, 8.1, 8, 7**<br/><br/>(32/64 bits) | Somente no local.<br/><br/> Para fazer backup do Windows 10 com o DPM 2012 R2, é recomendável instalar a [atualização 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager). | Volume/compartilhamento/pasta/arquivo<br/><br/> Compatíveis com volumes com eliminação de duplicação<br/><br/> Os volumes devem ter pelo menos 1 GB e NTFS.
**Windows Server 2016 (Datacenter, Standard, não Nano)**<br/><br/> 64/32 bits | VM local/Azure.<br/><br/> Somente o DPM 2016.| Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal<br/><br/> Compatíveis com volumes com eliminação de duplicação.
**Windows Server 2012 R2 (Datacenter e Standard)**<br/><br/> 64/32 bits | VM local/Azure | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo<br/><br/> Compatível com volumes com eliminação de duplicação a partir do DPM 2012 R2 em diante.
**Windows Server 2012 com SP1 (Datacenter e Standard)**<br/><br/> 64/32 bits | VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo<br/><br/> Compatível com volumes com eliminação de duplicação a partir do DPM 2012 R2 em diante.
**Windows Server 2012 (Datacenter e Standard)**<br/><br/> 64/32 bits |  VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo<br/><br/> Compatível com volumes com eliminação de duplicação a partir do DPM 2012 R2 em diante.
**Windows 2008 R2 com SP1 (Standard e Enterprise)**<br/><br/> 64/32 bits | VM local/Azure <br/><br/> O [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) deve ser instalado. |   **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo.
**Windows 2008 R2 (Standard e Enterprise)**<br/><br/> 64/32 bits | No local.<br/><br/> O DPM não pode ser instalado como uma VM do VMware.<br/><br/> Não é compatível com o DPM em execução na VM do Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal
**Windows Server 2008 com SP2**<br/><br/> 64/32 bits | Somente no local.<br/><br/> O DPM é compatível ao executar como uma VM do VMware. Não é compatível ao executar como um servidor físico ou VM do Hyper-V. | Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.
**Windows Storage Server 2008** | O DPM no local em execução como um servidor físico ou VM do Hyper-V. | Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.
**SQL Server 2017** | O DPM SAC; O DPM 2016 executando o pacote cumulativo de atualizações 5 ou posterior<br/><br/> VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs.
**SQL Server 2016 com SP1** | Não é compatível com o DPM 2012 R2; Compatível com o DPM SAC, o DPM 2016 executando o pacote cumulativo de atualizações 4 ou posterior.<br/><br/> VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs.
**SQL Server 2016** | Não é compatível com o DPM 2012 R2. Compatível com o DPM SAC, o DPM 2016 com o pacote cumulativo de atualizações 2 ou posterior.<br/><br/> VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | SQL Server 2014 com o DPM 2012 R2 executando o pacote cumulativo de atualizações 4 ou posterior.<br/><br/> VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Para o Exchange 2016, o DPM 2012 R2 precisa do pacote cumulativo de atualizações 9 ou posterior.<br/><br/> Configuração local | Fazer backup de servidor autônomo do Exchange, banco de dados no DAG<br/><br/> Recupere a caixa de correio, o banco de dados da caixa de correio no DAG.<br/><br/> Não é compatível com ReFS.<br/><br/> Fazer backup de clusters de discos não compartilhados.<br/><br/> Fazer backup do Exchange Server configurado para replicação contínua.
**SharePoint 2016**<br/><br/> **Sharepoint 2013**<br/><br/> **SharePoint 2010** | SharePoint 2016 no DPM 2016 em diante.<br/><br/>VM local/Azure | Fazer backup de farm, servidor web front-end.<br/><br/> Recupere o farm, banco de dados, aplicativo Web, arquivo ou item de lista, pesquisa do SharePoint, servidor web front-end.<br/><br/> Você não pode fazer backup de um farm usando o AlwaysOn nos bancos de dados de conteúdo.
**Hyper-V no Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (com SP1)** | O Hyper-V no 2016 é compatível com o DPM 2016 em diante.<br/><br/> Configuração local | **Agente do MABS no host Hyper-V**: Fazer backup de VMs inteiras e de arquivos de dados do host. Fazer backup das VMs com armazenamento local, das VMs em cluster com armazenamento CSV, das VMs com armazenamento de servidor de arquivos SMB.<br/><br/> **Agente do MABS na VM convidada**: Fazer backup de cargas de trabalho em execução na VM. CSVs.<br/><br/> **Recuperação**: VM, recuperação em nível de item de VHD/volume/pastas/arquivos.<br/><br/> **VMs Linux**: Fazer backup com o Hyper-V em execução no Windows Server 2012 R2 em diante. A recuperação de VMs do Linux é para o computador inteiro.
**VMs do VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2, v1.<br/><br/> O DPM 2012 R2 precisa do System Center com pacote cumulativo de atualizações 1) | Configuração local | Fazer backup de VMs do VMware no armazenamento CSVs, NFS e SAN.<br/><br/> Recuperar toda a VM.<br/><br/> Backup do Windows/Linux.<br/><br/> Recuperação em nível de item de pasta/arquivos somente para VMs do Windows.<br/><br/> Não é compatível com vApps de VMware.<br/><br/> A recuperação de VMs do Linux é para o computador inteiro.


- Observe que os backups das cargas de trabalho clusterizadas no MABS/DPM devem estar no mesmo domínio que o MABS/DPM ou em um domínio filho/confiável. 
- Você pode usar a autenticação NTLM/certificado para fazer backup de dados em grupos de trabalho ou domínios não confiáveis.



## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura do MABS.
- [Revise](backup-support-matrix-mars-agent.md) o que é compatível com o agente do MARS.
- [Configure](backup-azure-microsoft-azure-backup.md) um servidor MABS.
- [Configurar](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180
