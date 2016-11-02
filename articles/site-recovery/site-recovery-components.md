<properties
    pageTitle="Como funciona a Recuperação de Site? | Microsoft Azure"
    description="Este artigo fornece uma visão geral da arquitetura de Recuperação de Site"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/13/2016"
    ms.author="raynew"/>


# <a name="how-does-azure-site-recovery-work?"></a>Como funciona o Azure Site Recovery?

Leia este artigo para compreender a arquitetura do serviço Azure Site Recovery e os componentes que permitem o seu funcionamento.

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Visão geral

Você precisa de uma estratégia de BCDR (continuidade dos negócios e recuperação de desastre) que mantém as cargas de trabalho e os dados em execução e disponíveis durante o tempo de inatividade planejado e não planejado, e para recuperar as condições normais de trabalho assim que possível. 

O Site Recovery é um serviço do Azure que contribui para sua estratégia de BCDR. Ele orquestra a replicação de máquinas virtuais e servidores físicos locais para a nuvem (Azure), ou para um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. Leia uma visão geral em [O que é o Site Recovery?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Recuperação de Site no portal do Azure

O Azure tem dois [modelos de implantação](../resource-manager-deployment-model.md) diferentes para criar e trabalhar com recursos: o modelo Azure Resource Manager e o modelo de gerenciamento de serviços clássico. O Azure também tem dois portais: o [Portal Clássico do Azure](https://manage.windowsazure.com/), que dá suporte ao modelo de implantação clássico, e o [Portal do Azure](https://portal.azure.com), com suporte aos dois modelos de implantação.

- A Recuperação de Site está disponível no Portal Clássico e no Portal do Azure.
- No portal clássico do Azure, você pode dar suporte ao Site Recovery com o modelo de gerenciamento de serviços clássico.
- No Portal do Azure, é possível oferecer suporte às implantações no modelo clássico ou do Resource Manager. 

As informações neste artigo se aplicam às implantações clássicas e do portal do Azure. A tabela resume as diferenças.

**Replicar** | **Portal do Azure** | **Portal clássico**
--- | --- | ---
**Replicação da VM VMware para o Azure** | Processo de implantação simplificado.<br/><br/> Execute o failover de VMs para o armazenamento clássico ou com base no Resource Manager.<br/><br/> Replique para o armazenamento clássico ou com base no Resource Manager.<br/><br/> Use redes clássicas ou do Resource Manager para conectar as VMs do Azure após o failover.<br/><br/> Use o armazenamento LRS ou GRS. | Execute o failover somente para o armazenamento clássico.<br/><br/> Use redes clássicas somente para se conectar a VMs após o failover.<br/><br/> Use o armazenamento GRS.
**Replicação de VMs Hyper-V (sem VMM) no Azure** | Processo de implantação simplificado.<br/><br/> Execute o failover de VMs para o armazenamento clássico ou com base no Resource Manager.<br/><br/> Replique para o armazenamento clássico ou com base no Resource Manager.<br/><br/> Use redes clássicas ou do Resource Manager para conectar as VMs do Azure após o failover.
**Replicação de VMs Hyper-V (com VMM) no Azure** | Processo de implantação simplificado.<br/><br/> Execute o failover de VMs para o armazenamento clássico ou com base no Resource Manager.<br/><br/> Replique para o armazenamento clássico ou com base no Resource Manager.<br/><br/> Use redes clássicas ou do Resource Manager para conectar as VMs do Azure após o failover.<br/><br/> É necessário configurar o mapeamento de rede | Execute o failover somente para o armazenamento clássico.<br/><br/> Use redes clássicas somente para se conectar a VMs após o failover.
**Replicação de VMs Hyper-V (com VMM) para um site secundário** | Processo de implantação simplificado.<br/><br/> Execute o failover de VMs para o armazenamento clássico ou com base no Resource Manager.<br/><br/> Replique para o armazenamento clássico ou com base no Resource Manager.<br/><br/> Use redes clássicas ou do Resource Manager para conectar as VMs do Azure após o failover.<br/><br/> É necessário configurar o mapeamento de rede | Execute o failover somente para o armazenamento clássico.<br/><br/> Use redes clássicas somente para se conectar a VMs após o failover.<br/><br/> Você pode configurar o mapeamento de armazenamento. <br/><br/> Não há suporte para replicação SAN.



## <a name="deployment-scenarios"></a>Cenários de implantação

A Recuperação de Site pode ser implantada para orquestrar a replicação em vários cenários:

- **Replicar VMs VMware**: você pode replicar máquinas virtuais VMware locais no Azure ou em um datacenter secundário.
- **Replicar computadores físicos**: você pode replicar computadores físicos executando Windows ou Linux no armazenamento do Azure ou em um datacenter secundário. O processo de replicação de máquinas físicas é quase o mesmo que o processo de replicação de VMs VMware.
- **Replicar VMs Hyper-V**: se os seus hosts do Hyper-V forem gerenciados em nuvens do System Center VMM, você poderá replicar VMs no Azure ou em um datacenter secundário. Se os hosts não forem gerenciados por VMM, você poderá replicar somente no Azure. Você pode replicar VMs Hyper-V que não são gerenciadas por VMM no armazenamento do Azure.
- **Migrar VMs**: você pode usar o Site Recovery para [migrar VMs IaaS do Azure](site-recovery-migrate-azure-to-azure.md) entre regiões ou para [migrar instâncias do Windows do AWS](site-recovery-migrate-aws-to-azure.md) para VMs IaaS do Azure. Não há suporte no momento para a replicação total. Você pode replicar para migração (failover), mas não pode realizar o failback. 

A Recuperação de Site pode replicar a maioria dos aplicativos em execução nas VMs e nos servidores físicos. Você pode obter um resumo completo dos aplicativos com suporte em [Quais cargas de trabalho o Azure Site Recovery pode proteger?](site-recovery-workload.md)


## <a name="replicate-vmware-virtual-machines-to-azure"></a>Replique as máquinas virtuais VMware para o Azure

![Avançado](./media/site-recovery-components/arch-enhanced.png)

**Componente** | **Detalhes**
--- | ---
**As tabelas** | **Conta** você precisa de uma conta do Azure.<br/><br/> **Armazenamento**: você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados. Você pode usar uma conta clássica ou uma conta de armazenamento do Resource Manager. A conta pode ser LRS ou GRS quando você implantar no Portal do Azure. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover.<br/><br/> **Rede**: você precisa de uma rede virtual do Azure com a qual as VMs do Azure se conectarão quando forem criadas no failover. No Portal do Azure, as redes podem ser criadas no modelo clássico do gerenciador de serviço ou no modelo do Resource Manager.
**Servidor de configuração local** |  Você precisa de uma máquina local com Windows Server 2012 R2 que execute o servidor de configuração e outros componentes do Site Recovery.<br/><br/> Ela deve ser uma VM VMware altamente disponível.<br/><br/> Os componentes do servidor incluem:<br/><br/> **Servidor de configuração**: coordena a comunicação entre o ambiente local e o Azure e gerencia a recuperação e a replicação de dados.<br/><br/> **Servidor de processo**: atua como um gateway de replicação. Ele recebe dados de replicação de computadores de origem protegida, otimiza-os com caching, compactação e criptografia e envia os dados para o armazenamento do Azure. Ele também manipula a instalação por push do Serviço de mobilidade em computadores protegidos e executa a descoberta automática de máquinas virtuais VMware. À medida que a implantação cresce, você pode adicionar outros servidores de processo dedicados separados para lidar com o aumento do volume de tráfego de replicação.<br/><br/> **Servidor de destino mestre**: lida com os dados de replicação durante o failback do Azure.
**Servidores de virtualização locais** | Um ou mais hosts vSphere. Também recomendamos um servidor vCenter para gerenciar os hosts.
**VMs para replicar** | Cada VM VMware que você quiser replicar para o Azure precisa ter o componente de Serviço de mobilidade instalado. Esse serviço captura gravações de dados no computador e as encaminha ao servidor de processo. Esse componente pode ser instalado manualmente ou pode ser enviado por push e instalado automaticamente pelo servidor de processo quando você habilita a replicação em um computador.

- [Saiba mais](site-recovery-vmware-to-azure.md#azure-prerequisites) sobre os requisitos de implantação no Portal do Azure.
- [Saiba mais](site-recovery-failback-azure-to-vmware.md) sobre o failback no Portal do Azure.


## <a name="replicate-physical-servers-to-azure"></a>Replicar servidores físicos para o Azure

![Avançado](./media/site-recovery-components/arch-enhanced.png)

**Componente** | **Detalhes**
--- | ---
**As tabelas** | **Conta** você precisa de uma conta do Azure.<br/><br/> **Armazenamento**: você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados. Você pode usar uma conta clássica ou uma conta de armazenamento do Resource Manager. A conta pode ser LRS ou GRS quando você implantar no Portal do Azure. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover.<br/><br/> **Rede**: você precisa de uma rede virtual do Azure com a qual as VMs do Azure se conectarão quando forem criadas no failover. No Portal do Azure, as redes podem ser criadas no modelo clássico do gerenciador de serviço ou no modelo do Resource Manager.
**Servidor de configuração local** |  Você precisa de uma máquina local com Windows Server 2012 R2 que execute o servidor de configuração e outros componentes do Site Recovery.<br/><br/> O servidor pode ser físico ou virtual.<br/><br/> Os componentes do servidor incluem:<br/><br/> **Servidor de configuração**: coordena a comunicação entre o ambiente local e o Azure e gerencia a recuperação e a replicação de dados.<br/><br/> **Servidor de processo**: atua como um gateway de replicação. Ele recebe dados de replicação de computadores de origem protegida, otimiza-os com caching, compactação e criptografia e envia os dados para o armazenamento do Azure. Ele também manipula a instalação por push do Serviço de mobilidade em computadores protegidos e executa a descoberta automática de máquinas virtuais VMware. À medida que a implantação cresce, você pode adicionar outros servidores de processo dedicados separados para lidar com o aumento do volume de tráfego de replicação.<br/><br/> **Servidor de destino mestre**: lida com os dados de replicação durante o failback do Azure.
**Computadores a serem replicados** | Cada Windows/Linux físico que você quiser replicar para o Azure precisará ter o componente de Serviço de mobilidade instalado. Esse serviço captura gravações de dados no computador e as encaminha ao servidor de processo. Esse componente pode ser instalado manualmente ou pode ser enviado por push e instalado automaticamente pelo servidor de processo quando você habilita a replicação em um computador.
**Failback** | Não há suporte para failback de físico para físico. Isso significa que, se você realizar o failover de servidores físicos para o Azure e, depois, quiser realizar o failback, deverá realizar o failback para uma VM VMware. Você não pode realizar o failback para um servidor físico. Você precisará de uma VM do Azure para fazer o failback, e se você não tiver implantado o servidor de configuração como uma VM VMware, será necessário configurar um servidor de destino mestre separado como uma VM VMware. Isso é necessário porque o servidor de destino mestre interage e anexa no armazenamento VMware para restaurar os discos para uma VM VMware.


- [Saiba mais](site-recovery-vmware-to-azure.md#azure-prerequisites) sobre os requisitos de implantação no Portal do Azure.
- [Saiba mais](site-recovery-failback-azure-to-vmware.md) sobre o failback no Portal do Azure.


## <a name="replicate-hyper-v-vms-not-managed-by-vmm-to-azure"></a>Replicar VMs Hyper-V não gerenciadas pelo VMM no Azure

![Site do Hyper-V para Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**Componente** | **Detalhes**
--- | ---
**As tabelas** | **Conta** você precisa de uma conta do Azure.<br/><br/> **Armazenamento**: você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados. Você pode usar uma conta clássica ou uma conta de armazenamento do Resource Manager. A conta deve ser GRS. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover.<br/><br/> **Rede**: você precisa de uma rede virtual do Azure com a qual as VMs do Azure se conectarão quando forem criadas no failover.
**Hosts/VMs Hyper-V** | Um ou mais hosts Hyper-V, executando uma ou mais VMs.<br/><br/> O provedor do Site Recovery e o agente dos Serviços de Recuperação são instalados em cada host durante a implantação.

- [Saiba mais](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) sobre os requisitos de implantação no Portal do Azure.
- [Saiba mais](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) sobre os requisitos de implantação no Portal Clássico.



## <a name="replicate-hyper-v-vms-managed-by-vmm-to-azure"></a>Replicar VMs Hyper-V gerenciadas pelo VMM no Azure


![VMM no Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

**Componente** | **Detalhes**
--- | ---
**As tabelas** | **Conta** você precisa de uma conta do Azure.<br/><br/> **Armazenamento**: você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados. Você pode usar uma conta clássica ou uma conta de armazenamento do Resource Manager. A conta deve ser GRS. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover.<br/><br/> **Rede**: você precisa de uma rede virtual do Azure com a qual as VMs do Azure se conectarão quando forem criadas no failover.
**Servidor VMM** | Você precisa de um ou mais servidores VMM locais, com uma ou mais nuvens privadas. O provedor do Site Recovery é instalado em cada servidor durante a implantação.
**Hosts/VMs Hyper-V** | Um ou mais hosts Hyper-V, executando uma ou mais VMs.<br/><br/> O agente dos Serviços de Recuperação é instalado em cada host durante a implantação.


- [Saiba mais](site-recovery-vmm-to-azure.md#azure-requirements) sobre os requisitos de implantação no Portal do Azure.
- [Saiba mais](site-recovery-vmm-to-azure-classic.md#before-you-start) sobre os requisitos de implantação no Portal Clássico.




## <a name="replicate-vmware-virtual-machines-or-physical-server-to-a-secondary-site"></a>Replicar máquinas virtuais VMware ou servidor físico para um site secundário

![VMware para VMware](./media/site-recovery-components/vmware-to-vmware.png)


**Componente** | **Detalhes**
--- | ---
**As tabelas** | **Conta do Azure**: implante este cenário usando o InMage Scout. Para obtê-lo, você precisará de uma conta do Azure.<br/><br/> Depois de criar um cofre do Site Recovery, baixe o InMage Scout e instale as atualizações mais recentes para configurar a implantação.
**Site primário** | **Servidor de processo**: configure o componente do servidor de processo em seu site primário para lidar com o armazenamento em cache, a compactação e a otimização de dados. Ele também trata da instalação por push do Agente Unificado nas máquinas que você deseja proteger.<br/><br/> **VMware ESX/ESXi e vCenter**: você precisa de um hipervisor VMware EXS/ESXi e, opcionalmente, um servidor do VMware vCenter.<br/><br/> **VMs** | Durante a implantação, o Agente Unificado é instalado nas máquinas que você deseja proteger. O agente atua como um provedor de comunicação entre todos os componentes.
**Site secundário** | **Servidor de configuração**: o servidor de configuração é o primeiro componente que você instala, e é instalado no site secundário a fim de gerenciar, configurar e monitorar sua implantação, usando o site de gerenciamento ou o console do vContinuum. Há apenas um servidor de configuração única em uma implantação e ele deve ser instalado em um computador que esteja executando o Windows Server 2012 R2.<br/><br/> **Servidor vContinuum (site secundário)**: instalado no mesmo local que o servidor de configuração. Ele fornece um console para o gerenciamento e monitoramento de seu ambiente protegido. Em uma instalação padrão, o servidor vContinuum é o primeiro servidor de destino mestre e possui o Agente Unificado instalado.<br/><br/> **Servidor de destino mestre**: o servidor de destino mestre armazena os dados replicados. Ele recebe os dados do servidor de processo, cria uma máquina de réplica no site secundário e mantém os pontos de retenção dos dados. O número de servidores de destino mestre necessário depende do número de máquinas que você está protegendo. Se você quiser realizar o failback no site primário, também precisará de um servidor de destino mestre nesse local.


Para replicar VMs VMware ou servidores físicos em um site secundário, você deve baixar o InMage Scout que está incluído na assinatura do Azure Site Recovery. Ele pode ser baixado do Portal do Azure ou no Portal Clássico do Azure.

Configure os servidores de componente em cada site (configuração, processo, destino mestre) e instale o Agente Unificado nos computadores que você deseja replicar. Após a replicação inicial, os agentes em cada computador enviam as alterações de replicação delta para o servidor de processo. O servidor de processo otimiza os dados e os transfere para o servidor de destino mestre no site secundário. O servidor de configuração gerencia o processo de replicação.


## <a name="replicate-hyper-v-vms-managed-by-vmm-to-a-secondary-site"></a>Replicar em um site secundário: VMs Hyper-V gerenciadas pelo VMM

![Local para o próprio local](./media/site-recovery-components/arch-onprem-onprem.png)

**Componente** | **Detalhes**
--- | ---
**As tabelas** | **Conta** você precisa de uma conta do Azure.
**Servidor VMM** | Recomendamos um servidor VMM no site primário e um no site secundário. Cada servidor precisa de uma ou mais nuvens privadas.<br/><br/> Durante a implantação, você instalará o provedor do Azure Site Recovery no servidor VMM.
**Hosts/VMs Hyper-V** | Um ou mais hosts Hyper-V em execução nas nuvens do VMM no site primário e secundário<br/><br/> Cada host deverá ter uma ou mais VMs para replicar.<br/><br/>. O agente dos Serviços de Recuperação é instalado em cada host durante a implantação.

- [Saiba mais](site-recovery-vmm-to-vmm.md#azure-prerequisites) sobre os requisitos de implantação no Portal do Azure.
- [Saiba mais](site-recovery-vmm-to-vmm-classic.md#before-you-start) sobre os requisitos de implantação no Portal Clássico do Azure.


## <a name="replicate-hyper-v-vms-managed-by-vmm-to-a-secondary-site-using-san-replication"></a>Replicar VMs Hyper-V gerenciadas por VMM em um site secundário usando a replicação SA

![Replicação SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

Você pode replicar VMs Hyper-V gerenciadas em nuvens do VMM para um site secundário usando a replicação de SAN usando o Portal Clássico. No momento, esse cenário não tem suporte no Portal do Azure.

**Componente** | **Detalhes**
--- | ---
**As tabelas** | **Conta** você precisa de uma conta do Azure.
**Servidor VMM** | Recomendamos um servidor VMM no site primário e um no site secundário. Cada servidor precisa de uma ou mais nuvens privadas.<br/><br/> Durante a implantação, você instalará o provedor do Azure Site Recovery no servidor VMM.
**SAN** | Uma matriz SAN com suporte gerenciada pelo servidor VMM primário.<br/><br/> A SAN deve compartilhar uma infraestrutura de rede com outra matriz de SAN no site secundário.
**Hosts/VMs Hyper-V** | Um ou mais hosts Hyper-V em execução nas nuvens do VMM no site primário e secundário<br/><br/> Cada host deverá ter uma ou mais VMs para replicar.<br/><br/>. O agente dos Serviços de Recuperação é instalado em cada host durante a implantação.

Nesse cenário, o provedor do Azure Site Recovery é instalado durante a implantação da Recuperação de Site no servidor VMM. O Provedor coordena e organiza a replicação com o serviço Recuperação de Site pela Internet. Os dados são replicados entre as matrizes de armazenamento primária e secundária usando a replicação síncrona de SAN.

[Saiba mais](site-recovery-vmm-san.md#before-you-start) sobre os requisitos de implantação.

## <a name="hyper-v-protection-lifecycle"></a>Ciclo de vida de proteção do Hyper-V

Este fluxo de trabalho mostra o processo de proteção, replicação e failover em máquinas virtuais Hyper-V.

1. **Habilitar proteção**: configure o cofre do Site Recovery, defina as configurações de replicação para uma nuvem VMM ou um site Hyper-V e habilite a proteção para VMs. Um trabalho chamado **Habilitar Proteção** é iniciado e pode ser monitorado na guia **Trabalhos**. O trabalho verifica se a máquina está em conformidade com os pré-requisitos e, em seguida, invoca o método [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) que configura a replicação no Azure com as configurações definidas por você. O trabalho **Habilitar proteção** também invoca o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) para inicializar uma replicação completa da VM.
2. **Replicação inicial**: é tirado um instantâneo da máquina virtual, e os discos rígidos virtuais são replicados individualmente até que sejam todos copiados no Azure ou no datacenter secundário. O tempo de conclusão necessário depende do tamanho da VM, da largura de banda de rede e do método de replicação inicial. Se houver alterações no disco durante a replicação, o controlador de replicação de Réplica do Hyper-V mostrará essas alterações em logs de replicação do Hyper-V (.hrl) localizados na mesma pasta que os discos. Cada disco tem um arquivo .hrl associado que será enviado ao armazenamento secundário. Observe que o instantâneo e os arquivos de log consomem recursos de disco durante a replicação inicial. Após a conclusão da replicação inicial, o instantâneo da VM é excluído e as alterações de disco delta no log são sincronizadas e mescladas.
3. **Finalizar proteção**: após a conclusão da replicação inicial, o trabalho **Finalizar proteção** definirá outras configurações de rede e pós-replicação para que a máquina virtual fique protegida. Se você estiver replicando no Azure, talvez seja necessário ajustar as configurações para a máquina virtual de modo que ela fique pronta para o failover. Aqui você já pode executar um failover de teste para verificar se tudo está funcionando conforme o esperado.
4. **Replicação**: depois de iniciada a sincronização delta da replicação inicial, de acordo com as configurações de replicação.
    - **Falha de replicação**: se a replicação delta falhar, e uma replicação completa for dispendiosa em termos de largura de banda ou tempo, ocorre a ressincronização. Por exemplo, se os arquivos .hrl atingirem 50% do tamanho do disco, a VM será marcada para ressincronização. A ressincronização minimiza a quantidade de dados enviados calculando as somas de verificação das máquinas virtuais de origem e de destino e enviando apenas o delta. Após a conclusão da ressincronização, a replicação delta será retomada. Por padrão a ressincronização está agendada para execução automática fora do expediente, mas você pode ressincronizar uma máquina virtual manualmente.
    - **Erro de replicação**: se um erro de replicação ocorrer, haverá uma repetição interna. Se for um erro sem recuperação, por exemplo, um erro de autenticação ou de autorização, ou se uma máquina réplica estiver em um estado inválida, nenhuma repetição ocorrerá. Se for um erro recuperável, por exemplo, um erro de rede ou de espaço em disco/memória baixo(a), uma nova tentativa será realizada em intervalos crescentes (a cada 1, 2, 4, 8, 10 e 30 minutos).
4. **Failovers planejados/não planejados**: você pode executar failovers planejados ou não planejados, conforme necessário. Se você executar um failover planejado, as VMs de origem serão desligadas para evitar a perda de dados. Após a criação das VMs de réplica, elas entram em um estado de confirmação pendente. Você precisa confirmá-las para concluir o failover, a menos que esteja replicando com SAN, pois, nesse caso, a confirmação é automática. Assim que o site primário estiver em execução, o failback poderá ocorrer. Se você tiver replicado no Azure, a replicação inversa será automática. Caso contrário, você precisará iniciar uma replicação inversa manualmente.


![fluxo de trabalho](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Próximas etapas

[Preparar para a implantação](site-recovery-best-practices.md)



<!--HONumber=Oct16_HO2-->


