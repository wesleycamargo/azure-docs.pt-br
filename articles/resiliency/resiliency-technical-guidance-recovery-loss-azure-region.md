---
title: "Diretrizes técnicas de resiliência para recuperação de perda de uma região do Azure | Microsoft Docs"
description: "Entender e criar aplicativos flexíveis, altamente disponíveis e tolerante a falhas e planejar a recuperação de desastres."
services: 
documentationcenter: na
author: adamglick
manager: saladki
editor: 
ms.assetid: f2f750aa-9305-487e-8c3f-1f8fbc06dc47
ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick
translationtype: Human Translation
ms.sourcegitcommit: 65fe1b29b55b58a160c8c1a3baed117530040c71
ms.openlocfilehash: 4d98ff207e08184fffd6dbfa93a018c1e208fa95
ms.lasthandoff: 02/17/2017


---
# <a name="azure-resiliency-technical-guidance-recovery-from-a-region-wide-service-disruption"></a>Orientações técnicas de resiliência do Azure: recuperação de uma interrupção de serviço em toda a região
O Azure é dividido fisicamente e logicamente em unidades chamadas de regiões. Uma região consiste em um ou mais datacenters próximos. Para obter a lista atual de regiões, confira a [página Regiões do Azure](https://azure.microsoft.com/regions/).

Em raras circunstâncias é possível que instalações em toda a região se tornem inacessíveis, por exemplo, devido a falhas de rede. As instalações também podem ser inteiramente perdidas, por exemplo, devido a um desastre natural. Este artigo explica os recursos do Azure para criar aplicativos que são distribuídos entre regiões. Esse tipo de distribuição ajuda a minimizar a possibilidade de que uma falha em uma região possa afetar outras regiões.

## <a name="cloud-services"></a>Serviços de Nuvem
### <a name="resource-management"></a>Gerenciamento de recursos
Você pode distribuir as instâncias de computação entre regiões criando um serviço de nuvem separado em cada região de destino e publicando o pacote de implantação em cada serviço de nuvem. No entanto, a distribuição de tráfego pelos serviços de nuvem em diferentes regiões deve ser implementada pelo desenvolvedor do aplicativo ou com um serviço de gerenciamento de tráfego.

Determinar o número de instâncias de função de reposição a implantar com antecedência para recuperação de desastre é um aspecto importante do planejamento de capacidade. Ter uma implantação secundária completa garante que a capacidade já estará disponível quando necessário. No entanto, isso efetivamente dobra o custo. Um padrão comum é ter uma implantação secundária pequena, mas grande o suficiente para executar serviços críticos. Essa implantação secundária pequena é uma boa ideia, para reservar capacidade e para testar a configuração do ambiente secundário.

> [!NOTE]
> A cota da assinatura não é uma garantia de capacidade. A cota é simplesmente um limite de crédito. Para garantir a capacidade, o número necessário de funções deve ser definido no modelo de serviço, e as funções devem ser implantadas.
>
>

### <a name="load-balancing"></a>Balanceamento de carga
Balancear a carga de tráfego entre regiões requer uma solução de gerenciamento de tráfego. O Azure oferece o [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/). Você também pode tirar proveito dos serviços de terceiros que fornecem recursos de gerenciamento de tráfego semelhantes.

### <a name="strategies"></a>Estratégias
Muitas estratégias alternativas estão disponíveis para implementação de computação distribuída entre regiões. Essas estratégias devem ser personalizadas segundo os requisitos específicos de negócios e as circunstâncias do aplicativo. Em um nível elevado, as abordagens podem ser divididas nas seguintes categorias:

* **Reimplantação em caso de desastre**. Nessa abordagem, o aplicativo é reimplantado do zero no momento do desastre. Essa reimplementação é adequada para aplicativos não críticos, que não exigem um tempo de recuperação garantido.
* **Reposição morna (ativo/passivo)**. Um serviço hospedado secundário é criado em uma região alternativa e funções são implantadas para garantir a capacidade mínima. No entanto, as funções não recebem tráfego de produção. Essa abordagem é útil para aplicativos que não foram projetados para distribuir tráfego entre regiões.
* **Sobressalente (ativa/ativa)**. O aplicativo foi projetado para receber a carga de produção em várias regiões. Os serviços de nuvem em cada região podem ser configurados para capacidade maior do que o necessário para fins de recuperação de desastre. Como alternativa, os serviços de nuvem podem escalar horizontalmente conforme o necessário no momento de um desastre e failover. Essa abordagem exige um investimento substancial no design do aplicativo, mas tem benefícios significativos. Isso inclui tempo de recuperação baixo e garantido, testes contínuos de todos os locais de recuperação e uso eficiente da capacidade.

Uma discussão completa sobre design distribuído está fora do escopo deste artigo. Para saber mais, confira [Recuperação de desastres e alta disponibilidade para aplicativos criados no Microsoft Azure](https://aka.ms/drtechguide).

## <a name="virtual-machines"></a>Máquinas virtuais
A recuperação de VMs (máquinas virtuais) de IaaS (infraestrutura como um serviço) é semelhante à recuperação de computação de PaaS (plataforma como serviço) em muitos aspectos. No entanto, há diferenças importantes, pois uma VM IaaS consiste na VM e no disco de VM.

* **Use o Backup do Azure para criar backups entre regiões consistentes com aplicativos**.
  [Backup do Azure](https://azure.microsoft.com/services/backup/) habilita os clientes a criar backups consistentes com aplicativos em vários discos de VM e dar suporte à replicação de backups entre regiões. Você realiza esta tarefa ao optar por replicar geograficamente o cofre de backup no momento da criação. A replicação do cofre de backup deve ser configurada no momento da criação. Ele não pode ser definido mais tarde. Se uma região for perdida, a Microsoft disponibilizará os backups para os clientes. Os clientes podem restaurar para qualquer um dos seus pontos de restauração configurados.
* **Separar os discos de dados do disco do sistema operacional**. Uma consideração importante para VMs IaaS é que você não pode alterar o disco do sistema operacional sem recriar a VM. Esse processo não será um problema se sua estratégia de recuperação for a de reimplantar após desastre. No entanto, poderá ser um problema se você estiver usando a abordagem de reposição morna para capacidade reserva. Para implementar adequadamente, você deve ter o disco de sistema operacional correto implantado nos locais primário e secundário, e os dados de aplicativos devem ser armazenados em uma unidade separada. Se possível, use uma configuração de sistema operacional padrão, que possa ser fornecida em ambos os locais. Após um failover, você deve anexar a unidade de dados às VMs IaaS existentes no controlador de domínio secundário. Use o AzCopy para copiar instantâneos de cada disco de dados para um site remoto.
* **Esteja ciente dos possíveis problemas de consistência após um failover geográfico de vários discos de VM**. Cada disco VM é implementado como uma instância de armazenamento de BLOBs do Azure, e cada disco tem as mesmas características de replicação geográfica. A menos que o [Backup do Azure](https://azure.microsoft.com/services/backup/) seja usado, não há garantia de consistência entre discos, pois a replicação geográfica é assíncrona e é replicada de maneira independente. Discos de VM individuais têm a garantia de estar em um estado com controle de falhas após um failover geográfico, mas não consistente entre discos. Isso pode causar problemas em alguns casos (por exemplo, no caso de divisão entre discos).

## <a name="storage"></a>Armazenamento
### <a name="recovery-by-using-geo-redundant-storage-of-blob-table-queue-and-vm-disk-storage"></a>Recuperação usando armazenamento com redundância geográfica de blob, tabela, fila e armazenamento em disco de VM
Em blobs do Azure, tabelas, filas e discos de VM são todos replicados geograficamente por padrão. Isso é conhecido como armazenamento com redundância geográfica. O armazenamento com redundância geográfica replica dados de armazenamento para um datacenter emparelhado a centenas de milhas de distância dentro de uma região geográfica específica. O armazenamento com redundância geográfica é destinado a fornecer durabilidade adicional caso ocorra um desastre de grandes proporções no datacenter. A Microsoft controla quando ocorre o failover, que se limita a grandes desastres em que o local principal de origem é considerado irrecuperável em um período de tempo razoável. Em alguns cenários, isso pode consistir em vários dias. Os dados normalmente são replicados em poucos minutos, embora o intervalo de sincronização ainda não seja coberto por um contrato de nível de serviço.

Se ocorrer um failover geográfico, não haverá alteração na maneira como a conta é acessada (a URL e a chave da conta não serão alteradas). No entanto, a conta de armazenamento estará em uma região diferente após o failover. Isso pode afetar aplicativos que exigem afinidade regional com sua conta de armazenamento. Mesmo para serviços e aplicativos que não exigem uma conta de armazenamento no mesmo datacenter, os encargos de largura de banda e a latência entre datacenters podem ser motivos convincentes para mover temporariamente o tráfego para a região de failover. Isso pode influenciar uma estratégia geral de recuperação de desastre.

Além do failover automático fornecido pelo armazenamento com redundância geográfica, o Azure introduziu um serviço que fornece a você acesso de leitura à cópia dos dados no local de armazenamento secundário. Isso é chamado de armazenamento com redundância geográfica com acesso de leitura.

Para saber mais sobre o armazenamento com redundância geográfica e o armazenamento com redundância geográfica de acesso de leitura, veja [Replicação do Armazenamento do Azure](../storage/storage-redundancy.md).

### <a name="geo-replication-region-mappings"></a>Mapeamentos de região de replicação geográfica
É importante saber onde os dados são replicados geograficamente para saber onde implantar as outras instâncias de dados que exigem afinidade regional com o armazenamento. A tabela a seguir mostra os emparelhamentos de locais primários e secundários.

[!INCLUDE [paired-region-list](../../includes/paired-region-list.md)]

### <a name="geo-replication-pricing"></a>Preços da replicação geográfica
A replicação geográfica está incluída no preço atual do Armazenamento do Azure, como armazenamento com redundância geográfica. Se não quiser que os dados sejam replicados geograficamente, você poderá desabilitar a replicação geográfica para sua conta. Isso é chamado de armazenamento com redundância local e é cobrado com um preço com desconto em comparação com o armazenamento com redundância geográfica.

### <a name="determine-if-a-geo-failover-occurred"></a>Determinar se ocorreu um failover geográfico
Se ocorrer um failover geográfico, isso será postado no [Painel de Integridade de Serviço do Azure](https://azure.microsoft.com/status/). No entanto, os aplicativos podem implementar um meio automatizado de detectar a falha monitorando a região geográfica da conta de armazenamento. Essa detecção automatizada pode ser usado para disparar outras operações de recuperação, como a ativação de recursos de computação na região geográfica para a qual o armazenamento foi movido. Você pode executar uma consulta para esse evento usando a API de gerenciamento de serviços, usando [Obter Propriedades de Conta de Armazenamento](https://msdn.microsoft.com/library/ee460802.aspx). As propriedades relevantes são:

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

### <a name="vm-disks-and-geo-failover"></a>Discos de VM e failover geográfico
Conforme discutido anteriormente na seção "Máquinas Virtuais" deste artigo, há nenhuma garantia de consistência dos dados em discos VM após um failover. Para garantir a exatidão de backups, um produto de backup como o System Center Data Protection Manager deve ser usado para fazer backup de dados de aplicativos e restaurá-los.

## <a name="database"></a>Banco de dados
### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure
O Banco de Dados SQL do Azure fornece dois tipos de recuperação: Restauração Geográfica e Replicação Geográfica Ativa.

#### <a name="geo-restore"></a>Restauração geográfica
[Restauração geográfica](../sql-database/sql-database-recovery-using-backups.md#geo-restore) também está disponível para bancos de dados Basic, Standard e Premium. Ele fornece a opção de recuperação padrão quando o banco de dados está indisponível devido a um incidente na região em que o banco de dados está hospedado. De forma semelhante à Recuperação Pontual, a Restauração Geográfica conta com backups de banco de dados no Armazenamento do Azure com redundância geográfica. Ele restaura da cópia de backup replicado geograficamente e, assim, é resistente a falhas de armazenamento na região primária. Para saber mais, veja [Restaurar um Banco de Dados SQL do Azure ou fazer failover para um secundário](../sql-database/sql-database-disaster-recovery.md).

#### <a name="active-geo-replication"></a>Replicação geográfica ativa
[Replicação geográfica ativa](../sql-database/sql-database-geo-replication-overview.md) está disponível para todas as camadas de banco de dados. Ela foi criada para aplicativos que têm requisitos de recuperação mais agressivos do que a Restauração Geográfica pode oferecer. Usando a Replicação Geográfica Ativa, você pode criar até quatro secundários legíveis nos servidores em regiões diferentes. Você pode iniciar o failover para qualquer um dos secundários. Além disso, a replicação geográfica ativa pode ser usada para suportar a atualização do aplicativo ou os cenários de realocação, bem como o balanceamento de carga para cargas de trabalho somente leitura. Para obter detalhes, veja [configurar a Replicação Geográfica Ativa](../sql-database/sql-database-geo-replication-portal.md) e [Failover para o banco de dados secundário](../sql-database/sql-database-geo-replication-failover-portal.md). Para obter detalhes sobre design e implementação de aplicativos e da atualização de aplicativos sem tempo de inatividade, veja [Design de aplicativo para recuperação de desastre na nuvem usando a Replicação Geográfica Ativa no Banco de Dados SQL](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Gerenciamento das atualizações sem interrupção de aplicativos na nuvem usando a Replicação Geográfica Ativa do Banco de Dados SQL](../sql-database/sql-database-manage-application-rolling-upgrade.md).

### <a name="sql-server-on-azure-virtual-machines"></a>SQL Server nas Máquinas Virtuais do Azure
Várias opções estão disponíveis para recuperação e alta disponibilidade para o SQL Server 2012 (e posterior) em execução em uma implementação de Máquinas Virtuais do Azure. Para saber mais, confira [Alta disponibilidade e recuperação de desastres para o SQL Server em Máquinas Virtuais do Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md).

## <a name="other-azure-platform-services"></a>Outros serviços da plataforma Azure
Ao tentar executar seu serviço de nuvem em várias regiões do Azure, você deve considerar as implicações para cada uma de suas dependências. Nas seções a seguir, as diretrizes específicas do serviço pressupõem que você deve usar o mesmo serviço do Azure em um datacenter alternativo do Azure. Isso envolve tanto tarefas de configuração quanto de replicação de dados.

> [!NOTE]
> Em alguns casos, essas etapas podem ajudar a atenuar uma interrupção de serviço específico no lugar de um evento no datacenter inteiro. Da perspectiva do aplicativo, uma interrupção de serviço específico pode ser igualmente limitadora e exigiria a migração temporária do serviço para uma região alternativa do Azure.
>
>

### <a name="azure-service-bus"></a>Barramento de Serviço do Azure
O Barramento de Serviço do Azure usa um namespace exclusivo que não abrange regiões do Azure. Portanto, o primeiro requisito é configurar os namespaces do Barramento de Serviço necessários na região alternativa. No entanto, também há considerações sobre a durabilidade das mensagens na fila. Há várias estratégias para replicar mensagens entre regiões do Azure. Para obter detalhes sobre essas estratégias de replicação e outras estratégias de recuperação de desastre, confira [Práticas recomendadas para isolar aplicativos contra interrupções e desastres do Barramento de Serviço](../service-bus-messaging/service-bus-outages-disasters.md). Para obter outras considerações sobre disponibilidade, confira [Barramento de Serviço (Disponibilidade)](resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

### <a name="azure-app-service"></a>Serviço de aplicativo do Azure
Para migrar um aplicativo do Serviço de Aplicativo do Azure, como Aplicativos Web ou Aplicativos Móveis, para uma região do Azure secundária, você deve ter um backup do site disponível para publicação. Se a interrupção não envolver todo o datacenter do Azure, talvez seja possível usar o FTP para baixar um backup recente do conteúdo do site. Em seguida, crie um novo aplicativo na região alternativa, a menos que você tenha feito isso anteriormente para a capacidade reserva. Publique o site na nova região e faça quaisquer alterações de configuração necessárias. Essas alterações podem incluir cadeias de conexão de banco de dados ou outras configurações específicas de região. Se necessário, adicione o certificado SSL do site e altere o registro DNS CNAME para que o nome de domínio personalizado aponte para a URL dos Aplicativos Web reimplantados.

### <a name="azure-hdinsight"></a>Azure HDInsight
Os dados associados ao Azure HDInsight são armazenados por padrão no Armazenamento de Blobs do Azure. O HDInsight requer que um cluster Hadoop processando trabalhos de MapReduce esteja colocalizado na mesma região da conta de armazenamento que contém os dados sendo analisados. Se você usar o recurso de replicação geográfica disponível para o Armazenamento do Azure, poderá acessar os dados na região secundária onde os dados foram replicados se a região primária não estiver mais disponível. Você pode criar um cluster Hadoop na região em que os dados foram replicados e continuar processando-os. Para obter outras considerações sobre disponibilidade, confira [HDInsight (Disponibilidade)](resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

### <a name="sql-reporting"></a>Relatórios SQL
Atualmente, a recuperação da perda de uma região do Azure exige várias instâncias de Relatórios SQL em diferentes regiões do Azure. Essas instâncias de Relatórios SQL devem acessar os mesmos dados, e esses dados devem ter seu próprio plano de recuperação em caso de desastre. Você também pode manter cópias de backup externas do arquivo RDL para cada relatório.

### <a name="azure-media-services"></a>Serviços de Mídia do Azure
Os Serviços de Mídia do Azure têm abordagens de recuperação diferentes para codificação e streaming. Normalmente, a transmissão é mais crítica durante uma interrupção regional. Para se preparar, você deve ter uma conta de Serviços de Mídia em duas regiões do Azure diferentes. O conteúdo codificado deve estar localizado em ambas as regiões. Durante uma falha, você pode redirecionar o tráfego de streaming para a região alternativa. A codificação pode ser executada em qualquer região do Azure. Se a codificação for sensível ao tempo, por exemplo, durante o processamento de eventos ao vivo, você deverá estar preparado para enviar trabalhos para um datacenter alternativo durante eventuais falhas.

### <a name="virtual-network"></a>Rede virtual
Os arquivos de configuração fornecem a maneira mais rápida de configurar uma rede virtual em uma região alternativa do Azure. Após configurar a rede virtual na região primária do Azure, [exporte as configurações de rede virtual](../virtual-network/virtual-networks-create-vnet-classic-portal.md) da rede atual para um arquivo de configuração de rede. Para uma interrupção na região primária, [restaure a rede virtual](../virtual-network/virtual-networks-create-vnet-classic-portal.md) do arquivo de configuração armazenado. Em seguida, configure outros serviços de nuvem, máquinas virtuais ou configurações entre locais para trabalhar com a nova rede virtual.

## <a name="checklists-for-disaster-recovery"></a>Listas de verificação para recuperação de desastre
## <a name="cloud-services-checklist"></a>Lista de verificação de serviços de nuvem
1. Examinar a seção “Serviços de nuvem” deste artigo.
2. Criar uma estratégia de recuperação de desastre entre regiões.
3. Compreender as compensações em reservar capacidade em regiões alternativas.
4. Usar ferramentas de roteamento de tráfego, como o Gerenciador de Tráfego do Azure.

## <a name="virtual-machines-checklist"></a>Lista de verificação de máquinas virtuais
1. Examine a seção “Máquinas Virtuais” deste artigo.
2. Usar [Backup do Azure](https://azure.microsoft.com/services/backup/) para criar backups consistentes com aplicativos em regiões.

## <a name="storage-checklist"></a>Lista de verificação de armazenamento
1. Examine a seção “Armazenamento” deste artigo.
2. Não desabilite a replicação geográfica dos recursos de armazenamento.
3. Compreenda a região alternativa para a replicação geográfica se ocorrer um failover.
4. Criar estratégias de backup personalizadas para estratégias de failover controlado pelo usuário.

## <a name="azure-sql-database-checklist"></a>Lista de verificação de Banco de Dados SQL do Azure
1. Consulte a seção "Banco de Dados SQL do Azure" deste artigo.
2. Use a [Restauração Geográfica](../sql-database/sql-database-recovery-using-backups.md#geo-restore) ou a [Replicação Geográfica Ativa](../sql-database/sql-database-geo-replication-overview.md) conforme apropriado.

## <a name="sql-server-on-azure-virtual-machines-checklist"></a>Lista de verificação do SQL Server em Máquinas Virtuais do Azure
1. Examine a seção “SQL Server em Máquinas Virtuais do Azure” deste artigo.
2. Usar Grupos de Disponibilidade AlwaysOn entre regiões ou o espelhamento de banco de dados.
3. Como alternativa, use o backup e restauração para o armazenamento de Blobs.

## <a name="service-bus-checklist"></a>Lista de verificação do Barramento de Serviço
1. Consulte a seção "Barramento de serviço do Azure" deste artigo.
2. Configurar um namespace do Barramento de Serviço em uma região alternativa.
3. Considerar estratégias de replicação personalizadas para mensagens entre regiões.

## <a name="app-service-checklist"></a>Lista de verificação do Serviço de Aplicativo
1. Examine a seção “Serviço de Aplicativo do Azure” deste artigo.
2. Manter os backups de sites fora da região primária.
3. Se a interrupção for parcial, tentar recuperar o site atual com FTP.
4. Planejar implantar o site em um site novo ou existente em uma região alternativa.
5. Planeje alterações de configuração para o aplicativo e para os registros CNAME do DNS.

## <a name="hdinsight-checklist"></a>Lista de verificação do HDInsight
1. Consulte a seção "Azure HDInsight" deste artigo.
2. Crie um cluster Hadoop na região com dados replicados.

## <a name="sql-reporting-checklist"></a>Lista de verificação de Relatórios SQL
1. Examine a seção “Relatórios SQL” deste artigo.
2. Manter uma instância de Relatórios SQL alternativa em uma região diferente.
3. Manter um plano separado para replicar o destino para essa região.

## <a name="media-services-checklist"></a>Lista de verificação de Serviços de Mídia
1. Examine a seção “Serviços de Mídia do Azure” deste artigo.
2. Criar uma conta de Serviços de Mídia em uma região alternativa.
3. Codificar o mesmo conteúdo em ambas as regiões para dar suporte a failover de streaming.
4. Envie trabalhos de codificação para uma região alternativa no caso de uma interrupção do serviço.

## <a name="virtual-network-checklist"></a>Lista de verificação de rede virtual
1. Examine a seção “Rede virtual” deste artigo.
2. Usar as configurações da rede virtual exportadas para recriá-la em outra região.

## <a name="next-steps"></a>Próximas etapas
Este artigo faz parte de uma série que tem como foco [Orientações técnicas de resiliência do Azure](resiliency-technical-guidance.md). O próximo artigo desta série se concentra em [recuperação de um datacenter local para o Azure](resiliency-technical-guidance-recovery-on-premises-azure.md).

