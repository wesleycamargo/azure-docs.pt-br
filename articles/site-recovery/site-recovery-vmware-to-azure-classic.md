<properties
	pageTitle="Replicar as máquinas virtuais VMware e os servidores físicos no Azure com o Azure Site Recovery | Microsoft Azure"
	description="Esse artigo descreve como implantar o Azure Site Recovery para coordenar a replicação, o failover e a recuperação de máquinas virtuais VMware e servidores físicos Windows/Linux locais no Azure."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="raynew"/>

# Replicar máquinas virtuais VMware e servidores físicos no Azure com o Azure Site Recovery

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmware-to-azure.md)
- [Portal clássico](site-recovery-vmware-to-azure-classic.md)
- [Portal Clássico (herdado)](site-recovery-vmware-to-azure-classic-legacy.md)


O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. As máquinas podem ser replicadas no Azure ou em um datacenter local secundário. Para uma breve visão geral, leia [O que é o Azure Site Recovery?](site-recovery-overview.md).

## Visão geral

Este artigo descreve como:

- **Replicar máquinas virtuais VMware no Azure** — implante a Recuperação de Site para coordenar a replicação, failover e recuperação de máquinas virtuais do VMware locais no armazenamento do Azure.
- **Replicar servidores físicos no Azure**—Implante o Azure Site Recovery para coordenar a replicação, failover e recuperação de servidores do Windows e Linux físicos locais no Azure.

>[AZURE.NOTE] Este artigo descreve como replicar no Azure. Se você quiser replicar as VMs do VMware ou servidores físicos do Windows/Linux em um datacenter secundário, siga as instruções [neste artigo](site-recovery-vmware-to-vmware.md).

Publique quaisquer comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Implantação avançada

Este artigo contém instruções para uma implantação avançada no portal clássico do Azure. Recomendamos que você use esta versão para todas as novas implantações. Se já tiver implantado usando a versão anterior herdada, recomendamos que você migre para a nova versão. Leia [mais](site-recovery-vmware-to-azure-classic-legacy.md##migrate-to-the-enhanced-deployment) sobre migração.

A implantação avançada é uma atualização importante. Veja um resumo dos aprimoramentos que fizemos:

- **Nenhuma VM de infraestrutura no Azure**: os dados replicam diretamente para uma conta de armazenamento do Azure. Além da replicação e failover, não é preciso configurar nenhuma VM de infraestrutura (servidor de configuração, servidor de destino mestre) como precisávamos na implantação herdada.  
- **Instalação unificada**: uma única instalação permite uma configuração simples e escalabilidade para os componentes locais.
- **Implantação segura**: todo o tráfego é criptografado e as comunicações de gerenciamento da replicação são enviadas por HTTPS 443.
- **Pontos de recuperação**: suporte para falhas e pontos de recuperação consistentes com aplicativos para ambientes do Windows e Linux, e suporte das configurações consistentes com VM única ou várias VMs.
- **Failover de teste**: suporte do failover de teste sem interrupções no Azure, sem afetar a produção ou pausar a replicação.
- **Failover não planejado**: suporte do failover não planejado para o Azure com uma opção avançada para desligar as VMs automaticamente antes do failover.
- **Failback**: failback integrado que replica somente as alterações delta de volta para o site local.
- **vSphere 6.0**: suporte limitado para as implantações do VMware Vsphere 6.0.


## Como a Recuperação de Site ajuda a proteger servidores físicos e máquinas virtuais?


- Os administradores do VMware podem configurar proteção externa de cargas de trabalho de negócios e aplicativos executados em máquinas virtuais VMware para o Azure. Os gerenciadores de servidor podem replicar servidores Windows e Linux físicos locais no Azure.
- O console do Azure Site Recovery fornece um único local para uma instalação simples e para o gerenciamento de replicação, failover e processos de recuperação.
- Se você replicar máquinas virtuais VMware que são gerenciadas por um servidor vCenter, a Recuperação de Site poderá descobrir essas VMs automaticamente. Se os computadores estiverem em um host ESXi, a Recuperação de Site encontrará máquinas virtuais no host.
- Execute facilmente failovers de sua infraestrutura local para o Azure e failback (restauração) do Azure para servidores de máquinas virtuais VMware no site local.
- Configure planos de recuperação que agrupam cargas de trabalho de aplicativos organizadas em camadas, em vários computadores. Você pode fazer failover nesses planos e a Recuperação de Site assegura a consistência de várias VMs para que os computadores executando as mesmas cargas de trabalho possam ser recuperados juntos em um ponto de dados consistente.

## Arquitetura de cenário

Componentes do cenário:

- **Um servidor de gerenciamento local**: o servidor de gerenciamento executa os componentes da Recuperação de Site:
	- **Servidor de configuração**: coordena a comunicação e gerencia os processos de replicação e recuperação de dados.
	- **Servidor de processo**: atua como um gateway de replicação. Ele recebe dados de computadores de origem protegida, otimiza-os com caching, compactação e criptografia e envia os dados de replicação para o armazenamento do Azure. Ele também manipula a instalação por push do Serviço de mobilidade em computadores protegidos e executa a descoberta automática de máquinas virtuais VMware.
	- **Servidor de destino mestre**: lida com dados de replicação durante o failback do Azure. Você também pode implantar um servidor de gerenciamento que atue somente como um servidor de processo para dimensionar sua implantação.
- **Serviço de mobilidade**: esse componente é implantado em cada computador (VM do VMware ou servidor físico) que você deseja replicar no Azure. Ele captura gravações de dados no computador e as encaminha ao servidor de processo.
- **Azure**: você não precisa criar VMs do Azure para lidar com a replicação e o failover. O serviço Recuperação de Site lida com o gerenciamento de dados e os dados são replicados diretamente no armazenamento do Azure. As VMs do Azure replicadas são criadas automaticamente somente quando ocorre o failover no Azure. No entanto, se você quiser fazer failback do Azure para o site local, precisará configurar uma VM do Azure para atuar como um servidor de processo.


O diagrama mostra como esses componentes interagem.

![Arquitetura](./media/site-recovery-vmware-to-azure-classic/architecture.png)

## Planejamento da capacidade

Ao planejar a capacidade, veja o que planejar:

- **Ambiente de origem** — planejamento da capacidade ou da infraestrutura do VMware e requisitos do computador de origem.
- **Servidor de gerenciamento** — planejamento dos servidores de gerenciamento locais que executam os componentes da Recuperação de Site.
- **Largura de banda da rede da origem ao destino** — planejamento da largura de banda necessária para a replicação entre a origem e o Azure

### Considerações sobre o ambiente de origem

- **Taxa de alteração diária máxima** — um computador protegido só pode usar um servidor de processo e um servidor de processo pode lidar com até 2 TB de alteração de dados por dia. Portanto, 2 TB é a taxa de alteração diária máxima com suporte para um computador protegido.
- **Taxa de transferência máxima**— uma máquina replicada pode pertencer a uma conta de armazenamento no Azure. Uma conta de armazenamento padrão pode lidar com um máximo de 20 mil solicitações por segundo e recomendamos que você mantenha o número de IOPS em um computador de origem como 20 mil. Para o exemplo, se você tiver um computador de origem com 5 discos e cada disco gerar 120 IOPS (8K de tamanho) na origem, ele estará dentro do limite IOPS por disco do Azure de 500. O número de contas de armazenamento necessárias = total de IOPs de origem/20000.


### Considerações sobre o servidor de gerenciamento

O servidor de gerenciamento executa os componentes da Recuperação de Site que tratam da replicação, gerenciamento e otimização de dados. Ele deve ser capaz de lidar com a capacidade de taxa de alteração diária em todas as cargas de trabalho em execução em computadores protegidos e ter largura de banda suficiente para replicar continuamente os dados no armazenamento do Azure. Especificamente:

- O servidor de processo recebe dados de replicação de computadores protegidos e os otimiza com caching, compactação e criptografia antes de enviar para o Azure. O servidor de gerenciamento deve ter recursos suficientes para executar essas tarefas.
- O servidor em processo usa o cache baseado em disco. Recomendamos um disco de cache separado de 600 GB ou mais para lidar com alterações de dados armazenados em caso de afunilamento ou interrupção de rede. Durante a implantação, você pode configurar o cache em qualquer unidade que tenha pelo menos 5 GB de armazenamento disponível, mas 600 GB é a recomendação mínima.
- Como prática recomendada, sugerimos que o servidor de gerenciamento esteja localizado na mesma rede e segmento de LAN que os computadores que deseja proteger. Ele pode estar localizado em uma rede diferente, mas os computadores que você deseja proteger devem ter visibilidade de rede L3 para ele.

As recomendações de tamanho para o servidor de gerenciamento estão resumidas na tabela a seguir.

**CPU do servidor de gerenciamento** | **Memória** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos**
--- | --- | --- | --- | ---
8 vCPUs (2 soquetes x 4 núcleos a 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | Implante um servidor de gerenciamento com essas configurações para replicar menos de 100 computadores.
12 vCPUs (2 soquetes x 6 núcleos a 2,5 GHz) | 18 GB | 600 GB | 500 GB a 1 TB | Implante um servidor de gerenciamento com essas configurações para replicar entre 100 e 150 computadores.
16 vCPUs (2 soquetes x 8 núcleos a 2,5 GHz) | 32 GB | 1 TB | 1 TB a 2 TB | Implante um servidor de gerenciamento com essas configurações para replicar entre 150 e 200 computadores.
Implantar outro servidor de processo | | | > 2 TB | Implante servidores de processo adicionais se estiver replicando mais de 200 computadores ou se a taxa de alteração diária de dados ultrapassar 2 TB.

Em que:

- Cada computador de origem é configurado com 3 discos de 100 GB.
- Usamos armazenamento de benchmark de 8 unidades SAS de 10K RPM com RAID 10 para as medidas do disco de cache.

### Largura de banda de rede de origem para destino
Calcule a largura de banda que seria necessária para a replicação inicial e a replicação delta usando a [ferramenta do planejador de capacidade](site-recovery-capacity-planner.md)

#### Limitação de largura de banda usada para replicação

O tráfego VMware replicado no Azure passa por um servidor de processo específico. Você pode limitar a largura de banda que está disponível para replicação de Recuperação de Site no servidor da seguinte maneira:

1. Abra o snap-in de MMC de Backup do Microsoft Azure no servidor de gerenciamento principal ou em um servidor de gerenciamento com mais servidores de processo provisionados. Por padrão, um atalho para o Backup do Microsoft Azure é criado na área de trabalho ou você pode encontrá-lo em: C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.

	![Limitar a largura de banda](./media/site-recovery-vmware-to-azure-classic/throttle1.png)

3. Na guia **Limitação**, especifique a largura de banda que pode ser usada para a replicação de Recuperação de Site e o agendamento aplicável.

	![Limitar a largura de banda](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

Opcionalmente, você também pode definir a limitação usando o PowerShell. Aqui está um exemplo:

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### Maximizar o uso da largura de banda
Para aumentar a largura de banda utilizada para replicação pelo Azure Site Recovery, seria necessário alterar uma chave de registro.

A chave a seguir controla o número de threads por disco de replicação usado durante a replicação

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 Em uma rede "sobreprovisionada", essa chave de registro precisa ser alterada a partir dos valores padrão. Damos suporte ao máximo de 32.


[Saiba mais](site-recovery-capacity-planner.md) sobre o planejamento da capacidade detalhado.

### Servidores de processo adicionais

Se precisar proteger mais de 200 computadores ou se a taxa de alteração diária é maior que 2 TB, você pode adicionar mais servidores para lidar com a carga. Para escalar horizontalmente, você pode:

- Aumentar o número de servidores de gerenciamento. Por exemplo, você pode proteger até 400 computadores com dois servidores de gerenciamento.
- Adicionar servidores de processo adicionais e usá-los para lidar com o tráfego em vez (ou além) do servidor de gerenciamento.

Esta tabela descreve um cenário em que:

- Você configura o servidor de gerenciamento original para usá-lo somente como um servidor de configuração.
- Você configura um servidor de processo adicional.
- Você configura máquinas virtuais para usar o servidor de processo adicional.
- Cada computador de origem protegido é configurado com três discos de 100 GB cada.

**Servidor de gerenciamento original**<br/><br/>(servidor de configuração) | **Servidor de processo adicional**| **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos**
--- | --- | --- | --- | ---
8 vCPUs (2 soquetes x 4 núcleos a 2,5 GHz), 16 GB de memória | 4 vCPUs (2 soquetes x 2 núcleos a 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Você pode replicar 85 computadores ou menos.
8 vCPUs (2 soquetes x 4 núcleos a 2,5 GHz), 16 GB de memória | 8 vCPUs (2 soquetes x 4 núcleos a 2,5 GHz), 12 GB de memória | 600 GB | 250 GB a 1 TB | Você pode replicar entre 85 e 150 computadores.
12 vCPUs (2 soquetes x 6 núcleos a 2,5 GHz), 18 GB de memória | 12 vCPUs (2 soquetes x 6 núcleos a 2,5 GHz), 24 GB de memória | 1 TB | 1 TB a 2 TB | Você pode replicar entre 150 e 225 máquinas.


A maneira como você dimensiona seus servidores depende de sua preferência por um modelo que escale verticalmente ou horizontalmente. Você pode escalar verticalmente implantando alguns servidores de processo e de gerenciamento de alto nível ou escalar horizontalmente implantando mais servidores com menos recursos. Por exemplo: se você precisar proteger os 220 computadores, poderá fazer o seguinte:

- Configure o servidor de gerenciamento original com 12vCPU, 18 GB de memória, um servidor de processo adicional com 12vCPU, 24 GB de memória e os computadores protegidos para usar somente o servidor de processo adicional.
- Ou, como alternativa, você pode configurar dois servidores de gerenciamento (2 x 8vCPU, 16 GB de RAM) e dois servidores de processo adicionais (1 x 8vCPU e 4vCPU x1 para lidar com 135 + 85, 220 computadores) e configurar computadores protegidos para usar somente os servidores de processo adicionais.


[Siga estas instruções](#deploy-additional-process-servers) para configurar um servidor de processo adicional.

## Antes de começar a implantação

As tabelas resumem os pré-requisitos para implantar esse cenário.


### Pré-requisitos do Azure

**Pré-requisito** | **Detalhes**
--- | ---
**Conta do Azure**| Você precisará de uma conta do [Microsoft Azure](https://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site.
**Armazenamento do Azure** | Você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados no Azure. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover. <br/><br/>Você precisa de uma [conta de armazenamento com redundância geográfica padrão](../storage/storage-redundancy.md#geo-redundant-storage). A conta deve estar localizada na mesma região que o serviço de Recuperação de Site e associada à mesma assinatura. Observe que a replicação para contas de armazenamento premium atualmente não tem suporte e não deve ser usada.<br/><br/>Não há suporte para a migração de contas de armazenamento criadas usando o [novo Portal do Azure](../storage/storage-create-storage-account.md) em grupos de recursos.[Leia sobre](../storage/storage-introduction.md) o armazenamento do Azure.<br/><br/>
**Rede do Azure** | Você precisará de uma rede virtual do Azure com a qual as máquinas virtuais do Azure se conectarão quando ocorrer failover. A rede virtual do Azure tem que estar na mesma região do cofre da Recuperação de Site.<br/><br/>Observe que para fazer o failback depois do failover no Azure, você precisará de uma conexão VPN (ou Rota Expressa do Azure) configurada da rede do Azure para o site local.


### Pré-requisitos do local

**Pré-requisito** | **Detalhes**
--- | ---
**Servidor de gerenciamento** | Você precisa de um servidor Windows 2012 R2 local em execução em uma máquina virtual ou em um servidor físico. Todos os componentes locais da Recuperação de Site estão instalados nesse servidor de gerenciamento<br/><br/> Recomendamos que você implante o servidor como uma VM do VMware altamente disponível. O failback para o site local do Azure é sempre para máquinas virtuais VMware, independentemente de o failover ter sido em VMs ou em servidores físicos. Se você não configurar o Servidor de gerenciamento como uma VM da VMware, será necessário configurar um servidor de destino mestre separado como uma VM da VMware para receber o tráfego de failback.<br/><br/>O servidor não deve ser um Controlador de Domínio.<br/><br/>O servidor deve ter um endereço IP estático.<br/><br/>O nome do host do servidor deve ter 15 caracteres ou menos.<br/><br/>A localidade do sistema operacional deve estar somente em inglês.<br/><br/>O servidor de gerenciamento requer acesso à Internet.<br/><br/>É necessário ter o acesso de saída do servidor da seguinte maneira: acesso temporário em HTTP 80 durante a instalação dos componentes da Recuperação de Site (para baixar o MySQL); acesso de saída contínuo em HTTPS 443 para o gerenciamento de replicação; acesso de saída contínuo em HTTPS 9443 para o tráfego de replicação (essa porta pode ser modificada)<br/><br/> Verifique se essas URLs são acessíveis no servidor de gerenciamento: <br/>- *.hypervrecoverymanager.windowsazure.com<br/>- *.accesscontrol.windows.net<br/>- *.backup.windowsazure.com<br/>- *.blob.core.windows.net<br/>- *.store.core.windows.net<br/>-https://www.msftncsi.com/ncsi.txt<br/>- [ https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi "https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>Se você tiver regras de firewall baseadas em endereço IP no servidor, verifique se as regras permitem a comunicação com o Azure. Você precisará permitir os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) e o protocolo HTTPS (433). Você também precisará colocar intervalos de endereços IP para a região de sua assinatura do Azure e para o Oeste dos EUA na lista branca. A URL [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi "https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi") é usada para baixar o MySQL.
**VMware vCenter/host ESXi**: | Você precisa de um ou mais hipervisores vMware vSphere ESX/ESXi gerenciando suas máquinas virtuais do VMware e executando o ESX/ESXi versão 6.0, 5.5 ou 5.1 com as atualizações mais recentes.<br/><br/> Recomendamos que você implante um servidor VMware vCenter para gerenciar os hosts ESXi. Ele deve executar o vCenter versão 6.0 ou 5.5 com as atualizações mais recentes.<br/><br/>Observe que a Recuperação de Site não dá suporte aos novos recursos do vCenter e vSphere 6.0, como o vCenter vMotion cruzado, os volumes virtuais e o DRS de armazenamento. O suporte à Recuperação de Site está limitado a recursos que também estavam disponíveis na versão 5.5.
**Computadores protegidos**: | **AZURE**<br/><br/>Os computadores que você deseja proteger devem estar em conformidade com os [pré-requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) para a criação de VMs do Azure.<br><br/>Se você quiser conectar-se às VMs do Azure após o failover, precisará habilitar as conexões da Área de Trabalho Remota no firewall local.<br/><br/>A capacidade de disco individual nos computadores protegidos não deve ser maior que 1.023 GB. Uma VM pode ter até 64 discos (portanto, até 64 TB). Se você tiver discos com mais de 1 TB, considere o uso da replicação de banco de dados, como o SQL Server Always On ou Oracle Data Guard<br/><br/>Não há suporte para clusters convidados de disco compartilhado. Se você tiver uma implantação clusterizada, considere o uso da replicação de banco de dados, como o SQL Server Always On ou Oracle Data Guard.<br/><br/>Não há suporte para inicialização UEFI (Unified Extensible Firmware Interface)/EFI (Extensible Firmware Interface).<br/><br/>Os nomes dos computadores devem conter entre 1 e 63 caracteres (letras, números e hifens). O nome deve começar com uma letra ou número e terminar com uma letra ou número. Assim que o computador estiver protegido, será possível modificar o nome do Azure.<br/><br/>**VMs da VMware**<br/><br>Você precisará instalar o VMware vSphere Power CLI 6.0. no servidor de gerenciamento (servidor de configuração).<br/><br/>As VMs da VMware que você deseja proteger devem ter as ferramentas da VMware instaladas e em execução.<br/><br/>Se a VM de origem tiver um agrupamento NIC, ele será convertido em uma única NIC após o failover no Azure.<br/><br/>Se as VMs protegidas tiverem um disco iSCSI, a Recuperação de Site converterá o disco iSCSI da VM protegida em um arquivo VHD após o failover da VM no Azure. Se o destino iSCSI puder ser alcançado pela VM do Azure, ela se conectará ao destino iSCSI e verá basicamente dois discos: o disco VHD na VM do Azure e o disco iSCSI de origem. Nesse caso, será necessário desconectar o destino iSCSI que aparece na VM do Azure com failover.<br/><br/>[Saiba mais](#vmware-permissions-for-vcenter-access) sobre as permissões de usuário da VMware necessárias para a Recuperação de Site.<br/><br/> **COMPUTADORES COM WINDOWS SERVER (na VM da VMware ou no servidor físico)**<br/><br/>O servidor deve executar um sistema operacional de 64 bits com suporte: Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 com, no mínimo, SP1.<br/><br/>O sistema operacional deve estar instalado na unidade C:\\ e o disco do sistema operacional deve ser um disco básico do Windows (o sistema operacional não deve ser instalado em um disco dinâmico do Windows).<br/><br/>Para os computadores com Windows Server 2008 R2, será necessário ter o .NET Framework 3.5.1 instalado.<br/><br/>Você precisará fornecer uma conta de administrador (deve ser um administrador local no computador com Windows) para a instalação por push do Serviço de Mobilidade nos servidores do Windows. Se a conta fornecida não for uma conta de domínio, você precisará desabilitar o controle Acesso de Usuário Remoto no computador local. [Saiba mais](#install-the-mobility-service-with-push-installation).<br/><br/>A Recuperação de Site dá suporte a VMs com disco RDM. Durante o failback, a Recuperação de Site reutilizará o disco RDM se o disco RDM e a VM de origem estiverem disponíveis. Se eles não estiverem disponíveis, durante o failback, a Recuperação de Site criará um novo arquivo VMDK para cada disco.<br/><br/>**COMPUTADORES COM LINUX**<br/><br/>Você precisará de um sistema operacional de 64 bits com suporte: Red Hat Enterprise Linux 6.7; Centos 6.5, 6.6, 6.7; Oracle Enterprise Linux 6.4, 6.5 que executa o kernel compatível com Red Hat ou UEK3 (Unbreakable Enterprise Kernel Versão 3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>Os arquivos /etc/hosts nos computadores protegidos devem conter entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede. <br/><br/>Se você quiser conectar-se a uma máquina virtual do Azure que executa o Linux após o failover com um cliente SSH (Secure Shell), verifique se o serviço Secure Shell na máquina protegida foi definido para ser iniciado automaticamente na inicialização do sistema e se as regras de firewall permitem uma conexão SSH com ele.<br/><br/>A proteção só pode ser habilitada para computadores com Linux com o seguinte armazenamento: Sistema de arquivos (EXT3, ETX4, ReiserFS, XFS); Mapeador de Dispositivo por software multipath (multipath); Gerenciador de volumes: (LVM2). Não há suporte a servidores físicos com o armazenamento de controlador HP CCISS. Só há suporte para o sistema de arquivos ReiserFS no SUSE Linux Enterprise Server 11 SP3.<br/><br/>A Recuperação de Site dá suporte às VMs com disco RDM. Durante o failback para Linux, a Recuperação de Site não reutiliza o disco RDM. Em vez disso, ele cria um novo arquivo VMDK para cada disco RDM correspondente.

Somente para VM do Linux: verifique se você definiu a configuração disk.enableUUID=true nos Parâmetros de Configuração da VM no VMware. Se essa linha não existir, adicione-a. Isso é necessário para fornecer um UUID consistente para o VMDK para que ele seja montado corretamente. Observe também que, sem essa configuração, o failback causará um download completo, mesmo que a VM esteja disponível no local. Adicionar essa configuração garantirá que apenas as alterações delta sejam transferidas durante o failback.

## Etapa 1: criar um cofre

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com/).
2. Expanda **Serviços de Dados** > **Serviços de Recuperação** e clique em **Cofre de Recuperação de Site**.
3. Clique em **Criar Novo** > **Criação Rápida**.
4. Em **Nome**, digite um nome amigável para identificar o cofre.
5. Em **Região**, selecione a região geográfica para o cofre. Para verificar as regiões suportadas, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços de Recuperação de Site do Azure](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Clique em **Criar cofre**.![Novo cofre](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como **Ativo** na página principal de **Serviços de Recuperação**.

## Etapa 2: configurar uma rede do Azure

Configure uma rede do Azure para que as VMs do Azure sejam conectadas a uma rede após o failover e para que o failback para o site local possa funcionar conforme o esperado.

1. No Portal do Azure > **Criar rede virtual**, especifique o nome da rede. Intervalo e nome da sub-rede do endereço IP.
2. Você precisaria adicionar VPN/Rota Expressa à rede se precisar fazer failback. A VPN/Rota Expressa pode ser adicionada à rede até mesmo após o failover.

[Leia mais](../virtual-network/virtual-networks-overview.md) sobre as redes do Azure.

## Etapa 3: instalar os componentes do VMware

Se você quiser replicar máquinas virtuais VMware, instale os seguintes componentes de VMware no servidor de gerenciamento:

1. [Baixe](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) e instale o VMware vSphere Power CLI 6.0.
2. Reinicie o servidor.


## Etapa 4: baixar uma chave de registro do cofre

1. No servidor de gerenciamento, abra o console de Recuperação de Site no Azure. Na página **Serviços de Recuperação**, clique no cofre para abrir a página de Início Rápido. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

	![Ícone de Inicialização Rápida](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)

2. Na página **Início Rápido**, clique em **Preparar Recursos de Destino** > **Baixar uma chave de registro**. O arquivo de registro é gerado automaticamente. Ele é válido por cinco dias após ter sido gerado.


## Etapa 5: instalar o servidor de gerenciamento
> [AZURE.TIP] Verifique se que essas URLs podem ser acessadas a partir do servidor de gerenciamento:
>
- *.hypervrecoverymanager.windowsazure.com
- *.accesscontrol.windows.net
- *.backup.windowsazure.com
- *.blob.core.windows.net
- *.store.core.windows.net
- https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi
- https://www.msftncsi.com/ncsi.txt




[AZURE.VIDEO enhanced-vmware-to-azure-setup-registration]

1. Na página **Início Rápido**, baixe o arquivo de instalação unificada no servidor.
2. Execute o arquivo de instalação para iniciar a instalação no Assistente de Instalação Unificada de Recuperação de Site.
3. Em **Antes de começar**, selecione **Instalar o servidor de configuração e o servidor de processo**. Dependendo do tamanho de sua implantação, talvez seja necessário adicionar mais servidores de processo posteriormente, mas não ao configurar essa implantação pela primeira vez.

	![Antes de começar](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)

4. Em **Instalação de Software de Terceiros**, clique em **Aceitar** para baixar e instalar o MySQL.

	![Software de terceiros](./media/site-recovery-vmware-to-azure-classic/combined-wiz2.png)

5. Em **Configurações de Internet**, especifique como o Provedor que será instalado no servidor se conectará ao Azure Site Recovery pela Internet.

	- Se você quiser que o Provedor se conecte diretamente, selecione **Conectar-se diretamente sem um proxy**.
	- Se você quiser conectar-se com o proxy que está configurado atualmente no servidor, selecione **Conectar-se com as configurações de proxy existentes**.
	- Se o proxy existente exigir autenticação ou se você quiser usar um proxy personalizado para a conexão do Provedor, selecione **Conectar-se com as configurações personalizadas do proxy**.
	- Se você usar um proxy personalizado, precisará especificar o endereço, a porta e as credenciais
	- Se você estiver usando um proxy, as seguintes URLs deverão estar acessíveis por meio dele:

	![Firewall](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)

7. Em **Verificação de Pré-requisitos**, a configuração executa uma verificação de pré-requisitos no servidor.

	![Pré-requisitos](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

>[AZURE.WARNING] Se você vir um aviso para a verificação de pré-requisitos da **Sincronização de Horário Global**, verifique se a hora no relógio do sistema é a mesma do fuso horário.

![TimeSyncIssue](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

8. Em **Configuração do MySQL**, crie credenciais para fazer logon na instância do servidor MySQL. Você pode especificar os seguintes caracteres especiais: '\_', '!', ' @', '$', ' \\', '%'.

	![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

9. Em **Detalhes do Ambiente**, especifique se você pretende replicar as VMs da VMware. Se a resposta for afirmativa, a instalação verificará se o PowerCLI 6.0 está instalado.

	![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)

10. Em **Local de Instalação**, selecione o local em que você deseja instalar os binários e armazenar o cache. Recomendamos que a unidade de cache tenha 600 GB ou mais de espaço livre.

	![Local de instalação](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)

11. Em **Seleção de Rede**, especifique o ouvinte (adaptador de rede e porta SSL) no qual o servidor enviará e receberá os dados de replicação. Você pode modificar a porta padrão (9443). Além dessa porta, a porta 443 será aberta no servidor para enviar e receber informações sobre a orquestração da replicação. A 443 não deve ser usada para dados de replicação.


	![Seleção de rede](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)

12. Em **Registro**, procure e escolha a chave de registro que você baixou do cofre.

	![Registro](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)

13.  Em **Resumo**, examine as informações.

	![Resumo](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)
>[AZURE.WARNING] O proxy do agente de serviço de recuperação do Microsoft Azure precisa ser configurado. Quando a instalação for concluída, inicie um aplicativo chamado “Shell de Serviços de Recuperação do Microsoft Azure” no menu Iniciar do Windows. Na janela de comando que abre, execute o seguinte conjunto de comandos para configurar as configurações do servidor proxy.
>
	$pwd = ConvertTo-SecureString -String ProxyUserPassword
	Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\\username -ProxyPassword $pwd
	net stop obengine
	net start obengine



### Executar a instalação a partir da linha de comando

Você também pode executar o assistente unificado a partir da linha de comando da seguinte maneira:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Em que:

- /ServerMode: obrigatório. Especifica se a instalação deve instalar servidores de configuração e de processo ou somente o servidor de processo (usado para instalar servidores de processo adicionais). Valores de entrada: CS, PS.
- InstallDrive: obrigatório. Especifica a pasta onde os componentes estão instalados.
- /MySQLCredFilePath. Obrigatório. Especifica o caminho para um arquivo onde as credenciais do servidor MySQL estão instaladas. Obtenha o modelo para criar o arquivo.
- /VaultCredFilePath. Obrigatório. Local do arquivo de credenciais do cofre
- /EnvType. Obrigatório. Tipo de instalação. Valores: VMware, NonVMware
- /PSIP e /CSIP. Obrigatório. Endereço IP do servidor de processo e do servidor de configuração.
- /PassphraseFilePath. Obrigatório. Local do arquivo de senha.
- /ByPassProxy. Opcional. Especifica se o servidor de gerenciamento se conecta ao Azure sem um proxy.
- /ProxySettingsFilePath. Opcional. Especifica as configurações para um proxy personalizado (proxy padrão no servidor que requer autenticação ou proxy personalizado)




## Etapa 6: configurar as credenciais para o servidor vCenter

> [AZURE.VIDEO enhanced-vmware-to-azure-discovery]

O servidor de processo pode descobrir automaticamente máquinas virtuais VMware que são gerenciadas por um servidor vCenter. Para a descoberta automática, a Recuperação de Site precisa de uma conta e de credenciais que possam acessar o servidor vCenter. Isso não é relevante se você estiver replicando apenas os servidores físicos.

Faça isso da seguinte forma:

1. No servidor vCenter, crie uma função (**Azure\_Site\_Recovery**) no nível do vCenter com as [permissões necessárias](#vmware-permissions-for-vcenter-access).
2. Atribua a função **Azure\_Site\_Recovery** a um usuário do vCenter.

	>[AZURE.NOTE] Uma conta de usuário do vCenter que tenha a função somente leitura pode executar failover sem desligar os computadores de origem protegida. Se você quiser desligar as máquinas, você precisará da função Azure\_Site\_Recovery. Observe que se você estiver migrando apenas as máquinas virtuais do VMware para o Azure e não precisar fazer failback, a função somente leitura será suficiente.

3. Para adicionar a conta, abra **cspsconfigtool**. Ela está disponível como um atalho na área de trabalho e está localizada na pasta [LOCAL DE INSTALAÇÃO]\\home\\svsystems\\bin.
2. na guia **Gerenciar Contas**, clique em **Adicionar Conta**.

	![Adicionar conta](./media/site-recovery-vmware-to-azure-classic/credentials1.png)

3. Em **Detalhes da Conta**, adicione as credenciais que podem ser usadas para acessar o servidor vCenter. Observe que pode levar mais de 15 minutos para que o nome da conta apareça no portal. Para atualizar imediatamente, clique em Atualizar na guia **Servidores de Configuração**.

	![Detalhes](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## Etapa 7: adicionar servidores vCenter e hosts ESXi

Se você estiver replicando máquinas virtuais VMware, precisará adicionar um servidor vCenter (ou host ESXi).

1. Na guia **Servidores** > **Servidores de Configuração**, selecione o servidor de configuração > **Adicionar servidor vCenter**.

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)

2. Adicione o servidor vCenter ou os detalhes do host ESXi, o nome da conta especificada para acessar o servidor vCenter na etapa anterior e o servidor de processo que será usado para descobrir máquinas virtuais VMware que são gerenciadas pelo servidor vCenter. Observe que o servidor vCenter ou host ESXi deve estar localizado na mesma rede que o servidor no qual o servidor de processo está instalado.

	>[AZURE.NOTE] Se você estiver adicionando o servidor vCenter ou o host ESXi com uma conta que não tem privilégios de administrador no servidor vCenter ou servidor host, verifique se o vCenter ou as contas ESXi têm esses privilégios habilitados: Datacenter, Armazenamento de Dados, Pasta, Jost, Rede, Recursos, Máquina virtual, vSphere Distributed Switch. Além disso, o servidor vCenter precisa do privilégio de exibição do Armazenamento.

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)

3. Após a conclusão da descoberta, o servidor vCenter será listado na guia **Servidores de Configuração**.

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)


## Etapa 8: criar um grupo de proteção

> [AZURE.VIDEO enhanced-vmware-to-azure-protection]


Grupos de proteção são agrupamentos lógicos de máquinas virtuais ou servidores físicos que você deseja proteger usando as mesmas configurações de proteção. Aplique as configurações de proteção a um grupo de proteção, e elas serão aplicadas a todas as máquinas virtuais/servidores físicos que você adicionar ao grupo.

1. Abra **Itens Protegidos** > **Grupo de Proteção** e clique para adicionar um grupo de proteção.

	![Criar grupo de proteção](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)

2. Na página **Especificar Configurações do Grupo de Proteção**, especifique um nome para o grupo e em **De**, selecione o servidor de configuração no qual você deseja criar o grupo. O **Destino** é Azure.

	![Configurações do grupo de proteção](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)

3. Na página **Especificar Configurações de Replicação**, defina as configurações de replicação que serão usadas para todos os computadores do grupo.

	![Replicação do grupo de proteção](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

	- **Consistência de várias VMs**: se você ativar essa configuração, ela cria pontos de recuperação compartilhados consistentes com aplicativos nos computadores do grupo de proteção. Essa configuração é mais relevante quando todos os computadores no grupo de proteção estão executando a mesma carga de trabalho. Todos os computadores serão recuperados para o mesmo ponto de dados. Esse recurso estará disponível se você estiver replicando máquinas virtuais VMware ou servidores físicos Windows/Linux.
	- **Limite de RPO**: define o RPO. Alertas serão gerados quando a replicação de proteção de dados contínuos exceder o valor do limite de RPO configurado.
	- **Retenção do ponto de recuperação**: especifica a janela de retenção. Computadores protegidos podem ser recuperados para qualquer ponto nessa janela.
	- **Frequência do instantâneo consistente com aplicativo**: especifica com que frequência são criados os pontos de recuperação que incluam instantâneos consistentes com aplicativos.

Quando você clicar na marca de seleção, um grupo de proteção será criado com o nome especificado. Além disso, um segundo grupo de proteção será criado com o nome < protection-group-name-Failback). Esse grupo de proteção será usado se você fizer failback no site local após o failover no Azure. Você pode monitorar os grupos de proteção à medida que eles são criados na página **Itens Protegidos**.

## Etapa 9: instalar o Serviço de mobilidade manualmente

A primeira etapa da habilitação da proteção para máquinas virtuais e servidores físicos é a instalação do Serviço de mobilidade. É possível fazer isso de duas formas:

- Do servidor de processo, enviar por push e instalar automaticamente o serviço em cada computador. Observe que, ao adicionar computadores a um grupo de proteção e eles já estiverem executando uma versão adequada do Serviço de mobilidade, a instalação por push não ocorrerá.
- Instale automaticamente o serviço usando um método por push corporativo, como o WSUS ou o System Center Configuration Manager. Verifique se que você configurou o servidor de gerenciamento antes de fazer isso.
- Instale manualmente em cada computador que você deseja proteger. Verifique se você configurou o servidor de gerenciamento antes de fazer isso.


### Instalar o Serviço de mobilidade com a instalação por push

Quando você adiciona computadores a um grupo de proteção, o Serviço de mobilidade é enviado automaticamente e instalado em cada computador pelo servidor em processo.


#### Preparar para o envio por push automático em computadores com Windows

Veja como preparar os computadores com Windows para que o Serviço de mobilidade possa ser instalado automaticamente pelo servidor de processo.

1.  Crie uma conta que pode ser usada pelo servidor de processo para acessar o computador. A conta deve ter privilégios de administrador (local ou domínio). Observe que essas credenciais são usadas somente para a instalação por push do Serviço de mobilidade.

	>[AZURE.NOTE] Se você não estiver usando uma conta de domínio, precisará desabilitar o controle Acesso de Usuário Remoto no computador local. Para fazer isso, no registro, em HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System, adicione a entrada DWORD LocalAccountTokenFilterPolicy com um valor de 1. Para adicionar a entrada de Registro de um comando de abertura da CLI ou usando o PowerShell, insira **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  No Firewall do Windows do computador que você deseja proteger, selecione **Permitir um aplicativo ou recurso pelo Firewall** e habilite o **Compartilhamento de Arquivo e Impressora** e a **Instrumentação de Gerenciamento do Windows**. Para computadores que pertencem a um domínio, você pode configurar a política de firewall com um GPO.

	![Configurações de firewall](./media/site-recovery-vmware-to-azure-classic/mobility1.png)

2. Adicione a conta que você criou:

	- Abra **cspsconfigtool**. Ela está disponível como um atalho na área de trabalho e está localizada na pasta [LOCAL DE INSTALAÇÃO]\\home\\svsystems\\bin.
	- Na guia **Gerenciar Contas**, clique em **Adicionar Conta**.
	- Adicione a conta que você criou. Depois de adicionar a conta, será preciso fornecer as credenciais quando adicionar um computador a um grupo de proteção.


#### Preparar para o envio por push automático em servidores Linux

1.	Verifique se há suporte para o computador com Linux que você deseja proteger, conforme descrito em [Pré-requisitos locais](#on-premises-prerequisites). Verifique se há conectividade de rede entre o computador que você deseja proteger e o servidor de gerenciamento que executa o servidor de processo.

2.	Crie uma conta que pode ser usada pelo servidor de processo para acessar o computador. A conta deve ser de um usuário raiz no servidor Linux de origem. Observe que essas credenciais são usadas somente para a instalação por push do Serviço de mobilidade.

	- Abra **cspsconfigtool**. Ela está disponível como um atalho na área de trabalho e está localizada na pasta [LOCAL DE INSTALAÇÃO]\\home\\svsystems\\bin.
	- Na guia **Gerenciar Contas**, clique em **Adicionar Conta**.
	- Adicione a conta que você criou. Depois de adicionar a conta, será preciso fornecer as credenciais quando adicionar um computador a um grupo de proteção.

3.	Verifique se o arquivo /etc/hosts no servidor Linux de origem contém entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede.
4.	Instale os últimos pacotes openssh, openssh-server e openssl no computador que você deseja proteger.
5.	Verifique se SSH está habilitado e em execução na porta 22.
6.	Habilite a autenticação de subsistema e senha SFTP no arquivo sshd\_config, como se segue:

	- Faça logon como raiz.
	- No arquivo /etc/ssh/sshd\_config, localize a linha que começa com PasswordAuthentication.
	- Remova a marca de comentário da linha e altere o valor de **no** para **yes**.
	- Localize a linha que começa com **Subsystem** e remova a marca de comentário dela.

		![Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)


### Instalar o Serviço de Mobilidade manualmente

Os instaladores estão disponíveis em C:\\Program Files (x86) \\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository.

Sistema operacional de origem | Arquivo de instalação do Serviço de mobilidade
--- | ---
Windows Server (somente 64 bits) | Microsoft-ASR\_UA\_9.*.0.0\_Windows\_* release.exe
CentOS 6.4, 6.5, 6.6 (somente 64 bits) | Microsoft-ASR\_UA\_9.*.0.0\_RHEL6-64\_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (somente 64 bits) | Microsoft-ASR\_UA\_9.*.0.0\_SLES11-SP3-64\_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (somente 64 bits) | Microsoft-ASR\_UA\_9.*.0.0\_OL6-64\_*release.tar.gz


#### Instalar manualmente em um servidor Windows


1. Baixe e execute o instalador relevante.
2. Em **Antes de começar**, selecione **Serviço de mobilidade**.

	![Serviço de mobilidade](./media/site-recovery-vmware-to-azure-classic/mobility3.png)

3. Em **Detalhes do Servidor de Configuração**, especifique o endereço IP do servidor de gerenciamento e a senha que foi gerada quando você instalou os componentes do servidor de gerenciamento. Você pode recuperar a senha executando: **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n** no servidor de gerenciamento.

	![Serviço de mobilidade](./media/site-recovery-vmware-to-azure-classic/mobility6.png)

4. Em **Local de Instalação**, deixe o local padrão e clique em **Avançar** para iniciar a instalação.
5. Em **Progresso da Instalação**, monitore a instalação e, se for solicitado, reinicie o computador.

Você também pode instalar a partir da linha de comando:

UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]

Em que:

- /Role: obrigatório. Especifica se o Serviço de mobilidade deve ser instalado.
- /InstallLocation: obrigatório. Especifica onde instalar o serviço.
- /PassphraseFilePath: obrigatório. Especifica a senha do servidor de configuração.
- /LogFilePath: obrigatório. Especifica o local dos arquivos de configuração de log

#### Modificar o endereço IP do servidor de gerenciamento

Após executar o assistente, você pode modificar o endereço IP do servidor de gerenciamento da seguinte maneira:

1. Abra o arquivo hostconfig.exe (localizado na área de trabalho).
2. Na guia **Global**, é possível alterar o endereço IP do servidor de gerenciamento.

	>[AZURE.NOTE] Você deve alterar somente o endereço IP do servidor de gerenciamento. O número da porta para comunicações do servidor de gerenciamento deve ser 443 e a opção Usar HTTPS deve estar habilitada para a esquerda. A senha não deve ser modificada.

	![Endereço IP do servidor de gerenciamento](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### Instale manualmente em um servidor Linux:

1. Copie o arquivo tar apropriado baseado na tabela acima para a máquina Linux que você deseja proteger.
2. Abra um programa do shell e extraia o arquivo tar compactado em um caminho local executando: `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Crie um arquivo passphrase.txt no diretório local para o qual você extraiu o conteúdo do arquivo tar. Para fazer isso, copie a senha de C:\\ProgramData\\Microsoft Azure Site Recovery\\private\\connection.passphrase no servidor de gerenciamento e salve-a em passphrase.txt executando *`echo <passphrase> >passphrase.txt`* no shell.
4. Para instalar o Serviço de Mobilidade, insira *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especifique o endereço IP interno do servidor de gerenciamento e verifique se a porta 443 está selecionada.

**Também é possível instalar por meio da linha de comando**:

1. Copie a senha em C:\\Program Files (x86)\\InMage Systems\\private\\connection no servidor de gerenciamento e salve-a como "passphrase.txt" no servidor de gerenciamento. Em seguida, execute estes comandos. Em nosso exemplo, o endereço IP do servidor de gerenciamento é 104.40.75.37 e a porta HTTPS deve ser 443:

Para instalar em um servidor de produção:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Para instalar no servidor mestre de destino:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## Etapa 10: habilitar a proteção para um computador

Para habilitar a proteção, adicione máquinas virtuais e servidores físicos a um grupo de proteção. Antes de começar, observe as seguintes condições se você estiver protegendo máquinas virtuais VMware:

- As máquinas virtuais VMware são descobertas a cada 15 minutos e podem levar mais de 15 minutos para aparecer no portal de Recuperação de Site após a descoberta.
- As alterações de ambiente na máquina virtual (como instalação de ferramentas VMware) também podem levar mais de 15 minutos para serem atualizadas na Recuperação de Site.
- É possível verificar a hora da última descoberta de VMs da VMware no campo **Último Contato Em** do servidor vCenter/host ESXi, na guia **Servidores de Configuração**.
- Se você tiver um grupo de proteção já criado e adicionar um Servidor vCenter ou host ESXi depois disso, poderá demorar mais de 15 minutos para que o portal do Azure Site Recovery seja atualizado e para que as máquinas virtuais sejam listadas na caixa de diálogo **Adicionar computadores a um grupo de proteção**.
- Se quiser continuar imediatamente com a adição de computadores ao grupo de proteção sem precisar esperar pela descoberta agendada, destaque o servidor de configuração (não clique nele) e clique no botão **Atualizar**.

Além disso, observe que:

- Recomendamos que você arquitete seus grupos de proteção para que reflitam suas cargas de trabalho. Por exemplo, coloque computadores que executam um aplicativo específico no mesmo grupo.
- Ao adicionar computadores a um grupo de proteção, o servidor de processo envia automaticamente e instala o Serviço de mobilidade se ele ainda não tiver sido instalado. Observe que você precisará ter o mecanismo de envio por push preparado conforme descrito na etapa anterior.


Adicionar computadores a um grupo de proteção:

1. Clique em **Itens Protegidos** > **Grupo de Proteção** > **Computadores** > Adicionar Computadores. \\Como a melhor prática
2. Em **Selecionar Máquinas Virtuais**, se estiver protegendo máquinas virtuais VMware, selecione um Servidor vCenter que esteja gerenciando suas máquinas virtuais, ou o host ESXi no qual elas estão em execução, e selecione os computadores.

	![Habilitar proteção](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)

3.  Em **Selecionar Máquinas Virtuais**, se estiver protegendo servidores físicos, vá para o assistente **Adicionar Máquinas Físicas** e forneça o endereço IP e um nome amigável. Em seguida, selecione a família do sistema operacional.

	![Habilitar proteção](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)

4. Em **Especificar Recursos de Destino**, selecione a conta de armazenamento que está sendo usada para a replicação e escolha se as configurações devem ser usadas para todas as cargas de trabalho. Observe que as contas de armazenamento Premium não têm suporte no momento.

	>[AZURE.NOTE] Não há suporte para a movimentação de contas de Armazenamento criadas com o [novo portal do Azure](../storage/storage-create-storage-account.md) entre grupos de recursos.

	![Habilitar proteção](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)

5. Em **Especificar Contas**, selecione a conta que você [configurou](#install-the-mobility-service-with-push-installation) para usar na instalação automática do Serviço de mobilidade.

	![Habilitar proteção](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)

6. Clique na marca de seleção para terminar de adicionar computadores ao grupo de proteção e iniciar a replicação inicial para cada computador.

	>[AZURE.NOTE] Se a instalação por push foi preparada, o Serviço de mobilidade é instalado automaticamente nos computadores que não o têm ao serem adicionados ao grupo de proteção. Após a instalação do serviço, um trabalho de proteção é iniciado e apresenta falha. Após a falha, você precisará reiniciar cada computador que teve o Serviço de mobilidade instalado manualmente. Após a reinicialização, o trabalho de proteção começa novamente e a replicação inicial ocorrerá.

Você pode monitorar o status na página **Trabalhos**.

![Habilitar proteção](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

Além disso, o status da proteção pode ser monitorado em **Itens Protegidos** > <protection group name> > **Máquinas Virtuais**. Depois que a replicação inicial é concluída e os dados são sincronizados, o status da máquina é alterado para **Protegidos**.

![Habilitar proteção](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)


## Etapa 11: definir propriedades de computador protegido

1. Quando um computador tem o status **Protegido**, você pode configurar as respectivas propriedades de failover. Nos detalhes do grupo de proteção, escolha o computador e abra a guia **Configurar**.
2. A Recuperação de Site sugere automaticamente propriedades para a VM do Azure e detecta as configurações da rede local.

	![Definir propriedades da máquina virtual](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)

3. Você pode modificar estas configurações:

	-  **Nome da VM do Azure**: esse é o nome que será dado ao computador no Azure após o failover. O nome deve atender aos requisitos do Azure.

	-  **Tamanho da VM do Azure**: o número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino. [Leia mais](../virtual-machines/virtual-machines-linux-sizes.md/#size-tables) sobre tamanhos e adaptadores. Observe que:

		- Quando você modifica o tamanho de uma máquina virtual e salva as configurações, o número do adaptador de rede será alterado na próxima vez em que você abrir a guia **Configurar**. O número de adaptadores de rede de máquinas virtuais de destino é o mínimo do número de adaptadores de rede na máquina virtual de origem e o número máximo de adaptadores de rede compatíveis com o tamanho da máquina virtual selecionada.
			- Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que a origem.
			- Se o número de adaptadores para máquina virtual de origem exceder o número permitido para o tamanho de destino e o tamanho máximo de destino será usado.
			- Por exemplo, se uma máquina de origem tiver dois adaptadores de rede e o tamanho da máquina de destino oferecer suporte a quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino com suporte oferecer suporte apenas a uma máquina de destino, ela terá apenas um adaptador.
		- Se a máquina virtual tiver vários adaptadores de rede, todos deverão estar conectados à mesma rede do Azure.
	- **Rede do Azure**: é necessário especificar uma rede do Azure à qual as VMs do Azure serão conectadas após o failover. Se você não especificar a rede, as VMs do Azure não serão conectadas a nenhuma rede. Além disso, você precisará especificar uma rede do Azure se deseja fazer failback do Azure para o site local. O failback requer uma conexão VPN entre uma rede do Azure e uma rede local.
	- **Endereço IP/sub-rede do Azure**: para cada adaptador de rede, você seleciona a sub-rede à qual a VM do Azure deve se conectar. Observe que:
		- Se o adaptador de rede do computador de origem estiver configurado para usar um endereço IP estático, você poderá especificar um endereço IP estático para a VM do Azure. Se você não fornecer um endereço IP estático, um endereço IP disponível será alocado. Se o endereço IP de destino for especificado, mas já estiver em uso por outra VM no Azure, o failover falhará. Se o adaptador de rede do computador de origem estiver configurado para usar DHCP, você terá DHCP como configuração do Azure.

## Etapa 12: criar um plano de recuperação e executar um failover

> [AZURE.VIDEO enhanced-vmware-to-azure-failover]

Você pode fazer failover de um único computador ou fazer failover de várias máquinas virtuais que executam a mesma tarefa ou executam a mesma carga de trabalho. Para fazer failover em vários computadores ao mesmo tempo, adicione-os a um plano de recuperação.

### Criar um plano de recuperação

1. Na página **Planos de Recuperação**, clique em **Adicionar Plano de Recuperação** e adicione um plano de recuperação. Especifique os detalhes do plano e selecione **Azure** como destino.

	![Configurar plano de recuperação](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)

2. Em **Selecionar Máquina Virtual**, selecione um grupo de proteção, bem como os computadores do grupo a serem adicionados ao plano de recuperação.

	![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

Você pode personalizar o plano para criar grupos e sequenciar a ordem em que os computadores no plano de recuperação serão submetidos a failover. Você também pode adicionar scripts e prompts para ações manuais. Os scripts podem ser criados manualmente ou usando os [Runbooks de Automação do Azure](site-recovery-runbook-automation.md). [Saiba mais](site-recovery-create-recovery-plans.md) sobre como personalizar os planos de recuperação.

## Executar um failover

Antes de executar um failover, observe que:


- Verifique se o servidor de gerenciamento está em execução e disponível. Caso contrário, o failover falhará.
- Se você executar um failover não planejado, observe que:

	- Se possível, você deve desligar os computadores primários antes de fazer um failover não planejado. Isso faz com que você não tenha os computadores de origem e de réplica em execução ao mesmo tempo. Se estiver replicando máquinas virtuais VMware, ao executar um failover não planejado, você poderá especificar que a recuperação de Site faça o melhor para desligar os computadores de origem. Dependendo do estado do site primário, isso pode ou não funcionar. Se você estiver replicando servidores físicos, a Recuperação de Site não oferece essa opção.
	- Quando você executa um failover não planejado, ele interrompe a replicação de dados de computadores primários para que qualquer delta de dados não seja transferido após o início de um failover não planejado.

- Se você deseja se conectar à máquina virtual de réplica no Azure após o failover, habilite a Conexão de Área de Trabalho Remota no computador de origem antes de fazer failover e permitir a conexão de RDP pelo firewall. Você também precisará permitir RDP no ponto de extremidade público de máquina virtual do Azure após o failover. Siga estas [melhores práticas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) para garantir o funcionamento do RDP após um failover.

>[AZURE.NOTE] Para obter o melhor desempenho ao fazer um failover para o Azure, não deixe de instalar o agente do Azure no computador protegido. Isso ajuda na inicialização mais rápida e também no diagnóstico em caso de problemas. O agente do Linux pode ser encontrado [aqui](https://github.com/Azure/WALinuxAgent) e o agente do Windows pode ser encontrado [aqui](http://go.microsoft.com/fwlink/?LinkID=394789)

### Execute um teste de failover

Execute um failover de teste para simular os processos de failover e de recuperação em uma rede isolada que não afete seu ambiente de produção e na qual a replicação normal continue normalmente. O failover de teste inicia-se na origem e você pode executá-lo de duas maneiras:

- **Não especificar uma rede do Azure**: se você executar um failover de teste sem uma rede, o teste simplesmente verificará se as máquinas virtuais iniciam e aparecem corretamente no Azure. As máquinas virtuais não serão conectadas a nenhuma rede do Azure após o failover.
- **Especificar uma rede do Azure**: esse tipo de failover verifica se o ambiente de replicação inteiro é mostrado conforme esperado e se as máquinas virtuais do Azure estão conectadas à rede especificada.


1. Na página **Planos de Recuperação**, selecione o plano e clique em **Failover de Teste**.

	![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)

2. Em **Confirmar Failover de Teste**, selecione **Nenhum** para indicar que você não deseja usar uma rede do Azure para o failover de teste ou selecione a rede à qual as VMs de teste serão conectadas após o failover. Clique na marca de seleção para iniciar o failover.

	![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)

3. Monitore o progresso do failover na guia **Trabalhos**.

	![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)

4. Após a conclusão do failover, você também deverá conseguir ver a réplica do computador do Azure no portal do Azure > **Máquinas Virtuais**. Se você quiser iniciar uma conexão de RDP com a VM do Azure, precisará abrir a porta 3389 no ponto de extremidade da VM.

5. Assim que terminar, quando o failover atingir a fase Concluir teste, clique em Concluir Teste para finalizar. Em Observações, registre e salve todas as observações associadas ao failover de teste.

6. Clique em **O failover de teste está concluído** para limpar automaticamente o ambiente de teste. Depois de isso ser feito, o failover de teste mostrará o status **Concluído**. Todos os elementos ou máquinas virtuais criadas automaticamente durante o teste de failover são excluídos. Observe que se um failover de teste continuar por mais de duas semanas, ele será concluído à força.



### Executar um failover não planejado

O failover não planejado é iniciado do Azure e pode ser executado mesmo se o site primário não estiver disponível.


1. Na página **Planos de Recuperação**, selecione o plano e clique em **Failover** > **Failover Não Planejado**.

	![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)

2. Se você estiver replicando máquinas virtuais VMware, poderá tentar desligar as VMs locais. Essa é a melhor maneira possível e o failover continuará se o esforço for ou não bem-sucedido. Se não for bem-sucedido, os detalhes do erro serão exibidos na guia **Trabalhos** > **Trabalhos de Failover Não Planejados**.

	![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

	>[AZURE.NOTE] Essa opção não estará disponível se você estiver replicando servidores físicos. Você precisará tentar desligá-los manualmente, se possível.

3. Em **Confirmar Failover**, verifique a direção do failover (para o Azure) e selecione o ponto de recuperação que você deseja usar para o failover. Se você tiver habilitado Várias VMs durante a configuração das propriedades de replicação, poderá recuperar o ponto de recuperação mais recente consistente com o aplicativo ou com o controle de falhas. Também é possível selecionar **Ponto de recuperação personalizado** para recuperar até um ponto anterior no tempo. Clique na marca de seleção para iniciar o failover.

	![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)

3. Aguarde a conclusão do trabalho de failover não planejado. É possível monitorar o progresso do failover na guia **Trabalhos**. Observe que, mesmo que ocorram erros durante um failover não planejado, o plano de recuperação será executado até que seja concluído. Você também deve conseguir ver a máquina de réplica do Azure aparecer em Máquinas Virtuais no portal do Azure.

### Conectar-se com as máquinas virtuais replicadas do Azure após o failover

Para se conectar a máquinas virtuais replicadas no Azure após o failover, veja o que você precisa:

1. Uma conexão de Área de Trabalho remota deve ser habilitada na máquina primária.
2. O Firewall do Windows na máquina principal para permitir RDP.
3. Após o failover, você precisará adicionar o RDP ao ponto de extremidade público para a máquina virtual do Azure.

[Leia mais](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) sobre a configuração.


## Implantar servidores de processo adicionais

Se tiver que escalar horizontalmente sua implantação para mais de 200 computadores de origem ou a taxa diária de rotatividade total ultrapassar 2 TB, você precisará de servidores de processo adicionais para tratar do volume de tráfego. Para configurar um servidor em processo adicional, verifique os requisitos em [Servidores em processo adicionais](#additional-process-servers) e siga as instruções aqui para configurar o servidor em processo. Depois de configurar o servidor, você pode configurar os computadores de origem para usá-lo.

### Configurar um servidor de processo adicional

Você pode configurar um servidor de processo adicional da seguinte maneira:

- Execute o assistente unificado para configurar um servidor de gerenciamento como somente servidor de processo.
- Se você deseja gerenciar a replicação de dados usando apenas o novo servidor de processo, para isso, será necessário migrar seus computadores protegidos.

### Instalar o servidor de processo

1. Na página Início Rápido, baixe o arquivo de instalação unificada para a instalação do componente de Recuperação de Site. Execute a instalação.
2. Em **Antes de começar**, selecione **Adicionar servidores de processo adicionais para escalar horizontalmente a implantação**.

	![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)

3. Conclua o assistente da mesma forma que fez ao [configurar](#step-5-install-the-management-server) o primeiro servidor de gerenciamento.
4. Em **Detalhes do Servidor de Configuração**, especifique o endereço IP do servidor de gerenciamento original no qual você instalou o servidor de configuração e a senha. No servidor de gerenciamento original, execute **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe – n** para obter a senha.

	![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### Migrar computadores para usar o novo servidor de processo

1. Abra **Servidores de Configuração** > **Servidor** > nome do servidor de gerenciamento original > **Detalhes do Servidor**.

	![Atualizar servidor de processo](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)

2. Na lista **Servidores de Processo**, clique em **Alterar Servidor de Processo** ao lado do servidor que deseja modificar.

	![Atualizar servidor de processo](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)

3. Em **Alterar Servidor em Processo** > **Servidor em Processo de Destino**, selecione o novo servidor de gerenciamento e as máquinas virtuais que serão tratadas pelo novo servidor em processo. Clique no ícone de informações para obter informações sobre o servidor. O espaço médio necessário para replicar cada máquina virtual selecionada no novo servidor de processo será exibido para lhe ajudar a tomar decisões sobre a carga. Clique na marca de seleção para começar a replicar no novo servidor de processo.

	![Atualizar servidor de processo](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)




## Permissões de VMware para acesso do vCenter

O servidor de processo pode descobrir as VMs em um servidor vCenter automaticamente. Para executar a descoberta automática, você precisará definir uma função (Azure\_Site\_Recovery) no nível do vCenter para permitir que a Recuperação de Site acesse o servidor vCenter. Observe que se você só precisa migrar máquinas virtuais VMware para o Azure e não precisa do failback do Azure, pode definir uma função somente leitura que seja suficiente. Configure as permissões, conforme descrito na [Etapa 6: Configurar as credenciais para o servidor vCenter](#step-6-set-up-credentials-for-the-vcenter-server). As permissões de função são resumidas na tabela a seguir.

**Função** | **Detalhes** | **Permissões**
--- | --- | ---
Função Azure\_Site\_Recovery | Descoberta de máquina virtual VMware |Atribua estes privilégios ao servidor v-Center:<br/><br/>Armazenamento de Dados -> Alocar espaço, Procurar armazenamento de dados, Operações de arquivo de nível baixo, Remover arquivo, Atualizar arquivos da máquina virtual<br/><br/>Rede -> Atribuir rede<br/><br/>Recurso -> Atribuir máquina virtual ao pool de recursos, Migrar máquina virtual desligada, Migrar máquina virtual ligada<br/><br/>Tarefas -> Criar tarefa, atualizar tarefa<br/><br/>Máquina virtual -> Configuração<br/><br/>Máquina virtual -> Interagir -> Responder a pergunta, Conexão de dispositivos, Configurar mídia de CD, Configurar mídia de disquete, Desligar, Ligar, Instalação de ferramentas da VMware<br/><br/>Máquina virtual -> Inventário -> Criar, Registrar, Desfazer registro<br/><br/>Máquina virtual -> Provisionamento -> Permitir download da máquina virtual, Permitir upload de arquivos da máquina virtual<br/><br/>Máquina virtual -> Instantâneos -> Remover instantâneos
Função de usuário do vCenter | Descoberta de máquina virtual VMware/Failover sem o desligamento da VM de origem | Atribua estes privilégios ao servidor do v-Center:<br/><br/>Objeto do Data Center -> Propagar para Objeto Filho, função=Somente leitura <br/><br/>O usuário é atribuído no nível do datacenter e, portanto, tem acesso a todos os objetos no datacenter. Se você quiser restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts ESX, armazenamentos de dados, VMs e redes).
Função de usuário do vCenter | Failover e failback | Atribua estes privilégios ao servidor do vCenter:<br/><br/>Objeto do datacenter – Propagar para objeto filho, função=Azure\_Site\_Recovery<br/><br/>O usuário é atribuído no nível do datacenter e, portanto, tem acesso a todos os objetos no datacenter. Se você quiser restringir o acesso, atribua a função **Nenhum acesso** com **Propagar para objeto filho** ao objeto filho (hosts ESX, armazenamentos de dados, VMs e redes). 



## Avisos e informações de softwares de terceiros

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”). Microsoft is the not original author of the Third Party Code. The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

## Próximas etapas

[Saiba mais sobre o failback](site-recovery-failback-azure-to-vmware-classic.md) para recolocar seus computadores com failover em execução no Azure no ambiente local.

<!---HONumber=AcomDC_0622_2016-->