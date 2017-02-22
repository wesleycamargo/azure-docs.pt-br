---
title: "Planejar capacidade e dimensionamento para replicação VMware no Azure | Microsoft Docs"
description: Use este artigo para planejar a capacidade e o dimensionamento ao replicar VMs VMware no Azure
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/05/2017
ms.author: rayne
translationtype: Human Translation
ms.sourcegitcommit: 6521cada7adeacd98fae46e5119ceffa0351e9b5
ms.openlocfilehash: a5c6759d9826084ae339dd291140f8383b55b6db


---
# <a name="plan-capacity-and-scaling-for-vmware-replication-with-azure-site-recovery"></a>Planejar capacidade e dimensionamento para replicação VMware com o Azure Site Recovery

Use este artigo para descobrir como planejar a capacidade e o dimensionamento ao replicar VMs VMware locais e servidores físicos no Azure com o [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Como iniciar o planejamento de capacidade?

1. Colete informações sobre seu ambiente de replicação usando o Planejador de Capacidade do Azure Site Recovery. Isso inclui informações sobre VMs, discos por VM e armazenamento por disco.
2. Faça uma estimativa da alteração diária (taxa de rotatividade) dos dados replicados no ambiente.


## <a name="gather-information"></a>Coletar informações

1. Baixe e execute o [Planejador de Capacidade[(https://gallery.technet.microsoft.com/Azure-Recovery-Capacity-d01dc40e)].
2. [Obtenha instruções](site-recovery-capacity-planner.md) para executar a ferramenta.


## <a name="estimate-the-daily-churn-rate"></a>Fazer estimativa da taxa de rotatividade diária

O Planejador de Capacidade do Site Recovery exige que você informe a taxa média de alteração de dados diária como uma porcentagem. Atualmente, você pode obter essas informações usando o [dispositivo de planejamento de capacidade vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).

Na ferramenta, é possível computar a porcentagem apontando a ferramenta de planejamento vSphere para todas as VMs de origem e obter a alteração total diária. Esse é basicamente o tráfego da rede. [Saiba mais](https://blogs.vmware.com/vsphere/2014/04/vsphere-replication-capacity-planning-appliance.html) sobre a execução dessa ferramenta.


## <a name="capacity-considerations"></a>Considerações sobre a capacidade

**Componente** | **Detalhes** |
--- | --- | ---
**Replicação** | **Taxa de alteração diária máxima**— Um computador protegido só pode usar um servidor de processo, e um servidor de processo único pode manusear uma taxa de alteração diária máxima de 2 TB. Portanto, 2 TB é a taxa de alteração diária máxima com suporte para um computador protegido.<br/><br/> **Taxa de transferência máxima**— uma máquina replicada pode pertencer a uma conta de armazenamento no Azure. Uma conta de armazenamento padrão pode lidar com um máximo de 20 mil solicitações por segundo e recomendamos que você mantenha o número de IOPS em um computador de origem como 20 mil. Por exemplo, se você tiver um computador de origem com 5 discos e cada disco gerar 120 IOPS (8 K de tamanho) na origem, ele estará dentro do limite IOPS de 500 por disco do Azure. O número de contas de armazenamento necessárias = total de IOPs de origem/20000.
**Servidor de configuração** | O servidor de configuração deve ser capaz de lidar com a capacidade de taxa de alteração diária em todas as cargas de trabalho em execução em computadores protegidos e precisa ter largura de banda suficiente para replicar continuamente os dados para o armazenamento do Azure.<br/><br/> Como prática recomendada, aconselhamos que o servidor de configuração esteja localizado na mesma rede e no segmento de LAN que os computadores que deseja proteger. Ele pode estar localizado em uma rede diferente, mas os computadores que você deseja proteger devem ter visibilidade de rede L3 para ele.<br/><br/> As recomendações de tamanho para o servidor de configuração estão resumidas na tabela abaixo.
**Servidor de processo** | O primeiro servidor de processo é instalado por padrão no servidor de configuração. Você pode implantar servidores de processo adicionais par dimensionar seu ambiente. Observe que:<br/><br/> o servidor de processo recebe os dados de replicação de computadores protegidos e os otimiza com caching, compactação e criptografia antes de enviar para o Azure. O computador do servidor de processo deve ter recursos suficientes para executar essas tarefas.<br/><br/> O servidor em processo usa o cache baseado em disco. Recomendamos um disco de cache separado de 600 GB ou mais para lidar com alterações de dados armazenados em caso de afunilamento ou interrupção de rede.

## <a name="size-recommendations-for-the-configuration-server"></a>Recomendações de tamanho para o servidor de configuração

**CPU** | **Memória** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos**
--- | --- | --- | --- | ---
8 vCPUs (2 soquetes x 4 núcleos @ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | Replique menos de 100 computadores.
12 vCPUs (2 soquetes x 6 núcleos @ 2,5 GHz) | 18 GB | 600 GB | 500 GB a 1 TB | Replique entre 100 e 150 computadores.
16 vCPUs (2 soquetes x 8 núcleos @ 2,5 GHz) | 32 GB | 1 TB | 1 TB a 2 TB | Replique entre 150 e 200 computadores.
Implantar outro servidor de processo | | | > 2 TB | Implante servidores de processo adicionais se estiver replicando mais de 200 computadores ou se a taxa de alteração diária de dados ultrapassar 2 TB.

Em que:

* Cada computador de origem é configurado com 3 discos de 100 GB.
* Usamos armazenamento de benchmark de 8 unidades SAS de 10K RPM com RAID 10 para as medidas do disco de cache.

## <a name="size-recommendations-for-the-process-server"></a>Recomendações de tamanho para o servidor de processo

Se precisar proteger mais de 200 computadores ou se a taxa de alteração diária for maior que 2 TB, adicione mais servidores em processo para manipular a carga de replicação. Para escalar horizontalmente, você pode:

* Aumente o número de servidores de configuração. Por exemplo, você pode proteger até 400 computadores com dois servidores de configuração.
* Adicione mais servidores em processo e use-os para manipular o tráfego em vez do (ou além do) servidor de configuração.

Esta tabela descreve um cenário em que:

* Você não planeja usar o servidor de configuração como um servidor de processo.
* Você configurou um servidor de processo adicional.
* Você configurou máquinas virtuais protegidas para usar o servidor em processo adicional.
* Cada computador de origem protegido é configurado com três discos de 100 GB cada.

**Servidor de configuração** | **Servidor de processo adicional** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos**
--- | --- | --- | --- | ---
8 vCPUs (2 soquetes x 4 núcleos @ 2,5 GHz), 16 GB de memória | 4 vCPUs (2 soquetes x 2 núcleos @ 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Replique 85 computadores ou menos.
8 vCPUs (2 soquetes x 4 núcleos @ 2,5 GHz), 16 GB de memória | 8 vCPUs (2 soquetes x 4 núcleos @ 2,5 GHz), 12 GB de memória | 600 GB | 250 GB a 1 TB | Replique entre 85 e 150 computadores.
12 vCPUs (2 soquetes x 6 núcleos @ 2,5 GHz), 18 GB de memória | 12 vCPUs (2 soquetes x 6 núcleos @ 2,5 GHz), 24 GB de memória | 1 TB | 1 TB a 2 TB | Replique entre 150 e 225 computadores.

A maneira como você escala seus servidores depende de sua preferência por um modelo que escale vertical ou horizontalmente.  Você pode escalar verticalmente implantando alguns servidores de processo e de configuração de alto nível ou escalar horizontalmente implantando mais servidores com menos recursos. Por exemplo, se você precisa proteger 220 computadores, faça o seguinte:

* Defina o servidor de configuração com 12vCPU, 18 GB de memória, um servidor de processo adicional com 12vCPU, 24 GB de memória e configure os computadores protegidos para usar somente o servidor de processo adicional.
* Como alternativa, configure dois servidores de configuração (2 x 8vCPU, 16 GB de RAM) e dois servidores de processo adicionais (1 x 8vCPU e 4vCPU x1 para manipular 135 + 85 [220] computadores) e configure os computadores protegidos para usar somente os servidores em processo adicionais.


## <a name="control-network-bandwidth"></a>Controlar largura de banda da rede

Você pode usar a ferramenta de planejador de capacidade para calcular a largura de banda necessária para a replicação (replicação inicial e depois a delta). Para controlar a quantidade de largura de banda usada para a replicação, você tem algumas opções:

* **Restringir a largura de banda**: o tráfego VMware que replica para o Azure passa por um servidor de processo específico. Você pode limitar a largura de banda nos computadores em execução como servidores de processo.
* **Influenciar a largura de banda**: você pode influenciar a largura de banda usada para a replicação usando algumas chaves do Registro:
  * O valor de registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** especifica o número de threads usados para a transferência de dados (replicação inicial ou delta) de um disco. Um valor mais alto aumenta a largura de banda de rede usada para replicação.
  * O **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM** especifica o número de threads usados para transferência de dados durante o failback.

### <a name="throttle-bandwidth"></a>Restringir a largura de banda
1. Abra o snap-in MMC do Backup do Microsoft Azure no computador atuando como servidor de processo. Por padrão, um atalho para o Backup do Microsoft Azure está disponível na área de trabalho ou C:\Arquivos de Programas\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.

    ![Restringir a largura de banda](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Na guia **Limitação**, selecione **Habilitar limitação de uso de largura de banda da Internet para operações de backup** e defina os limites do horário comercial e não comercial. Os intervalos válidos são de 512 Kbps a 102 Mbps por segundo.

    ![Restringir a largura de banda](./media/site-recovery-vmware-to-azure/throttle2.png)

Você também pode usar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para definir a limitação. Veja um exemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que nenhuma limitação é necessária.

#### <a name="influence-network-bandwidth-for-a-vm"></a>Influenciar largura de banda da rede para uma VM

1. No Registro da VM, navegue até **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Para influenciar o tráfego de largura de banda em um disco de replicação, modifique o valor de **UploadThreadsPerVM**ou crie a chave caso ela não exista.
   * Para influenciar a largura de banda para o tráfego de failback do Azure, modifique o valor **DownloadThreadsPerVM**.
2. O valor padrão é 4. Em uma rede "sobreprovisionada", os valores padrão dessas chaves do registro precisam ser alterados. O máximo é 32. Monitore o tráfego para otimizar o valor.


## <a name="deploy-additional-process-servers"></a>Implantar servidores de processo adicionais

Se for necessário escalar horizontalmente sua implantação para além de 200 computadores de origem ou uma taxa diária de rotatividade total acima de 2 TB, você precisará de servidores em processo adicionais para manipular o volume de tráfego. Siga estas instruções para configurar um servidor de processo. Depois de configurar o servidor, você migrará os computadores de origem para usá-lo.

1. Nos **servidores do Site Recovery**, clique no servidor de configuração > **Servidor de processo**.

    ![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps1.png)
2. Em **Tipo de Servidor**, clique em **Servidor em processo (local)**.

    ![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
3. Baixe o arquivo de Configuração Unificada do Site Recovery e execute-o para instalar o servidor em processo e registrá-lo no cofre.
4. Em **Antes de começar**, selecione **Adicionar servidores em processo adicionais para escalar horizontalmente a implantação**.
5. Conclua o assistente da mesma forma que fez ao [configurar](#step-2-set-up-the-source-environment) o servidor de configuração.

    ![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure/add-ps1.png)
6. Em **Detalhes do Servidor de Configuração**, especifique o endereço IP do servidor de configuração e a frase secreta. Para obter a frase secreta, execute **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n** no servidor de configuração.

    ![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar computadores para usar o novo servidor de processo
1. Em **Configurações** > **Servidores do Site Recovery**, clique no servidor de configuração e expanda **Servidores em processo**.

    ![Atualizar servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Clique com o botão direito do mouse no servidor em processo atualmente em uso, e clique em **Mudar**.

    ![Atualizar servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. Em **Selecionar servidor em processo de destino**, selecione o novo servidor em processo que você deseja usar e selecione as máquinas virtuais que serão manipuladas pelo novo servidor em processo. Clique no ícone de informações para obter informações sobre o servidor. Para ajudá-lo a tomar decisões de carregamento, o espaço médio necessário para replicar cada máquina virtual selecionada no novo servidor de processo será exibido. Clique na marca de seleção para começar a replicar no novo servidor de processo.










<!--HONumber=Feb17_HO2-->


