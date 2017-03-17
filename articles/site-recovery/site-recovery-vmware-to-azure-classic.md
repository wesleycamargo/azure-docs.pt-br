---
title: "Replicar VMs do VMware e servidores físicos para o Azure no Portal Clássico | Microsoft Docs"
description: "Esse artigo descreve como implantar o Azure Site Recovery para coordenar a replicação, o failover e a recuperação de máquinas virtuais VMware e servidores físicos Windows/Linux locais no Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: a9022c1f-43c1-4d38-841f-52540025fb46
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 37291b16a966c17b684a4622f15d0393a5a5ce6d
ms.openlocfilehash: 0984ccc8163c94f56517e65919f8ea089a3e8b5f
ms.lasthandoff: 02/27/2017


---
# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery"></a>Replicar máquinas virtuais VMware e servidores físicos no Azure com o Azure Site Recovery
> [!div class="op_single_selector"]
> * [O portal do Azure](site-recovery-vmware-to-azure.md)
> * [O portal clássico](site-recovery-vmware-to-azure-classic.md)
> * [O portal clássico (herdado)](site-recovery-vmware-to-azure-classic-legacy.md)
>
>

O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. As máquinas podem ser replicadas no Azure ou em um datacenter local secundário. Para ter uma breve visão geral, consulte [O que é Azure Site Recovery?](site-recovery-overview.md)

## <a name="overview"></a>Visão geral
Este artigo descreve como:

* **Replicar máquinas virtuais VMware no Azure**: Implante o Site Recovery para coordenar a replicação, failover e recuperação de máquinas virtuais do VMware locais no armazenamento do Azure.
* **Replicar servidores físicos no Azure**: Implante o Azure Site Recovery para coordenar a replicação, failover e recuperação de servidores do Windows e Linux físicos locais no Azure.

> [!NOTE]
> Este artigo descreve como replicar no Azure. Se você quiser replicar as VMs VMware ou servidores físicos do Windows/Linux em um datacenter secundário, consulte [Site Recovery VMware para VMware](site-recovery-vmware-to-vmware.md).
>
>

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="enhanced-deployment"></a>Implantação avançada
Este artigo inclui instruções para uma implantação avançada no portal clássico do Azure. Recomendamos que você use esta versão para todas as novas implantações. Se já tiver implantado usando a versão anterior herdada, recomendamos que você migre para a nova versão. Para obter mais informações sobre migração, confira [Site Recovery VMware para clássico do Azure herdado](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment).

A implantação avançada é uma atualização importante. Veja um resumo dos aprimoramentos que fizemos:

* **Nenhuma VM de infraestrutura no Azure**: os dados replicam diretamente para uma conta de armazenamento do Azure. Além disso, não é preciso configurar nenhuma VM de infraestrutura (como servidor de configuração ou servidor de destino mestre) para replicação e failover, como acontecia na implantação herdada.  
* **Instalação unificada**: uma única instalação permite uma configuração simples e escalabilidade para os componentes locais.
* **Implantação segura**: todo o tráfego é criptografado, e as comunicações de gerenciamento da replicação são enviadas por HTTPS 443.
* **Pontos de recuperação**: suporte para falhas e pontos de recuperação consistentes com aplicativos para ambientes do Windows e Linux, e suporte para configurações consistentes com VM única ou várias VMs.
* **Failover de teste**: suporte do failover de teste sem interrupções no Azure, sem afetar a produção ou pausar a replicação.
* **Failover não planejado**: suporte do failover não planejado para o Azure com uma opção avançada para desligar automaticamente as VMs antes do failover.
* **Failback**: failback integrado que replica somente as alterações delta de volta para o site local.
* **vSphere 6.0**: suporte limitado para as implantações do VMware Vsphere 6.0.

## <a name="how-does-site-recovery-help-protect-virtual-machines-and-physical-servers"></a>Como a Recuperação de Site ajuda a proteger servidores físicos e máquinas virtuais?
* Os administradores do VMware podem configurar garantias externas para proteger o Azure de cargas de trabalho de negócios e aplicativos em execução em máquinas virtuais VMware. Os gerenciadores de servidor podem replicar servidores Windows e Linux físicos locais no Azure.
* O console do Azure Site Recovery fornece um único local para uma instalação simples e para o gerenciamento de replicação, failover e processos de recuperação.
* Se você replicar máquinas virtuais VMware que são gerenciadas por um servidor vCenter, a Recuperação de Site poderá descobrir essas VMs automaticamente. Se os computadores estiverem em um host ESXi, o Site Recovery encontrará VMs no host.
* Se executar failovers fáceis de sua infraestrutura local para o Azure, você pode fazer o failback (restauração) do Azure para servidores de VMs VMware no site local.
* Você pode configurar planos de recuperação que agrupem cargas de trabalho de aplicativos que estão organizadas em camadas em vários computadores. Se fizer failover desses planos, o Site Recovery assegura a consistência de várias VMs para que computadores que estejam executando as mesmas cargas de trabalho possam ser recuperados juntos em um ponto de dados consistente.

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte
### <a name="windows-64-bit-only"></a>Windows (somente&64; bits)
* Windows Server 2008 R2 SP1+
* Windows Server 2012
* Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (somente&64; bits)
* Red Hat Enterprise Linux 6.7, 7.1, e 7.2
* CentOS 6.5, 6.6, 6.7, 7.0, 7.1 e 7.2
* Oracle Enterprise Linux 6.4 e 6.5 que executa o kernel compatível com Red Hat ou o UEK3 (Unbreakable Enterprise Kernel Versão 3)
* SUSE Linux Enterprise Server 11 SP3

## <a name="scenario-architecture"></a>Arquitetura de cenário
Componentes do cenário:

* **Um servidor de gerenciamento local**: o servidor de gerenciamento executa os componentes da Recuperação de Site a seguir.
  * **Servidor de configuração**: coordena a comunicação e gerencia os processos de replicação e recuperação de dados.
  * **Servidor de processo**: atua como um gateway de replicação. Ele recebe dados de computadores de origem protegida, otimiza-os com caching, compactação e criptografia e envia os dados de replicação para o armazenamento do Azure. Ele também manipula a instalação por push do Serviço de mobilidade em computadores protegidos e executa a descoberta automática de máquinas virtuais VMware.
  * **Servidor de destino mestre**: lida com os dados de replicação durante o failback do Azure.
    Você também pode implantar um servidor de gerenciamento que atue somente como um servidor de processo para dimensionar sua implantação.
* **Serviço de mobilidade**: esse componente é implantado em cada computador (VM do VMware ou servidor físico) que você deseja replicar no Azure. Ele captura gravações de dados no computador e as encaminha ao servidor de processo.
* **Azure**: você não precisa criar VMs do Azure para lidar com a replicação e o failover. O serviço Recuperação de Site lida com o gerenciamento de dados e os dados são replicados diretamente no armazenamento do Azure. As VMs do Azure replicadas são criadas automaticamente somente quando ocorre o failover no Azure. No entanto, se quiser fazer failback do Azure para o site local, você precisará configurar uma VM do Azure para atuar como um servidor de processo.

O gráfico a seguir (criado pelo Henry Robalino) mostra como esses componentes interagem:

![Arquitetura de componente](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

## <a name="capacity-planning"></a>Planejamento da capacidade
Ao planejar a capacidade, veja o que planejar:

* **Ambiente de origem**: Planejamento da capacidade ou da infraestrutura do VMware e requisitos do computador de origem.
* **Servidor de gerenciamento**: Planejamento dos servidores de gerenciamento locais que executam os componentes do Site Recovery.
* **Largura de banda da rede da origem ao destino**: Planejamento da largura de banda necessária para a replicação entre a origem e o Azure.

### <a name="source-environment-considerations"></a>Considerações sobre o ambiente de origem
* **Taxa de alteração diária máxima**: um computador protegido pode usar apenas um servidor de processo. Um único servidor de processo pode gerenciar até 2 TB de alterações de dados por dia. Portanto, 2 TB é a taxa de alteração diária máxima com suporte para um computador protegido.
* **Taxa de transferência máxima**: Uma máquina replicada pode pertencer a uma conta de armazenamento no Azure. Uma conta de armazenamento padrão pode lidar com um máximo de 20 mil solicitações por segundo e recomendamos que você mantenha o número de IOPS em um computador de origem como 20 mil. Por exemplo, se você tiver um computador de origem com 5 discos e cada disco gerar 120 IOPS (8 KB de tamanho) na origem, ele estará dentro do limite IOPS de 500 por disco do Azure. O número de contas de armazenamento necessárias = total de IOPs de origem/20.000.

### <a name="management-server-considerations"></a>Considerações sobre o servidor de gerenciamento
O servidor de gerenciamento executa os componentes do Site Recovery que tratam da replicação, gerenciamento e otimização de dados. Ele deve ser capaz de lidar com a capacidade de taxa de alteração diária em todas as cargas de trabalho em execução em computadores protegidos e ter largura de banda suficiente para replicar continuamente os dados no armazenamento do Azure. Especificamente:

* O servidor de processo recebe os dados de replicação de computadores protegidos e os otimiza com caching, compactação e criptografia antes de enviá-los para o Azure. O servidor de gerenciamento deve ter recursos suficientes para executar essas tarefas.
* O servidor em processo usa o cache baseado em disco. Recomendamos um disco de cache separado de 600 GB ou mais para lidar com alterações de dados armazenados em caso de afunilamento ou interrupção de rede. Durante a implantação, você pode configurar o cache em qualquer unidade que tenha pelo menos 5 GB de armazenamento disponível, mas 600 GB é a recomendação mínima.
* Como prática recomendada, sugerimos que o servidor de gerenciamento esteja localizado na mesma rede e segmento de LAN que os computadores que deseja proteger. Ele pode estar localizado em uma rede diferente, mas os computadores que você deseja proteger devem ter visibilidade de rede L3 para ele.

As recomendações de tamanho para o servidor de gerenciamento estão resumidas na tabela a seguir:

| **CPU do servidor de gerenciamento** | **Memória** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos** |
| --- | --- | --- | --- | --- |
| 8 vCPUs (2 soquetes * 4 núcleos @ 2,5 GHz) |16 GB |300 GB |500 GB ou menos |Implante um servidor de gerenciamento com essas configurações para replicar menos de 100 computadores. |
| 12 vCPUs (2 soquetes * 6 núcleos @ 2,5 GHz) |18 GB |600 GB |500 GB a 1 TB |Implante um servidor de gerenciamento com essas configurações para replicar de 100 a 150 computadores. |
| 16 vCPUs (2 soquetes * 8 núcleos @ 2,5 GHz) |32 GB |1 TB |1 TB a 2 TB |Implante um servidor de gerenciamento com essas configurações para replicar de 150 a 200 computadores. |
| Implantar outro servidor de processo | | |> 2 TB |Implante servidores de processo adicionais se estiver replicando mais de 200 computadores ou se a taxa de alteração diária de dados ultrapassar 2 TB. |

Em que:

* Cada computador de origem é configurado com 3 discos de 100 GB.
* Usamos armazenamento de benchmark de 8 unidades SAS de 10K RPM com RAID 10.000 para as medidas do disco de cache.

### <a name="network-bandwidth-from-source-to-target"></a>Largura de banda da rede da origem ao destino
Calcule a largura de banda que seria necessária para a replicação inicial e a replicação delta usando a [ferramenta do Planejador de Capacidade](site-recovery-capacity-planner.md).

#### <a name="throttling-bandwidth-used-for-replication"></a>Limitação de largura de banda usada para replicação
O tráfego VMware replicado no Azure passa por um servidor de processo específico. Você pode limitar a largura de banda que está disponível para replicação de Recuperação de Site no servidor da seguinte maneira:

1. Abra o snap-in de MMC de Backup do Microsoft Azure no servidor de gerenciamento principal ou em um servidor de gerenciamento com mais servidores de processo provisionados. Por padrão, um atalho para o Backup do Microsoft Azure é criado na área de trabalho. Ou você pode encontrá-lo em C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.

    ![Limitar propriedades de largura de banda](./media/site-recovery-vmware-to-azure-classic/throttle1.png)
3. Na guia **Limitação**, especifique a largura de banda que pode ser usada para a replicação de Site Recovery e o agendamento aplicável.

    ![Limitar replicação de largura de banda](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

Você também pode definir a limitação usando o PowerShell. Aqui está um exemplo:

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### <a name="maximizing-bandwidth-usage"></a>Maximizar o uso da largura de banda
Para aumentar a largura de banda utilizada para replicação pelo Azure Site Recovery, é necessário alterar uma chave de registro.

A chave a seguir controla o número de threads por disco de replicação usado durante a replicação:

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 Em uma rede sobreprovisionada, essa chave de registro precisa ser alterada a partir dos valores padrão. Damos suporte ao máximo de 32.  

Para obter mais informações sobre o planejamento de capacidade detalhada, confira [Planejador de capacidade do Site Recovery](site-recovery-capacity-planner.md).

### <a name="additional-process-servers"></a>Servidores de processo adicionais
Se precisar proteger mais de 200 computadores ou se a taxa de alteração diária for maior que 2 TB, você pode adicionar mais servidores para lidar com a carga. Para escalar horizontalmente, você pode:

* Aumentar o número de servidores de gerenciamento. Por exemplo, você pode proteger até 400 computadores com dois servidores de gerenciamento.
* Adicionar servidores de processo adicionais e usá-los para lidar com o tráfego em vez (ou além) do servidor de gerenciamento.

Esta tabela descreve um cenário em que:

* Você configura o servidor de gerenciamento original para usá-lo somente como um servidor de configuração.
* Você configura um servidor de processo adicional.
* Você configura máquinas virtuais para usar o servidor de processo adicional.
* Cada computador de origem protegido é configurado com três discos de 100 GB cada.

| **Servidor de gerenciamento original**<br/><br/>(servidor de configuração) | **Servidor de processo adicional** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos** |
| --- | --- | --- | --- | --- |
| 8 vCPUs (2 soquetes * 4 núcleos @ 2,5 GHz), 16 GB de RAM |4 vCPUs (2 soquetes * 2 núcleos @ 2,5 GHz), 8 GB de RAM |300 GB |250 GB ou menos |Você pode replicar 85 computadores ou menos. |
| 8 vCPUs (2 soquetes * 4 núcleos @ 2,5 GHz), 16 GB de RAM |8 vCPUs (2 soquetes * 4 núcleos @ 2,5 GHz), 12 GB de RAM |600 GB |250 GB a 1 TB |Você pode replicar de 85 a 150 computadores. |
| 12 vCPUs (2 soquetes * 6 núcleos @ 2,5 GHz), 18 GB de RAM |12 vCPUs (2 soquetes * 6 núcleos @ 2,5 GHz), 24 GB de RAM |1 TB |1 TB a 2 TB |Você pode replicar de 150 a 225 computadores. |

A maneira como você escala seus servidores depende de sua preferência por um modelo que escale vertical ou horizontalmente. Você pode escalar verticalmente implantando alguns servidores de processo e de gerenciamento de alto nível ou escalar horizontalmente implantando mais servidores com menos recursos. Por exemplo: se precisar proteger 220 computadores, você adotar umas das opções a seguir:

* Configure o servidor de gerenciamento original com 12 vCPUs e 18 GB de RAM. Configure o servidor de gerenciamento original com 12 vCPUs e 24 GB de RAM. Configure computadores protegidos para usar apenas o servidor de processo adicional.
* Configure dois servidores de gerenciamento (2 x 8 CPUs, 16 GB de RAM) e dois servidores de processo adicionais (1 x 8 vCPUs e 4 vCPUs x 1 para gerenciar 135 + 85 (220) computadores). Configure computadores protegidos para usar apenas os servidores de processo adicionais.

Siga as instruções em [Implantar servidores de processo adicional](#deploy-additional-process-servers) para configurar um servidor de processo adicional.

## <a name="before-you-start-deployment"></a>Antes de começar a implantação
As tabelas a seguir resumem os pré-requisitos para implantar esse cenário.

### <a name="azure-prerequisites"></a>Pré-requisitos do Azure
| **Pré-requisito** | **Detalhes** |
| --- | --- |
| **Conta do Azure** |Você precisa de uma conta do [Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). Para obter mais informações sobre os preços do Site Recovery, consulte [Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/). |
| **Armazenamento do Azure** |Você precisa de uma conta de armazenamento do Azure para armazenar os dados replicados. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover. <br/><br/>Você precisa de uma [conta de armazenamento com redundância geográfica standard](../storage/storage-redundancy.md#geo-redundant-storage). A conta deve estar localizada na mesma região que o serviço do Site Recovery e estar associada à mesma assinatura. A replicação para contas de armazenamento premium atualmente não tem suporte e não deve ser usada.<br/><br/>Não há suporte para a movimentação de contas de armazenamento criadas usando o [portal do Azure](../storage/storage-create-storage-account.md) entre grupos de recursos. Para obter mais informações, consulte: [Introdução ao Armazenamento do Microsoft Azure](../storage/storage-introduction.md) .<br/><br/> |
| **Rede do Azure** |Você precise de uma rede virtual do Azure à qual as VMs do Azure vão se conectar quando ocorrer o failover. A rede virtual do Azure tem que estar na mesma região do cofre da Recuperação de Site.<br/><br/>Para fazer o failback depois do failover no Azure, você precisa de uma conexão VPN (ou Rota Expressa do Azure) configurada da rede do Azure para o site local. |

### <a name="on-premises-prerequisites"></a>Pré-requisitos do local
| **Pré-requisito** | **Detalhes** |
| --- | --- |
| **Servidor de gerenciamento** |Você precisa de um servidor Windows 2012 R2 local em execução em uma máquina virtual ou em um servidor físico. Todos os componentes locais do Site Recovery estão instalados nesse servidor de gerenciamento.<br/><br/> É recomendável que você implante o servidor como uma VM do VMware altamente disponível. O failback para o site local do Azure é sempre para VMs VMware, independentemente de o failover ter sido em VMs ou em servidores físicos. Se você não configurar o servidor de gerenciamento como uma VM VMware, será necessário configurar um servidor de destino mestre separado como uma VM VMware para receber o tráfego de failback.<br/><br/>O servidor não deve ser um controlador de domínio.<br/><br/>O servidor deve ter um endereço IP estático.<br/><br/>O nome de host do servidor deve ter 15 caracteres ou menos.<br/><br/>O idioma do sistema operacional deve ser apenas inglês.<br/><br/>O servidor de gerenciamento requer acesso à Internet.<br/><br/>Você precisa ter acesso de saída do servidor da seguinte maneira: acesso temporário em HTTP 80 durante a instalação dos componentes do Site Recovery (para baixar o MySQL); acesso de saída em andamento em HTTPS 443 para gerenciamento de replicação; acesso de saída em andamento em HTTPS 9443 para o tráfego de replicação (essa porta pode ser modificada).<br/><br/> Verifique se que essas URLs podem ser acessadas a partir do servidor de gerenciamento: <br/>- \*.hypervrecoverymanager.windowsazure.com<br/>- \*.accesscontrol.windows.net<br/>- \*.backup.windowsazure.com<br/>- \*.blob.core.windows.net<br/>- \*.store.core.windows.net<br/>- https://www.msftncsi.com/ncsi.txt<br/>- [ https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>Se você tiver regras de firewall baseadas em endereço IP no servidor, verifique se as regras permitem a comunicação com o Azure. Você precisa permitir os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) e a porta HTTPS (443). Você também precisa colocar intervalos de endereços IP para a região do Azure de sua assinatura e para o Oeste dos EUA na lista de permissões. A URL [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi") é usada para baixar o MySQL. |
| **vCenter VMware/host ESXi** |Você precisa de um ou mais hipervisores VMware vSphere ESX/ESXi gerenciando suas máquinas virtuais do VMware e executando o ESX/ESXi versão 6.0, 5.5 ou 5.1 com as atualizações mais recentes.<br/><br/> Recomendamos que você implante um servidor VMware vCenter para gerenciar os hosts ESXi. Ele deve executar o vCenter versão 6.0 ou 5.5 com as atualizações mais recentes.<br/><br/>Observe que a Recuperação de Site não dá suporte aos novos recursos do vCenter e vSphere 6.0, como o vCenter vMotion cruzado, os volumes virtuais e o DRS de armazenamento. O suporte à Recuperação de Site está limitado a recursos que também estavam disponíveis na versão 5.5. |
| **Computadores protegidos** |**As tabelas**<br/><br/>Os computadores que você deseja proteger devem estar em conformidade com os [pré-requisitos do Azure](site-recovery-prereq.md) para criar VMs do Azure.<br><br/>Se quiser se conectar às VMs do Azure após o failover, você precisa habilitar conexões de Área de Trabalho Remota no firewall local.<br/><br/>A capacidade do disco individual nos computadores protegidos não deve ser maior que 1023 GB. Uma VM pode ter até 64 discos (portanto, até 64 TB). Se você tiver discos com mais de 1 TB, considere o uso da replicação de banco de dados, como o AlwaysOn do SQL Server ou Oracle Data Guard.<br/><br/>Mínimo de 2 GB de espaço disponível na unidade de instalação para instalação de componentes.<br/><br/>Não há suporte para clusters convidados de disco compartilhado. Se você tiver uma implantação clusterizada, considere o uso da replicação de banco de dados, como o AlwaysOn do SQL Server ou Oracle Data Guard.<br/><br/>Não há suporte para inicialização EFI (Extensible Firmware Interface)/UEFI (Unified Extensible Firmware Interface).<br/><br/>Os nomes dos computadores devem conter entre 1 e 63 caracteres (letras, números e hifens). O nome deve começar com uma letra ou número e terminar com uma letra ou número. Depois que um computador é protegido, você pode modificar o nome do Azure.<br/><br/>**VMs VMware**<br/><br>Você precisa instalar o VMware vSphere PowerCLI 6.0. no servidor de gerenciamento (servidor de configuração).<br/><br/>As VMs VMware que você deseja proteger devem ter as ferramentas VMware instaladas e em execução.<br/><br/>Se a VM de origem tiver agrupamento NIC, ela será convertida para uma única NIC após o failover no Azure.<br/><br/>Se as VMs protegidas tiverem um disco iSCSI, o Site Recovery converterá o disco de iSCSI de VM protegido em um arquivo VHD quando a VM realizar failover no Azure. Se o destino iSCSI puder ser alcançado pela VM do Azure, ela se conectará ao destino iSCSI e verá basicamente dois discos: o disco VHD na VM do Azure e o disco iSCSI de origem. Neste caso, você precisa desconectar o destino iSCSI que aparece na VM do Azure com failover.<br/><br/>Para obter mais informações sobre as permissões de usuário do VMware necessárias para o Site Recovery, confira [Permissões VMware para acesso ao vCenter](#vmware-permissions-for-vcenter-access).<br/><br/> **Computadores com Windows Server (na VM VMware ou no servidor físico)**<br/><br/>O servidor deve estar executando um sistema operacional de 64 bits com suporte: Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 com, ao menos, SP1.<br/><br/>O sistema operacional deve ser instalado na unidade C. O disco do SO deve ser um disco básico do Windows. (O SO não deve ser instalado em um disco dinâmico do Windows.)<br/><br/>Para computadores com Windows Server 2008 R2, você precisa ter o .NET Framework 3.5.1 instalado.<br/><br/>Você precisa fornecer uma conta de administrador (deve ser um administrador local no computador com Windows) para a instalação por push do Serviço de Mobilidade nos servidores do Windows. Se a conta fornecida não for uma conta de domínio, você precisa desabilitar o controle Acesso de Usuário Remoto no computador local. Para obter mais informações, consulte [Instalar o serviço de mobilidade com instalação por push](#install-the-mobility-service-with-push-installation).<br/><br/>A Recuperação de Site dá suporte a VMs com um disco RDM. Durante o failback, o Site Recovery Site vai reutilizar o disco RDM se a VM de origem e o disco de RDM estiverem disponíveis. Se eles não estiverem disponíveis, o Site Recovery vai criar um novo arquivo VMDK para cada disco durante o failback.<br/><br/>**Computadores Linux**<br/><br/>Você precisa de um sistema operacional de 64 bits com suporte: Red Hat Enterprise Linux 6.7; CentOS 6.5, 6.6 ou 6.7; Oracle Enterprise Linux 6.4 ou 6.5 executando o kernel compatível do Red Hat ou o Unbreakable Enterprise Kernel Versão 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>Os arquivos /etc/hosts nos computadores protegidos devem conter entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede. <br/><br/>Se você quiser se conectar a uma máquina virtual do Azure que executa o Linux após o failover usando um cliente Secure Shell (ssh), verifique se o serviço do Secure Shell na máquina protegida está definido para ser iniciado automaticamente na inicialização do sistema e se as regras de firewall permitem uma conexão ssh para ele.<br/><br/>A proteção pode ser habilitada somente para computadores Linux com o armazenamento a seguir: sistema de arquivos (EXT3, ETX4, ReiserFS, XFS); Multipath software-Device Mapper (vários caminhos); Gerenciador de volumes (LVM2). Não há suporte a servidores físicos com o armazenamento de controlador HP CCISS. O sistema de arquivos ReiserFS só tem suporte no SUSE Linux Enterprise Server 11 SP3.<br/><br/>A Recuperação de Site dá suporte a VMs com um disco RDM. Durante o failback para Linux, a Recuperação de Site não reutiliza o disco RDM. Em vez disso, ele cria um novo arquivo VMDK para cada disco RDM correspondente. |

Somente para VM do Linux: verifique se você definiu a configuração disk.enableUUID=true nos Parâmetros de Configuração da VM no VMware. Se essa linha não existir, adicione-a. Isso é necessário para fornecer um UUID consistente para o VMDK para que ele seja montado corretamente. Sem essa configuração, o failback causará um download completo, mesmo que a VM esteja disponível no local. Adicionar essa configuração garante que apenas as alterações delta sejam transferidas durante o failback.

## <a name="step-1-create-a-vault"></a>Etapa 1: criar um cofre
1. Entre no [Portal do Azure](https://manage.windowsazure.com/).
2. Expanda **Serviços de Dados** > **Serviços de Recuperação** e clique em **Cofre do Site Recovery**.
3. Clique em **Criar Novo** > **Criação Rápida**.
4. Em **Nome**, digite um nome amigável para identificar o cofre.
5. Em **Região**, selecione a região geográfica para o cofre. Para verificar as regiões com suporte, consulte os [detalhes do preço do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Clique em **Criar cofre**.
    ![Criar cofre](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como **Ativo** na página de **Serviços de Recuperação**.

## <a name="step-2-set-up-an-azure-network"></a>Etapa 2: configurar uma rede do Azure
Configure uma rede do Azure para que as VMs do Azure sejam conectadas a uma rede após o failover e para que o failback para o site local possa funcionar conforme o esperado.

1. No portal do Azure, selecione **Criar rede virtual** e especifique o nome da rede, o intervalo de endereços IP e o nome da sub-rede.
2. Se precisar fazer failback, adicione VPN/ExpressRoute à rede. A VPN/Rota Expressa pode ser adicionada à rede até mesmo após o failover.

Para obter mais informações sobre redes do Azure, consulte [Visão geral de redes virtuais](../virtual-network/virtual-networks-overview.md).

> [!NOTE]
> [Migração de redes](../azure-resource-manager/resource-group-move-resources.md) nos grupos de recursos dentro da mesma assinatura ou nas assinaturas não tem suporte para as redes usadas para a implantação do Site Recovery.
>
>

## <a name="step-3-install-the-vmware-components"></a>Etapa 3: instalar os componentes do VMware
Se você quiser replicar máquinas virtuais VMware, instale os seguintes componentes de VMware no servidor de gerenciamento:

1. [Baixe](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) e instale o VMware vSphere Power CLI 6.0.
2. Reinicie o servidor.

## <a name="step-4-download-a-vault-registration-key"></a>Etapa 4: baixar uma chave de registro do cofre
1. No servidor de gerenciamento, abra o console do Site Recovery no Azure. Na página **Serviços de Recuperação**, clique no cofre para abrir a página de **Início Rápido**. Você também pode abrir a página **Início Rápido** a qualquer momento clicando no ícone.

    ![Ícone Início Rápido](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)
2. Na página **Início Rápido**, clique em **Preparar Recursos de Destino** > **Baixar uma chave de registro**. O arquivo de registro é gerado automaticamente. Ele é válido por cinco dias após ter sido gerado.

## <a name="step-5-install-the-management-server"></a>Etapa 5: instalar o servidor de gerenciamento
> [!TIP]
> Verifique se que essas URLs podem ser acessadas a partir do servidor de gerenciamento:
>
> * *.hypervrecoverymanager.windowsazure.com
> * *.accesscontrol.windows.net
> * *.backup.windowsazure.com
> * *.blob.core.windows.net
> * *.store.core.windows.net
> * https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi
> * https://www.msftncsi.com/ncsi.txt
>
>



>[!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Setup-Registration/player]



1. Na página **Início Rápido**, baixe o arquivo de instalação unificada para o servidor.
2. Execute o arquivo de instalação para iniciar a instalação no assistente de **Instalação Unificada do Site Recovery**.
3. Em **Antes de começar**, selecione **Instalar o servidor de configuração e o servidor em processo**.

   ![Antes de começar](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)
4. Em **Licença de Software de Terceiros**, clique em **Aceito** para baixar e instalar o MySQL.

    ![Software de terceiros](./media/site-recovery-vmware-to-azure-classic/combined-wiz105.PNG)
5. Em **Registro**, procure e selecione a chave de registro que você baixou do cofre.

    ![Registro](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)
6. Em **Configurações da Internet**, especifique como o provedor em execução no servidor de configuração se conectará ao Azure Site Recovery pela Internet.

   * Se você desejar se conectar ao proxy que está configurado atualmente no computador, selecione **Conectar-se com as configurações de proxy existentes**.
   * Se desejar que o provedor se conecte diretamente, selecione **Conectar diretamente sem um proxy**.
   * Se o proxy existente exigir autenticação ou se você quiser usar um proxy personalizado para a conexão do provedor, selecione **Conectar-se com configurações de proxy personalizadas**.
     * Se usar um proxy personalizado, você precisará especificar o endereço, a porta e as credenciais.
     * Se estiver usando um proxy, você já deverá ter permitido as seguintes URLs:
       * *.hypervrecoverymanager.windowsazure.com    
       * *.accesscontrol.windows.net
       * *.backup.windowsazure.com
       * *.blob.core.windows.net
       * *.store.core.windows.net

    ![Firewall](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

1. Em **Verificação de Pré-requisitos**, a configuração executa uma verificação para garantir que a instalação pode ser executada.

    ![Pré-requisitos](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

     Se aparecer um aviso sobre a **Verificação de sincronização de tempo global**, verifique se a hora no relógio do sistema (configurações de **Data e Hora**) é a mesma que a do fuso horário.

     ![Problema de sincronização de tempo](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

1. Em **Configuração do MySQL**, crie credenciais para fazer se conectar à instância do servidor MySQL que será instalada.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)
2. Em **Detalhes do Ambiente**, selecione se você replicará as VMs VMware. Se a resposta for positiva, a instalação verificará se o PowerCLI 6.0 está instalado.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)
3. Em **Localização de Instalação**, selecione a localização em que você deseja instalar os binários e armazenar o cache. Você pode selecionar uma unidade que tem ao menos 5 GB de espaço em disco disponível, mas é recomendável uma unidade de cache com ao menos 600 GB de espaço em disco disponível.

   ![Local de instalação](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)
4. Em **Seleção da Rede**, especifique o ouvinte (adaptador de rede e porta SSL) no qual o servidor de configuração enviará e receberá os dados de replicação. Você pode modificar a porta padrão (9443). Além dessa porta, a porta 443 será usada por um servidor Web que orquestra operações de replicação. Não use a 443 para receber tráfego de replicação.

    ![Seleção da Rede](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)



1. Em **Resumo**, examine as informações e clique em **Instalar**. Após a conclusão da instalação, uma frase secreta é gerada. Você precisará dela quando habilitar a replicação, portanto copie-a e guarde-a em um local seguro.

   ![Resumo](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)


> [!WARNING]
> O proxy do Agente do Serviço de Recuperação do Microsoft Azure precisa ser configurado.
> Quando a instalação estiver concluída, inicie o Shell de Serviços de Recuperação do Microsoft Azure no menu Iniciar do Windows. Na janela de comando que se abrir, execute o seguinte conjunto de comandos para configurar as configurações do servidor proxy.
>
>
    $pwd = ConvertTo-SecureString -String ProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
>


### <a name="run-setup-from-the-command-line"></a>Executar a instalação a partir da linha de comando
Você também pode executar o assistente unificado a partir da linha de comando da seguinte maneira:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Em que:

* /ServerMode: obrigatório. Especifica se a instalação deve instalar servidores de configuração e de processo ou somente o servidor de processo (usado para instalar servidores de processo adicionais). Valores de entrada: CS, PS.
* InstallDrive: obrigatório. Especifica a pasta onde os componentes estão instalados.
* /MySQLCredFilePath. Obrigatório. Especifica o caminho para um arquivo onde as credenciais do servidor MySQL estão instaladas. Obtenha o modelo para criar o arquivo.
* /VaultCredFilePath. Obrigatório. Local do arquivo de credenciais do cofre.
* /EnvType. Obrigatório. Tipo de instalação. Valores: VMware, NonVMware.
* /PSIP e /CSIP. Obrigatório. Endereço IP do servidor de processo e do servidor de configuração.
* /PassphraseFilePath. Obrigatório. Local do arquivo de senha.
* /ByPassProxy. Opcional. Especifica o servidor de gerenciamento que se conecta ao Azure sem um proxy.
* /ProxySettingsFilePath. Opcional. Especifica as configurações para um proxy personalizado (proxy padrão no servidor que requer autenticação ou proxy personalizado).

## <a name="step-6-set-up-credentials-for-the-vcenter-server"></a>Etapa 6: configurar as credenciais para o servidor vCenter
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Discovery/player]
>
>

O servidor de processo pode descobrir automaticamente máquinas virtuais VMware que são gerenciadas por um servidor vCenter. Para a descoberta automática, o Site Recovery precisa de uma conta e de credenciais que possam acessar o servidor vCenter. Isso não é relevante se você estiver replicando apenas servidores físicos.

Para definir a conta e as credenciais:

1. No servidor vCenter, crie uma função (**Azure_Site_Recovery**) no nível do vCenter com as [permissões necessárias](#vmware-permissions-for-vcenter-access).
2. Atribua a função **Azure_Site_Recovery** a um usuário do vCenter.

   > [!NOTE]
   > Uma conta de usuário do vCenter que tenha a função somente leitura pode executar failover sem desligar os computadores de origem protegida. Se quiser desligar as máquinas, você precisará da função Azure_Site_Recovery. Se você estiver migrando somente as VMs do VMware para o Azure e não precisar fazer failback, a função somente leitura será suficiente.
   >
   >
3. Para adicionar a conta, abra **cspsconfigtool**. Ela está disponível como um atalho na área de trabalho e está localizada na pasta [LOCAL DE INSTALAÇÃO]\home\svsystems\bin.
4. Na guia **Gerenciar Contas**, clique em **Adicionar Conta**.

    ![Adicionar Conta](./media/site-recovery-vmware-to-azure-classic/credentials1.png)
5. Em **Detalhes da Conta**, adicione as credenciais que podem ser usadas para acessar o servidor vCenter. Pode levar mais de 15 minutos para que o nome da conta apareça no portal. Para atualizar imediatamente, clique em **Atualizar** na guia **Servidores de Configuração**.

    ![Detalhes](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## <a name="step-7-add-vcenter-servers-and-esxi-hosts"></a>Etapa 7: adicionar servidores vCenter e hosts ESXi
Se você estiver replicando máquinas virtuais VMware, precisará adicionar um servidor vCenter (ou host ESXi).

1. Na guia **Servidores** > **Servidores de Configuração**, selecione **Adicionar servidor vCenter**.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)
2. Adicione o servidor vCenter ou os detalhes do host ESXi, o nome da conta que você especificou para acessar o servidor vCenter na etapa anterior e o servidor de processo que será usado para descobrir máquinas virtuais VMware que são gerenciadas pelo servidor vCenter. O servidor vCenter ou host ESXi deve estar localizado na mesma rede que o servidor no qual o servidor de processo está instalado.

   > [!NOTE]
   > Se você estiver adicionando o servidor vCenter ou o host ESXi com uma conta que não tem privilégios de administrador no servidor vCenter ou servidor host, verifique se o vCenter ou as contas ESXi têm esses privilégios habilitados: Datacenter, Armazenamento de Dados, Pasta, Jost, Rede, Recursos, Máquina Virtual e vSphere Distributed Switch. O servidor vCenter precisa do privilégio de Exibições do Armazenamento habilitado.
   >
   >

    ![Adicionar servidor vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)
3. Após a conclusão da descoberta, o servidor vCenter será listado na guia **Servidores de Configuração** .

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)

## <a name="step-8-create-a-protection-group"></a>Etapa 8: criar um grupo de proteção
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Protection/player]
>
>

Grupos de proteção são agrupamentos lógicos de máquinas virtuais ou servidores físicos que você deseja proteger ao usar as mesmas configurações de proteção. Aplique as configurações de proteção em um grupo de proteção e essas configurações serão aplicadas em todas as máquinas (virtuais ou físicas) que você adicionar ao grupo.

1. Vá até **Itens Protegidos** > **Grupo de Proteção** e clique no ícone para adicionar um grupo de proteção.

    ![Criar grupo de proteção](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)
2. Na página **Especificar configurações do grupo de proteção**, especifique um nome para o grupo. Na lista suspensa **De**, selecione o servidor de configuração no qual você deseja criar o grupo. O **Destino** é Microsoft Azure.

    ![Configurações do grupo de proteção](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)
3. Na página **Especificar Configurações de Replicação**, defina as configurações de replicação que serão usadas para todos os computadores do grupo.

    ![Replicação do grupo de proteção](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

   * **Consistência de várias VMs**: se você ativar essa configuração, ela cria pontos de recuperação compartilhados consistentes com aplicativos nos computadores do grupo de proteção. Essa configuração é mais relevante quando todos os computadores do grupo de proteção estiverem executando a mesma carga de trabalho. Todos os computadores serão recuperados para o mesmo ponto de dados. Esse recurso estará disponível se você estiver replicando VMs VMware ou servidores físicos (Windows ou Linux).
   * **Limite de RPO**: define o RPO. Alertas são gerados quando a replicação de proteção de dados contínuos excede o valor do limite de RPO configurado.
   * **Retenção do ponto de recuperação**: especifica a janela de retenção. Computadores protegidos podem ser recuperados para qualquer ponto nessa janela.
   * **Frequência do instantâneo consistente com aplicativo**: especifica com que frequência são criados os pontos de recuperação que contêm instantâneos consistentes com aplicativos.

Ao selecionar a marca de seleção, um grupo de proteção é criado com o nome especificado. Além disso, um segundo grupo de proteção será criado com o nome *protection-group-name*Failback. Esse grupo de proteção será usado se você fizer failback no site local após o failover no Azure. Você pode monitorar os grupos de proteção à medida que eles são criados na página **Itens Protegidos** .

## <a name="step-9-install-the-mobility-service"></a>Etapa 9: instalar o Serviço de mobilidade manualmente
A primeira etapa da habilitação da proteção para máquinas virtuais e servidores físicos é a instalação do Serviço de mobilidade. É possível fazer isso de duas formas:

* Do servidor de processo, enviar por push e instalar automaticamente o serviço em cada computador. Ao adicionar computadores a um grupo de proteção e eles já estiverem executando uma versão adequada do Serviço de mobilidade, a instalação por push não ocorrerá. Você também pode instalar automaticamente o serviço usando um método por push corporativo, como o WSUS ou o System Center Configuration Manager. Verifique se que você configurou o servidor de gerenciamento antes de fazer isso.
* Instale o serviço manualmente em cada computador que você deseja proteger. Verifique se que você configurou o servidor de gerenciamento antes de fazer isso.

### <a name="install-the-mobility-service-with-push-installation"></a>Instalar o Serviço de mobilidade com a instalação por push
Quando você adiciona computadores a um grupo de proteção, o Serviço de mobilidade é enviado automaticamente e instalado em cada computador pelo servidor em processo.

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Preparar para o envio por push automático em computadores com Windows
Para preparar os computadores com Windows para que o Serviço de mobilidade possa ser instalado automaticamente pelo servidor de processo:

1. Crie uma conta que o servidor de processo possa usar para acessar o computador. A conta deve ter privilégios de administrador (local ou domínio). Essas credenciais são usadas somente para a instalação por push do Serviço de mobilidade.

   > [!NOTE]
   > Se não estiver usando uma conta de domínio, você precisará desabilitar o controle Acesso de Usuário Remoto no computador local. Para fazer isso, no registro, em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, adicione a entrada DWORD LocalAccountTokenFilterPolicy com um valor de 1. Para adicionar a entrada de Registro de um comando de abertura da CLI ou usando o PowerShell, insira **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.
   >
   >
2. Para o Firewall do Windows no computador que você deseja proteger, selecione **Permitir um aplicativo ou recurso pelo Firewall** e habilite o **Compartilhamento de Arquivos e Impressoras** e a **Instrumentação de Gerenciamento do Windows**. Para computadores que pertencem a um domínio, você pode configurar a política de firewall com um GPO.

   ![Configurações de firewall](./media/site-recovery-vmware-to-azure-classic/mobility1.png)
3. Adicione a conta que você criou:

   * Abra **cspsconfigtool**. Ela está disponível como um atalho na área de trabalho e está localizada na pasta [LOCAL DE INSTALAÇÃO]\home\svsystems\bin.
   * Na guia **Gerenciar Contas**, clique em **Adicionar Conta**.
   * Adicione a conta que você criou. Depois de adicionar a conta, será preciso fornecer as credenciais quando adicionar um computador a um grupo de proteção.

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Preparar para o envio por push automático em servidores Linux
1. Verifique se há suporte para o computador com Linux que você deseja proteger, como descrito em [Pré-requisitos locais](#on-premises-prerequisites). Verifique se há conectividade de rede entre o computador que você deseja proteger e o servidor de gerenciamento que executa o servidor de processo.
2. Crie uma conta que o servidor de processo possa usar para acessar o computador. A conta deve ser de um usuário raiz no servidor Linux de origem. Essas credenciais são usadas somente para a instalação por push do Serviço de mobilidade.

   * Abra **cspsconfigtool**. Ela está disponível como um atalho na área de trabalho e está localizada na pasta [LOCAL DE INSTALAÇÃO]\home\svsystems\bin.
   * Na guia **Gerenciar Contas**, clique em **Adicionar Conta**.
   * Adicione a conta que você criou. Depois de adicionar a conta, será preciso fornecer as credenciais quando adicionar um computador a um grupo de proteção.
3. Verifique se o arquivo /etc/hosts no servidor Linux de origem contém entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede.
4. Instale os pacotes openssh, openssh-server e openssl mais recentes no computador que você deseja proteger.
5. Verifique se SSH está habilitado e em execução na porta 22.
6. Habilite a autenticação de subsistema e senha SFTP no arquivo sshd_config, como se segue:

   * Entre como raiz.
   * No arquivo /etc/ssh/sshd_config, localize a linha que começa com PasswordAuthentication.
   * Remova a marca de comentário da linha e altere o valor de **no** para **yes**.
   * Localize a linha que começa com **Subsystem** e remova a marca de comentário existente nessa linha.

     ![Padrão de substituição de nenhum subsistema do Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>Instalar o Serviço de Mobilidade manualmente
Os instaladores estão disponíveis em C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository.

| Sistema operacional de origem | Arquivo de instalação do Serviço de mobilidade |
| --- | --- |
| Windows Server (somente&64; bits) |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4, 6.5, 6.6 (somente 64 bits) |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3 (somente 64 bits) |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4, 6.5 (somente 64 bits) |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-the-mobility-service-manually-on-a-windows-server"></a>Instalar o Serviço de mobilidade manualmente em um servidor Linux
1. Baixe e execute o instalador relevante.
2. Em **Antes de começar**, selecione **Serviço de mobilidade**.

    ![Instalação do Serviço de mobilidade](./media/site-recovery-vmware-to-azure-classic/mobility3.png)
3. Em **Configuration Server Details (Detalhes do Servidor de Configuração)**, especifique o endereço IP do servidor de gerenciamento e a senha que foi gerada quando você instalou os componentes do servidor de gerenciamento. Você pode recuperar a senha ao executar **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n** no servidor de gerenciamento.

    ![Serviço de mobilidade](./media/site-recovery-vmware-to-azure-classic/mobility6.png)
4. Em **Local de Instalação**, mantenha a localização padrão e clique em **Avançar** para iniciar a instalação.
5. Em **Progresso da Instalação**, monitore a instalação e, se for solicitado, reinicie o computador.

Você também pode fazer a instalação ao digitar o seguinte texto da linha de comando:

    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]

No comando anterior:

* /Role: obrigatório. Especifica se o Serviço de mobilidade deve ser instalado.
* /InstallLocation: obrigatório. Especifica onde instalar o serviço.
* /PassphraseFilePath: obrigatório. Especifica a senha do servidor de configuração.
* /LogFilePath: obrigatório. Especifica o local do arquivo de configuração de log.

#### <a name="uninstall-the-mobility-service-manually"></a>Desinstalar o serviço de Mobilidade manualmente
Você pode desinstalar o serviço de Mobilidade usando **Desinstalar ou alterar um programa** no Painel de Controle ou usando a linha de comando.

O comando para desinstalar o Serviço de mobilidade usando a linha de comando é:

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="change-the-ip-address-of-the-management-server"></a>Alterar o endereço IP do servidor de gerenciamento
Após executar o assistente, você pode alterar o endereço IP do servidor de gerenciamento da seguinte maneira:

1. Abra o arquivo hostconfig.exe (localizado na área de trabalho).
2. Na guia **Global**, altere o endereço IP do servidor de gerenciamento.

   > [!NOTE]
   > Altere apenas o endereço IP do servidor de gerenciamento. O número da porta para comunicações do servidor de gerenciamento deve ser 443, e a opção **Usar HTTPS** precisa estar habilitada para a esquerda. Não altere a senha.
   >
   >

    ![Endereço IP do servidor de gerenciamento](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### <a name="install-the-mobility-service-manually-on-a-linux-server"></a>Instalar o Serviço de Mobilidade manualmente em um servidor Linux
1. Copie o arquivo tar apropriado no computador Linux que você deseja proteger. Confira a tabela em [Instalar o Serviço de mobilidade manualmente](#install-the-mobility-service-manually) para determinar qual arquivo tar você deve usar.
2. Abra um programa do shell e extraia o arquivo tar compactado para um caminho local executando: `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Crie um arquivo chamado passphrase.txt no diretório local para o qual você extraiu o conteúdo do arquivo tar. Para fazer isso, copie a senha de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase no servidor de gerenciamento e salve-a em passphrase.txt executando *`echo <passphrase> >passphrase.txt`* no shell.
4. Instale o serviço de mobilidade usando a linha de comando a seguir: *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*
5. Especifique o endereço IP interno do servidor de gerenciamento e verifique se a porta 443 está selecionada.

#### <a name="install-the-mobility-service-from-the-command-line"></a>Instalar o Serviço de mobilidade da linha de comando

Copie a senha em C:\Program Files (x86)\InMage Systems\private\connection no servidor de gerenciamento e salve-a como "passphrase.txt" no servidor de gerenciamento. Em seguida, execute o comando a seguir. Em nosso exemplo, o endereço IP do servidor de gerenciamento é 104.40.75.37 e a porta HTTPS é 443:

Para instalar em um servidor de produção:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Para instalar no servidor mestre de destino:

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## <a name="step-10-enable-protection-for-a-machine"></a>Etapa 10: habilitar a proteção para um computador
Para habilitar a proteção, adicione máquinas virtuais e servidores físicos a um grupo de proteção. Antes de começar, observe as seguintes condições se você estiver protegendo máquinas virtuais VMware:

* As máquinas virtuais VMware são descobertas a cada 15 minutos e podem levar mais de 15 minutos para aparecer no portal de Site Recovery após a descoberta.
* As alterações de ambiente na máquina virtual (como instalação de ferramentas VMware) também podem levar mais de 15 minutos para serem atualizadas na Recuperação de Site.
* É possível verificar a hora da última descoberta de VMs do VMware no campo **Último Contato Em** do servidor vCenter/host ESXi na guia **Servidores de Configuração**.
* Se você adicionar um servidor vCenter ou host ESXi após criar um grupo de proteção, poderá demorar mais de 15 minutos para que o portal do Azure Site Recovery seja atualizado e as máquinas virtuais sejam listadas na caixa de diálogo **Adicionar computadores a um grupo de proteção** .
* Se quiser continuar imediatamente e adicionar computadores a um grupo de proteção sem precisar esperar pela descoberta agendada, destaque o servidor de configuração (não clique nele) e clique em **Atualizar** .

Além disso:

* Recomendamos que você arquitete seus grupos de proteção para que reflitam suas cargas de trabalho. Por exemplo, adicione computadores que executam um aplicativo específico no mesmo grupo.
* Ao adicionar computadores a um grupo de proteção, o servidor de processo envia automaticamente e instala o Serviço de mobilidade se ele ainda não tiver sido instalado. Você precisará ter o mecanismo de envio por push preparado conforme descrito na etapa anterior.

### <a name="add-machines-to-a-protection-group"></a>Adicionar computadores a um grupo de proteção

1. Vá até **Itens Protegidos** > **Grupo de Proteção** > **Computadores** > **Adicionar Computadores**.
2. Se estiver protegendo máquinas virtuais VMware, em **Selecionar Máquinas Virtuais**, selecione um servidor vCenter que esteja gerenciando suas máquinas virtuais (ou o host ESXi no qual elas estão em execução) e, em seguida, selecione os computadores.

    ![Habilitar a proteção para máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)
3. Se estiver protegendo servidores físicos, em **Selecionar Máquinas Virtuais**, abra o assistente **Adicionar Computadores Físicos** e forneça o endereço IP e um nome amigável. Em seguida, selecione a família do sistema operacional.

   ![Habilitar a proteção para servidores físicos](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)
4. Em **Especificar Recursos de Destino**, selecione a conta de armazenamento que está sendo usada para a replicação e escolha se as configurações devem ser usadas para todas as cargas de trabalho. Observe que as contas de armazenamento Premium não têm suporte no momento.

   > [!NOTE]
   > Não há suporte para a movimentação de contas de armazenamento criadas usando o [portal do Azure](../storage/storage-create-storage-account.md) entre grupos de recursos.                           
   > [Migração de contas de armazenamento](../azure-resource-manager/resource-group-move-resources.md) nos grupos de recursos dentro da mesma assinatura ou nas assinaturas não tem suporte para as contas de armazenamento usadas para a implantação do Site Recovery.
   >
   >

    ![Definir as configurações de destino](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)
5. Em **Especificar Contas**, selecione a conta que você [configurou](#install-the-mobility-service-with-push-installation) para usar na instalação automática do Serviço de mobilidade.

    ![Especificar as contas](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)
6. Clique na marca de seleção para terminar de adicionar computadores ao grupo de proteção e iniciar a replicação inicial para cada computador.

   > [!NOTE]
   > Se a instalação por push foi preparada, o Serviço de mobilidade é instalado automaticamente nos computadores que não o têm ao serem adicionados ao grupo de proteção. Depois que o serviço é instalado, um trabalho de proteção é iniciado e apresenta falha. Após a falha, será preciso reiniciar manualmente cada computador que tenha o Serviço de mobilidade instalado. Após a reinicialização, o trabalho de proteção começa novamente e a replicação inicial ocorrerá.
   >
   >

Você pode monitorar o status na página **Trabalhos** .

![Monitorar status na página Trabalhos](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

O status da proteção também pode ser monitorado em **Itens Protegidos** > *nome do grupo de proteção* > **Máquinas Virtuais**. Depois da conclusão da replicação e da sincronização dos dados, o status do computador é alterado para** Protegido**.

![Monitorar o status em Itens Protegidos](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)

## <a name="step-11-set-protected-machine-properties"></a>Etapa 11: definir propriedades de computador protegido
1. Quando um computador tem o status **Protegido**, você pode configurar as respectivas propriedades de failover. Nos detalhes do grupo de proteção, escolha o computador e abra a guia **Configurar**.
2. A Recuperação de Site sugere automaticamente propriedades para a VM do Azure e detecta as configurações da rede local.

    ![Definir propriedades da máquina virtual](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)
3. Você pode alterar essas configurações:

   * **Nome da VM do Azure**: esse é o nome que será dado ao computador no Azure após o failover. O nome deve atender aos requisitos do Azure.
   * **Tamanho da VM do Azure**: o número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino. Para obter mais informações sobre tamanhos e adaptadores, confira as [tabelas de tamanhos](../virtual-machines/virtual-machines-linux-sizes.md#size-tables). Observe que:

     * Quando você modificar o tamanho de uma máquina virtual e salvar as configurações, o número de adaptadores de rede será alterado na próxima vez em que você abrir a guia **Configurar**. O número mínimo de adaptadores de rede em máquinas virtuais de destino é igual ao número mínimo de adaptadores de rede em uma máquina virtual de origem. O número máximo de adaptadores de rede é determinado pelo tamanho da máquina virtual.
       * Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que a origem.
       * Se o número de adaptadores para máquina virtual de origem exceder o número permitido para o tamanho de destino, o tamanho máximo de destino será usado.
        Por exemplo, se uma máquina de origem tiver dois adaptadores de rede e o tamanho da máquina de destino der suporte a quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino com suporte só der suporte a uma, a máquina de destino terá apenas um adaptador.
     * Se a máquina virtual tiver vários adaptadores de rede, todos deverão estar conectados à mesma rede do Azure.
   * **Rede do Azure**: é necessário especificar uma rede do Azure à qual as VMs do Azure serão conectadas após o failover. Se você não especificar a rede, as VMs do Azure não serão conectadas a nenhuma rede. Além disso, você precisa especificar uma rede do Azure se quiser fazer failback do Azure para o site local. O failback requer uma conexão VPN entre uma rede do Azure e uma rede local.
   * **Endereço IP/sub-rede do Azure**: Para cada adaptador de rede, selecione a sub-rede à qual a VM do Azure deve se conectar. Observe que se o adaptador de rede do computador de origem estiver configurado para usar um endereço IP estático, você poderá especificar um endereço IP estático para a VM do Azure. Se você não fornecer um endereço IP estático, um endereço IP disponível será alocado. Se o endereço IP de destino for especificado, mas já estiver em uso por outra VM no Azure, o failover falhará. Se o adaptador de rede do computador de origem estiver configurado para usar DHCP, você terá DHCP como configuração do Azure.      

## <a name="step-12-create-a-recovery-plan-and-run-a-failover"></a>Etapa 12: criar um plano de recuperação e executar um failover
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Failover/player]
>
>

Você pode fazer failover de um único computador ou fazer failover de várias máquinas virtuais que executam a mesma tarefa ou executam a mesma carga de trabalho. Para fazer failover em vários computadores ao mesmo tempo, adicione-os a um plano de recuperação.

Para criar um plano de recuperação:

1. Na página **Planos de Recuperação**, clique em **Adicionar Plano de Recuperação** e adicione um plano de recuperação. Especifique os detalhes do plano e selecione **Azure** como destino.

 ![Configurar plano de recuperação](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)
2. Em **Selecionar Máquina Virtual**, selecione um grupo de proteção, bem como os computadores do grupo a serem adicionados ao plano de recuperação.

 ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

Você pode personalizar o plano para criar grupos e sequenciar a ordem em que os computadores no plano de recuperação serão submetidos a failover. Você também pode adicionar scripts e prompts para ações manuais. Os scripts podem ser criados manualmente ou usando os [Runbooks de Automação do Azure](site-recovery-runbook-automation.md). Para obter mais informações sobre como personalizar os planos de recuperação, confira [Criar planos de recuperação](site-recovery-create-recovery-plans.md).

## <a name="run-a-failover"></a>Executar um failover
Antes de executar um failover:

* Verifique se o servidor de gerenciamento está em execução e disponível. Se não estiver, o failover falhará.
* Se executar um failover não planejado:

  * Se possível, você deve desligar os computadores primários antes de fazer um failover não planejado. Isso faz com que você não tenha os computadores de origem e de réplica em execução ao mesmo tempo. Se estiver replicando VMs VMware ao executar um failover não planejado, você pode especificar que o Site Recovery deve tentar desligar os computadores de origem. Dependendo do estado do site primário, isso pode ou não funcionar. Se você estiver replicando servidores físicos, o Site Recovery não oferecerá essa opção.
  * Um failover não planejado interrompe a replicação de dados de computadores primários para que qualquer delta de dados não seja transferido após o início desse failover não planejado.
  * Se você deseja se conectar à máquina virtual de réplica no Azure após o failover, habilite a Conexão de Área de Trabalho Remota no computador de origem antes de fazer failover. Em seguida, permita a conexão de RDP pelo firewall. Você também precisará permitir RDP no ponto de extremidade público de máquina virtual do Azure após o failover. Siga estas [práticas recomendadas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) para garantir o funcionamento do RDP após um failover.

> [!NOTE]
> Para obter o melhor desempenho ao fazer um failover para o Azure, não deixe de instalar o agente do Azure no computador protegido. Isso ajuda o computador a inicializar mais rapidamente e ajuda a diagnosticar problemas. O Agente do Azure está disponível para [Linux](https://github.com/Azure/WALinuxAgent) e [Windows](http://go.microsoft.com/fwlink/?LinkID=394789).
>
>

### <a name="run-a-test-failover"></a>Execute um teste de failover
Execute um failover de teste para simular os processos de failover e de recuperação em uma rede isolada que não afete seu ambiente de produção e permite que a replicação normal continue normalmente. O failover de teste é iniciado na origem, e você pode executá-lo de duas maneiras:

* **Não especificar uma rede do Azure**: Se você executar um failover de teste sem uma rede, o teste vai verificar se as máquinas virtuais iniciam e aparecem corretamente no Azure. As máquinas virtuais não serão conectadas a uma rede do Azure após o failover.
* **Especificar uma rede do Azure**: esse tipo de failover verifica se o ambiente de replicação inteiro é mostrado como o esperado e se as máquinas virtuais do Azure estão conectadas à rede especificada.

Para executar um failover de teste:

1. Na página **Planos de Recuperação**, selecione o plano e clique em **Failover de Teste**.

 ![Selecione o plano](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)
2. Em **Confirmar Failover de Teste**, selecione **Nenhum** para indicar que você não deseja usar uma rede do Azure para o failover de teste ou selecione a rede à qual as VMs de teste serão conectadas após o failover. Clique na marca de seleção para iniciar o failover.

 ![Faça uma seleção](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)
3. Monitore o progresso do failover na guia **Trabalhos** .

 ![Monitorar o progresso](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)
4. Após a conclusão do failover, você também deve conseguir ver a réplica do computador em **Máquinas Virtuais** no portal do Azure. Se você quiser iniciar uma conexão de RDP com a VM do Azure, precisará abrir a porta 3389 no ponto de extremidade da VM.
5. Assim que terminar, quando o failover atingir a fase de teste **Concluído**, clique em **Concluir Teste** para finalizar. Em **Observações**, registre e salve todas as observações associadas ao failover de teste.
6. Clique em **O failover de teste está concluído** para limpar automaticamente o ambiente de teste. Depois de isso ser feito, o failover de teste mostrará o status **Concluído** . Todos os elementos ou máquinas virtuais criadas automaticamente durante o teste de failover são excluídos. Se um failover de teste continuar por mais de duas semanas, ele forçado a concluir.

### <a name="run-an-unplanned-failover"></a>Executar um failover não planejado
O failover não planejado é iniciado do Azure e pode ser executado mesmo se o site primário não estiver disponível.

1. Na página **Planos de Recuperação**, selecione o plano e clique em **Failover** > **Failover Não Planejado**.

 ![Selecione o plano](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)
2. Se estiver replicando máquinas virtuais VMware, você pode tentar desligar as VMs locais. Essa é a melhor ação possível, e o failover continuará mesmo que o esforço não seja bem-sucedido. Se não for bem-sucedido, os detalhes do erro serão exibidos na guia **Trabalhos** em **Trabalhos de Failover Não Planejados**.

 ![Opção para desligar as VMs locais](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

 > [!NOTE]
 > Essa opção não estará disponível se você estiver replicando servidores físicos. Você precisa tentar desligá-los manualmente, se possível.
 >
 >

3. Em **Confirmar Failover**, verifique a direção do failover (para o Azure) e selecione o ponto de recuperação que você deseja usar para o failover. Se você tiver habilitado Várias VMs durante a configuração das propriedades de replicação, poderá recuperar o ponto de recuperação mais recente consistente com o aplicativo ou com o controle de falhas. Também é possível selecionar **Ponto de recuperação personalizado** para r realizar a recuperação em um ponto anterior no tempo. Clique na marca de seleção para iniciar o failover.

 ![Confirmar direção do failover](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)
4. Aguarde a conclusão do trabalho de failover não planejado. É possível monitorar o progresso do failover na guia **Trabalhos** . Mesmo que ocorram erros durante um failover não planejado, o plano de recuperação será executado até que seja concluído. Você também deve conseguir ver a máquina de réplica do Azure aparecer em **Máquinas Virtuais** no portal do Azure.

### <a name="connect-to-replicated-azure-virtual-machines-after-failover"></a>Conectar-se com as máquinas virtuais replicadas do Azure após o failover
Para se conectar a máquinas virtuais replicadas no Azure após o failover, você precisa de:

- Uma conexão de Área de Trabalho habilitada na máquina principal.
- Firewall do Windows na máquina principal configurado para permitir RDP.
- RDP adicionado ao ponto de extremidade público para a máquina virtual do Azure.

Para obter mais informações sobre a configuração, confira [Solucionando problemas de conexão de área de trabalho remota após failover usando ASR](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

## <a name="deploy-additional-process-servers"></a>Implantar servidores de processo adicionais
Se precisar escalar horizontalmente sua implantação para mais de 200 computadores de origem ou se a taxa diária de rotatividade total ultrapassar 2 TB, você precisará de servidores de processo adicionais para gerenciar o volume de tráfego. Para configurar um servidor de processo adicional, verifique os requisitos em [Servidores de processo adicionais](#additional-process-servers) e configure o servidor de processo de acordo com as seguintes instruções. Depois de definir o servidor, você pode configurar os computadores de origem para usá-lo.

### <a name="set-up-an-additional-process-server"></a>Configurar um servidor de processo adicional
Defina um servidor de processo adicional da seguinte maneira:

* Execute o assistente unificado para configurar um servidor de gerenciamento como somente servidor de processo.
* Se você deseja gerenciar a replicação de dados usando apenas o novo servidor de processo, será necessário migrar seus computadores protegidos.

### <a name="install-the-process-server"></a>Instalar o servidor de processo
1. Na página **Início Rápido**, baixe o arquivo de instalação unificada para a instalação do componente do Site Recovery. Execute a instalação.
2. Em **Antes de começar**, selecione **Adicionar servidores em processo adicionais para escalar horizontalmente a implantação**.

 ![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)
3. Conclua o assistente como fez ao [configurar](#step-5-install-the-management-server) o primeiro servidor de gerenciamento.
4. Em **Detalhes do Servidor de Configuração**, insira o endereço IP do servidor de gerenciamento original no qual você instalou o servidor de configuração e, em seguida, insira a senha. Se não tiver a senha, execute **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** no gerenciamento original para recuperá-la.

 ![Detalhes do servidor de configuração](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar computadores para usar o novo servidor de processo
1. Abra **Servidores de Configuração** > **Servidor** > *nome do servidor de gerenciamento original* > **Detalhes do Servidor**.

 ![Detalhes do servidor](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)
2. Na lista **Servidores de Processo**, clique em **Alterar Servidor de Processo** ao lado do servidor que deseja modificar.

 ![Atualizar o servidor de processo](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)
3. Selecione **Alterar Servidor de Processo**, selecione **Servidor de Processo de Destino** e, em seguida, selecione o novo servidor de gerenciamento. Selecione as máquinas virtuais que o novo servidor de processo vai gerenciar. Clique no ícone de informações para obter informações sobre o servidor. O espaço médio necessário para replicar cada máquina virtual selecionada no novo servidor de processo será exibido para lhe ajudar a tomar decisões sobre a carga. Clique na marca de seleção para começar a replicar no novo servidor de processo.

 ![Alterar o servidor de processo](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)

## <a name="vmware-permissions-for-vcenter-access"></a>Permissões de VMware para acesso do vCenter
O servidor de processo pode descobrir as VMs em um servidor vCenter automaticamente. Para executar a descoberta automática, você precisa definir uma função (Azure_Site_Recovery) no nível do vCenter para permitir que o Site Recovery acesse o servidor vCenter. Se você só precisa migrar máquinas virtuais VMware para o Azure e não precisa do failback do Azure, pode definir uma função somente leitura que seja suficiente. Defina as permissões conforme descrito em [Etapa 6: configurar credenciais para servidor do vCenter](#step-6-set-up-credentials-for-the-vcenter-server). As permissões de função necessárias estão resumidas na tabela a seguir:

| **Função** | **Detalhes** | **Permissões** |
| --- | --- | --- |
| Função Azure_Site_Recovery |Descoberta de máquina virtual VMware |Atribua estes privilégios ao servidor v-Center:<br/><br/>Datastore: Alocar espaço, Pesquisar datastore, Operações de arquivo de nível baixo, Remover arquivo, Atualizar arquivos de máquina virtual<br/><br/>Rede: Atribuição de rede<br/><br/>Recursos: Atribuir máquina virtual ao pool de recursos, Migrar máquina virtual desligada, Migrar máquina virtual ligada<br/><br/>Tarefas: Criar tarefa, Atualizar tarefa<br/><br/>Máquina virtual: Configuração<br/><br/>Máquina virtual: Interagir -> Responder pergunta, Conexão de dispositivo, Configurar mídia de CD, Configurar mídia de disquete, Desligar, Ligar, Instalação de ferramentas VMware<br/><br/>Máquina virtual > Inventário > Criar, Registrar, Cancelar registro<br/><br/>Máquina virtual > Provisionamento -> Permitir download de máquina virtual, Permitir upload de arquivos de máquina virtual<br/><br/>Máquina virtual > Instantâneos > Remover instantâneos |
| Função de usuário do vCenter |Descoberta de máquina virtual VMware/Failover sem o desligamento da VM de origem |Atribua estes privilégios ao servidor v-Center:<br/><br/>Objeto de Data Center > Propagar para o Objeto Filho, role=Read-only <br/><br/>O usuário é atribuído no nível do datacenter e, portanto, tem acesso a todos os objetos do datacenter. Se quiser restringir o acesso, atribua a função **Nenhum acesso** com **Propagar para objeto filho** aos objetos filho (hosts ESX, armazenamentos de dados, VMs e redes). |
| Função de usuário do vCenter |Failover e failback |Atribua estes privilégios ao servidor v-Center:<br/><br/>Objeto de data center – Propagar para o objeto filho, função = Azure_Site_Recovery<br/><br/>O usuário é atribuído no nível do datacenter e, portanto, tem acesso a todos os objetos no datacenter.  Se quiser restringir o acesso, atribua a função **Nenhum acesso** com **Propagar para objeto filho** para o objeto filho (hosts ESX, armazenamentos de dados, VMs e redes). |

## <a name="third-party-software-notices-and-information"></a>Avisos e informações de softwares de terceiros
<!--Do Not Translate or Localize-->

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o failback](site-recovery-failback-azure-to-vmware-classic.md) para recolocar seus computadores com failover em execução no Azure no ambiente local.

