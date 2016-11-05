---
title: Alta Disponibilidade e Recuperação de Desastres para SQL Server | Microsoft Docs
description: Uma discussão sobre os diversos tipos de estratégias HADR do SQL Server em execução em máquinas virtuais do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/12/2016
ms.author: MikeRayMSFT

---
# Alta disponibilidade e recuperação de desastre para SQL Server nas Máquinas Virtuais do Azure
## Visão geral
VMs (Máquinas virtuais) do Microsoft Azure com SQL Server podem ajudar a reduzir o custo de uma solução de banco de dados HADR (Alta Disponibilidade e Recuperação de Desastre). A maioria das soluções HADR do SQL Server tem suporte em máquinas virtuais do Azure, tanto como somente Azure ou como soluções híbridas. Em uma solução somente Azure, todo o sistema HADR é executado no Azure. Em uma configuração híbrida, parte da solução é executada no Azure e a outra parte é executada localmente em sua organização. A flexibilidade do ambiente do Azure permite que você se mova parcial ou completamente para o Azure para atender aos requisitos de orçamento e HADR de seus sistemas de banco de dados do SQL Server.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Compreendendo a necessidade de uma solução HADR
Cabe a você garantir que seu sistema de banco de dados possua os recursos HADR que o SLA (Contrato de Nível de Serviço) exige. O fato de que o Azure fornece mecanismos de alta disponibilidade, como recuperação de serviço para serviços de nuvem e detecção de recuperação de falhas para máquinas virtuais, não é garantia de que você possa atender ao SLA desejado. Esses mecanismos protegem a alta disponibilidade das VMs, mas não a alta disponibilidade do SQL Server em execução nas VMs. É possível que a instância do SQL Server falhe enquanto a VM estiver online e íntegra. Além disso, até mesmo os mecanismos de alta disponibilidade fornecidos pelo Azure permitem tempo de inatividade das VMs em razão de eventos como recuperação de software ou falhas de hardware e atualizações do sistema operacional.

Além disso, o GRS (Armazenamento com Redundância Geográfica) no Azure, que é implementado com um recurso chamado replicação geográfica, pode não ser uma solução de recuperação de desastres adequada para seus bancos de dados. Como a replicação geográfica envia dados de forma assíncrona, atualizações recentes podem ser perdidas no caso de desastre. Mais informações sobre limitações de replicação geográfica são abordadas na seção [Replicação geográfica sem suporte para arquivos de dados e log em discos separados](#geo-replication-support).

## Arquiteturas de implantação de HADR
As tecnologias HADR do SQL Server que têm suporte no Azure incluem:

* [Grupos de disponibilidade AlwaysOn](https://technet.microsoft.com/library/hh510230.aspx)
* [Espelhamento de banco de dados](https://technet.microsoft.com/library/ms189852.aspx)
* [Envio de logs](https://technet.microsoft.com/library/ms187103.aspx)
* [Backup e restauração com o Serviço de armazenamento de blob do Azure](https://msdn.microsoft.com/library/jj919148.aspx)
* [Instâncias do cluster de failover AlwaysOn](https://technet.microsoft.com/library/ms189134.aspx)

É possível combinar as tecnologias para implementar uma solução SQL Server com alta disponibilidade e recursos de recuperação de desastre. Dependendo da tecnologia usada, uma implantação híbrida pode exigir um túnel VPN com a rede virtual do Azure. As seções a seguir mostram algumas das arquiteturas de implantação de exemplo.

## Somente Azure: soluções de alta disponibilidade
Você pode ter uma solução de alta disponibilidade para seus bancos de dados do SQL Server no Azure usando grupos de disponibilidade AlwaysOn ou espelhamento de banco de dados.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade AlwaysOn** |Todas as réplicas de disponibilidade executadas em VMs do Azure para alta disponibilidade na mesma região. Você precisa configurar uma VM de controlador de domínio, porque o WSFC (Windows Server Failover Clustering) requer um domínio do Active Directory.<br/> ![Grupos de disponibilidade AlwaysOn](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Para obter mais informações, consulte [Configure Always On Availability Groups in Azure (GUI)](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) (Configurar grupos de disponibilidade AlwaysOn no Azure (GUI)). |
| **Instâncias do cluster de failover AlwaysOn** |FCI (Instâncias de Cluster de Failover), que exigem armazenamento compartilhado, podem ser criadas de duas maneiras diferentes.<br/><br/>1. Uma FCI em um WSFC de dois nós em execução nas VMs do Azure com armazenamento que dá suporte a uma solução de cluster de terceiros. Para obter um exemplo específico que usa o SIOS DataKeeper, veja [Alta disponibilidade para um compartilhamento de arquivos usando o WSFC e o software de terceiros SIOS Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>2. Uma FCI em um WSFC de dois nós em execução em VMs do Azure com armazenamento em um bloco compartilhado de destino iSCSI por meio da Rota Expressa. Por exemplo, o NPS (Armazenamento Privado do NetApp) expõe um destino iSCSI por meio da Rota Expressa com o Equinix para VMs do Azure.<br/><br/>Para obter soluções de armazenamento compartilhado e replicação de dados de terceiros, entre em contato com o fornecedor para tratar problemas relacionados ao acesso a dados no failover.<br/><br/>Observe que ainda não há suporte para o uso do FCI no [Armazenamento de arquivos do Azure](https://azure.microsoft.com/services/storage/files/), pois esta solução não utiliza o Armazenamento Premium. Estamos trabalhando para dar suporte a isso em breve. |

## Somente Azure: soluções de recuperação de desastre
Você pode ter uma solução de recuperação de desastre para seus bancos de dados do SQL Server no Azure usando grupos de disponibilidade AlwaysOn, espelhamento de banco de dados ou backup e restauração com blobs de armazenamento.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade AlwaysOn** |Réplicas de disponibilidade executadas em vários datacenters em VMs do Azure para recuperação de desastres. Essa solução de regiões cruzadas protege contra interrupção completa de site. <br/> ![Grupos de disponibilidade AlwaysOn](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>Dentro de uma região, todas as réplicas devem estar dentro do mesmo serviço de nuvem e na mesma VNet. Como cada região terá uma VNet separada, essas soluções necessitam de VNet para conectividade VNet. Para obter mais informações, consulte [Configurar uma VPN site a site no portal clássico do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md). |
| **Espelhamento de banco de dados** |Servidores principal e de espelho em execução em diferentes datacenters para recuperação de desastres. Você deve implantar usando certificados de servidor, pois um domínio do Active Directory não pode abranger vários datacenters.<br/>![Espelhamento de banco de dados](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Backup e restauração com o Serviço de armazenamento de blob do Azure** |Bancos de dados de produção com backup direto no armazenamento de blob em um datacenter diferente para recuperação de desastre.<br/>![Backup e restauração](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Para obter mais informações, consulte [Backup e Restauração para SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md). |

## TI híbrida: soluções de recuperação de desastre
Você pode ter uma solução de recuperação de desastre para seus bancos de dados do SQL Server em um ambiente de TI híbrida usando grupos de disponibilidade AlwaysOn, espelhamento de banco de dados, envio de log e backup e restauração com o armazenamento de blobs do Azure.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade AlwaysOn** |Algumas réplicas de disponibilidade executadas em VMs do Azure e outras réplicas executadas localmente para recuperação de desastres intersite. O site de produção pode ser local ou em um datacenter do Azure.<br/>![Grupos de disponibilidade AlwaysOn](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Como todas as réplicas de disponibilidade devem estar no mesmo cluster WSFC, o cluster WSFC deve abranger as duas redes (um cluster WSFC de várias sub-redes). Essa configuração requer uma conexão VPN entre o Azure e a rede local.<br/><br/>Para a recuperação de desastres bem-sucedida de seus bancos de dados, você também deve instalar um controlador de domínio de réplica no site de recuperação de desastres.<br/><br/>É possível usar o Assistente de Adição de Réplica no SSMS para adicionar uma réplica do Azure a um grupo de disponibilidade AlwaysOn existente. Para obter mais informações, consulte o Tutorial: estender seu grupo de disponibilidade AlwaysOn para o Azure. |
| **Espelhamento de banco de dados** |Um parceiro executado em uma VM do Azure e o outro executado localmente para recuperação de desastres intersite usando certificados de servidor. Os parceiros não precisam estar no mesmo domínio do Active Directory e nenhuma conexão VPN é necessária.<br/>![Espelhamento de banco de dados](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Outro cenário que o espelhamento de banco de dados envolve é um parceiro em execução em uma VM do Azure e o outro em execução localmente no mesmo domínio do Active Directory para recuperação de desastres intersite. É necessária uma [conexão VPN entre a rede virtual do Azure e a rede local](../vpn-gateway/vpn-gateway-site-to-site-create.md).<br/><br/>Para recuperação de desastres bem-sucedida de seus bancos de dados, você também deve instalar um controlador de domínio de réplica no local da recuperação de desastres. |
| **Envio de logs** |Um servidor em execução em uma VM do Azure e outro em execução local para recuperação de desastre intersite. O envio de log depende do compartilhamento de arquivos do Windows, assim, uma conexão VPN entre a rede virtual do Azure e a rede local é necessária.<br/>![Envio de logs](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Para recuperação de desastre bem-sucedida de seus bancos de dados, você também deve instalar um controlador de domínio de réplica no site de recuperação de desastres. |
| **Backup e restauração com o Serviço de armazenamento de blob do Azure** |Bancos de dados de produção local com backup diretamente no armazenamento de blob do Azure para recuperação de desastres.<br/>![Backup e restauração](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Para obter mais informações, consulte [Backup e Restauração do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md). |

## Considerações importantes para HADR do SQL Server no Azure
VMs do Azure, armazenamento e rede têm características operacionais diferentes da infraestrutura de TI local, não virtualizada. Uma implementação bem-sucedida de uma solução HADR SQL Server no Azure requer que você compreenda essas diferenças e crie sua solução para acomodá-las.

### Nós de alta disponibilidade em um conjunto de disponibilidade
Conjuntos de disponibilidade no Azure permitem que você coloque os nós de alta disponibilidade em FDs (Domínios de Falha) e UDs (Domínios de Atualização) separados. Para que VMs do Azure sejam colocadas no mesmo conjunto de disponibilidade, você deve implantá-las no mesmo serviço de nuvem. Somente nós no mesmo serviço de nuvem podem participar do mesmo conjunto de disponibilidade. Para obter mais informações, consulte [Gerenciar a Disponibilidade de Máquinas Virtuais](virtual-machines-windows-manage-availability.md).

### Comportamento do cluster WSFC no sistema de rede do Azure
O serviço DHCP não compatível com RFC no Azure pode causar falha na criação de certas configurações do cluster WSFC porque um endereço IP duplicado está sendo atribuído ao nome da rede de cluster, assim como o mesmo endereço IP de um dos nós do cluster. Isso é um problema quando você implementa grupos de disponibilidade AlwaysOn que dependem do recurso WSFC.

Considere o cenário onde um cluster de dois nós é criado e colocado online:

1. O cluster fica online e NODE1 solicita um endereço IP atribuído dinamicamente ao nome de rede de cluster.
2. Nenhum endereço IP que não seja o próprio endereço IP do NODE1 é fornecido pelo serviço DHCP, já que o serviço DHCP reconhece que a solicitação vem do próprio NODE1.
3. O Windows detecta que um endereço duplicado é atribuído a NODE1 e ao nome da rede de cluster, e o grupo de clusters padrão não fica online.
4. O grupo de clusters padrão é movido para o NODE2, que trata o endereço IP do NODE1 como o endereço IP do cluster e coloca o grupo de clusters padrão online.
5. Quando NODE2 tenta estabelecer conectividade com NODE1, pacotes direcionados ao NODE1 nunca deixam o NODE2, pois ele resolve o endereço IP do NODE1 para si mesmo. O NODE2 não pode estabelecer conectividade com NODE1, perde quorum e fecha o cluster.
6. Enquanto isso, o NODE1 pode enviar pacotes para o NODE2, mas NODE2 não pode responder. O NODE1 perde quorum e fecha o cluster.

Esse cenário pode ser evitado atribuindo um estático endereço IP não usado, como um endereço IP de link local como 169.254.1.1, ao nome da rede de cluster para colocar o nome de rede do cluster online. Para simplificar esse processo, consulte [Configurando o cluster de failover do Windows no Azure para grupos de disponibilidade AlwaysOn](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Para obter mais informações, consulte [Configure Always On Availability Groups in Azure (GUI)](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) (Configurar grupos de disponibilidade AlwaysOn no Azure (GUI)).

### Suporte do ouvinte do grupo de disponibilidade
Ouvintes do grupo de disponibilidade têm suporte em VMs do Azure que executam o Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Esse suporte é possibilitado pelo uso de pontos de extremidade habilitados em VMs do Azure, que são nós do grupo de disponibilidade. Você deve seguir etapas especiais de configuração para que os ouvintes trabalhem para ambos os aplicativos clientes que estão em execução no Azure, bem como os que estão em execução local.

Há duas opções principais para configurar o ouvinte: externo (público) ou interno. O ouvinte (público) externo é associado a um IP Virtual público (VIP) que seja acessível pela Internet. Com um ouvinte externo, você deve habilitar o retorno de servidor direto, o que significa que você deve se conectar ao ouvinte de um computador que não está no mesmo serviço de nuvem que os nós do grupo de disponibilidade AlwaysOn. A outra opção é um ouvinte interno que usa o balanceador de carga interno (ILB). Um ouvinte interno só oferece suporte a clientes na mesma rede virtual.

Se o Grupo de disponibilidade abranger várias sub-redes do Azure (como uma implantação que cruza regiões do Azure), a cadeia de conexão do cliente deve incluir "**MultisubnetFailover=True**". Isso resulta em tentativas de conexão em paralelo às réplicas nas diferentes sub-redes. Para obter instruções sobre como configurar um ouvinte, consulte

* [Configurar um ouvinte de ILB para grupos de disponibilidade AlwaysOn no Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).
* [Configurar um ouvinte externo para grupos de disponibilidade AlwaysOn no Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

Você pode ainda se conectar a cada réplica de disponibilidade separadamente conectando-se diretamente à instância do serviço. Além disso, como os grupos de disponibilidade AlwaysOn são compatíveis com versões anteriores com clientes de espelhamento de banco de dados, você pode se conectar a réplicas de disponibilidade, como parceiros de espelhamento, desde que as réplicas sejam configuradas de forma semelhante ao espelhamento do banco de dados:

* Uma réplica primária e uma réplica secundária
* A réplica secundária é configurada como ilegível (**opção Secundária Legível** definida como **Não**)

Um exemplo de cadeia de conexão de cliente que corresponde à configuração deste espelhamento de banco de dados usando ADO.NET ou o SQL Server Native Client é ilustrado abaixo:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Para obter mais informações sobre conectividade de cliente, consulte:

* [Usando palavras-chave da cadeia de conexão com o SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Conectar clientes a uma sessão de espelhamento de banco de dados (SQL Server) ](https://technet.microsoft.com/library/ms175484.aspx)
* [Conectando-se ao ouvinte do grupo de disponibilidade em TI híbrida](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Ouvintes de grupo de disponibilidade, conectividade de cliente e failover de aplicativo (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Usando cadeias de conexão de espelhamento de banco de dados com grupos de disponibilidade](https://technet.microsoft.com/library/hh213417.aspx)

### Latência de rede em TI híbrida
Você deve implantar sua solução HADR com a suposição de que pode haver períodos com alta latência da rede entre sua rede local e o Azure. Ao implantar réplicas do Azure, você deve usar confirmação assíncrona, em vez de confirmação síncrona, para o modo de sincronização. Ao implantar servidores de espelhamento de banco de dados localmente e no Azure, use o modo de alto desempenho, em vez do modo de alta segurança.

### Suporte para replicação geográfica
A replicação geográfica em discos do Azure não dá suporte ao arquivo de dados e ao arquivo de log do mesmo banco de dados a ser armazenado em discos separados. GRS replica as alterações em cada disco, de forma independente e assíncrona. Esse mecanismo garante a ordem de gravação em um único disco na cópia replicada geograficamente, mas não em cópias replicadas geograficamente de vários discos. Se você configurar um banco de dados para armazenar arquivo de dados e arquivo de log em discos separados, os discos recuperados após um desastre poderão conter uma cópia mais recente do arquivo de dados do que o arquivo de log, interrompendo o log write-ahead no SQL Server e as propriedades ACID das transações. Se você não tiver a opção de desabilitar a replicação geográfica na conta de armazenamento, deverá manter todos os dados e arquivos de log em determinado banco de dados no mesmo disco. Se você precisar usar mais de um disco devido ao tamanho do banco de dados, terá de implantar uma das soluções de recuperação de desastres listadas anteriormente para garantir a redundância dos dados.

## Próximas etapas
Se você precisar criar uma máquina virtual do Azure com SQL Server, consulte [Provisionando uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para obter o melhor desempenho do SQL Server em execução em uma VM do Azure, consulte as diretrizes em [Práticas Recomentadas para o Desempenho do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-performance.md).

Para outros tópicos relacionados à execução do SQL Server em VMs do Azure, consulte [SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

### Outros recursos
* [Instalar uma nova floresta do Active Directory no Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
* [Create WSFC Cluster for Always On Availability Groups in Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) (Criar cluster WSFC para grupos de disponibilidade AlwaysOn em uma VM do Azure)

<!---HONumber=AcomDC_0713_2016-->