<properties
	pageTitle="Matriz de suporte do Azure Site Recovery | Microsoft Azure"
	description="Resume os sistemas operacionais e componentes com suporte para o Azure Site Recovery"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="09/07/2016"
	ms.author="raynew"/>

# Matriz de suporte do Azure Site Recovery

Este artigo resume os sistemas operacionais e componentes com suporte para implantações do Azure Site Recovery. Uma lista de pré-requisitos e componentes com suporte está disponível para cada cenário de implantação em cada artigo de implantação, mas este tópico os resume.

## Sistemas operacionais com suporte para servidores de virtualização


**Replicar** | **O que é replicado** | **Replicar para** | **SO host**
---|---|---|--- 
**Hosts Hyper-V** | Qualquer carga de trabalho | As tabelas | Windows Server 2012 R2 com as atualizações mais recentes
**Hosts Hyper-V em nuvens do VMM** | Qualquer carga de trabalho | As tabelas | Windows Server 2012 R2 com as atualizações mais recentes
**Hosts Hyper-V em nuvens do VMM** | Qualquer carga de trabalho | Site secundário de VMM | Pelo menos o Windows Server 2012 com as atualizações mais recentes
**Hosts VMware/vCenter** | Qualquer carga de trabalho | As tabelas | vCenter 5.5 ou 6.0 (suporte somente a recursos do 5.5) <br/><br/> vSphere 6.0, 5.5 ou 5.1 com as atualizações mais recentes
**Hosts VMware/vCenter** | Qualquer carga de trabalho | Site secundário do VMware | vCenter 5.5 ou 6.0 (suporte somente a recursos do 5.5) <br/><br/> vSphere 6.0, 5.5 ou 5.1 com as atualizações mais recentes

## Requisitos com suporte para máquinas replicadas

**Replicar** | **O que é replicado** | **Replicar para** | **SO host**
---|---|---|--- 
**VMs Hyper-V** | Qualquer carga de trabalho | As tabelas | Qualquer SO convidado [com suporte do Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> As VMs devem atender aos [requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMs do Hyper-V na nuvem do VMM** | Qualquer carga de trabalho | As tabelas | Qualquer SO convidado [com suporte do Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> As VMs devem atender aos [requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMs do Hyper-V em nuvens do VMM** | Qualquer carga de trabalho | Site secundário de VMM | Qualquer SO convidado [com suporte do Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)
**VMs VMware** | Qualquer carga de trabalho em execução na VM do Windows | As tabelas | Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1<br/><br/> As VMs devem atender aos [requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMs VMware** | Qualquer carga de trabalho em execução na VM Linux | As tabelas | Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 executando o kernel compatível ou o Unbreakable Enterprise Kernel Release 3 (UEK3) do Red Hat <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Armazenamento necessário: Sistema de arquivos (EXT3, ETX4, ReiserFS, XFS); Mapeador de dispositivo por software de vários caminhos (multipath)); Gerenciador de volumes: (LVM2). Não há suporte a servidores físicos com o armazenamento de controlador HP CCISS. Há suporte apenas para o sistema de arquivos ReiserFS no SUSE Linux Enterprise Server 11 SP3.<br/><br/> As VMs devem atender aos [requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMs VMware** | Qualquer carga de trabalho em execução na VM do Windows | Site secundário do VMware | Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1
**VMs VMware** | Qualquer carga de trabalho em execução na VM Linux | Site secundário do VMware | Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 executando o kernel compatível ou o Unbreakable Enterprise Kernel Release 3 (UEK3) do Red Hat <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Armazenamento necessário: Sistema de arquivos (EXT3, ETX4, ReiserFS, XFS); Mapeador de dispositivo por software de vários caminhos (multipath)); Gerenciador de volumes: (LVM2). Não há suporte a servidores físicos com o armazenamento de controlador HP CCISS. Há suporte apenas para o sistema de arquivos ReiserFS no SUSE Linux Enterprise Server 11 SP3.
**Servidores físicos** | Qualquer carga de trabalho em execução no Windows | As tabelas | Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 com pelo menos SP1
**Servidores físicos** | Qualquer carga de trabalho em execução no Linux | As tabelas | Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 executando o kernel compatível ou o Unbreakable Enterprise Kernel Release 3 (UEK3) do Red Hat <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Armazenamento necessário: Sistema de arquivos (EXT3, ETX4, ReiserFS, XFS); Mapeador de dispositivo por software de vários caminhos (multipath)); Gerenciador de volumes: (LVM2). Não há suporte a servidores físicos com o armazenamento de controlador HP CCISS. Há suporte apenas para o sistema de arquivos ReiserFS no SUSE Linux Enterprise Server 11 SP3.
**Servidores físicos** | Qualquer carga de trabalho em execução no Windows | Site secundário | Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 com pelo menos SP1
**Servidores físicos** | Qualquer carga de trabalho em execução no Linux | Site secundário | Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 executando o kernel compatível ou o Unbreakable Enterprise Kernel Release 3 (UEK3) do Red Hat <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Armazenamento necessário: Sistema de arquivos (EXT3, ETX4, ReiserFS, XFS); Mapeador de dispositivo por software de vários caminhos (multipath)); Gerenciador de volumes: (LVM2). Não há suporte a servidores físicos com o armazenamento de controlador HP CCISS. Há suporte apenas para o sistema de arquivos ReiserFS no SUSE Linux Enterprise Server 11 SP3.


## Versões do provedor

**Nome** | **Descrição** | **Última versão** | **Suporte** | **Detalhes**
---|---|---|---| ---
**Provedor do Azure Site Recovery** | Coordena as comunicações entre servidores locais e o Azure/site secundário <br/><br/> Instalado em servidores VMM locais ou servidores Hyper-V, se não houver servidor VMM | 5\.1.1700 (disponível no portal) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Configuração unificada do Azure Site Recovery (VMware para Azure)** | Coordena as comunicações entre servidores VMware locais e o Azure <br/><br/> Instalado nos servidores VMware no locais | 9\.3.4246.1 (disponível no portal) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Serviço de mobilidade** | Coordena a replicação entre servidores VMware/servidores físicos locais e o Azure/site secundário | NA (disponível no portal) | Instalado em cada VM VMware ou servidor físico que você deseja replicar. **Agente de MARS (Serviços de Recuperação do Microsoft Azure)** | Coordena a replicação entre VMs Hyper-V e o Azure<br/><br/> Instalado nos servidores Hyper-V locais (com ou sem um servidor VMM) | 2\.0.8689.0 (disponível no portal) | Esse agente é usado pelo Azure Site Recovery e Backup do Azure). [Saiba mais](https://support.microsoft.com/pt-BR/kb/2997692)

## Próximas etapas

[Preparar para a implantação](site-recovery-best-practices.md)

<!---HONumber=AcomDC_0907_2016-->