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
	ms.date="02/16/2016"
	ms.author="raynew"/>

# Como funciona o Azure Site Recovery?

Este artigo descreve a arquitetura subjacente da Recuperação de Site e os componentes que realizam o trabalho. Após a leitura deste artigo, você poderá publicar perguntas sobre no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Visão geral

As organizações precisam de uma estratégia de BCDR (continuidade dos negócios e recuperação de desastre) que determine como os aplicativos, as cargas de trabalho e os dados permanecerão em execução e disponíveis durante o tempo de inatividade planejado e não planejado, e como recuperarão as condições normais de trabalho assim que possível. Sua estratégia de BCDR está centrada em soluções que mantêm os dados comerciais seguros e passíveis de recuperação, e as cargas de trabalho continuamente disponíveis, mediante um desastre.

A Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR por meio da coordenação da replicação de servidores físicos e máquinas virtuais locais na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback.

A Recuperação de Site pode ser usada em vários cenários e pode proteger diversas cargas de trabalho.

- **Proteger máquinas virtuais VMware**: você pode proteger máquinas virtuais VMware locais replicando-as para o [Azure](site-recovery-vmware-to-azure-classic.md) ou para um [datacenter secundário](site-recovery-vmware-to-vmware.md).
- **Proteger VMs Hyper-V**: você pode proteger VMs Hyper-V locais em nuvens do VMM replicando-as para o [Azure](site-recovery-vmm-to-azure.md) ou para um [datacenter secundário](site-recovery-vmm-to-vmm.md). Você pode replicar máquinas virtuais Hyper-V que não são gerenciadas pelo VMM para o [Azure](site-recovery-hyper-v-site-to-azure.md).
- **Proteger servidores físicos para o Azure**: você pode proteger máquinas físicas que executam Windows ou Linux replicando-as no [Azure](site-recovery-vmware-to-azure-classic.md) ou em um [datacenter secundário](site-recovery-vmware-to-vmware.md).
- **Migrar VMs**: você pode usar a Recuperação de Site para [migrar VMs IaaS do Azure](site-recovery-migrate-azure-to-azure.md) entre regiões ou para [migrar instâncias do Windows do AWS](site-recovery-migrate-aws-to-azure.md) para VMs IaaS do Azure.

A Recuperação de Site pode replicar a maioria dos aplicativos em execução nas VMs e nos servidores físicos. Você pode obter um resumo completo dos aplicativos com suporte em [Quais cargas de trabalho o Azure Site Recovery pode proteger?](site-recovery-workload.md)


## Replicar máquinas virtuais VMware em locais ou servidores físicos no Azure

Se você quiser proteger VMs VMware ou máquinas físicas com Windows/Linux replicando-as no Azure, estas são as etapas necessárias.

Há duas arquiteturas diferentes disponíveis para esse cenário de replicação:

- **Arquitetura herdada**: essa arquitetura não deve ser usada para novas implantações. 
- **Arquitetura aprimorada**: essa é a solução mais recente e deve ser usada para todas as novas implantações. Você também pode [migrar sua arquitetura herdada](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) para essa nova solução.

Aqui está a arquitetura de implantação avançada

![Avançado](./media/site-recovery-components/arch-enhanced.png)

- **Local**: quando você implanta a arquitetura avançada, não precisa implantar as VMs de infraestrutura no Azure. Além disso, todo o tráfego é criptografado e as comunicações de gerenciamento de replicação são enviadas por HTTPS 443. Aqui está o que será necessário em sua infraestrutura local:

	- **Servidor de gerenciamento**: um único servidor de gerenciamento que executa todos os componentes da Recuperação de Site, que incluem:

		- **Servidor de configuração**: para coordenar a comunicação entre o ambiente local e o Azure e gerenciar processos de recuperação e replicação de dados.
		- **Servidor de processo**: atua como um gateway de replicação. Ele recebe dados de computadores de origem protegida, otimiza-os com caching, compactação e criptografia e envia os dados de replicação para o armazenamento do Azure. Ele também manipula a instalação por push do Serviço de mobilidade em computadores protegidos e executa a descoberta automática de máquinas virtuais VMware. À medida que a implantação cresce, você pode adicionar mais servidores dedicados que são executados como servidores de processo somente para lidar com volumes maiores de tráfego de replicação.
		- **Servidor de destino mestre**: lida com dados de replicação durante o failback do Azure. 

	- **Servidor vCenter e host do VMware ESX/ESXi**: um ou mais servidores host ESX/ESXi nos quais as VMs VMware estão localizadas. Recomendamos que você tenha um servidor vCenter para gerenciar os hosts. Observe que, mesmo que você esteja protegendo servidores físicos, precisará de um ambiente VMware para failback do Azure para seu site local.
	
	- **Computadores protegidos**: cada computador que você desejar replicar para o Azure precisará ter o componente de serviço de Mobilidade instalado. Ele captura gravações de dados no computador e as encaminha ao servidor de processo. Esse componente pode ser instalado manualmente ou pode ser enviado por push e instalado automaticamente pelo servidor de processo quando você habilita a proteção para um computador.

- **Azure**: aqui está o que você precisará ter em sua infraestrutura do Azure:
	- **Conta do Azure**: você precisará de uma conta do Microsoft Azure.
	- **Armazenamento do Azure**: você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados no Azure. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover. 
	- **Rede do Azure**: você precisará de uma rede virtual do Azure com a qual as máquinas virtuais do Azure se conectarão quando ocorrer failover. Você também precisará de uma conexão VPN (ou Rota Expressa do Azure) configurada da rede do Azure para o site local.

	![Avançado](./media/site-recovery-components/arch-enhanced2.png)

Saiba mais sobre os [requisitos de implantação](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) exatos.

### Arquitetura de failback

- O failback do Azure deve ser feito para VMs VMware. Atualmente, você não pode realizar o failback para um servidor físico.
- Para realizar o failback, você precisará de uma conexão VPN (ou Rota Expressa do Azure) da rede do Azure para sua rede local.
- Você precisará de um servidor de processo no Azure para o failback. Você pode excluí-lo após a conclusão do failback.
- Você precisará de um servidor de destino mestre local Um servidor de destino mestre é instalado por padrão no servidor de gerenciamento quando você o configura no local. Porém, para volumes maiores de tráfego, é recomendável configurar um servidor de destino mestre separado no local para o failback.

![Failback avançado](./media/site-recovery-components/enhanced-failback.png)

Saiba mais sobre o [failback](site-recovery-failback-azure-to-vmware-classic.md).

### Arquitetura herdada

A arquitetura herdada requer um servidor de configuração local e um servidor de processo, hosts VMware ESX/ESXi e servidor vCenter e o serviço de mobilidade instalado nos computadores que você deseja proteger. No Azure, você configura VMs do Azure para o servidor de Configuração e o servidor de destino mestre. Você também precisará de uma assinatura do Azure, de uma conta de armazenamento e de uma rede virtual.



## Replicar VMs Hyper-V em nuvens VMM para o Azure

Se suas VMs estiverem em um host Hyper-V gerenciado em uma nuvem do System Center VMM, você precisará do seguinte para replicá-las no Azure.

- Local: 
	- **Servidor VMM**: pelo menos um servidor VMM configurado com pelo menos uma nuvem privada VMM. O servidor deve estar em execução no System Center 2012 R2. O servidor VMM deve ter conectividade com a Internet. Se você quiser garantir que as VMs do Azure estão conectadas a uma rede após o failover, precisará configurar o mapeamento de rede. Para fazer isso, você precisa conectar a VMs de origem a uma rede de VMs VMM. Essa rede deve ser vinculada a uma rede lógica que esteja associada à nuvem.
	- **Servidor Hyper-V**: pelo menos um servidor de host Hyper-V localizado na nuvem VMM. Os hosts Hyper-V devem estar em execução no Windows Server 2012 R2.
	- **Máquinas protegidas**: o servidor host Hyper-V de origem deve ter pelo menos uma VM que você deseja proteger.
	
- Azure:
	- **Conta do Azure**: você precisará de uma conta do Microsoft Azure.
	- **Armazenamento do Azure**: você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados no Azure. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover.
	- **Rede do Azure**: se você quiser garantir que as VMs do Azure estão conectadas a redes após o failover, precisará configurar o mapeamento de rede. Para fazer isso, você precisará de uma rede do Azure configurada.

	![VMM para Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

Nesse cenário, o Provedor do Azure Site Recovery é instalado no servidor VMM durante a implantação da Recuperação de Site. Ele coordena e organiza a replicação com o serviço Recuperação de Site pela Internet. O agente de Serviços de Recuperação do Azure é instalado durante a implantação da Recuperação de Site no servidor host Hyper-V, e os dados são replicados entre ele e o armazenamento do Azure através de HTTPS 443. As comunicações do provedor e do agente são protegidas e criptografadas. Os dados replicados no armazenamento do Azure também são criptografados.

Saiba mais sobre os [requisitos de implantação](site-recovery-vmm-to-azure.md#before-you-start) exatos.

## Como replicar VMs Hyper-V no Azure (sem a VMM)

Se suas VMs não forem gerenciadas por um servidor do System Center VMM, estas serão as etapas necessárias para replicá-las no Azure

- Local: 
	- **Servidor Hyper-V**: pelo menos um servidor de host Hyper-V. Os hosts Hyper-V devem estar em execução no Windows Server 2012 R2.
	- **Máquinas protegidas**: o servidor host Hyper-V de origem deve ter pelo menos uma VM que você deseja proteger.
	
- Azure:
	- **Conta do Azure**: você precisará de uma conta do Microsoft Azure.
	- **Armazenamento do Azure**: você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados no Azure. Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure se adaptam quando ocorre failover.

	![Site do Hyper-V para Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

Nesse cenário, o provedor do Azure Site Recovery e o agente de Serviços de Recuperação do Azure são instalados no servidor VMM durante a implantação da Recuperação de Site. O Provedor coordena e organiza a replicação com o serviço Recuperação de Site pela Internet. O agente manipula dados de replicação de dados através de HTTPS 443. As comunicações do provedor e do agente são protegidas e criptografadas. Os dados replicados no armazenamento do Azure também são criptografados.

Saiba mais sobre os [requisitos de implantação](site-recovery-hyper-v-site-to-azure.md#before-you-start) exatos

## Replicar VMs do Hyper-V em um datacenter secundário

Se você quiser proteger suas VMs do Hyper-V replicando-as em um datacenter secundário, estas são as etapas necessárias. Perceba que você só poderá fazer isso caso seu servidor de host Hyper-V seja gerenciado em uma nuvem do System Center VMM.

- **Local**: 
	- **Servidor VMM**: recomendamos um servidor VMM no site primário e outro no site secundário, cada um contendo pelo menos uma nuvem privada VMM. O servidor deve estar executando pelo menos o System Center 2012 SP1 com as últimas atualizações e deve estar conectado à Internet. As nuvens devem ter o perfil de recursos do Hyper-V definido.
	- **Servidor Hyper-V**: servidores host Hyper-V localizados nas nuvens VMM primárias e secundárias. Os servidores host devem estar executando pelo menos o Windows Server 2012 com as últimas atualizações instaladas e devem estar conectados à Internet.
	- **Máquinas protegidas**: o servidor host Hyper-V de origem deve ter pelo menos uma VM que você deseja proteger.
	
- **Azure**: você precisará de uma assinatura do Azure.

	![Local para o próprio local](./media/site-recovery-components/arch-onprem-onprem.png)

Nesse cenário, o provedor do Azure Site Recovery é instalado durante a implantação da Recuperação de Site no servidor VMM. Ele coordena e organiza a replicação com o serviço Recuperação de Site pela Internet. Os dados são replicados entre os servidores de host Hyper-V primários e secundários pela LAN ou VPN usando a autenticação Kerberos ou de certificados. As comunicações do provedor e dos servidores de host Hyper-V são protegidas e criptografadas.

Saiba mais sobre os [requisitos de implantação](site-recovery-vmm-to-vmm.md#before-you-start) exatos



## Replicar VMs Hyper-V em um datacenter secundário com replicação SAN

Se suas VMs estiverem em um host Hyper-V gerenciado em uma nuvem do System Center VMM e você estiver usando o armazenamento SAN, você precisará do seguinte para replicá-las entre dois datacenters.

- **Local**: 
	- **Matriz SAN**: uma [matriz SAN com suporte](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) gerenciada pelo servidor VMM primário. A SAN compartilha uma infraestrutura de rede com outra matriz de SAN no site secundário.
	- **Servidor VMM**: recomendamos um servidor VMM no site primário e outro no site secundário, cada um contendo pelo menos uma nuvem privada VMM. O servidor deve estar executando pelo menos o System Center 2012 SP1 com as últimas atualizações e deve estar conectado à Internet. As nuvens devem ter o perfil de recursos do Hyper-V definido.
	- **Servidor Hyper-V**: servidores host Hyper-V localizados nas nuvens VMM primárias e secundárias. Os servidores host devem estar executando pelo menos o Windows Server 2012 com as últimas atualizações instaladas e devem estar conectados à Internet.
	- **Máquinas protegidas**: o servidor host Hyper-V de origem deve ter pelo menos uma VM que você deseja proteger.
	
- **Azure**: você precisará de uma assinatura do Azure.

	![Replicação SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

Nesse cenário, o provedor do Azure Site Recovery é instalado durante a implantação da Recuperação de Site no servidor VMM. Ele coordena e organiza a replicação com o serviço Recuperação de Site pela Internet. Os dados são replicados entre as matrizes de armazenamento primária e secundária usando a replicação síncrona de SAN.

Saiba mais sobre os [requisitos de implantação](site-recovery-vmm-san.md#before-you-start) exatos


## Replicar máquinas virtuais VMware ou servidores físicos para um local secundário

Se você quiser proteger as VMs VMware ou máquinas físicas com Windows/Linux replicando-as entre dois datacenters locais, estas são as etapas necessárias.

- **Primário local**: 
	- **Servidor de processo**: configure o componente do servidor de processo em seu site primário para lidar com o armazenamento em cache, a compactação e a otimização de dados. Ele também trata da instalação por push do Agente Unificado nas máquinas que você deseja proteger.
	- **Servidor VMware ESX/ESXi e vCenter**: se estiver protegendo VMs VMware, você precisará de um hipervisor VMware EXS/ESXi ou de um servidor VMware vCenter gerenciando vários hipervisores
	- Máquinas protegidas: as VMs VMware ou os servidores físicos Windows/Linux que você deseja proteger precisarão ter o Agente Unificado instalado. O Agente Unificado também é instalado nas máquinas que atuam como servidor de destino mestre. Ele atua como um provedor de comunicação entre todos os componentes do InMage.
	
- **Secundário local**:
	- **Servidor de configuração**: o servidor de configuração é o primeiro componente que você instala, e é instalado no site secundário a fim de gerenciar, configurar e monitorar sua implantação, usando o site de gerenciamento ou o console do vContinuum. O servidor de configuração também inclui o mecanismo de envio por push para a implantação remota do Agente Unificado. Há apenas um servidor de configuração única em uma implantação e ele deve ser instalado em um computador que esteja executando o Windows Server 2012 R2.
	- **Servidor vContinuum**: sua instalação é feita no mesmo local (site secundário) que o servidor de configuração. Ele fornece um console para o gerenciamento e monitoramento de seu ambiente protegido. Em uma instalação padrão, o servidor vContinuum é o primeiro servidor de destino mestre e possui o Agente Unificado instalado.
	- **Servidor de destino mestre**: o servidor de destino mestre armazena os dados replicados. Ele recebe os dados do servidor de processo, cria uma máquina de réplica no site secundário e mantém os pontos de retenção dos dados. O número de servidores de destino mestre necessário depende do número de máquinas que você está protegendo. Se você quiser realizar o failback no site primário, também precisará de um servidor de destino mestre nesse local. 

- **Azure**: você precisará de uma assinatura do Azure. Baixe o InMage Scout para configurar a implantação após a criação do cofre de Recuperação de Site. Instale também a atualização mais recente para todos os servidores de componente InMage.


	![VMware para VMware](./media/site-recovery-components/vmware-to-vmware.png)

Nesse cenário, as alterações de replicação delta são enviadas do Agente Unificado em execução na máquina protegida para o servidor de processo. O servidor de processo otimiza esses dados e os transfere para o servidor de destino mestre no site secundário. O servidor de configuração gerencia o processo de replicação.


## Ciclo de vida de proteção do Hyper-V

Este fluxo de trabalho mostra o processo de proteção, replicação e failover em máquinas virtuais Hyper-V.

1. **Habilitar proteção**: configure o cofre do Site Recovery, defina as configurações de replicação para uma nuvem VMM ou um site Hyper-V e habilite a proteção para VMs. Um trabalho chamado **Habilitar proteção** é iniciado e pode ser monitorado na guia **Trabalhos**. O trabalho verifica se a máquina está em conformidade com os pré-requisitos e, em seguida, invoca o método [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) que configura a replicação no Azure com as configurações definidas por você. O trabalho **Habilitar proteção** também invoca o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx)para inicializar uma replicação completa da VM.
2. **Replicação inicial**: é tirado um instantâneo da máquina virtual, e os discos rígidos virtuais são replicados individualmente até que sejam todos copiados no Azure ou no datacenter secundário. O tempo de conclusão depende do tamanho e da largura de banda de rede e do método de replicação inicial escolhido. Se houver alterações no disco durante a replicação, o Controlador de Replicação de Réplica do Hyper-V mostrará essas alterações em Logs de Replicação do Hyper-V (.hrl) localizados na mesma pasta que os discos. Cada disco tem um arquivo .hrl associado que será enviado ao armazenamento secundário. Observe que o instantâneo e os arquivos de log consomem recursos de disco durante a replicação inicial. Após a conclusão da replicação inicial, o instantâneo da VM é excluído e as alterações de disco delta no log são sincronizadas e mescladas.
3. **Finalizar proteção**: após a conclusão da replicação inicial, o trabalho **Finalizar proteção** define outras configurações de rede e pós-replicação, e a máquina virtual fica protegida. Se você estiver replicando no Azure, talvez seja necessário ajustar as configurações para a máquina virtual de modo que ela fique pronta para o failover. Neste ponto, você pode executar um failover de teste para verificar se tudo está funcionando conforme o esperado.
4. **Replicação**: após a replicação inicial, ocorre a sincronização delta, de acordo com as configurações e o método de replicação. 
	- **Falha de replicação**: se a replicação delta falhar, e uma replicação completa for dispendiosa em termos de largura de banda ou tempo, ocorre a ressincronização. Por exemplo, se os arquivos .hrl atingirem 50% do tamanho do disco, a máquina virtual será marcada para ressincronização. A ressincronização minimiza a quantidade de dados enviados calculando as somas de verificação das máquinas virtuais de origem e de destino e enviando apenas o delta. Após a conclusão da ressincronização, a replicação delta deverá ser retomada. Por padrão a ressincronização está agendada para execução automática fora do expediente, mas você pode ressincronizar uma máquina virtual manualmente.
	- **Erro de replicação**: se um erro de replicação ocorrer, haverá uma repetição interna. Se for um erro sem recuperação, por exemplo, um erro de autenticação ou de autorização, ou se uma máquina réplica estiver em um estado inválida, nenhuma repetição ocorrerá. Se for um erro recuperável, por exemplo, um erro de rede ou de espaço em disco/memória baixo(a), uma nova tentativa será realizada em intervalos crescentes (a cada 1, 2, 4, 8, 10 e 30 minutos).
4. **Failovers planejados/não planejados**: execute failovers planejados/não planejados quando surgir a necessidade. Se você executar um failover planejado, as VMs de origem serão desligadas para assegurar que nenhum dados seja perdido. Após a criação das VMs de réplica, elas ficam em um estado de confirmação pendente. Você precisa confirmá-las para concluir o failover, a menos que você esteja replicando com SAN, pois, nesse caso, a confirmação é automática. Assim que o site primário estiver em execução, o failback poderá ocorrer. Se você tiver replicado no Azure, a replicação inversa será automática. Caso contrário, você precisará iniciar uma replicação inversa.
 

![fluxo de trabalho](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## Próximas etapas

[Preparar-se para a implantação](site-recovery-best-practices.md).

<!---HONumber=AcomDC_0218_2016-->