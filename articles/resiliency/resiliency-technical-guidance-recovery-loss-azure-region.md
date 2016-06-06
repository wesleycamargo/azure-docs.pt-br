<properties
   pageTitle="Diretrizes técnicas de resiliência para recuperação de perda de uma região do Azure | Microsoft Azure"
   description="Artigo sobre compreensão e design de aplicativos resilientes, altamente disponíveis e tolerante a falhas, bem como planejamento de recuperação de desastre"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Orientações técnicas de resiliência do Azure - recuperação de interrupção do serviço em toda uma região

O Azure é dividido fisicamente e logicamente em unidades chamadas de regiões. Uma região consiste em um ou mais datacenters nas proximidades. No momento da redação deste artigo, o Azure tem vinte e quatro regiões pelo mundo. Em raras circunstâncias é possível que instalações em toda a região se tornem inacessíveis, por exemplo, devido a falhas de rede; ou então, é possível também que sejam totalmente perdidas, por exemplo, devido a um desastre natural. Esta seção explica os recursos do Azure para criar aplicativos que são distribuídos entre regiões. As regiões são concebidas para minimizar a possibilidade de uma falha em uma região afetar outras regiões.

##Serviços de Nuvem

###Gerenciamento de recursos
A distribuição de instâncias de computação entre regiões é realizada pela criação de um serviço de nuvem separado em cada região de destino e pela publicação do pacote de implantação para cada serviço de nuvem. No entanto, observe que a distribuição de tráfego pelos serviços de nuvem em diferentes regiões deve ser implementado pelo desenvolvedor do aplicativo ou com um serviço de gerenciamento de tráfego.

Determinar o número de instâncias de função de reposição a implantar com antecedência para recuperação de desastre é um aspecto importante do planejamento de capacidade. Ter uma implantação secundária completa garante que a capacidade esteja disponível quando necessário; no entanto, isso dobra efetivamente o custo. Um padrão comum é ter uma implantação secundária pequena, mas grande o suficiente para executar serviços críticos. É recomendável criar pelo menos uma implantação secundária pequena, tanto para capacidade reserva quanto para testar a configuração do ambiente secundário.

>[AZURE.NOTE]A cota da assinatura não é uma garantia de capacidade. A cota é simplesmente um limite de crédito. O número necessário de funções para garantir a capacidade deve ser definido no modelo de serviço, e as funções devem ser implantadas.

###Balanceamento de Carga
Balancear a carga de tráfego entre regiões requer o uso de uma solução de gerenciamento de tráfego. O Azure oferece o [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/). Você também pode tirar proveito dos serviços de terceiros que fornecem recursos de gerenciamento de tráfego semelhantes.

###Estratégias
Muitas estratégias alternativas estão disponíveis para implementação de computação distribuída entre regiões. Essas estratégias devem ser personalizadas segundo os requisitos específicos de negócios e as circunstâncias do aplicativo. Em um nível elevado, as abordagens podem ser divididas em 3 categorias:

  * __Reimplantar em caso de desastre__: nessa abordagem, o aplicativo é reimplantado do zero na hora do desastre. Isso é adequado para aplicativos não críticos, que não exigem um tempo de recuperação garantido.

  * __Reposição morna (ativo/passivo)__: um serviço hospedado secundário é criado em uma região alternativa e funções são implantadas para garantir a capacidade mínima; no entanto, as funções não recebem tráfego de produção. Essa abordagem é útil para aplicativos que não foram projetados para distribuir tráfego entre regiões.

  * __Reposição quente (ativo/ativo)__: O aplicativo foi projetado para receber a carga de produção em várias regiões. Os serviços de nuvem em cada região podem ser configurados para capacidade maior do que o necessário para fins de recuperação de desastre. Como alternativa, os serviços de nuvem podem escalar horizontalmente conforme o necessário no momento de um desastre e failover. Essa abordagem exige um investimento substancial no design do aplicativo, mas tem benefícios significativos, incluindo o tempo de recuperação baixo e garantido, testes contínuos de todos os locais de recuperação e o uso eficiente de capacidade.

Uma discussão completa sobre design distribuído está fora do escopo deste documento. Para obter mais informações, consulte [Recuperação de Desastre e Alta Disponibilidade para Aplicativos do Azure](https://aka.ms/drtechguide).

##Máquinas Virtuais
A recuperação de VMs (máquinas virtuais) de IaaS (infraestrutura como serviço) é semelhante à recuperação de computação de PaaS (plataforma como serviço) em vários aspectos, mas há diferenças importantes devido ao fato de que a VM IaaS consiste tanto da VM quanto do disco de VM.

  * __Use o Backup do Azure para criar backups entre regiões consistentes com aplicativos__: o [Backup do Azure](https://azure.microsoft.com/services/backup/) permite aos clientes criar backups consistentes com aplicativos em vários discos de VM e dar suporte à replicação de backups entre regiões. Isso é feito ao optar por replicar geograficamente o cofre de backup no momento da criação. Observe que a replicação do Cofre de backup deve ser configurada no momento da criação, não podendo ser definida mais tarde. Se uma região for perdida, a Microsoft tornará os backups disponíveis para os clientes. Os clientes poderão restaurar para qualquer um dos seus pontos de restauração configurados.

  * __Separar o disco de dados do disco do sistema operacional__: uma consideração importante para VMs de IaaS é que você não pode alterar o disco do SO sem recriar a VM. Isso não será um problema se sua estratégia de recuperação for a de reimplantar após desastre. No entanto, poderá ser um problema se você estiver usando a abordagem de reposição morna para capacidade reserva. Para implementar isso adequadamente, você deve ter o disco de SO correto implantado em ambos os locais primário e secundário, e os dados de aplicativos devem ser armazenados em uma unidade separada. Se possível, use uma configuração de SO padrão, que possa ser fornecida em ambos os locais. Após um failover, você deve anexar a unidade de dados às suas VMs IaaS existentes no controlador de domínio secundário. Use o AzCopy para copiar instantâneos dos discos de dados para um site remoto.

  * __Problemas potenciais de consistência após um failover geográfico de vários discos de VM__: os discos de VM são implementados como blobs de Armazenamento do Azure, e têm as mesmas características de replicação geográfica (veja abaixo). A menos que [Backup do Azure](https://azure.microsoft.com/services/backup/) seja usado, não há nenhuma garantia de consistência entre discos, porque a replicação geográfica é assíncrona e replica de maneira independente. Há garantia de que discos de VM individuais estejam em um estado com controle de falhas após um failover geográfico, mas não de que seja consistente entre os discos. Isso pode causar problemas em alguns casos (por exemplo, no caso de divisão entre discos).

##Armazenamento

###Recuperação usando armazenamento com redundância geográfica de blob, tabela, fila e armazenamento em disco de VM
Em blobs do Azure, tabelas, filas e discos de VM são todos replicados geograficamente por padrão. Isso é conhecido como GRS (armazenamento com redundância geográfica). O GRS replica dados de armazenamento para um datacenter emparelhado a centenas de milhas de distância dentro de uma região geográfica específica. O GRS é destinado a fornecer durabilidade adicional caso ocorra um desastre de grandes proporções no data center. A Microsoft controla quando ocorre failover, que limita-se a grandes desastres em que o local principal de origem é considerado irrecuperável em um período de tempo razoável. Em alguns cenários, isso pode ser vários dias. Dados normalmente são replicados em poucos minutos, embora o intervalo de sincronização ainda não seja coberto por um SLA.

No caso de um failover geográfico, não haverá nenhuma alteração ao modo como a conta é acessada (a URL e a chave de conta não serão alteradas), no entanto, a conta de armazenamento estará em uma região diferente após o failover, o que pode afetar aplicativos que exijam afinidade regional com sua conta de armazenamento. Mesmo para serviços e aplicativos que não exigem uma conta de armazenamento no mesmo data center, os encargos de largura de banda e a latência entre datacenters podem ser um motivo convincente para mover temporariamente o tráfego para a região de failover. Isso pode influenciar uma estratégia geral de recuperação de desastre.

Além do failover automático fornecido pelo GRS, o Azure introduziu um serviço que fornece a você acesso de leitura à cópia dos dados no local de armazenamento secundário. Isso é chamado de RA-GRS (armazenamento com redundância geográfica com acesso de leitura).

Para obter mais informações sobre ambas opções de armazenamento GRS e RA-GRS, consulte a [Replicação do Armazenamento do Azure](../storage/storage-redundancy.md).

###Mapeamentos de região de replicação geográfica:
É importante saber onde seus dados são replicados geograficamente para saber onde implantar as outras instâncias de dados que exigem afinidade regional com o seu armazenamento. A tabela a seguir mostra os emparelhamentos de locais primários e secundários:

[AZURE.INCLUDE [paired-region-list](../../includes/paired-region-list.md)]

###Preço de replicação geográfica:
A replicação geográfica está incluída no preço atual do Armazenamento do Azure. Isso se chama Armazenamento com Redundância Geográfica. Se não quiser que os dados sejam replicados geograficamente, você poderá desabilitar a replicação geográfica para sua conta. Isso é chamado de armazenamento com redundância local e é cobrado por um preço com desconto sobre o armazenamento replicado geograficamente.

###Determinando se um failover geográfico ocorreu
Caso um failover geográfico ocorra, isso será postado no [Painel de Integridade do Serviço Azure](https://azure.microsoft.com/status/); no entanto, os aplicativos podem implementar um meio automatizado de detectar isso monitorando a região geográfica para a sua conta de armazenamento. Isso pode ser usado para disparar outras operações de recuperação, como a ativação de recursos de computação na região geográfica para a qual seu armazenamento foi movido. Isso é consultável pela API de gerenciamento de serviços usando [Obter Propriedades da Conta de Armazenamento](https://msdn.microsoft.com/library/ee460802.aspx). As propriedades relevantes são:

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

###Discos de VM e failover geográfico
Conforme discutido na seção sobre discos de VM, não há nenhuma garantia de consistência dos dados entre discos de VM após um failover. Para garantir a exatidão de backups, um produto de backup como o Data Protection Manager deve ser usado para fazer backup de dados de aplicativos e restaurá-los.

##Banco de dados

###Banco de Dados SQL
O Banco de Dados SQL do Azure forneceu dois tipos de recuperação, a restauração geográfica e a replicação geográfica ativa.

####Restauração geográfica
A [restauração geográfica](../sql-database/sql-database-geo-restore.md) também está disponível com bancos de dados Básico, Standard e Premium. Ele fornece a opção de recuperação padrão quando o banco de dados também estiver indisponível devido a um incidente na região onde seu banco de dados está hospedado. Semelhante ao ponto de restauração pontual, a restauração geográfica depende de backups de banco de dados no armazenamento do Azure com redundância geográfica. Ele restaura a partir da cópia de backup replicado geograficamente e, portanto, é resistente a falhas de armazenamento na região primária. Consulte [Recuperação de uma falha](../sql-database/sql-database-disaster-recovery.md) para obter detalhes sobre como usar a restauração geográfica.

####Replicação geográfica ativa
A [replicação geográfica ativa](../sql-database/sql-database-geo-replication-overview.md) está disponível para todas as camadas de banco de dados. Ela foi criada para aplicativos que têm requisitos de recuperação mais agressivos do que a Restauração Geográfica pode oferecer. Com a replicação geográfica ativa, você pode criar até quatro secundários legíveis nos servidores em regiões diferentes. Você pode iniciar o failover para qualquer um dos secundários. Além disso, a replicação geográfica ativa pode ser usada para suportar a atualização do aplicativo ou os cenários de realocação, bem como o balanceamento de carga para cargas de trabalho somente leitura. Consulte [Projeto para continuidade de negócios](../sql-database/sql-database-business-continuity-design.md) para obter detalhes sobre como [configurar a replicação geográfica](../sql-database/sql-database-geo-replication-portal.md) e [fazer failover para o banco de dados secundário](../sql-database/sql-database-geo-replication-failover-portal.md). Consulte [Atualização de aplicativo sem tempo de inatividade](../sql-database/sql-database-business-continuity-application-upgrade.md) para obter detalhes sobre como implementar a atualização de aplicativo sem tempo de inatividade.

###SQL Server em máquinas virtuais
Várias opções estão disponíveis para recuperação e alta disponibilidade para o SQL Server 2012 (e posterior) em execução em máquinas virtuais do Azure. Para obter informações detalhadas, consulte [Alta disponibilidade e recuperação de desastre para o SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

##Outros serviços da plataforma Azure
Ao tentar executar seu serviço de nuvem em várias regiões do Azure, você deve considerar as implicações para cada uma de suas dependências. Nas seções a seguir, as diretrizes específicas do serviço pressupõem que você deve usar o mesmo serviço do Azure em um datacenter alternativo do Azure. Isso envolve tanto tarefas de configuração quanto de replicação de dados.

>[AZURE.NOTE]Em alguns casos, essas etapas podem ajudar a atenuar uma interrupção de serviço específico no lugar de um evento no datacenter inteiro. Da perspectiva do aplicativo, uma interrupção de serviço específico pode ser igualmente limitadora e exigiria a migração temporária do serviço para uma região alternativa do Azure.

###Barramento de Serviço
O Barramento de Serviço do Azure usa um namespace exclusivo que não abrange regiões do Azure. Portanto, o primeiro requisito é configurar os namespaces do barramento de serviço necessários na região alternativa. No entanto, também há considerações sobre a durabilidade das mensagens na fila. Há várias estratégias para replicar mensagens entre regiões do Azure. Para obter detalhes sobre essas estratégias de replicação e outras estratégias de recuperação de desastre, consulte [Práticas recomendadas para isolar aplicativos contra interrupções de Barramento de Serviço e desastres](../service-bus/service-bus-outages-disasters.md). Para outras considerações sobre disponibilidade, consulte [Barramento de Serviço (Disponibilidade)](./resiliency-technical-guidance-recovery-local-failures.md#service-bus).

###Aplicativos Web
Para migrar um aplicativo Web para uma região do Azure secundária, você deve ter um backup do site disponível para publicação. Se a interrupção não envolver todo o datacenter do Azure, talvez seja possível usar o FTP para baixar um backup recente do conteúdo do site. Em seguida, crie um novo aplicativo Web na região alternativa, a menos que você tenha feito isso anteriormente para a capacidade reserva. Publique o site na nova região e faça quaisquer alterações de configuração necessárias. Essas alterações podem incluir cadeias de conexão de banco de dados ou outras configurações específicas de região. Se necessário, adicione o certificado SSL do site e altere o registro DNS CNAME para que o nome de domínio personalizado aponte para a URL do aplicativo Web do Azure reimplantado.

###Serviços Móveis
Na região secundária do Azure, crie um serviço móvel de backup para seu aplicativo. Restaure o Banco de Dados SQL do Azure também para a região alternativa. Em seguida, use as ferramentas de linha de comando do Azure para mover o serviço móvel para a região alternativa. Então, configure o serviço móvel para usar o banco de dados restaurado. Para obter mais informações sobre esse processo, consulte [Recuperar o serviço móvel em caso de desastre](../mobile-services/mobile-services-disaster-recovery.md). Para outras considerações sobre disponibilidade, consulte [Serviços Móveis (Disponibilidade)](./resiliency-technical-guidance-recovery-local-failures.md#mobile-services).

###HDInsight
Os dados associados ao HDInsight são armazenados por padrão no armazenamento de Blobs do Azure. O HDInsight requer que um cluster Hadoop processando trabalhos de MapReduce esteja colocalizado na mesma região da conta de armazenamento que contém os dados sendo analisados. Desde que você use o recurso de replicação geográfica disponível para o Armazenamento do Azure, você poderá acessar os dados na região secundária onde os dados foram replicados se, por algum motivo, a região primária não estiver mais disponível. Você pode criar um novo cluster Hadoop na região em que os dados foram replicados e continuar processando-os. Para outras considerações sobre disponibilidade, consulte [HDInsight (Disponibilidade)](./resiliency-technical-guidance-recovery-local-failures.md#hdinsight).

###Relatórios SQL
Neste momento, a recuperação da perda de uma região do Azure exige várias instâncias de Relatórios SQL em diferentes regiões do Azure. Essas instâncias de Relatórios SQL devem acessar os mesmos dados, e esses dados devem ter seu próprio plano de recuperação em caso de desastre. Você também pode manter cópias de backup externas do arquivo RDL para cada relatório.

###Serviços de mídia
Os Serviços de Mídia do Azure têm abordagens de recuperação diferentes para codificação e streaming. Normalmente, a transmissão é mais crítica durante uma interrupção regional. Para se preparar para isso, você deve ter uma conta de Serviços de Mídia em duas regiões do Azure diferentes. O conteúdo codificado deve estar localizado em ambas as regiões. Durante uma falha, você pode redirecionar o tráfego de streaming para a região alternativa. A codificação pode ser executada em qualquer região do Azure. Se a codificação for sensível ao tempo, por exemplo, durante o processamento de eventos ao vivo, você deverá estar preparado para enviar trabalhos para um datacenter alternativo durante eventuais falhas.

###Rede Virtual
Os arquivos de configuração fornecem a maneira mais rápida para configurar uma rede virtual em uma região alternativa do Azure. Depois de configurar a rede virtual na região primária do Azure, [exporte as configurações de rede virtual](../virtual-network/virtual-networks-create-vnet-classic-portal.md) da rede atual para um arquivo de configuração de rede. Caso ocorra uma interrupção na região primária, [restaure a rede virtual](../virtual-network/virtual-networks-create-vnet-classic-portal.md) usando o arquivo de configuração armazenado. Em seguida, configure outros serviços de nuvem, máquinas virtuais ou configurações entre locais para trabalhar com a nova rede virtual.

##Listas de verificação para recuperação de desastre

##Lista de verificação de serviços de nuvem
  1. Examinar a seção [Serviços de Nuvem](#cloud-services) deste documento
  2. Criar uma estratégia de recuperação de desastre entre regiões
  3. Compreender as compensações em reservar capacidade em regiões alternativas
  4. Usar ferramentas de roteamento de tráfego, como o Gerenciador de Tráfego do Azure

##Lista de verificação de máquinas virtuais
  1. Examinar a seção [Máquinas Virtuais](#virtual-machines) deste documento
  2. Usar [Backup do Azure](https://azure.microsoft.com/services/backup/) para criar backups consistentes com aplicativos em regiões

##Lista de verificação de armazenamento
  1. Examinar a seção [Armazenamento](#storage) deste documento
  2. Não desabilitar a replicação geográfica dos recursos de armazenamento
  3. Compreender a região alternativa para a replicação geográfica em caso de failover
  4. Criar estratégias de backup personalizadas para estratégias de failover controlado pelo usuário

##Lista de verificação de Banco de Dados SQL
  1. Examinar a seção [Banco de Dados SQL](#sql-database) deste documento
  2. Usar [Restauração geográfica](../sql-database/sql-database-geo-restore.md) ou [Replicação geográfica](../sql-database/sql-database-geo-replication-overview.md) conforme apropriado

##Lista de verificação do SQL Server em máquinas virtuais
  1. Examinar a seção [SQL Server em máquinas virtuais](#sql-server-on-virtual-machines) deste documento
  2. Usar Grupos de Disponibilidade AlwaysOn entre regiões ou o espelhamento de banco de dados
  3. Como alternativa, usar o backup e restauração para o armazenamento de blobs

##Lista de verificação do Barramento de Serviço
  1. Examinar a seção [Barramento de Serviço](#service-bus) deste documento
  2. Configurar um namespace do Barramento de Serviço em uma região alternativa
  3. Considerar estratégias de replicação personalizadas para mensagens entre regiões

##Lista de verificação de aplicativos Web
  1. Examinar a seção [Aplicativos Web](#web-apps) deste documento
  2. Manter os backups de sites fora da região primária
  3. Se a interrupção for parcial, tentar recuperar o site atual com FTP
  4. Planejar implantar o site da Web em um site da Web novo ou existente em uma região alternativa
  5. Planejar alterações de configuração tanto de aplicativo quanto dos registros DNS CNAME

##Lista de verificação de serviços móveis
  1. Examinar a seção [Serviços Móveis](#mobile-services) deste documento
  2. Criar um serviço móvel de backup em uma região alternativa
  3. Gerenciar backups do Banco de Dados SQL do Azure associado para restaurar durante o failover
  4. Usar ferramentas de linha de comando do Azure para mover o serviço móvel

##Lista de verificação do HDInsight
  1. Examinar a seção [HDInsight](#hdinsight) deste documento
  2. Criar um novo cluster Hadoop na região com dados replicados

##Lista de verificação de Relatórios SQL
  1. Examinar a seção [Relatórios SQL](#sql-reporting) deste documento
  2. Manter uma instância de Relatórios SQL alternativa em uma região diferente
  3. Manter um plano separado para replicar o destino para essa região

##Lista de verificação de Serviços de Mídia
  1. Examinar a seção [Serviços de Mídia](#media-services) deste documento
  2. Criar uma conta de Serviços de Mídia em uma região alternativa
  3. Codificar o mesmo conteúdo em ambas as regiões para dar suporte a failover de streaming
  4. Enviar trabalhos de codificação em uma região alternativa caso ocorra uma interrupção

##Rede Virtual
  1. Examinar a seção [Rede Virtual](#virtual-network) deste documento
  2. Usar as configurações da rede virtual exportadas para recriá-la em outra região
 
##Próximas etapas
Este artigo faz parte de uma série que tem como foco [Orientações técnicas de resiliência do Azure](./resiliency-technical-guidance.md). O próximo artigo desta série se concentra em [recuperação de um datacenter local para o Azure](./resiliency-technical-guidance-recovery-on-premises-azure.md).

<!---HONumber=AcomDC_0525_2016-->