<properties
	pageTitle="Como funciona a Recuperação de Site? | Microsoft Azure"
	description="Este artigo fornece uma visão geral da arquitetura de Recuperação de Site"
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
	ms.topic="get-started-article"
	ms.date="03/27/2016"
	ms.author="raynew"/>

# Como funciona o Azure Site Recovery?

Leia este artigo para compreender a arquitetura subjacente do serviço Azure Site Recovery e os componentes que permitem o seu funcionamento.

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Visão geral

As organizações precisam de uma estratégia de BCDR (continuidade dos negócios e recuperação de desastre) que determine como os aplicativos, as cargas de trabalho e os dados permanecerão disponíveis durante o tempo de inatividade planejado e não planejado, e como recuperarão as condições normais de trabalho assim que possível.

A Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR por meio da coordenação da replicação de servidores físicos e máquinas virtuais locais na nuvem (Azure) ou em um site secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback.

A Recuperação de Site pode ser implantada para orquestrar a replicação em vários cenários:

- **Replicar máquinas virtuais VMware**: você pode replicar máquinas virtuais VMware locais no [Azure](site-recovery-vmware-to-azure-classic.md) ou em um [datacenter secundário](site-recovery-vmware-to-vmware.md).
- **Replicar computadores físicos**: você pode replicar computadores físicos executando Windows ou Linux no [Azure](site-recovery-vmware-to-azure-classic.md) ou em um [datacenter secundário](site-recovery-vmware-to-vmware.md).
- **Replicar VMs Hyper-V gerenciadas em nuvens do System Center VMM**: você pode replicar máquinas virtuais Hyper-V locais nas nuvens do VMM no [Azure](site-recovery-vmm-to-azure.md) ou em um [datacenter secundário](site-recovery-vmm-to-vmm.md). 
- **Replicar máquinas virtuais Hyper-V (sem VMM)**: você pode replicar máquinas virtuais Hyper-V que não são gerenciadas pelo VMM no [Azure](site-recovery-hyper-v-site-to-azure.md).
- **Migrar VMs**: você pode usar a Recuperação de Site para [migrar VMs IaaS do Azure](site-recovery-migrate-azure-to-azure.md) entre regiões ou para [migrar instâncias do Windows do AWS](site-recovery-migrate-aws-to-azure.md) para VMs IaaS do Azure. No momento, somente há suporte para migração, o que significa que essas VMs podem fazer failover, mas não failback.

A Recuperação de Site pode replicar a maioria dos aplicativos em execução nas VMs e nos servidores físicos. Você pode obter um resumo completo dos aplicativos com suporte em [Quais cargas de trabalho o Azure Site Recovery pode proteger?](site-recovery-workload.md)

## Replicar máquinas virtuais VMware/servidores físicos locais no Azure

Há duas arquiteturas diferentes disponíveis para replicar máquinas virtuais VMware ou servidores físicos do Windows/Linux no Azure:

- [Arquitetura herdada](site-recovery-vmware-to-azure-classic-legacy.md): essa arquitetura não deve ser usada para novas implantações. 
- [Arquitetura aprimorada](site-recovery-vmware-to-azure-classic.md): essa é a arquitetura mais recente e deve ser usada para todas as novas implantações. Se você já implantou esse cenário usando a arquitetura herdada, [saber mais sobre migração](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) para a implantação aprimorada.

Na implantação aprimorada, você precisará configurar um servidor de gerenciamento local com todos os componentes de Recuperação de Site. Para cada computador que você deseja proteger, envie o Serviço de mobilidade por push automaticamente (ou instale-o manualmente). Após a replicação inicial, o Serviço de mobilidade no computador envia dados de replicação delta para o servidor de processo, que os otimiza antes de enviá-los para o armazenamento do Azure.

![Avançado](./media/site-recovery-components/arch-enhanced.png) ![Avançado](./media/site-recovery-components/arch-enhanced2.png)

### Configuração local
Eis o que você precisa ter no local:

- **Servidor de gerenciamento**: você precisará de um computador Windows Server 2012 R2 para atuar como o servidor de gerenciamento. No servidor, você instalará todos esses componentes de Recuperação de Site com um único arquivo de instalação:

	- **Componente de servidor de configuração**: coordena a comunicação entre o ambiente local e o Azure e gerencia a recuperação e a replicação de dados.
	- **Componente de servidor de processo**: atua como um gateway de replicação. Ele recebe dados de replicação de computadores de origem protegida, otimiza-os com caching, compactação e criptografia e envia os dados para o armazenamento do Azure. Ele também manipula a instalação por push do Serviço de mobilidade em computadores protegidos e executa a descoberta automática de máquinas virtuais VMware. À medida que a implantação cresce, você pode adicionar outros servidores de processo dedicados separados para lidar com o aumento do volume de tráfego de replicação.
	- **Componente de servidor de destino mestre**: lida com dados de replicação durante o failback do Azure. 
- **Hosts VMware ESX/ESXi e servidor vCenter**: você precisará de um ou mais servidores host ESX/ESXi executando VMs VMware. Recomendamos que você tenha um servidor vCenter para gerenciar os hosts. **Observação:** **mesmo se você estiver replicando servidores físicos, terá que fazer failback para o VMware**. Quando você replica um servidor físico, ele é executado como uma VM do Azure quando há o failover para o Azure. Ele falhará para o local como uma VM do VMware. 
	
- **VMs/servidores físicos**: cada computador que você desejar replicar para o Azure precisará ter o componente de Serviço de mobilidade instalado. Esse serviço captura gravações de dados no computador e as encaminha ao servidor de processo. Esse componente pode ser instalado manualmente ou pode ser enviado por push e instalado automaticamente pelo servidor de processo quando você habilita a replicação em um computador.

### As tabelas

Eis o que você precisa na infraestrutura do Azure: - **Conta do Azure**: você precisará de uma conta do Microsoft Azure. - **Armazenamento do Azure**: você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados. Em caso de failover, os dados replicados são armazenados no armazenamento do Azure e as máquinas virtuais do Azure são criadas. - **Rede do Azure**: você precisará de uma rede virtual do Azure à qual as VMs do Azure se conectarão quando ocorrer failover.
	
	
### Failback

O failback para o local é sempre para VMs VMware, mesmo se o failover é em servidor físico. Você precisará de:

- **Servidor de processo temporário no Azure**: se você deseja fazer failback do Azure após o failover, precisa criar uma VM do Azure configurada como um servidor de processo para lidar com a replicação do Azure. Você pode excluir a VM após a conclusão do failback.
- **Conexão de VPN**: em caso de failback, você precisará de uma conexão de VPN (ou Rota Expressa do Azure) configurada da rede do Azure para o site local.
- **Servidor de destino mestre separado local**: o servidor de destino mestre local lida com o failback. O servidor de destino mestre está instalado por padrão no servidor de gerenciamento, mas se você estiver fazendo failback de volumes de tráfego maiores, deverá configurar um servidor de destino mestre separado local com essa finalidade. 

![Failback avançado](./media/site-recovery-components/enhanced-failback.png)

[Saiba mais](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sobre os requisitos de implantação exatos [Saiba mais](site-recovery-failback-azure-to-vmware-classic.md) sobre failback para a implantação aprimorada.




## Replicar VMs Hyper-V em nuvens VMM para o Azure

Para implantar esse cenário durante a Recuperação de Site, você deverá instalar o provedor do Azure Site Recovery no servidor VMM. O Provedor coordena e organiza a replicação com o serviço Recuperação de Site pela Internet. O agente de Serviços de Recuperação do Azure é instalado durante a implantação da Recuperação de Site no servidor host Hyper-V, e os dados são replicados entre ele e o armazenamento do Azure através de HTTPS 443. As comunicações do provedor e do agente são protegidas e criptografadas. Os dados replicados no armazenamento do Azure também são criptografados.

- Local: 
	- **Servidor VMM**: pelo menos um servidor VMM configurado com pelo menos uma nuvem privada VMM. O servidor deve estar em execução no System Center 2012 R2 e deve ter conexão com a Internet. Se você quiser garantir que as VMs do Azure estão conectadas a uma rede após o failover, precisará configurar o mapeamento de rede. Para fazer isso, suas VMs de origem deve estar conectadas a uma rede de VMs do VMM. Essa rede de VM deve ser vinculada a uma rede lógica associada à nuvem.
	- **Servidor Hyper-V**: pelo menos um servidor de host Hyper-V localizado na nuvem VMM. Os hosts Hyper-V devem estar em execução no Windows Server 2012 R2.
	- **Máquinas protegidas**: o servidor host Hyper-V de origem deve ter pelo menos uma VM que você deseja proteger.
	
- Azure:
	- **Conta do Azure**: você precisará de uma conta do Microsoft Azure.
	- **Armazenamento do Azure**: você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover.
	- **Rede do Azure**: se você quiser configurar o mapeamento de rede para que as VMs do Azure sejam conectadas a redes após o failover, precisará configurar o mapeamento de rede.

	![VMM para Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

Saiba mais sobre os [requisitos de implantação](site-recovery-vmm-to-azure.md#before-you-start) exatos

## Replicar máquinas virtuais VMware ou servidores físicos para um local secundário

Para replicar VMs VMware ou servidores físicos Windows/Linux em um site secundário, você deve baixar o InMage Scout que está incluído na assinatura do Azure Site Recovery. Configure os servidores de componente em cada site (configuração, processo, destino mestre) e instale o Agente Unificado nos computadores que você deseja replicar. Após a replicação inicial, os agentes em cada computador enviam as alterações de replicação delta para o servidor de processo. O servidor de processo otimiza os dados e os transfere para o servidor de destino mestre no site secundário. O servidor de configuração gerencia o processo de replicação.

![VMware para VMware](./media/site-recovery-components/vmware-to-vmware.png)

### Site primário local

- **Servidor de processo**: configure o componente do servidor de processo em seu site primário para lidar com o armazenamento em cache, a compactação e a otimização de dados. Ele também trata da instalação por push do Agente Unificado nas máquinas que você deseja proteger. 
- **Servidor VMware ESX/ESXi e vCenter**: se estiver protegendo VMs VMware, você precisará de um hipervisor VMware EXS/ESXi e, como opção, de um servidor VMware vCenter para gerenciar hipervisores
- **VMs/servidores físicos**: as VMs VMware ou os servidores físicos Windows/Linux que você deseja proteger precisarão ter o Agente Unificado instalado. O Agente Unificado também é instalado nas máquinas que atuam como servidor de destino mestre. O agente atua como um provedor de comunicação entre todos os componentes. 
	
### Site secundário local
 
- **Servidor de configuração**: o servidor de configuração é o primeiro componente que você instala, e é instalado no site secundário a fim de gerenciar, configurar e monitorar sua implantação, usando o site de gerenciamento ou o console do vContinuum. Há apenas um servidor de configuração única em uma implantação e ele deve ser instalado em um computador que esteja executando o Windows Server 2012 R2.
- **Servidor vContinuum**: instalado no mesmo local (site secundário) que o servidor de configuração. Ele fornece um console para o gerenciamento e monitoramento de seu ambiente protegido. Em uma instalação padrão, o servidor vContinuum é o primeiro servidor de destino mestre e possui o Agente Unificado instalado.
- **Servidor de destino mestre**: o servidor de destino mestre armazena os dados replicados. Ele recebe os dados do servidor de processo, cria uma máquina de réplica no site secundário e mantém os pontos de retenção dos dados. O número de servidores de destino mestre necessário depende do número de máquinas que você está protegendo. Se você quiser realizar o failback no site primário, também precisará de um servidor de destino mestre nesse local. 

### As tabelas

Implante este cenário usando o InMage Scout. Para obtê-lo, você precisará de uma assinatura do Azure. Depois de criar um cofre de Recuperação de Site, baixe o InMage Scout e instale as atualizações mais recentes para configurar a implantação.


## Como replicar VMs Hyper-V no Azure (sem a VMM)

Para replicar máquinas virtuais Hyper-V que não são gerenciadas em nuvens do VMM no Azure, instale o provedor do Azure Site Recovery e o agente de Serviços de Recuperação do Azure no host Hyper-V durante a implantação de Recuperação de Site. O Provedor coordena e organiza a replicação com o serviço Recuperação de Site pela Internet. O agente manipula dados de replicação de dados através de HTTPS 443. As comunicações do provedor e do agente são protegidas e criptografadas. Os dados replicados no armazenamento do Azure também são criptografados.

![Site do Hyper-V para Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

### Configuração local

- **Servidor Hyper-V**: pelo menos um servidor de host Hyper-V. Os hosts Hyper-V devem estar em execução no Windows Server 2012 R2.
- **Máquinas protegidas**: o servidor host Hyper-V de origem deve ter pelo menos uma VM que você deseja proteger.
	
### As tabelas

- **Conta do Azure**: você precisará de uma conta do Microsoft Azure.
- **Armazenamento do Azure**: você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover.

[Saiba mais](site-recovery-hyper-v-site-to-azure.md#before-you-start) sobre os requisitos de implantação.


## Replicar VMs Hyper-V em nuvens VMM para o Azure

Para implantar esse cenário, instale o provedor do Azure Site Recovery no servidor VMM e o agente dos Serviços de Recuperação do Azure no host Hyper-V durante a implantação da Recuperação de Site. O Provedor coordena e organiza a replicação com o serviço Recuperação de Site pela Internet. O agente manipula dados de replicação de dados através de HTTPS 443. As comunicações do provedor e do agente são protegidas e criptografadas. Os dados replicados no armazenamento do Azure (em repouso) também são criptografados.

![VMM para Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

### Configuração local

- **Servidor VMM**: pelo menos um servidor VMM configurado com pelo menos uma nuvem privada VMM. O servidor deve estar em execução no System Center 2012 R2 e deve ter conexão com a Internet. Se você quiser garantir que as VMs do Azure estão conectadas a uma rede após o failover, precisará configurar o mapeamento de rede. Para fazer isso, você precisa conectar a VMs de origem a uma rede de VMs VMM. Essa rede deve ser vinculada a uma rede lógica que esteja associada à nuvem.
- **Servidor Hyper-V**: pelo menos um servidor de host Hyper-V localizado na nuvem VMM. Os hosts Hyper-V devem estar em execução no Windows Server 2012 R2.
- **Máquinas protegidas**: o servidor host Hyper-V de origem deve ter pelo menos uma VM que você deseja proteger.
	
### As tabelas

- **Conta do Azure**: você precisará de uma conta do Microsoft Azure.
- **Armazenamento do Azure**: você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover.
- **Rede do Azure**: se você quiser garantir que as VMs do Azure estão conectadas a redes após o failover, precisará configurar o mapeamento de rede. Para fazer isso, você precisará de uma rede do Azure.

[Saiba mais](site-recovery-vmm-to-azure.md#before-you-start) sobre os requisitos de implantação.

## Replicar VMs do Hyper-V em um datacenter secundário

Para implantar esse cenário durante a Recuperação de Site, você deverá instalar o provedor do Azure Site Recovery no servidor VMM. O Provedor coordena e organiza a replicação com o serviço Recuperação de Site pela Internet. Os dados são replicados entre os servidores de host Hyper-V primários e secundários pela LAN ou VPN usando a autenticação Kerberos ou de certificados. As comunicações do provedor e dos servidores de host Hyper-V são protegidas e criptografadas.

![Local para o próprio local](./media/site-recovery-components/arch-onprem-onprem.png)

### Configuração local

- **Servidor VMM**: recomendamos um servidor VMM no site primário e outro no site secundário, cada um contendo pelo menos uma nuvem privada do VMM. O servidor deve executar pelo menos o System Center 2012 SP1 com as atualizações mais recentes e estar conectado à Internet. As nuvens devem ter o perfil de recursos do Hyper-V definido.
- **Servidor Hyper-V**: servidores host Hyper-V devem estar localizados nas nuvens VMM primárias e secundárias. Os servidores host devem estar executando pelo menos o Windows Server 2012 com as últimas atualizações instaladas e devem estar conectados à Internet.
- **Máquinas protegidas**: o servidor host Hyper-V de origem deve ter pelo menos uma VM que você deseja proteger.
	
### As tabelas

Você precisará de uma assinatura do Azure.

[Saiba mais](site-recovery-vmm-to-vmm.md#before-you-start) sobre os requisitos de implantação.


## Replicar VMs Hyper-V em um datacenter secundário com replicação SAN

Nesse cenário, o provedor do Azure Site Recovery é instalado durante a implantação da Recuperação de Site no servidor VMM. O Provedor coordena e organiza a replicação com o serviço Recuperação de Site pela Internet. Os dados são replicados entre as matrizes de armazenamento primária e secundária usando a replicação síncrona de SAN.

![Replicação SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

### Configuração local

- **Matriz SAN**: uma [matriz SAN com suporte](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) gerenciada pelo servidor VMM primário. A SAN compartilha uma infraestrutura de rede com outra matriz de SAN no site secundário.
- **Servidor VMM**: recomendamos um servidor VMM no site primário e outro no site secundário, cada um contendo pelo menos uma nuvem privada do VMM. O servidor deve executar pelo menos o System Center 2012 SP1 com as atualizações mais recentes e estar conectado à Internet. As nuvens devem ter o perfil de recursos do Hyper-V definido.
- **Servidor Hyper-V**: servidores host Hyper-V localizados nas nuvens VMM primárias e secundárias. Os servidores host devem estar executando pelo menos o Windows Server 2012 com as últimas atualizações instaladas e devem estar conectados à Internet.
- **Máquinas protegidas**: o servidor host Hyper-V de origem deve ter pelo menos uma VM que você deseja proteger.
	
### As tabelas

Você precisará de uma assinatura do Azure.

[Saiba mais](site-recovery-vmm-san.md#before-you-start) sobre os requisitos de implantação.


## Ciclo de vida de proteção do Hyper-V

Este fluxo de trabalho mostra o processo de proteção, replicação e failover em máquinas virtuais Hyper-V.

1. **Habilitar proteção**: configure o cofre do Site Recovery, defina as configurações de replicação para uma nuvem VMM ou um site Hyper-V e habilite a proteção para VMs. Um trabalho chamado **Habilitar proteção** é iniciado e pode ser monitorado na guia **Trabalhos**. O trabalho verifica se a máquina está em conformidade com os pré-requisitos e, em seguida, invoca o método [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) que configura a replicação no Azure com as configurações definidas por você. O trabalho **Habilitar proteção** também invoca o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx)para inicializar uma replicação completa da VM.
2. **Replicação inicial**: é tirado um instantâneo da máquina virtual, e os discos rígidos virtuais são replicados individualmente até que sejam todos copiados no Azure ou no datacenter secundário. O tempo de conclusão necessário depende do tamanho da VM, da largura de banda de rede e do método de replicação inicial. Se houver alterações no disco durante a replicação, o Controlador de Replicação de Réplica do Hyper-V mostrará essas alterações em Logs de Replicação do Hyper-V (.hrl) localizados na mesma pasta que os discos. Cada disco tem um arquivo .hrl associado que será enviado ao armazenamento secundário. Observe que o instantâneo e os arquivos de log consomem recursos de disco durante a replicação inicial. Após a conclusão da replicação inicial, o instantâneo da VM é excluído e as alterações de disco delta no log são sincronizadas e mescladas.
3. **Finalizar proteção**: após a conclusão da replicação inicial, o trabalho **Finalizar proteção** define outras configurações de rede e pós-replicação para que a máquina virtual fique protegida. Se você estiver replicando no Azure, talvez seja necessário ajustar as configurações para a máquina virtual de modo que ela fique pronta para o failover. Aqui você já pode executar um failover de teste para verificar se tudo está funcionando conforme o esperado.
4. **Replicação**: depois de iniciada a sincronização delta da replicação inicial, de acordo com as configurações de replicação. 
	- **Falha de replicação**: se a replicação delta falhar, e uma replicação completa for dispendiosa em termos de largura de banda ou tempo, ocorre a ressincronização. Por exemplo, se os arquivos .hrl atingirem 50% do tamanho do disco, a VM será marcada para ressincronização. A ressincronização minimiza a quantidade de dados enviados calculando as somas de verificação das máquinas virtuais de origem e de destino e enviando apenas o delta. Após a conclusão da ressincronização, a replicação delta será retomada. Por padrão a ressincronização está agendada para execução automática fora do expediente, mas você pode ressincronizar uma máquina virtual manualmente.
	- **Erro de replicação**: se um erro de replicação ocorrer, haverá uma repetição interna. Se for um erro sem recuperação, por exemplo, um erro de autenticação ou de autorização, ou se uma máquina réplica estiver em um estado inválida, nenhuma repetição ocorrerá. Se for um erro recuperável, por exemplo, um erro de rede ou de espaço em disco/memória baixo(a), uma nova tentativa será realizada em intervalos crescentes (a cada 1, 2, 4, 8, 10 e 30 minutos).
4. **Failovers planejados/não planejados**: você pode executar failovers planejados ou não planejados, conforme necessário. Se você executar um failover planejado, as VMs de origem serão desligadas para evitar a perda de dados. Após a criação das VMs de réplica, elas entram em um estado de confirmação pendente. Você precisa confirmá-las para concluir o failover, a menos que esteja replicando com SAN, pois, nesse caso, a confirmação é automática. Assim que o site primário estiver em execução, o failback poderá ocorrer. Se você tiver replicado no Azure, a replicação inversa será automática. Caso contrário, você precisará iniciar uma replicação inversa manualmente.
 

![fluxo de trabalho](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## Próximas etapas

[Preparar para a implantação](site-recovery-best-practices.md)

<!---HONumber=AcomDC_0330_2016-->