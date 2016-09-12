<properties
	pageTitle="Replicar as máquinas virtuais VMware e os servidores físicos no Azure com o Azure Site Recovery no portal do Azure | Microsoft Azure"
	description="Descreve como implantar o Azure Site Recovery para coordenar a replicação, o failover e a recuperação de máquinas virtuais VMware e servidores físicos Windows/Linux locais no Azure usando o portal do Azure."
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
	ms.date="08/12/2016"
	ms.author="raynew"/>

# Replicar máquinas virtuais VMware e computadores físicos para o Azure com o Azure Site Recovery usando o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmware-to-azure.md)
- [Clássico do Azure](site-recovery-vmware-to-azure-classic.md)
- [Clássico do Azure (herdado)](site-recovery-vmware-to-azure-classic-legacy.md)

Bem-vindo ao Azure Site Recovery! Use este artigo se você quiser replicar máquinas virtuais VMware locais ou servidores físicos Windows/Linux para o Azure, usando o Azure Site Recovery no portal do Azure.

> [AZURE.NOTE] O Azure tem dois [modelos de implantação](../resource-manager-deployment-model.md) diferentes para criar e trabalhar com recursos: ARM (Azure Resource Manager) e clássico. O Azure também tem dois portais – o portal clássico do Azure, que dá suporte ao modelo de implantação clássica, e o portal do Azure, com suporte para ambos os modelos de implantação.

O Site Recovery no portal do Azure fornece diversos recursos novos:

- Os serviços de Backup do Azure e Azure Site Recovery são combinados em um único cofre de Serviços de Recuperação para que você possa configurar e gerenciar a continuidade dos negócios e a recuperação de desastre (BCDR) em um único local. No painel unificado, você pode monitorar e gerenciar operações em seus sites locais e de nuvem pública do Azure.
- Agora, os usuários com assinaturas do Azure provisionadas pelo programa CSP (Provedor de Soluções na Nuvem) podem gerenciar as operações de Recuperação de Site no portal do Azure.
- A Recuperação de Site no portal do Azure pode replicar máquinas para contas de armazenamento do ARM. No failover, a Recuperação de Site cria VMs baseadas no ARM no Azure.
- A Recuperação de Site continua a dar suporte à replicação para as contas de armazenamento clássico. No failover, a Recuperação de Site cria máquinas virtuais usando o modelo clássico.

Depois de ler este artigo, publique quaisquer comentários na parte inferior dos comentários do Disqus. Faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Visão geral

As organizações precisam de uma estratégia de BCDR que determine como os aplicativos, as cargas de trabalho e os dados permanecerão em execução e disponíveis durante o tempo de inatividade planejado e não planejado, e como recuperarão as condições normais de trabalho assim que possível. Sua estratégia de BCDR devem manter os dados comerciais seguros e passíveis de recuperação e garantir que as cargas de trabalho permaneçam continuamente disponíveis mediante um desastre.

A Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR por meio da coordenação da replicação de servidores físicos e máquinas virtuais locais na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. Saiba mais em [O que é o Azure Site Recovery?](site-recovery-overview.md)

Este artigo fornece todas as informações de que você precisa para replicar VMs VMware locais e servidores físicos Windows/Linux para o Azure. Ele inclui uma visão de geral da arquitetura, das informações de planejamento e das etapas de implantação para configurar o Azure, os servidores locais, as configurações de replicação e o planejamento de capacidade. Depois de configurar a infraestrutura, você poderá habilitar a replicação nos computadores que deseja proteger e verificar se o failover funciona.

## Vantagens de negócios

- A Recuperação de Site oferece proteção externa para cargas de trabalho e aplicativos executados em VMs VMware e servidores físicos.
- O portal de Recuperação de Serviços oferece um único local para configurar, gerenciar e monitorar a replicação, o failover e a recuperação.
- A Recuperação de Site pode descobrir automaticamente VMs VMware adicionado aos hosts vSphere.
- Você pode executar facilmente failovers de sua infraestrutura local para o Azure e failback (restauração) do Azure para servidores de VM VMware no site local.
- Você pode habilitar várias VMs e criar grupos de replicação para que as cargas de trabalho do aplicativo em camadas em diversos computadores façam a replicação ao mesmo tempo. Todos os computadores em um grupo de replicação têm pontos de recuperação consistentes com o aplicativo e com falhas quando executam failover. Para o failover, você pode coletar diversos computadores nos planos de recuperação para que as cargas de trabalho do aplicativo em camadas façam failover juntas.

## Sistemas operacionais com suporte

### Windows (somente 64 bits)
- Windows Server 2008 R2 SP1+
- Windows Server 2012
- Windows Server 2012 R2

### Linux (somente 64 bits)
- Red Hat Enterprise Linux 6.7, 7.1 e 7.2
- CentOS 6.5, 6.6, 6.7, 7.0, 7.1 e 7.2
- Oracle Enterprise Linux 6.4, 6.5 que executa o kernel compatível com Red Hat ou o UEK3 (Unbreakable Enterprise Kernel Versão 3)
- SUSE Linux Enterprise Server 11 SP3

## Arquitetura de cenário

Estes são os componentes do cenário:

- **Servidor de configuração**: um computador local que coordena comunicação e gerencia processos de recuperação e replicação de dados. Neste computador, você executará um arquivo único de instalação para instalar o servidor de configuração e esses componentes adicionais:
	- **Servidor de processo**: atua como um gateway de replicação. Ele recebe dados de replicação de computadores de origem protegida, otimiza-os com caching, compactação e criptografia e os envia para o armazenamento do Azure. Ele também manipula a instalação por push do Serviço de mobilidade em computadores protegidos e executa a descoberta automática de máquinas virtuais VMware. O servidor de processo padrão é instalado no servidor de configuração. Você pode implantar servidores de processo autônomo adicionais para dimensionar sua implantação.
	- **Servidor de destino mestre**: lida com os dados de replicação durante o failback do Azure.

- **Serviço de mobilidade**: esse componente é implantado em cada computador (VM VMware ou servidor físico) que você deseja replicar para o Azure. Ele captura gravações de dados no computador e as encaminha ao servidor de processo.
- **Azure**: você não precisa criar VMs do Azure para lidar com a replicação e o failover no Azure. Você precisa de uma assinatura do Azure, de uma conta de armazenamento do Azure para armazenar os dados replicados e de uma rede virtual do Azure para que as VMs do Azure sejam conectadas a uma rede após o failover. A conta de armazenamento e a rede devem estar na mesma região que o cofre dos Serviços de Recuperação.
- **Failback**: quando estiver pronto para fazer failback do Azure para seu site local após um failover, você precisará criar uma VM do Azure como um servidor de processo temporário. Você poderá excluí-la após a conclusão do failback. Para o failback, você também precisará de uma conexão VPN (ou Rota Expressa do Azure) entre o site local e a rede do Azure em que suas VMs do Azure estão localizadas. Caso o tráfego de failback seja pesado, também convém configurar um computador do servidor de destino mestre dedicado localmente. Para tráfego mais leve, o servidor de destino mestre padrão em execução no servidor de configuração pode ser usado.


O gráfico mostra como esses componentes interagem.

![Arquitetura](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figura 1: VMware/físicos no Azure**

## Pré-requisitos do Azure

Aqui está o que será necessário no Azure para implantar este cenário.

**Pré-requisito** | **Detalhes**
--- | ---
**Conta do Azure**| Você precisará de uma conta do [Microsoft Azure](http://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site.
**Armazenamento do Azure** | Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure são criadas quando ocorre failover. <br/><br/>Para armazenar dados você precisará de uma conta de armazenamento padrão ou premium na mesma região que o cofre dos Serviços de Recuperação.<br/><br/>Você pode usar uma conta de armazenamento LRS ou GRS. É recomendável usar GRS para que os dados sejam resilientes caso ocorra uma interrupção regional, ou se a região principal não puder ser recuperada. [Saiba mais](../storage/storage-redundancy.md).<br/><br/> O [Armazenamento premium](../storage/storage-premium-storage.md) normalmente é usada para máquinas virtuais que precisam de um desempenho de E/S consistentemente alto e baixa latência para hospedar cargas de trabalho intensivas de E/S de host.<br/><br/> Se você desejar usar uma conta premium para armazenar dados replicados, você também precisará de uma conta de armazenamento padrão para armazenar os logs de replicação que capturam as alterações contínuas nos dados locais.<br/><br/> Observe que as contas de armazenamento criadas no portal do Azure não podem ser movidas entre grupos de recursos. Atualmente, também não há suporte para proteção para as contas de armazenamento premium na Índia Central e Sul da Índia.<br/><br/> [Leia sobre](../storage/storage-introduction.md) o armazenamento do Azure.
**Rede do Azure** | Você precisará de uma rede virtual do Azure com a qual as máquinas virtuais do Azure se conectarão quando ocorrer failover. A rede virtual do Azure deve estar na mesma região que o cofre dos Serviços de Recuperação.
**Failback do Azure** | Você precisará de servidor de processo temporário configurado como uma VM do Azure. Você pode criá-lo quando estiver pronto para realizar failback e excluí-lo após o failback ser concluído.<br/><br/> Para fazer failback, você precisará de uma conexão VPN (ou Rota Expressa do Azure) da rede do Azure para o site local.

## Pré-requisitos do servidor de configuração

Você vai configurar um computador local como o servidor de configuração.

**Pré-requisito** | **Detalhes**
--- | ---
**Servidor de configuração**| Você precisa de uma máquina virtual ou computador físico local executando o Windows Server 2012 R2. Todos os componentes locais da Recuperação de Site estão instalados nesse computador.<br/><br/>Para replicação de VM VMware, recomendamos que você implante o servidor como uma VM VMware altamente disponível. Se você estiver replicando computadores físicos, então o computador poderá ser um servidor físico.<br/><br/> O failback para o site local do Azure é sempre para VMs VMware, independentemente de o failover ter sido em VMs ou em servidores físicos. Se você não implantar o servidor de configuração como uma VM VMware, será necessário configurar um servidor de destino mestre como a VM VMware para receber o tráfego de failback.<br/><br/>Se o servidor for uma VM VMware, o tipo de adaptador de rede deverá ser VMXNET3. Se usar um tipo diferente de adaptador de rede, você precisará instalar uma [atualização VMware](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1) no servidor vSphere 5.5.<br/><br/>O servidor deve ter um endereço IP estático.<br/><br/>O servidor não deve ser um Controlador de Domínio.<br/><br/>O nome do host do servidor deve ter 15 caracteres ou menos.<br/><br/>O sistema operacional deve ser somente em inglês.<br/><br/> Você precisará instalar o VMware vSphere PowerCLI 6.0. no servidor de configuração.<br/><br/>O servidor de configuração precisa de acesso à Internet. O acesso de saída é necessário conforme o seguinte:<br/><br/>acesso temporário no HTTP 80 durante a instalação dos componentes da Recuperação de Site (para baixar o MySQL)<br/><br/>Acesso contínuo de saída em HTTPS 443 para gerenciamento de replicação<br/><br/>Acesso de saída contínuo em HTTPS 9443 para tráfego de replicação (essa porta pode ser modificada)<br/><br/>O servidor também precisará de acesso às seguintes URLs para que ele possa se conectar ao Azure: *.hypervrecoverymanager.windowsazure.com; *.accesscontrol.windows.net; *.backup.windowsazure.com; *.blob.core.windows.net; *.store.core.windows.net<br/><br/>Se você tiver regras de firewall baseadas no endereço IP no servidor, verifique se as regras permitem a comunicação com o Azure. Você precisará permitir os [Intervalos de IPs de datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e o protocolo HTTPS (443).<br/><br/>Permita os intervalos de endereços IP para a região do Azure de sua assinatura e para o Oeste dos EUA.<br/><br/>Baixe o MySQL desta URL: .http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi


## Pré-requisitos de host do VMware vCenter/vSphere

**Pré-requisito** | **Detalhes**
--- | ---
**vSphere**| Você precisa de um ou mais hipervisores do VMware vSphere.<br/><br/>Os hipervisores devem estar executando o vSphere versão 6.0, 5.5 ou 5.1 com as atualizações mais recentes.<br/><br/>Recomendamos que seus hosts vSphere e servidores vCenter estejam localizados na mesma rede que o servidor de processo (essa será a rede na qual o servidor de configuração estará localizado, a menos que você tenha configurado um servidor de processo dedicado).
**vCenter** | Recomendamos que você implante um servidor VMware vCenter para gerenciar seus hosts vSphere. Ele deve executar o vCenter versão 6.0 ou 5.5 com as atualizações mais recentes.<br/><br/>Observe que a Recuperação de Site não dá suporte aos novos recursos do vCenter e vSphere 6.0, como o vCenter vMotion cruzado, os volumes virtuais e o DRS de armazenamento. O suporte à Recuperação de Site está limitado a recursos que também estavam disponíveis na versão 5.5.


## Pré-requisitos de computadores protegidos

**Pré-requisito** | **Detalhes**
--- | ---
**VMs VMware local** | As VMs VMware que você deseja proteger devem ter as ferramentas VMware instaladas e em execução.<br/><br/> Os computadores que deseja proteger devem estar em conformidade com os [pré-requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) para criar VMs do Azure.<br/><br/>A capacidade de disco individual em computadores protegidos não deve ser superior a 1023 GB. Uma VM pode ter até 64 discos (portanto, até 64 TB). <br/><br/>Não há suporte para clusters de convidados de disco compartilhado.<br/><br/>Não há suporte para UEFI (Unified Extensible Firmware Interface)/inicialização EFI (Extensible Firmware Interface).<br/><br/>Os nomes dos computadores devem conter entre 1 e 63 caracteres (letras, números e hifens). O nome deve começar com uma letra ou número e terminar com uma letra ou número. Depois de habilitar a replicação para um computador, você poderá modificar o nome do Azure.<br/><br/>Se a VM de origem tiver Agrupamento NIC, ela será convertida em uma única NIC após o failover no Azure.<br/><br/>Se as VMs protegidas tiverem um disco iSCSI, a Recuperação de Site converterá o disco de iSCSI de VM protegido em um arquivo VHD quando a VM realizar failover no Azure. Se o destino iSCSI puder ser alcançado pela VM do Azure, ele se conectará ao destino iSCSI e verá basicamente dois discos – o disco VHD na VM do Azure e o disco iSCSI de origem. Nesse caso, será necessário desconectar o destino iSCSI que aparece na VM do Azure.
**Computadores do Windows (físico ou VMware)** | O computador deve estar executando um sistema operacional de 64 bits com suporte: Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 com, ao menos, SP1.<br/><br/> O sistema operacional deve ser instalado na unidade C:\\. O disco do sistema operacional deve ser um disco básico do Windows e não dinâmico. O disco de dados pode ser dinâmico.<br/><br/>A Recuperação de Site dá suporte a VMs com um disco RDM. Durante o failback, a Recuperação de Site reutilizará o disco RDM se a VM da fonte original e o disco RDM estiverem disponíveis. Se eles não estiverem disponíveis, durante o failback a Recuperação de Site criará um novo arquivo VMDK para cada disco.
**Computadores Linux** | Você precisará de um sistema operacional de 64 bits com suporte: Red Hat Enterprise Linux 6.7, 7.1, 7.2; Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2; Oracle Enterprise Linux 6.4, 6.5 executando o kernel compatível do Red Hat ou Unbreakable Enterprise Kernel versão 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>Os arquivos /etc/hosts em computadores protegidos devem conter entradas que mapeiem o nome do host local para endereços IP associados a todos os adaptadores de rede.<br/><br/>Se deseja se conectar a uma máquina virtual do Azure que executa o Linux após o failover usando um cliente Secure Shell (ssh), certifique-se de que o serviço Secure Shell na máquina protegida esteja definido para iniciar automaticamente na inicialização do sistema e que as regras de firewall permitam uma conexão ssh com ele.<br/><br/>O nome do host, os pontos de montagem, os nomes de dispositivo, assim como os nomes de arquivo e caminhos do sistema (por ex.: /etc/;/usr) devem estar somente em inglês.<br/><br/>A proteção pode ser habilitada para computadores Linux com o armazenamento a seguir: sistema de arquivos (EXT3, ETX4, ReiserFS, XFS); Multipath software-Device Mapper (vários caminhos); Gerenciador de volumes: (LVM2). Não há suporte a servidores físicos com o armazenamento de controlador HP CCISS. O sistema de arquivos ReiserFS só tem suporte no SUSE Linux Enterprise Server 11 SP3.<br/><br/>A Recuperação de Site dá suporte às VMs com um disco RDM. Durante o failback para Linux, a Recuperação de Site não reutiliza o disco RDM. Em vez disso, ele cria um novo arquivo VMDK para cada disco RDM correspondente.<br/><br/>Certifique-se de definir a configuração disk.enableUUID=true nos parâmetros de configuração da VM no VMware. Se ela não existir, crie a entrada. Isso é necessário para fornecer um UUID consistente para o VMDK para que ele seja montado corretamente. Adicionar essa configuração também garante que as alterações delta somente sejam transferidas para o local durante o failback e não uma replicação completa.
**Serviço de mobilidade** | **Windows**: para enviar por push automaticamente o serviço de Mobilidade para as VMs que executam o Windows, você precisará fornecer uma conta de administrador (administrador local no computador Windows) para que o servidor de processo possa fazer uma instalação por push.<br/><br/> **Linux**: para enviar por push automaticamente o serviço de Mobilidade para as VMs que executam o Linux, você precisará criar uma conta que pode ser usada pelo servidor de processo para fazer uma instalação por push.<br/><br/> Por padrão, todos os discos em um computador são replicados. Para [excluir um disco da replicação](#exclude-disks-from-replication), o serviço de Mobilidade deve ser instalado manualmente no computador antes de habilitar a replicação.

## Preparar para a implantação

Para se preparar para a implantação, você precisará:

1. [Configure uma rede do Azure](#set-up-an-azure-network), na qual as VMs do Azure estarão localizadas quando rotacionadas após o failover. Além disso, para fazer failback, você precisará configurar uma conexão VPN (ou Rota Expressa do Azure) por meio da rede do Azure para o site local.
2. [Configure uma conta de armazenamento do Azure](#set-up-an-azure-storage-account) para os dados replicados.
3. [Prepare uma conta](#prepare-an-account-for-automatic-discovery) no servidor vCenter ou nos hosts do vSphere para que a Recuperação de Site possa detectar automaticamente as VMs VMware adicionadas.
4. [Prepare o servidor de configuração](#prepare-the-configuration-server) para garantir que ele poderá acessar as URLs necessárias e instalar o vSphere PowerCLI 6.0.


### Configurar uma rede do Azure

- A rede deve estar na mesma região do Azure à que você implantará o cofre dos Serviços de Recuperação.
- Dependendo do modelo de recurso que você deseja usar para as VMs do Azure com failover, você configurará a rede do Azure no [modo ARM](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou no [modo clássico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Para fazer failback do Azure para seu site VMware local, você precisará de uma conexão VPN (ou uma conexão de Rota Expressa do Azure) pela rede do Azure na qual as VMs do Azure replicadas estão localizadas, para a rede local na qual o servidor de configuração é localizado.
- [Conheça](../vpn-gateway/vpn-gateway-site-to-site-create.md) os modelos de implantação com suporte para conexões VPN de site a site e saiba como [configurar uma conexão](../vpn-gateway/vpn-gateway-site-to-site-create.md#create-your-virtual-network).
- Como alternativa, você pode configurar a [Rota Expressa do Azure](../expressroute/expressroute-introduction.md). [Saiba mais](../expressroute/expressroute-howto-vnet-portal-classic.md) sobre como configurar uma rede com a Rota Expressa do Azure.

> [AZURE.NOTE] [Migration of networks](../resource-group-move-resources.md) nos grupos de recursos dentro da mesma assinatura ou nas assinaturas não tem suporte para as redes usadas para a implantação do Site Recovery.

### Configurar uma conta de armazenamento do Azure

- Você precisará de uma conta de armazenamento do Azure padrão ou premium para reter os dados replicados para o Azure. A rede deve estar na mesma região do que o cofre dos Serviços de Recuperação. Dependendo do modelo de recurso que deseja usar para fazer failover das VMs do Azure, você configurará uma conta no [modo ARM](../storage/storage-create-storage-account.md) ou no [modo clássico](../storage/storage-create-storage-account-classic-portal.md).
- Se você estiver usando uma conta premium para os dados replicados, precisará configurar uma conta padrão adicional para armazenar os logs de replicação que capturam as alterações contínuas nos dados locais.

> [AZURE.NOTE] [Migration of storage accounts](../resource-group-move-resources.md) nos grupos de recursos dentro da mesma assinatura ou nas assinaturas não tem suporte para as contas de armazenamento usadas para a implantação do Site Recovery.

### Preparar uma conta para a descoberta automática

O servidor de processo de Recuperação de Site pode descobrir automaticamente as VMs VMware em hosts vSphere ou em um servidor vCenter que gerencia os hosts. Para executar as credenciais da Recuperação de Site de descoberta automática que possam acessar o servidor VMware. Isso não será relevante se você estiver replicando apenas computadores físicos.

1. Para usar uma conta dedicada para a descoberta automática, crie uma função (por exemplo, Azure\_Site\_Recovery) no nível do vCenter com as [permissões necessárias](#vmware-account-permissions).
2. Crie um novo usuário no host vSphere ou servidor vCenter e atribua a função ao usuário. Posteriormente, você vai informar a Recuperação de Site sobre essas credenciais para que ela possa executar a descoberta automática.

	>[AZURE.NOTE] Uma conta de usuário do vCenter que tenha a função somente leitura pode executar failover, mas não consegue desligar os computadores de origem protegida. Se você quiser desligar as máquinas, você precisará da função [Azure\_Site\_Recovery](#vmware-account-permissions). Se você estiver migrando somente as VMs do VMware para o Azure e não precisar fazer failback, a função somente leitura será suficiente.

### Preparar o servidor de configuração

1.	Certifique-se de que o computador que você está usando para o servidor de configuração esteja em conformidade com os [pré-requisitos](#configuration-server-prerequisites). Em particular, certifique-se de que o computador esteja conectado à Internet com estas configurações:

	- Permitir o acesso a essas URLs: *.hypervrecoverymanager.windowsazure.com; *.accesscontrol.windows.net; *.backup.windowsazure.com; *.blob.core.windows.net; *.store.core.windows.net
	- Permita acesso a [http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi) para baixar o MySQL.
	- Permita a comunicação de firewall para o Azure com os [intervalos de IPs do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e o protocolo HTTPS (443).

2.	Baixe e instale o [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) no servidor de configuração. (Atualmente, outras versões do PowerCLI não são compatíveis, incluindo versões R da versão 6.0.)


## Criar um cofre dos Serviços de Recuperação

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Gerenciamento** > **Backup e Recuperação de Site (OMS)**. Como alternativa, você pode clicar em **Procurar** > **Cofre de Serviços de Recuperação** > **Adicionar**.

	![Novo cofre](./media/site-recovery-vmware-to-azure/new-vault3.png)

3. Em **Nome**, especifique um nome amigável para identificar o cofre. Se você tiver mais de uma assinatura, selecione uma delas.
4. [Crie um novo grupo de recursos](../resource-group-template-deploy-portal.md) ou selecione um existente. Especifique uma região do Azure. Os computadores serão replicados para essa região. Observe que o armazenamento do Azure e as redes usadas para a Recuperação de Site precisarão estar na mesma região. Para verificar as regiões suportadas, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços de Recuperação de Site do Azure](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Se você deseja acessar rapidamente o cofre pelo Painel, clique em **Fixar no painel** e, em seguida, em **Criar**.

	![Novo cofre](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

O novo cofre será exibido no **Painel** > **Todos os recursos** e na folha **Cofres dos Serviços de Recuperação** principal.

## Introdução

A Recuperação de Site fornece uma experiência de Introdução criada para você ficar em funcionamento o mais rápido possível. Ele verifica os pré-requisitos e explica as etapas que necessárias para que a Recuperação de Site seja implantada.

Você selecionará os tipos de computadores que deseja replicar e para onde deseja replicar. Configure a infraestrutura, incluindo servidores locais, as configurações do Azure, as políticas de replicação e o planejamento de capacidade. Após sua infraestrutura estar pronta, habilite a replicação para as VMs e para os servidores físicos. Então, você pode executar failovers para computadores específicos ou criar planos de recuperação para fazer failover de vários computadores.

Inicie a Introdução ao escolher como deseja implantar a Recuperação de Site. O fluxo de Introdução mudará ligeiramente, dependendo dos requisitos da sua replicação.


## Etapa 1: Escolher os objetivos de proteção

Selecione o que você deseja replicar e para onde deseja replicar.

1. Na folha **Cofres dos Serviços de Recuperação**, selecione seu cofre e clique em **Configurações**.
2. Em **Configurações** > **Introdução**, clique em **Recuperação de Site** > **Etapa 1: Preparar a Infraestrutura** > **Meta de proteção**.

	![Escolher metas](./media/site-recovery-vmware-to-azure/choose-goals.png)

3. Em **Meta de proteção**, selecione **Para o Azure** e selecione **Sim, com o Hipervisor VMware vSphere**. Em seguida, clique em **OK**.

	![Escolher metas](./media/site-recovery-vmware-to-azure/choose-goals2.png)


## Etapa 2: Configurar o ambiente de origem

Configure o servidor de configuração e registre-o no cofre dos Serviços de Recuperação. Se você estiver replicando VMs VMware, especifique a conta do VMware que você está usando para a descoberta automática.

1. Clique em **Etapa 1: Preparar a Infraestrutura** > **Origem**. Em **Preparar fonte**, se você não tiver um servidor de configuração, clique em **+Servidor de configuração** para adicionar um.

	![Configurar origem](./media/site-recovery-vmware-to-azure/set-source1.png)

2. Na folha **Adicionar Servidor**, verifique se o **Servidor de Configuração** aparece no **Tipo de Servidor**.
3. Antes de definir o servidor de configuração, verifique os [pré-requisitos](#configuration-server-prerequisites). Em específico, verifique se o computador pode acessar as URLs necessárias.
4.	Baixe o arquivo de instalação Configuração Unificada da Recuperação de Site.
5.	Baixe a chave do registro do cofre. Você precisará dela quando executar a Configuração Unificada. A chave é válida por 5 dias após ser gerada.

	![Configurar origem](./media/site-recovery-vmware-to-azure/set-source2.png)

6.	No computador que você estiver usando o servidor de configuração, execute a Configuração Unificada para instalar o servidor de configuração, o servidor de processo e o servidor de destino mestre.


### Execute a Configuração Unificada da Recuperação de Site

1.	Execute o arquivo de instalação de Configuração Unificada.
2.	Em **Antes de começar**, selecione **Instalar o servidor de configuração e o servidor de processo**.

	![Antes de começar](./media/site-recovery-vmware-to-azure/combined-wiz1.png)

3. Em **Licença de Software de Terceiros**, clique em **Eu Aceito** para baixar e instalar o MySQL.

	![Software de terceiros](./media/site-recovery-vmware-to-azure/combined-wiz105.PNG)

4. Em **Registro**, procure e escolha a chave de registro que você baixou do cofre.

	![Registro](./media/site-recovery-vmware-to-azure/combined-wiz3.png)

5. Em **Configurações da Internet**, especifique como o Provedor em execução no servidor de configuração se conectará ao Azure Site Recovery pela Internet.

	- Se você quiser se conectar ao proxy que está configurado atualmente no computador, escolha **Conectar-se com as configurações de proxy existentes**.
	- Se quiser que o Provedor se conecte diretamente, selecione **Connect directly without a proxy (Conectar diretamente sem um proxy)**.
	- Se o proxy existente exigir autenticação ou se você quiser usar um proxy personalizado para a conexão do Provedor, escolha **Conectar-se com configurações de proxy personalizadas**.
		- Se você usar um proxy personalizado, precisará especificar o endereço, a porta e as credenciais
		- Se estiver usando um proxy, você já deverá ter concedido as URLs descritas em [pré-requisitos](#configuration-server-prerequisites).

	![Firewall](./media/site-recovery-vmware-to-azure/combined-wiz4.png)

6. Em **Verificação de Pré-requisitos**, a configuração executa uma verificação para garantir que a instalação possa ser executada. Se aparecer um aviso sobre a **Verificação de sincronização de tempo global**, verifique se a hora no relógio do sistema (configurações de **Data e Hora**) é a mesma que a do fuso horário.

	![Pré-requisitos](./media/site-recovery-vmware-to-azure/combined-wiz5.png)

7. Em **Configuração do MySQL**, crie credenciais para fazer logon na instância do servidor MySQL que será instalada.

	![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)

8. Em **Detalhes do Ambiente**, selecione se você replicará as VMs VMware. Se a resposta for positiva, a instalação verificará se o PowerCLI 6.0 está instalado.

	![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz7.png)

9. Em **Localização de Instalação**, selecione a localização em que você deseja instalar os binários e armazenar o cache. Você pode selecionar uma unidade que tem ao menos 5 GB de armazenamento disponível, mas é recomendável uma unidade de cache com ao menos 600 GB de espaço livre.

	![Local de instalação](./media/site-recovery-vmware-to-azure/combined-wiz8.png)

10. Em **Seleção da Rede**, especifique o ouvinte (adaptador de rede e porta SSL) no qual o servidor de configuração enviará e receberá os dados de replicação. Você pode modificar a porta padrão (9443). Além dessa porta, a porta 443 será usada por um servidor Web que orquestra operações de replicação. A 443 não deve ser usada para receber tráfego de replicação.


	![Seleção de rede](./media/site-recovery-vmware-to-azure/combined-wiz9.png)



11.  Em **Resumo**, examine as informações e clique em **Instalar**. Após a conclusão da instalação, uma frase secreta é gerada. Você precisará dela quando habilitar a replicação, portanto copie-a e guarde-a em um local seguro.

	![Resumo](./media/site-recovery-vmware-to-azure/combined-wiz10.png)

12.  Após a conclusão do registro, o servidor é exibido na folha **Configurações** > **Servidores** no cofre.



#### Executar a instalação a partir da linha de comando

Você pode definir o servidor de configuração da linha de comando:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Quando a instalação terminar, para concluir o registro:

1. Inicie um aplicativo chamado “Shell de Serviços de Recuperação do Microsoft Azure” no menu Iniciar do Windows.
2. Na janela de comando que se abre, execute o seguinte conjunto de comandos para definir as configurações do servidor proxy.

		PS C:\Windows\System32> $pwd = ConvertTo-SecureString -String ProxyUserPassword
		PS C:\Windows\System32> Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
		PS C:\Windows\System32> net stop obengine.exe

Parâmetros:

- /ServerMode: obrigatório. Especifica se os servidores de configuração e o processo devem ser instalados ou somente o servidor de processo. Valores de entrada: CS, PS.
- InstallLocation: obrigatório. A pasta na qual os componentes estão instalados.
- /MySQLCredsFilePath. Obrigatório. O caminho do arquivo no qual as credenciais do servidor MySQL são armazenadas. O arquivo deve estar no seguinte formato:
	- [MySQLCredentials]
	- MySQLRootPassword = "<Senha>"
	- MySQLUserPassword = "<Senha>"
- /VaultCredsFilePath. Obrigatório. O local do arquivo de credenciais do cofre
- /EnvType. Obrigatório. O tipo de instalação. Valores: VMware, NonVMware
- /PSIP e /CSIP. Obrigatório. O endereço IP do servidor de processo e do servidor de configuração.
- /PassphraseFilePath. Obrigatório. O local do arquivo da frase secreta.
- /BypassProxy. Opcional. Especifica se o servidor de gerenciamento se conecta ao Azure sem um proxy.
- /ProxySettingsFilePath. Opcional. Configurações de proxy (o proxy padrão exigir autenticação ou um proxy personalizado). O arquivo deve estar no seguinte formato:
	- [ProxySettings]
	- ProxyAuthentication = "Sim/Não"
	- IP do Proxy = "Endereço IP>"
	- ProxyPort = "<Porta>"
	- ProxyUserName="<Nome de Usuário>"
	- ProxyPassword="<Senha>"
- DataTransferSecurePort. Opcional. O número da porta a ser usada para dados de replicação.
- SkipSpaceCheck. Opcional. Ignorar verificação de espaço do cache.
- AcceptThirdpartyEULA. Obrigatório. O sinalizador implica a aceitação do EULA de terceiros.
- ShowThirdpartyEULA. Obrigatório. Exibe o EULA de terceiros. Se fornecido como entrada, todos os outros parâmetros serão ignorados.

### Adicione a conta de VMware usada para descoberta automática

 Ao se preparar para a implantação, você deve ter [criado uma conta de VMware](#prepare-an-account-for-automatic-discovery) que a Recuperação de Site poderá usar para a descoberta automática. Adicione essa conta da seguinte maneira:

1. Abra o **CSPSConfigtool.exe**. Ela está disponível como um atalho na área de trabalho e está localizada na pasta [LOCAL DE INSTALAÇÃO]\\home\\svsystems\\bin.
2. Clique em **Gerenciar Contas** > **Adicionar Conta**.

	![Adicionar conta](./media/site-recovery-vmware-to-azure/credentials1.png)

3. Em **Detalhes da Conta**, adicione a conta que será usada para a descoberta automática. Observe que pode levar 15 minutos ou mais para que o nome da conta apareça no portal. Para atualizar imediatamente, clique em **Servidores de Configuração** > Nome do servidor > **Atualizar Servidor**.

	![Detalhes](./media/site-recovery-vmware-to-azure/credentials2.png)

### Conectar-se aos hosts vSphere e servidores vCenter

Se você estiver replicando VMs VMware, conecte-se aos hosts vSphere e servidores vCenter.

1. Verifique se o servidor de configuração tem acesso à rede para hosts vSphere e servidores vCenter.
2. Clique em **Preparar a Infraestrutura** > **Origem**. Em **Preparar fonte**, selecione o servidor de configuração e clique em **+vCenter** para adicionar um host vSphere ou servidor vCenter.
3. Em **Adicionar vCenter**, especifique um nome amigável para o host vSphere ou servidor vCenter e especifique o endereço IP ou o FQDN do servidor. Deixe a porta como 443, a menos que os servidores do VMware estejam configurados para escutar solicitações em uma porta diferente. Em seguida, selecione a conta que será usada para conectar ao servidor VMware. Clique em **OK**.

	![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

	>[AZURE.NOTE] Se você estiver adicionando o servidor vCenter ou o host vSphere com uma conta que não tem privilégios de administrador no vCenter ou no servidor host, certifique-se de que a conta possua esses privilégios habilitados: Datacenter, Repositório de Dados, Pasta, Host, Rede, Recursos, Máquina virtual e Comutador Distribuído do vSphere. Além disso, o servidor vCenter precisa do privilégio de exibição do Armazenamento.

A Recuperação de Site conecta-se a servidores VMware usando as configurações especificadas e descobre as VMs.

## Etapa 3: Configurar o ambiente de origem

Verifique se você tem uma conta de armazenamento para replicação e uma rede do Azure com a qual as VMs do Azure se conectarão após o failover.

1.	Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que você deseja usar.
2.	Especifique o modelo de implantação que você deseja usar para as VMs após o failover.
3.	A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

	![Destino](./media/site-recovery-vmware-to-azure/gs-target.png)

4.	Se você não tiver criado uma conta de armazenamento e se desejar criar uma usando o ARM, clique em **+Conta de armazenamento** para fazer isso de forma embutida. Na folha **Criar conta de armazenamento**, especifique um nome de conta, um tipo, uma assinatura e uma localização. A conta deve estar na mesma região que o cofre dos Serviços de Recuperação.

	![Armazenamento](./media/site-recovery-vmware-to-azure/gs-createstorage.png)

	Observe que:

	- Se você quiser criar uma conta de armazenamento usando o modelo clássico, terá de fazer isso no portal do Azure. [Saiba mais](../storage/storage-create-storage-account-classic-portal.md)
	- Se você estiver usando uma conta de armazenamento premium para os dados replicados, precisará configurar uma conta de armazenamento padrão adicional para armazenar os logs de replicação que capturam as alterações contínuas nos dados locais.
	
	> [AZURE.NOTE] Também não há suporte para proteção para as contas de armazenamento premium na Índia Central e Sul da Índia.

4.	Selecione uma rede do Azure. Se você não tiver criado uma rede e se quiser fazê-lo usando o ARM, clique em **+Rede** para fazer isso de forma embutida. Na folha **Criar rede virtual**, especifique um nome de rede, um intervalo de endereços, detalhes de sub-rede, uma assinatura e uma localização. A rede deve estar no mesmo local do que o cofre dos Serviços de Recuperação.

	![Rede](./media/site-recovery-vmware-to-azure/gs-createnetwork.png)

	Se você quiser criar uma rede usando o modelo clássico, terá de fazer isso no portal do Azure. [Saiba mais](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

## Etapa 4: Definir as configurações da replicação

1. Para criar uma nova política de replicação, clique em **Preparar a Infraestrutura** > **Configurações de Replicação** > **+Criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política.
3. Em **Limite de RPO**: especifique o limite de RPO. Alertas serão gerados quando a replicação contínua excede esse limite.
5. Em **Retenção do ponto de recuperação**, especifique, em horas, qual será a duração da janela de retenção para cada ponto de recuperação. Os computadores protegidos podem ser recuperados para qualquer ponto nessa janela. Há suporte para até 24 horas de retenção para computadores replicados para o armazenamento premium.
6. Em **Frequência do instantâneo consistente com o aplicativo**, especifique com que frequência (em minutos) os pontos de recuperação contendo instantâneos consistentes com aplicativos serão criados.
7. Quando você cria uma política de replicação, por padrão uma política correspondente é criada automaticamente para failback. Por exemplo, se a política de replicação for **rep-policy**, a política de failback será **rep-policy-failback**. Essa política não é usada até você iniciar um failback.
8. Clique em **OK** para criar a política.

	![Política de replicação](./media/site-recovery-vmware-to-azure/gs-replication2.png)

9. Quando você cria uma nova política, ela é automaticamente associada ao servidor de configuração. Clique em **OK**.

	![Política de replicação](./media/site-recovery-vmware-to-azure/gs-replication3.png)


## Etapa 5: Planejamento de capacidade

Agora que você tem a infraestrutura básica configurada, pode pensar sobre o planejamento de capacidade e descobrir se precisa de recursos adicionais.

A Recuperação de Site fornece um planejador de capacidade para ajudar você a alocar os recursos certos para seu ambiente de origem, para os componentes da recuperação de site, para a rede e para o armazenamento. Você pode executar o planejador no modo rápido para estimativas baseadas em um número médio de VMs, de discos e de armazenamento, ou no modo detalhado, no qual você vai inserir números no nível de carga de trabalho. Antes de começar, será necessário:

- Reunir informações sobre seu ambiente de replicação, inclusive VMs, discos por VMs e armazenamento por disco.
- Estimar a taxa de alteração (variação) diária de dados replicados. Você pode usar a [solução de planejamento de capacidade do vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) para ajudá-lo a fazer isso.

1.	Clique em **Baixar** para baixar a ferramenta e, então, executá-la. [Leia o artigo](site-recovery-capacity-planner.md) que acompanha a ferramenta.
2.	Quando terminar, selecione **Sim** em **Você executou o planejamento de capacidade?**

	![Planejamento da capacidade](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)

A tabela a seguir captura diversos pontos para ajudá-lo com o planejamento de capacidade para esse cenário.


**Componente** | **Detalhes**
--- | --- | ---
**Replicação** | **Taxa de alteração diária máxima** — Um computador protegido só pode usar um servidor de processo, e um servidor de processo único pode manusear uma taxa de alteração diária máxima de 2 TB. Portanto, 2 TB é a taxa de alteração diária máxima com suporte para um computador protegido.<br/><br/> **Taxa de transferência máxima** — uma máquina replicada pode pertencer a uma conta de armazenamento no Azure. Uma conta de armazenamento padrão pode lidar com um máximo de 20 mil solicitações por segundo e recomendamos que você mantenha o número de IOPS em um computador de origem como 20 mil. Para o exemplo, se você tiver um computador de origem com 5 discos e cada disco gerar 120 IOPS (8K de tamanho) na origem, ele estará dentro do limite IOPS por disco do Azure de 500. O número de contas de armazenamento necessárias = total de IOPs de origem/20000.
**Servidor de configuração** | O servidor de configuração deve ser capaz de lidar com a capacidade de taxa de alteração diária em todas as cargas de trabalho em execução em computadores protegidos e precisa ter largura de banda suficiente para replicar continuamente os dados para o armazenamento do Azure.<br/><br/> Como prática recomendada, recomendamos que o servidor de configuração esteja localizado na mesma rede e no segmento de LAN que os computadores que deseja proteger. Ele pode estar localizado em uma rede diferente, mas os computadores que você deseja proteger devem ter visibilidade de rede L3 para ele.<br/><br/> As recomendações de tamanho para o servidor de configuração estão resumidas na tabela abaixo.
**Servidor de processo** | O primeiro servidor de processo é instalado por padrão no servidor de configuração. Você pode implantar servidores de processo adicionais par dimensionar seu ambiente. Observe que:<br/><br/> o servidor de processo recebe os dados de replicação de computadores protegidos e os otimiza com caching, compactação e criptografia antes de enviar para o Azure. O computador do servidor de processo deve ter recursos suficientes para executar essas tarefas.<br/><br/> O servidor em processo usa o cache baseado em disco. Recomendamos um disco de cache separado de 600 GB ou mais para lidar com alterações de dados armazenados em caso de afunilamento ou interrupção de rede.

### Recomendações de tamanho para o servidor de configuração

**CPU** | **Memória** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos**
--- | --- | --- | --- | ---
8 vCPUs (2 soquetes x 4 núcleos a 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | Replique menos de 100 computadores.
12 vCPUs (2 soquetes x 6 núcleos a 2,5 GHz) | 18 GB | 600 GB | 500 GB a 1 TB | Replique entre 100 e 150 computadores.
16 vCPUs (2 soquetes x 8 núcleos a 2,5 GHz) | 32 GB | 1 TB | 1 TB a 2 TB | Replique entre 150 e 200 computadores.
Implantar outro servidor de processo | | | > 2 TB | Implante servidores de processo adicionais se estiver replicando mais de 200 computadores ou se a taxa de alteração diária de dados ultrapassar 2 TB.

Em que:

- Cada computador de origem é configurado com 3 discos de 100 GB.
- Usamos armazenamento de benchmark de 8 unidades SAS de 10K RPM com RAID 10 para as medidas do disco de cache.

### Recomendações de tamanho para o servidor de processo

Se precisar proteger mais de 200 computadores ou se a taxa de alteração diária for maior que 2 TB, você poderá adicionar mais servidores para lidar com a carga de replicação. Para escalar horizontalmente, você pode:

- Aumente o número de servidores de configuração. Por exemplo, você pode proteger até 400 computadores com dois servidores de configuração.
- Adicionar servidores de processo adicionais e usá-los para lidar com o tráfego em vez do (ou além) do servidor de configuração.

Esta tabela descreve um cenário em que:

- Você não planeja usar o servidor de configuração como um servidor de processo.
- Você configurou um servidor de processo adicional.
- Você configurou máquinas virtuais protegidas para usar o servidor de processo adicional.
- Cada computador de origem protegido é configurado com três discos de 100 GB cada.

**Servidor de configuração** | **Servidor de processo adicional**| **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Computadores protegidos**
--- | --- | --- | --- | ---
8 vCPUs (2 soquetes x 4 núcleos a 2,5 GHz), 16 GB de memória | 4 vCPUs (2 soquetes x 2 núcleos a 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Replique 85 computadores ou menos.
8 vCPUs (2 soquetes x 4 núcleos a 2,5 GHz), 16 GB de memória | 8 vCPUs (2 soquetes x 4 núcleos a 2,5 GHz), 12 GB de memória | 600 GB | 250 GB a 1 TB | Replique entre 85 e 150 computadores.
12 vCPUs (2 soquetes x 6 núcleos a 2,5 GHz), 18 GB de memória | 12 vCPUs (2 soquetes x 6 núcleos a 2,5 GHz), 24 GB de memória | 1 TB | 1 TB a 2 TB | Replique entre 150 e 225 computadores.


A maneira como você dimensiona seus servidores depende de sua preferência por um modelo que escale verticalmente ou horizontalmente. Você pode escalar verticalmente implantando alguns servidores de processo e de configuração de alto nível ou escalar horizontalmente implantando mais servidores com menos recursos. Por exemplo: se você precisar proteger os 220 computadores, poderá fazer o seguinte:

- Defina o servidor de configuração com 12vCPU, 18 GB de memória, um servidor de processo adicional com 12vCPU, 24 GB de memória e configure os computadores protegidos para usar somente o servidor de processo adicional.
- Como alternativa, você pode configurar dois servidores de configuração (2 x 8vCPU, 16 GB de RAM) e dois servidores de processo adicionais (1 x 8vCPU e 4vCPU x1 para lidar com 135 + 85, 220 computadores) e configurar computadores protegidos para usar somente os servidores de processo adicionais.

[Siga estas instruções](#deploy-additional-process-servers) para configurar um servidor de processo adicional.

### Considerações sobre largura de banda de rede

Você pode usar a ferramenta de planejador de capacidade para calcular a largura de banda necessária para a replicação (replicação inicial e depois a delta). Para controlar a quantidade de uso de largura de banda para a replicação, você tem algumas opções:

- **Restringir a largura de banda**: o tráfego VMware que replica para o Azure passa por um servidor de processo específico. Você pode limitar a largura de banda nos computadores em execução como servidores de processo.
- **Influenciar a largura de banda**: você pode influenciar a largura de banda usada para a replicação usando algumas chaves do Registro:
	- O valor de registro **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\UploadThreadsPerVM** especifica o número de threads usados para a transferência de dados (replicação inicial ou delta) de um disco. Um valor mais alto aumenta a largura de banda de rede usada para replicação.
	- O **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\DownloadThreadsPerVM** especifica o número de threads usados para transferência de dados durante o failback.

#### Limitar a largura de banda

1. Abra o snap-in MMC do Backup do Microsoft Azure no computador atuando como servidor de processo. Por padrão, um atalho para o Backup do Microsoft Azure está disponível na área de trabalho ou C:\\Arquivos de Programas\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.

	![Limitar a largura de banda](./media/site-recovery-vmware-to-azure/throttle1.png)

3. Na guia **Limitação**, selecione **Habilitar limitação de uso de largura de banda da Internet para operações de backup** e defina os limites do horário comercial e não comercial. Os intervalos válidos são de 512 Kbps a 102 Mbps por segundo.

	![Limitar a largura de banda](./media/site-recovery-vmware-to-azure/throttle2.png)

Você também pode usar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para definir a limitação. Veja um exemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que nenhuma limitação é necessária.


#### Influência da largura de banda de rede

1. No Registro, navegue até **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\Replication**.
	- Para influenciar o tráfego de largura de banda em um disco de replicação, modifique o valor de **UploadThreadsPerVM** ou crie a chave caso ela não exista.
	- Para influenciar a largura de banda para o tráfego de failback do Azure, modifique o valor **DownloadThreadsPerVM**.
2. O valor padrão é 4. Em uma rede "sobreprovisionada", os valores padrão dessas chaves do registro precisam ser alterados. O máximo é 32. Monitore o tráfego para otimizar o valor.

## Etapa 6: replicar aplicativos

Certifique-se de que os computadores que você deseja replicar estejam preparados para a instalação do serviço de Mobilidade e, em seguida, habilite a replicação.

### Instalar o serviço de Mobilidade

A primeira etapa da habilitação da proteção para máquinas virtuais e servidores físicos é a instalação do Serviço de mobilidade. Isso pode ser feito de duas maneiras:

- **Processar envio por push de servidor**: ao habilitar a replicação em um computador, envie por push e instale o componente do serviço de Mobilidade pelo servidor de processo. Observe que a instalação por push não ocorrerá se o computador já estiver executando uma versão atualizada do componente.
- **Envio por push corporativo**: instale automaticamente o componente usando um processo por push corporativo, como o WSUS ou o System Center Configuration Manager. Defina o servidor de configuração antes de fazer isso.
- **Instalação manual**: instale o componente manualmente em cada computador que você deseja replicar. Defina o servidor de configuração antes de fazer isso.


#### Preparar para o envio por push automático em computadores com Windows

Veja como preparar os computadores com Windows para que o Serviço de mobilidade possa ser instalado automaticamente pelo servidor de processo.

1.  Crie uma conta que pode ser usada pelo servidor de processo para acessar o computador. A conta deve ter privilégios de administrador (local ou domínio), que são usados somente para a instalação por push.

	>[AZURE.NOTE] Se você não estiver usando uma conta de domínio, precisará desabilitar o controle Acesso de Usuário Remoto no computador local. Para fazer isso, no registro, em HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System, adicione a entrada DWORD LocalAccountTokenFilterPolicy com um valor de 1. Para adicionar a entrada de Registro de um tipo CLI **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  No Firewall do Windows do computador que você deseja proteger, selecione **Permitir um aplicativo ou recurso pelo Firewall**. Habilite o **Compartilhamento de Arquivo e Impressora** e a **Instrumentação de Gerenciamento do Windows**. Para computadores que pertencem a um domínio, você pode definir as configurações do firewall com um GPO.

	![Configurações de firewall](./media/site-recovery-vmware-to-azure/mobility1.png)

2. Adicione a conta que você criou:

	- Abra **cspsconfigtool**. Ela está disponível como um atalho na área de trabalho e está localizada na pasta [LOCAL DE INSTALAÇÃO]\\home\\svsystems\\bin.
	- Na guia **Gerenciar Contas**, clique em **Adicionar Conta**.
	- Adicione a conta que você criou. Depois de adicionar a conta, será preciso fornecer as credenciais quando habilitar a replicação para um computador.


#### Preparar para o envio por push automático em servidores Linux

1.	Certifique-se de que o computador com Linux que você deseja proteger tem suporte, conforme descrito em [pré-requisitos de computador protegido](#protected-machine-prerequisites). Verifique se há conectividade de rede entre o computador Linux e o servidor de processo.

2.	Crie uma conta que pode ser usada pelo servidor de processo para acessar o computador. A conta deve ser um usuário raiz no servidor Linux de origem e ser usada somente para a instalação por push.

	- Abra **cspsconfigtool**. Ela está disponível como um atalho na área de trabalho e está localizada na pasta [LOCAL DE INSTALAÇÃO]\\home\\svsystems\\bin.
	- Na guia **Gerenciar Contas**, clique em **Adicionar Conta**.
	- Adicione a conta que você criou. Depois de adicionar a conta, será preciso fornecer as credenciais quando habilitar a replicação para um computador.

3.	Verifique se o arquivo /etc/hosts no servidor Linux de origem contém entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede.
4.	Instale os últimos pacotes openssh, openssh-server e openssl no computador que você deseja replicar.
5.	Verifique se SSH está habilitado e em execução na porta 22.
6.	Habilite a autenticação de subsistema e senha SFTP no arquivo sshd\_config, como se segue:

	- Faça logon como raiz.
	- No arquivo /etc/ssh/sshd\_config, localize a linha que começa com **PasswordAuthentication**.
	- Remova a marca de comentário da linha e altere o valor de **no** para **yes**.
	- Localize a linha que começa com **Subsystem** e remova a marca de comentário existente nessa linha.

		![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)


#### Instalar o Serviço de Mobilidade manualmente

Os instaladores estão disponíveis no servidor de processo em **C:\\Arquivos de Programas (x86)\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository**.

Sistema operacional de origem | Arquivo de instalação do Serviço de mobilidade
--- | ---
Windows Server (somente 64 bits) | Microsoft-ASR\_UA\_9.*.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (somente 64 bits) | Microsoft-ASR\_UA\_9.*.0.0\_RHEL6-64\_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (somente 64 bits) | Microsoft-ASR\_UA\_9.*.0.0\_SLES11-SP3-64\_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (somente 64 bits) | Microsoft-ASR\_UA\_9.*.0.0\_OL6-64\_*release.tar.gz


#### Instalar manualmente em um servidor Windows


1. Baixe e execute o instalador relevante.
2. Em **Antes de começar**, selecione **Serviço de mobilidade**.

	![Serviço de mobilidade](./media/site-recovery-vmware-to-azure/mobility3.png)

3. Em **Detalhes do Servidor de Configuração**, especifique o endereço IP do servidor de configuração e a frase secreta que foi gerada quando você executou a Configuração Unificada. Você pode recuperar a senha executando:**<PastaDeInstalaçãoDoSiteRecovery>\\home\\sysystems\\bin\\genpassphrase.exe –v** no servidor de configuração.

	![Serviço de mobilidade](./media/site-recovery-vmware-to-azure/mobility6.png)

4. Em **Localização de Instalação**, mantenha a configuração padrão e clique em **Avançar** para iniciar a instalação.
5. Em **Progresso da Instalação**, monitore a instalação e, se for solicitado, reinicie o computador. Após instalar o serviço, pode levar cerca de 15 minutos para o status ser atualizado no portal.

Você também pode instalar a partir da linha de comando:

UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Diretório Instalação>] [/CSIP <endereço IP do CS a ser registrado>] [/PassphraseFilePath <Caminho arquivo senha] [/LogFilePath <Caminho Arquivo Log>]

Em que:

- /Role: obrigatório. Especifica se o Serviço de mobilidade deve ser instalado.
- /InstallLocation: obrigatório. Especifica onde instalar o serviço.
- /PassphraseFilePath: obrigatório. A frase secreta do servidor de configuração.
- /LogFilePath: obrigatório. O local dos arquivos de configuração de log.

#### Desinstalar o Serviço de Mobilidade manualmente

O Serviço de Mobilidade pode ser desinstalado usando Adicionar ou Remover Programas no Painel de Controle ou usando a linha de comando.

O comando para desinstalar o Serviço de mobilidade usando a linha de comando é

	MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}


#### Instale manualmente em um servidor Linux:

1. Copie o arquivo tar apropriado baseado na tabela acima para o computador Linux que você deseja replicar.
2. Abra um programa do shell e extraia o arquivo tar compactado para um caminho local executando: `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Crie um arquivo passphrase.txt no diretório local para o qual você extraiu o conteúdo do arquivo tar. Para fazer isso, copie a senha de C:\\ProgramData\\Microsoft Azure Site Recovery\\private\\connection.passphrase no servidor de configuração e salve-a em passphrase.txt executando *`echo <passphrase> >passphrase.txt`* no shell.
4. Instale o Serviço de Mobilidade executando *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especifique o endereço IP interno do servidor de configuração e verifique se a porta 443 está selecionada. Após instalar o serviço, pode levar cerca de 15 minutos para o status ser atualizado no portal.

**Você também pode instalar por meio da linha de comando**:

1. Copie a senha em C:\\Program Files (x86)\\InMage Systems\\private\\connection no servidor de configuração e salve-a como "passphrase.txt" no servidor de configuração. Em seguida, execute estes comandos. Em nosso exemplo, o endereço IP do servidor de configuração é 104.40.75.37 e a porta HTTPS deve ser 443:

Para instalar em um servidor de produção:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Para instalar no servidor mestre de destino:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


### Habilitar a replicação

#### Antes de começar

Se você estiver replicando as máquinas virtuais VMware, observe o seguinte:

- As VMs VMware são descobertas a cada 15 minutos e pode levar 15 minutos ou mais para elas aparecerem no portal após a descoberta. Da mesma forma, a descoberta pode levar 15 minutos ou mais quando você adiciona um novo servidor vCenter ou host vSphere.
- As alterações de ambiente na máquina virtual (como instalação de ferramentas VMware) também podem levar 15 minutos ou mais para serem atualizadas no portal.
- Você pode verificar a hora da última descoberta das VMs VMware no campo **Último Contato Às** do servidor vCenter/host vSphere, na folha **Servidores de Configuração**.
- Para adicionar computadores para replicação sem precisar esperar pela descoberta agendada, destaque o servidor de configuração (não clique nele) e clique no botão **Atualizar**.
- Quando você habilitar a replicação, se o computador estiver preparado, o servidor de processo instalará automaticamente o servidor de Mobilidade nele.

#### Excluir discos da replicação

Quando você habilita a replicação, por padrão, todos os discos em um computador são replicados. Você pode excluir discos da replicação. Por exemplo, talvez você não queira replicar discos com dados temporários ou dados atualizados cada vez que um computador ou um aplicativo é reiniciado (por exemplo, pagefile.sys ou SQL Server tempdb). Se você quiser excluir discos, observe que:

- Você pode excluir somente os discos que já têm o serviço de Mobilidade instalado. Você precisará [instalar o serviço de Mobilidade manualmente](#install-the-mobility-service-manually) porque ele só é instalado usando o mecanismo de push após a replicação ser habilitada.
- Apenas discos básicos podem ser excluídos da replicação. Você não pode excluir o sistema operacional ou os discos dinâmicos.
- Depois que a replicação estiver habilitada, você não poderá adicionar ou remover discos para replicação. Se desejar adicionar ou excluir um disco, você precisará desabilitar a proteção para o computador, em seguida, habilitá-la novamente.
- Se você excluir um disco necessário para um aplicativo operar, após o failover no Azure você precisará criá-lo manualmente no Azure para que possa executar o aplicativo replicado. Como alternativa, você pode integrar a automação do Azure em um plano de recuperação para criar o disco durante o failover do computador.
- Haverá failback de discos que você criar manualmente no Azure. Por exemplo, se você executar failover de três discos e criar dois diretamente no Azure, todos os cinco farão failback. Você não pode excluir os discos criados manualmente do failback.

**Agora habilite a replicação da seguinte maneira**:

1. Clique em **Etapa 2: replicar aplicativo** > **Origem**. Depois de habilitar a replicação pela primeira vez, clique em **+Replicar** no cofre para habilitar a replicação para outros computadores.
2. Na folha **Origem** > **Origem**, selecione o servidor de configuração.
3. Em **Tipo de computador**, selecione **Máquinas Virtuais** ou **Computadores Físicos**.
4. Em **Hipervisor do vCenter/vSphere**, selecione o servidor vCenter que gerencia o host vSphere ou selecione o host. Essa configuração não será relevante se você estiver replicando computadores físicos.
5. Selecione o servidor de processo. Se você não criou nenhum servidor de processo adicional, esse será o nome do servidor de configuração. Em seguida, clique em **OK**.

	![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. Em **Destino**, selecione a assinatura de cofre e, em **Modelo de implantação após o failover**, selecione o modelo (gerenciamento de recursos ou clássico) que você deseja usar no Azure após o failover.
7. Selecione a conta de armazenamento do Azure que você usará para replicar os dados. Observe que:

	- Você pode selecionar uma conta de armazenamento padrão ou premium. Se você selecionar uma conta premium, precisará especificar uma conta de armazenamento padrão adicional para logs de replicação contínuos. As contas devem estar na mesma região que o cofre dos Serviços de Recuperação.
	- Se quiser usar uma conta de armazenamento diferente da que você tem, poderá [criar uma](#set-up-an-azure-storage-account). Para criar uma conta de armazenamento usando o modelo ARM, clique em **Criar novo**. Se você quiser criar uma conta de armazenamento usando o modelo clássico, terá de fazer isso [no portal do Azure](../storage/storage-create-storage-account-classic-portal.md).

8. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se conectarão quando forem rotacionadas após o failover. A rede deve estar na mesma região do que o cofre de Recuperação de Site. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador. Se você não tiver uma rede, precisará [criar uma](#set-up-an-azure-network). Para criar uma rede usando o modelo ARM, clique em **Criar nova**. Se você quiser criar uma rede usando o modelo clássico, fará isso [no portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selecione uma sub-rede, se aplicável. Em seguida, clique em **OK**.

	![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication3.png)

9. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, clique e selecione cada máquina que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**.

	![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication5.png)

10. Em **Propriedades** > **Configurar propriedades**, selecione a conta que será usada pelo servidor de processo para instalar automaticamente o serviço de Mobilidade no computador. Por padrão, todos os discos são replicados. Clique em **Todos os Discos** e desmarque os discos que você não deseja replicar. Em seguida, clique em **OK**. Você pode definir propriedades adicionais posteriormente.

	![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication6.png)

11. Em **Configurações de replicação** > **Definir configurações de replicação**, verifique se a política de replicação correta está selecionada. Você pode modificar as configurações da política de replicação em **Configurações** > **Políticas de replicação** > nome da política > **Editar Configurações**. Alterações aplicadas a uma política serão aplicadas a computadores novos e de replicação.

12. Habilite **Consistência de várias VMs** se você quiser reunir computadores em um grupo de replicação e especifique um nome para o grupo. Em seguida, clique em **OK**. Observe que:

	- Os computadores no grupo de replicação são replicados em conjunto e têm pontos de recuperação consistentes compartilhados com o aplicativo e com falhas quando executam failover.
	- É recomendável que você colete VMs e servidores físicos para que espelhem suas cargas de trabalho. Habilitar a consistência de várias VMs pode afetar o desempenho da carga de trabalho e só deve ser usada se os computadores estão executando a mesma carga de trabalho e precisam de consistência.

	![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication7.png)

13. Clique em **Habilitar a Replicação**. Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.

> [AZURE.NOTE] Se o computador estiver preparado para a instalação por push, o componente de serviço de Mobilidade será instalado quando a proteção estiver habilitada. Depois do componente estiver instalado no computador, um trabalho de proteção é iniciado e falha. Apos a falha, você precisa reiniciar manualmente cada computador. Após a reinicialização, o trabalho de proteção começa novamente e a replicação inicial ocorrerá.

### Exibir e gerenciar as propriedades da VM

É recomendável que você verifique as propriedades do computador de origem. Lembre-se de que o nome da VM do Azure deve estar em conformidade com os [Requisitos de máquina virtual do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Clique em **Configurações** > **Itens replicados** > e selecione o computador. A folha **Conceitos básicos** mostra as informações sobre as configurações e o status dos computadores.

2. Em **Propriedades**, você pode exibir informações de replicação e de failover para a VM.

	![Habilitar a replicação](./media/site-recovery-vmware-to-azure/test-failover2.png)

3. Em **Computação e Rede** > **Propriedades de computação**, você pode especificar o nome da VM do Azure e o tamanho de destino. Modifique o nome para que ele fique em conformidade com os requisitos do Azure, se for necessário. Você também pode exibir e adicionar as informações sobre a rede de destino, a sub-rede e o endereço IP que será atribuído à VM do Azure. Observe o seguinte:

	- Você pode definir o endereço IP de destino. Se você não fornecer um endereço, o computador com failover usará o DHCP. Se você definir um endereço que não esteja disponível no failover, o failover não funcionará. O mesmo endereço IP de destino poderá ser usado para failover de teste caso o endereço esteja disponível na rede de failover de teste.
	- O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino, como a seguir:

		- Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que a origem.
		- Se o número de adaptadores para máquina virtual de origem exceder o número permitido para o tamanho de destino e o tamanho máximo de destino será usado.
		- Por exemplo, se uma máquina de origem tiver dois adaptadores de rede e o tamanho da máquina de destino oferecer suporte a quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino com suporte oferecer suporte apenas a uma máquina de destino, ela terá apenas um adaptador.
	- Se a máquina virtual tiver vários adaptadores de rede, todos eles se conectarão à mesma rede.

	![Habilitar a replicação](./media/site-recovery-vmware-to-azure/test-failover4.png)

4. Em **Discos**, você pode ver o sistema operacional e os discos de dados na VM que serão replicados.


## Etapa 7: testar a implantação

Para testar a implantação, você pode executar um failover de teste para uma única máquina virtual ou um plano de recuperação que contém uma ou mais máquinas virtuais.


### Preparar para failover

- Para executar um failover de teste, é recomendável que você crie uma nova rede do Azure que esteja isolada da rede de produção do Azure (esse é o comportamento padrão quando você cria uma nova rede no Azure). [Saiba mais](site-recovery-failover.md#run-a-test-failover) sobre a execução de failovers de teste.
- Para obter o melhor desempenho ao fazer um failover para o Azure, instale o Agente do Azure no computador protegido. Ele torna a inicialização mais rápida e ajuda na solução de problemas. Instale o agente do [Linux](https://github.com/Azure/WALinuxAgent) ou do [Windows](http://go.microsoft.com/fwlink/?LinkID=394789).
- Para testar totalmente a implantação, você precisará de uma infraestrutura para o computador replicado funcionar como esperado. Se você quiser testar o Active Directory e o DNS, poderá criar uma máquina virtual como um controlador de domínio com DNS e replicar isso para o Azure usando o Azure Site Recovery. Leia mais em [considerações sobre failover de teste para o Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Certifique-se de que o servidor de configuração esteja em execução. Caso contrário, o failover falhará.
- Se você tiver excluído discos da replicação, convém criar esses discos manualmente no Azure após o failover para que o aplicativo seja executado conforme o esperado.
- Se você quiser executar um failover não planejado em vez de um teste de failover, observe o seguinte:

	- Se possível, você deve desligar os computadores primários antes de fazer um failover não planejado. Isso faz com que você não tenha os computadores de origem e de réplica em execução ao mesmo tempo. Se estiver replicando máquinas virtuais VMware, você poderá especificar que a Recuperação de Site faça o melhor para desligar os computadores de origem. Dependendo do estado do site primário, isso pode ou não funcionar. Se você estiver replicando servidores físicos, a Recuperação de Site não oferece essa opção.
	- Quando você executa um failover não planejado, ele interrompe a replicação de dados de computadores primários para que qualquer delta de dados não seja transferido após o início de um failover não planejado. Além disso se você executar um failover não planejado em um plano de recuperação, ele será executado até ser concluído, mesmo se ocorrer um erro.

### Preparar para conectar VMs do Azure após o failover

Se você quiser se conectar às VMs do Azure usando o RDP após o failover, faça o seguinte:

**No computador local antes do failover**:

- Para acesso pela Internet, habilite o RDP, certifique-se de que as regras de TCP e UDP sejam adicionadas para o **Público** e verifique se o RDP foi permitido no **Firewall do Windows** -> **Aplicativos e recursos permitidos** para todos os perfis.
- Para acesso por meio de uma conexão site a site, habilite o RDP na máquina e verifique se o RDP foi permitido no **Firewall do Windows** -> **Aplicativos e recursos permitidos** para as redes **Domínio** e **Particular**.
- Instale o [Agente de VM do Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) no computador local.
- [Instale manualmente o serviço de Mobilidade](#install-the-mobility-service-manually) nos computadores em vez de usar o servidor de processo para enviar por push automaticamente o serviço. Isso ocorre porque a instalação por push só acontece depois que o computador está habilitado para replicação.
- Certifique-se de que a política de SAN do sistema operacional esteja definida como OnlineAll. [Saiba mais](https://support.microsoft.com/kb/3031135)
- Desative o serviço IPSec antes de executar o failover.

**Na VM do Azure após o failover**:

- Adicione um ponto de extremidade público para o protocolo RDP (porta 3389) e especifique as credenciais de logon.
- Verifique se não há nenhuma política de domínio que o impeça de se conectar a uma máquina virtual usando um endereço público.
- Tente se conectar. Se você não puder se conectar, verifique se a VM está em execução. Para obter mais dicas de solução de problemas, leia este [artigo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


Se você quiser acessar uma VM do Azure que esteja executando o Linux após o failover usando um cliente do Secure Shell (ssh), faça o seguinte:

**No computador local antes do failover**:

- Verifique se o serviço Secure Shell na VM do Azure está definido para iniciar automaticamente na inicialização do sistema.
- Verifique se as regras de firewall permitem uma conexão SSH com ele.

**Na VM do Azure após o failover**:

- As regras de grupo de segurança de rede na VM com failover e a sub-rede do Azure à qual ela está conectada precisam permitir conexões de entrada para a porta SSH.
- Um ponto de extremidade público deve ser criado para permitir conexões de entrada na porta SSH (porta TCP 22, por padrão).
- Se a VM for acessada por meio de uma conexão VPN (Rota Expressa ou VPN site a site), então o cliente poderá ser usado para se conectar diretamente à VM via SSH.

**Na VM do Windows/Linux do Azure após o failover**:

Se você tiver um Grupo de Segurança de Rede associado à Máquina Virtual ou à sub-rede à qual o computador pertence, certifique-se de que o Grupo de Segurança de Rede tenha uma regra de saída para permitir HTTP/HTTPS. Além disso, certifique-se de que o DNS da rede para a qual a máquina virtual está obtendo failover esteja configurado corretamente. Caso contrário, o failover poderia atingir o tempo limite com o erro -'WaitForScriptExecutionTask da tarefa PreFailoverWorkflow atingiu o tempo limite'. Para compreender isso em detalhes, consulte a seção Recuperação no [Guia de monitoramento e de solução de problemas](site-recovery-monitoring-and-troubleshooting.md#recovery).

## Execute um teste de failover

1. Para fazer failover em um único computador, em **Configurações** > **Itens Replicados**, clique na VM e depois selecione o ícone **+Failover de Teste**.

	![Failover de teste](./media/site-recovery-vmware-to-azure/test-failover1.png)

2. Para fazer failover de um plano de recuperação, em **Configurações** > **Planos de Recuperação**, clique com o botão direito do mouse no plano > **Failover de Teste**. Para criar um plano de recuperação, [siga estas instruções](site-recovery-create-recovery-plans.md).

3. Em **Failover de Teste**, selecione a rede do Azure à qual as VMs do Azure serão conectadas após o failover.
4. Clique em **OK** para iniciar o failover. Você pode acompanhar o andamento clicando na VM para abrir suas propriedades ou no trabalho **Failover de Teste** no nome do cofre **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**.
5. Quando o failover atingir o status **Concluir teste**, faça o seguinte:

	1. Visualize a máquina virtual de réplica no portal do Azure. Verifique se a máquina virtual foi iniciada com êxito.
	2. Se tiver configurado para máquinas virtuais de acesso a rede local, você pode iniciar uma conexão de área de trabalho remota para a máquina virtual.
	3. Clique em **Concluir teste** para concluí-lo.

		![Failover de teste](./media/site-recovery-vmware-to-azure/test-failover6.png)


	4. Clique em **Observações** para gravar e salvar observações associadas ao failover de teste.
	5. Clique em **O failover de teste está concluído** para limpar automaticamente o ambiente de teste. Depois de isso ser feito, o failover de teste mostrará o status **Concluído**.
	6.  Neste ponto, todos os elementos ou máquinas virtuais criadas automaticamente pela Recuperação de Site durante o teste de failover são excluídos. Quaisquer elementos adicionais que você criou para o failover de teste não serão excluídos.

	> [AZURE.NOTE] Se um failover de teste continuar por mais de duas semanas, ele será concluído à força.


6. Após a conclusão do failover, você também deve ver a réplica do computador do Azure no portal do Azure > **Máquinas Virtuais**. Verifique se a VM é do tamanho apropriado, se está conectada à rede adequada e se está em execução.
7. Se você tiver se [preparado para conexões após o failover](#prepare-to-connect-to-azure-vms-after-failover), deverá ser capaz de se conectar à VM do Azure.

## Monitorar a implantação

Veja como você pode monitorar as definições de configuração, o status e a integridade para a implantação da Recuperação de Site:

1. Clique no nome do cofre para acessar o painel **Conceitos básicos**. Neste painel, você pode ver os trabalhos da Recuperação de Site, o status da replicação, os planos de recuperação, a integridade do servidor e os eventos. Você pode personalizar os Conceitos básicos para mostrar os blocos e os layouts mais úteis, incluindo o status de outros cofres da Recuperação de Site e do Backup.

![Conceitos básicos](./media/site-recovery-vmware-to-azure/essentials.png)

2. No bloco **Integridade**, você pode monitorar os servidores de site (servidores do VMM ou de configuração) que estejam enfrentando o problema, além dos eventos gerados pela Recuperação de Site nas últimas 24 horas.
3. Você pode gerenciar e monitorar a replicação nos blocos **Itens Replicados**, **Planos de Recuperação** e **Trabalhos de Recuperação de Site**. Você pode analisar detalhadamente os trabalhos em **Configurações** -> **Trabalhos** -> **Trabalhos de Recuperação de Site**.


## Implantar servidores de processo adicionais

Se tiver que escalar horizontalmente sua implantação para mais de 200 computadores de origem ou uma taxa diária de rotatividade total de mais de 2 TB, você precisará de servidores de processo adicionais para tratar do volume de tráfego.

Verifique as [recomendações de tamanho para servidores de processo](#size-recommendations-for-the-process-server) e, em seguida, siga estas instruções para configurar o servidor de processo. Depois de configurar o servidor, você migrará os computadores de origem para usá-lo.

### Instalar um servidor de processo adicional

1. Em **Configurações** > **Servidores de Recuperação de Site** clique no servidor de configuração > **Servidor de processo**.

	![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps1.png)

2. Em **Tipo de Servidor**, clique em **Servidor de processo (local)**.

	![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

3. Baixe o arquivo de Configuração Unificada de Recuperação de Site e execute-o para instalar o servidor de processo e registrá-lo no cofre.
4. Em **Antes de começar**, selecione **Add additional process servers to scale out deployment (Adicionar servidores de processo adicionais para escalar horizontalmente a implantação)**.
5. Conclua o assistente da mesma forma que fez ao [configurar](#step-2-set-up-the-source-environment) o servidor de configuração.

	![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure/add-ps1.png)

6. Em **Detalhes do Servidor de Configuração**, especifique o endereço IP do servidor de configuração e a frase secreta. Para obter a frase secreta, execute: **<PastaDeInstalaçãoDaRecuperaçãoDeSite>\\home\\sysystems\\bin\\genpassphrase.exe –n** no servidor de configuração.

	![Adicionar servidor de processo](./media/site-recovery-vmware-to-azure/add-ps2.png)

### Migrar computadores para usar o novo servidor de processo

1. Em **Configurações** > **Servidores de Recuperação de Site** clique no servidor de configuração e expanda o **Servidor de processo**.

	![Atualizar servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

2. Clique com o botão direito do mouse no servidor de processo atualmente em uso, e clique em **Alternar**.

	![Atualizar servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps3.png)

3. Em **Selecionar servidor de processo de destino**, selecione o novo servidor de processo que você deseja usar e, em seguida, selecione as máquinas virtuais que serão tratadas pelo novo servidor de processo. Clique no ícone de informações para obter informações sobre o servidor. Para ajudá-lo a tomar decisões de carregamento, o espaço médio necessário para replicar cada máquina virtual selecionada no novo servidor de processo será exibido. Clique na marca de seleção para começar a replicar no novo servidor de processo.

## Permissões de conta do VMware

O servidor de processo pode descobrir as VMs em um servidor vCenter automaticamente. Para executar a descoberta automática, você precisará [definir uma função (Azure\_Site\_Recovery)](#prepare-an-account-for-automatic-discovery) para permitir que a Recuperação de Site acesse o servidor VMware. Observe que se você só precisa migrar máquinas virtuais VMware para o Azure e não precisa do failback do Azure, pode definir uma função somente leitura que seja suficiente. As permissões de função necessárias estão resumidas na tabela a seguir.

**Função** | **Detalhes** | **Permissões**
--- | --- | ---
Função Azure\_Site\_Recovery | Descoberta de máquina virtual VMware |Atribua estes privilégios ao servidor v-Center:<br/><br/>Armazenamento de Dados -> Alocar espaço, Procurar armazenamento de dados, Operações de arquivo de nível baixo, Remover arquivo, Atualizar arquivos da máquina virtual<br/><br/>Rede -> Atribuir rede<br/><br/>Recurso -> Atribuir máquina virtual ao pool de recursos, Migrar máquina virtual desligada, Migrar máquina virtual ligada<br/><br/>Tarefas -> Criar tarefa, atualizar tarefa<br/><br/>Máquina virtual -> Configuração<br/><br/>Máquina virtual -> Interagir -> Responder a pergunta, Conexão de dispositivos, Configurar mídia de CD, Configurar mídia de disquete, Desligar, Ligar, Instalação de ferramentas do VMware<br/><br/>Máquina virtual -> Inventário -> Criar, Registrar, Desfazer registro<br/><br/>Máquina virtual -> Provisionamento -> Permitir download da máquina virtual, Permitir upload de arquivos da máquina virtual<br/><br/>Máquina virtual -> Instantâneos -> Remover instantâneos
Função de usuário do vCenter | Descoberta de máquina virtual VMware/Failover sem o desligamento da VM de origem | Atribua estes privilégios ao servidor do v-Center:<br/><br/>Objeto do Datacenter -> Propagar para Objeto Filho, função=Somente leitura <br/><br/>O usuário é atribuído no nível do datacenter e, portanto, tem acesso a todos os objetos no datacenter. Se você quiser restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para objeto filho** aos objetos filho (hosts vSphere, repositório de dados, VMs e redes).
Função de usuário do vCenter | Failover e failback | Atribua estes privilégios ao servidor do v-Center:<br/><br/>Objeto do datacenter – Propagar para objeto filho, função=Azure\_Site\_Recovery<br/><br/>O usuário é atribuído no nível do datacenter e, portanto, tem acesso a todos os objetos no datacenter. Se você quiser restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para objeto filho** aos objetos filho (hosts vSphere, repositório de dados, VMs e redes).  
## Próximas etapas

- [Saiba mais](site-recovery-failover.md) sobre os diferentes tipos de failover.
- [Saiba mais sobre o failback](site-recovery-failback-azure-to-vmware.md) para recolocar seus computadores com failover em execução no Azure no ambiente local.

## Avisos e informações de softwares de terceiros

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”). Microsoft is the not original author of the Third Party Code. The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

<!---HONumber=AcomDC_0831_2016-->