<properties
   pageTitle="Diretrizes técnicas de recuperação de falhas locais no Azure | Microsoft Azure"
   description="Artigo sobre compreensão e design de aplicativos resilientes, altamente disponíveis e tolerante a falhas, bem como planejamento de recuperação de desastre concentrado em falhas locais no Azure."
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

#Orientações técnicas de resiliência do Azure - recuperação de falhas locais no Azure

Há duas ameaças principais à disponibilidade de aplicativos: falha dos dispositivos como unidade e servidores e esgotamento de recursos críticos, como computação sob condições de carga de pico. O Azure fornece uma combinação de gerenciamento de recursos, elasticidade, balanceamento de carga e particionamento para habilitar a alta disponibilidade sob essas circunstâncias. Alguns desses recursos são executados automaticamente para todos os serviços de nuvem; no entanto, em alguns casos, o desenvolvedor do aplicativo deve fazer algum trabalho adicional para beneficiar-se deles.

##Serviços de Nuvem
Todos os serviços de nuvem hospedados pelo Azure são coleções de uma ou mais funções de trabalho ou da Web. Uma ou mais instâncias de uma determinada função podem ser executadas simultaneamente. O número de instâncias é determinado pela configuração. Instâncias de função são monitoradas e gerenciadas com um componente chamado de Controlador de Malha. O Controlador de Malha detecta e responde tanto a falhas de hardware quando de software automaticamente.

  * Cada instância de função é executada em sua própria VM (máquina virtual) e se comunica com seu Controlador de Malha por meio de um agente convidado. O agente convidado coleta métricas de recurso e de nó, incluindo o uso da VM, status, logs, uso de recursos, exceções e condições de falha. O Controlador de Malha consultará o agente convidado em intervalos configuráveis e reinicializará a VM se o agente convidado não responder.
  * No caso de falha de hardware, o Controlador de Malha associado move todas as instâncias de função afetadas para um novo nó de hardware e reconfigura a rede para rotear o tráfego para lá.

Para tirar proveito desses recursos, os desenvolvedores devem garantir que todas as funções de serviço evitem armazenar o estado nas instâncias de função. Em vez disso, todos os dados persistentes devem ser acessados de armazenamento durável, como Serviços de Armazenamento do Azure ou Banco de Dados SQL do Azure. Isso permite que as solicitações sejam tratadas por quaisquer funções. Isso também significa que as instâncias de função podem ficar inoperantes a qualquer momento sem criar inconsistências no estado transiente ou persistente do serviço.

O requisito de armazenar o estado externo nas funções tem várias implicações. Significa, por exemplo, que todas as alterações relacionadas a uma tabela de Armazenamento do Azure devem ser modificadas em uma única transação de grupo de entidades, se possível. Obviamente, não é sempre possível fazer todas as alterações em uma única transação. Cuidado especial deve ser tomado para garantir que falhas de instância de função não causem problemas quando interromperem operações de longa duração abrangendo duas ou mais atualizações para o estado persistente do serviço. Se outra função tentar repetir essa operação, ela deverá prever e tratar do caso em que o trabalho foi parcialmente concluído.

Por exemplo, em um serviço que particiona dados em vários repositórios, se uma função de trabalho ficar indisponível ao realocar um fragmento, a realocação do fragmento poderá não ser concluída, ou poderá ser repetida desde o seu início por uma função de trabalho diferente, potencialmente causando a geração de dados órfãos ou dados corrompidos. Para evitar problemas, operações de execução demorada devem ser idempotentes (ou seja, repetíveis sem efeitos colaterais) e/ou reinicializáveis incrementalmente (ou seja, capazes de continuar partindo do ponto de falha mais recente).

  * Para ser idempotente, uma operação de execução demorada deve ter o mesmo efeito, não importando quantas vezes ela é executada, mesmo que ela seja interrompida durante a execução.
  * Para ser reinicializável incrementalmente, uma operação de execução demorada deve consistir em uma sequência de operações atômicas menores e deve registrar seu andamento em armazenamento durável, para que cada invocação subsequente retome de onde sua antecessora foi interrompida.

Por fim, todas as operações de execução demorada devem ser invocadas repetidamente até que tenham êxito. Por exemplo, uma operação de provisionamento pode ser colocada em uma fila do Azure, para ser removida da fila por uma função de trabalho somente quando for bem-sucedida. A coleta de lixo pode ser necessária para limpar os dados criados por operações interrompidas.

###Elasticidade
O número inicial de instâncias em execução para cada função é determinado na configuração de cada função. Os administradores devem configurar inicialmente cada uma das funções para executar com duas ou mais instâncias, com base na carga esperada. No entanto, instâncias de função podem ser escaladas ou reduzidas verticalmente com facilidade conforme os padrões de uso mudam. Isso pode ser feito com o manualmente no Portal do Azure ou pode ser automatizado usando o Windows PowerShell, a API de Gerenciamento de Serviços ou ferramentas de terceiros. Consulte aqui para obter informações sobre [Como dimensionar automaticamente um serviço de nuvem](../cloud-services/cloud-services-how-to-scale.md).

###Particionamento
O Controlador de Malha do Azure usa dois tipos de partições: domínios de atualização e domínios de falha.

  * Um domínio de atualização é usado para atualizar instâncias de função de um serviço em grupos. O Azure implanta instâncias do serviço em vários domínios de atualização. Para uma atualização in-loco, o Controlador de Malha derruba todas as instâncias em um domínio de atualização, atualiza essas instâncias e, em seguida, reinicia-as antes de passar para o próximo domínio de atualização. Essa abordagem impede que o serviço inteiro fique indisponível durante o processo de atualização.
  * Um domínio de falha define pontos potenciais de falha de hardware ou de rede. Para qualquer função com mais de uma instância, o Controlador de Malha garante que as instâncias sejam distribuídas entre vários domínios de falha, para evitar que falhas de hardware isoladas interrompam o serviço. Toda a exposição a falhas de cluster e de servidor no Azure é controlada por domínios de falha.

Por meio do [SLA do Azure](https://azure.microsoft.com/support/legal/sla/), a Microsoft garante que, quando duas ou mais instâncias de função web forem implantadas em domínios de falha e atualização diferentes, elas terão conectividade externa em pelo menos 99,95% do tempo. Ao contrário do que ocorre nos domínios de atualização, não há nenhuma maneira de controlar o número de domínios de falha. O Azure aloca domínios de falha e distribui instâncias de função entre eles automaticamente. Pelo menos as primeiras duas instâncias de cada função são colocadas em diferentes domínios de falha e de atualização para garantir que qualquer função com pelo menos duas instâncias atenderá ao SLA. Isso é representado no diagrama a seguir.

![Isolamento de domínio de falha (Exibição simplificada)](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-1.png "Isolamento de domínio de falha - Exibição simplificada")

###Balanceamento de Carga
Todo o tráfego de entrada para uma função web passa por um balanceador de carga sem estado, que distribui solicitações de cliente entre instâncias de função. Instâncias de função individuais não têm endereços IP públicos e não são diretamente endereçáveis da Internet. Funções Web são sem estado, para que qualquer solicitação de cliente possa ser roteada para qualquer instância de função. Um evento [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck.aspx) é gerado a cada 15 segundos. Isso pode ser usado para indicar se a função está pronta para receber o tráfego ou está ocupada e deve ser retirada da rotação do balanceador de carga.

##Máquinas Virtuais
Máquinas virtuais do Azure são diferentes de funções de computação de PaaS em vários aspectos, no que se refere à alta disponibilidade. Em alguns casos, você precisa fazer trabalho adicional para garantir a alta disponibilidade.

###Durabilidade de disco
Diferentemente do que ocorre nas instâncias de função de PaaS, os dados armazenados em unidades de Máquina Virtual são persistentes mesmo quando a máquina virtual é realocada. Máquinas virtuais do Azure usam discos de VM que existem como blobs no Armazenamento do Azure. Devido às características de disponibilidade do Armazenamento do Azure, os dados armazenados nas unidades de uma Máquina Virtual também têm alta disponibilidade. Observe que a unidade D: é a exceção a essa regra. A unidade D: é na verdade o armazenamento físico no servidor de rack que hospeda a VM, e seus dados serão perdidos se a VM for reciclada. A unidade D: unidade destina-se somente a armazenamento temporário.

###Particionamento
O Azure compreende nativamente as camadas em um aplicativo de PaaS (função web e função de trabalho) e, portanto, pode distribuí-las entre domínios de falha e atualização. Por outro lado, as camadas em aplicativos de IaaS devem ser definidas manualmente usando conjuntos de disponibilidade. Conjuntos de disponibilidade são necessários para um SLA de IaaS.

![Conjuntos de disponibilidade para Máquinas Virtuais do Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-2.png "Conjuntos de disponibilidade para Máquinas Virtuais do Microsoft Azure")

No diagrama acima, a camada de IIS (Internet Information Server) (que funciona como uma camada de aplicativo Web) e a camada SQL (que funciona como uma camada de dados) são atribuídas a diferentes conjuntos de disponibilidade. Isso garante que todas as instâncias de cada camada tenham redundância de hardware ao distribuí-las pelos domínios de falha, e essas instâncias permanecem ativas durante uma atualização.

###Balanceamento de Carga
Se as VMs devem ter tráfego distribuído, você deve agrupar as VMs em um serviço de nuvem e realizar o balanceamento de carga por um ponto de extremidade TCP ou UDP específico. Para obter mais informações, consulte [Balanceamento de carga de máquinas virtuais](../virtual-machines/virtual-machines-linux-load-balance.md). Se as máquinas virtuais receberem entrada de outra fonte (por exemplo, um mecanismo de enfileiramento de mensagens), não será necessário um balanceador de carga. O balanceador de carga usará uma verificação básica de integridade para determinar se o tráfego deve ser enviado para o nó. Também é possível criar suas próprias investigações para implementar as métricas de integridade específicas do aplicativo que determinam se a VM deve receber tráfego.

##Armazenamento
O Armazenamento do Azure é o serviço de dados durável de linha de base para o Azure, fornecendo armazenamento em tabela, blobs, fila e em disco de VM. Ele usa uma combinação de replicação e gerenciamento de recursos para fornecer alta disponibilidade em um único data center. O SLA de disponibilidade do Armazenamento do Azure garante que, em pelo menos 99,9% do tempo, solicitações corretamente formatadas de adicionar, atualizar, ler e excluir dados serão processadas corretamente e com êxito, e que as contas de armazenamento terão conectividade com o gateway de Internet.

###Replicação
A durabilidade dos dados do armazenamento do Azure é viabilizada pela manutenção de várias cópias de todos os dados em unidades diferentes localizadas em subsistemas de armazenamento físico totalmente independentes dentro da região. Os dados são replicados de forma síncrona e todas as cópias são confirmadas antes da confirmação da gravação. O Armazenamento do Azure é altamente consistente, o que significa que há garantia de que as leituras reflitam as gravações mais recentes. Além disso, cópias de dados são verificadas continuamente para detectar e reparar bits corrompidos, uma ameaça frequentemente desconsiderada à integridade dos dados armazenados. Os serviços se beneficiam da replicação apenas pelo uso do Armazenamento do Azure. Nenhum trabalho adicional é requerido do desenvolvedor do serviço para recuperação de uma falha local.

###Gerenciamento de recursos
Contas de armazenamento criadas depois de 7 de junho de 2012 podem crescer até 200 TB (o máximo anterior era 100 TB). Se espaço adicional for necessário, os aplicativos deverão ser criados para aproveitar várias contas de armazenamento.

###Discos de máquina virtual
Um Disco de VM de uma máquina virtual é armazenado como um blob de páginas no armazenamento do Azure, permitindo que você tenha as mesmas propriedades de durabilidade e escalabilidade do armazenamento de Blobs. Esse design torna os dados em um disco de máquina virtual persistentes mesmo se o servidor que executa a VM falhar e a VM precisa ser reiniciada em outro servidor.

##Banco de dados

###Banco de Dados SQL
O Banco de Dados SQL do Microsoft Azure fornece banco de dados como um serviço, permitindo que os aplicativos rapidamente provisionem, insiram dados e consultem bancos de dados relacionais. Ele fornece muitos dos recursos e funcionalidades familiares do SQL Server, ao mesmo tempo em que abstrai a carga de hardware, configuração, aplicação de patch e resiliência.

>[AZURE.NOTE]O Banco de Dados SQL do Azure não fornece paridade de recursos 1 para 1 com o SQL Server e é desenvolvido para atender a um conjunto diferente de requisitos, exclusivamente adequados para aplicativos em nuvem (escala elástica, banco de dados como serviço para reduzir os custos de manutenção e assim por diante). Para obter mais informações, consulte [Escolher uma opção do SQL Server de nuvem: Banco de Dados do SQL Azure (PaaS) ou SQL Server em VMs do Azure (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

####Replicação
O Banco de Dados SQL do Azure fornece resiliência interna a falhas no nível de nó. Todas as gravações em um banco de dados são replicadas automaticamente em dois ou mais nós em segundo plano usando uma técnica de confirmação de quorum (o primário e pelo menos um secundário devem confirmar a gravação da atividade no log de transações antes que a transação seja considerada bem-sucedida e retorne). No caso de falha de nó, o banco de dados faz failover automaticamente para uma das réplicas secundárias. Isso causa uma interrupção de conexão transitória para aplicativos cliente. Por esse motivo, todos os clientes do Banco de Dados SQL do Microsoft Azure devem implementar alguma forma de tratamento de conexão transitória. Para obter mais informações, consulte [Diretriz específica do serviço de repetição](../best-practices-retry-service-specific.md).

####Gerenciamento de recursos
Cada banco de dados, quando criado, é configurado com um limite de tamanho superior. O tamanho máximo disponível atualmente é de 150 GB. Quando um banco de dados atinge seu limite de tamanho superior, ele rejeita os comandos adicionais INSERT ou UPDATE (ainda é possível consultar e excluir dados).

Dentro de um banco de dados, o Banco de Dados SQL do Microsoft Azure usa uma malha para gerenciar recursos. No entanto, em vez de um controlador de malha, ele usa uma topologia de anel para detectar falhas. Cada réplica em um cluster tem dois vizinhos e é responsável por detectar quando eles são desativados. Quando uma réplica é desativada, seus vizinhos disparam um RA (agente de reconfiguração) para recriá-la em outro computador. A limitação de mecanismo é fornecida para garantir que um servidor lógico não use recursos demais em um computador ou exceda seus limites físicos.

###Elasticidade
Se o aplicativo exigir mais do que o limite de banco de dados de 150 GB, ele deverá implementar uma abordagem de escalonamento horizontal. O escalonamento horizontal com o Banco de Dados SQL do Microsoft Azure é feito por particionamento manual (também conhecido como fragmentação) de dados em vários Bancos de Dados SQL do Azure. Essa abordagem de escalonamento horizontal fornece a oportunidade de atingir o crescimento de custo praticamente linear com escala. O crescimento elástico ou a capacidade sob demanda podem crescer com custos incrementais conforme necessário, porque os bancos de dados são cobrados com base no tamanho real médio usado por dia, e não no tamanho máximo possível.

##SQL Server em máquinas virtuais
Instalando o SQL Server em máquinas virtuais do Azure (versão 2014 ou posterior), você pode tirar proveito dos recursos tradicionais de disponibilidade do SQL Server, como grupos de disponibilidade AlwaysOn ou espelhamento de banco de dados. Observe que VM do Azure, armazenamento e rede têm características operacionais diferentes da infraestrutura de TI local, não virtualizada. Uma implementação bem-sucedida de uma solução do SQL Server de alta disponibilidade/recuperação de desastre no Azure requer que você compreenda essas diferenças e crie sua solução para acomodá-las.

###Nós de alta disponibilidade em um conjunto de disponibilidade
Quando você implementa uma solução de alta disponibilidade no Azure, o conjunto de disponibilidade no Azure permite que você coloque os nós de alta disponibilidade em domínios de falha e domínios de atualização separados. Para ser claro, o conjunto de disponibilidade é um conceito do Azure. É uma prática recomendada que deve ser seguida para certificar-se de que seus bancos de dados são, de fato, altamente disponíveis, esteja você usando grupos de disponibilidade AlwaysOn, espelhamento de banco de dados ou outro meio. Se você não seguir essa recomendação, você poderá estar se baseando no pressuposto incorreto de que o sistema está altamente disponível, mas na verdade seus nós podem todos falhar simultaneamente porque eles podem ter sido colocados no mesmo domínio de falha no datacenter do Azure. Essa recomendação não é tão aplicável no envio de logs já que, como um recurso de recuperação de desastre, você deve garantir que os servidores estejam executando em locais (regiões) separados do datacenter do Azure. Por definição, esses locais do datacenter são domínios de falha separados.

Para que VMs do Azure sejam colocadas no mesmo conjunto de disponibilidade, você deve implantá-las no mesmo serviço de nuvem. Somente nós no mesmo serviço de nuvem podem participar do mesmo conjunto de disponibilidade. Além disso, as VMs devem estar na mesma rede virtual para garantir que elas mantenham os IPs mesmo depois da recuperação de serviço, evitando, assim, os tempos de atualização de DNS.

###Somente Azure: soluções de alta disponibilidade
Você pode ter uma solução de alta disponibilidade para seus bancos de dados do SQL Server no Azure usando grupos de disponibilidade AlwaysOn ou espelhamento de banco de dados.

O diagrama a seguir mostra a arquitetura de grupos de disponibilidade AlwaysOn em execução em máquinas virtuais do Azure. Este diagrama foi retirado do artigo de aprofundamento sobre esse assunto, [Alta disponibilidade e recuperação de desastre para SQL Server em Máquinas Virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Grupos de disponibilidade AlwaysOn no Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-1.png "Grupos de disponibilidade AlwaysOn no Microsoft Azure")

Você também pode provisionar automaticamente a implantação de um grupo de disponibilidade AlwaysOn ponta a ponta em VMs do Azure usando o modelo AlwaysOn no Portal do Microsoft Azure. Para obter mais informações, consulte [Oferta do AlwaysOn do SQL Server na Galeria do Portal do Microsoft Azure](https://blogs.technet.microsoft.com/dataplatforminsider/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery/).

O diagrama a seguir demonstra o uso de espelhamento de banco de dados em máquinas virtuais do Azure. Isso também foi extraído do tópico de aprofundamento, [Alta disponibilidade e recuperação de desastre para SQL Server em Máquinas Virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Espelhamento de banco de dados no Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-2.png "Espelhamento de banco de dados no Microsoft Azure")

>[AZURE.NOTE]Em ambas as arquiteturas, é necessário um controlador de domínio. No entanto, com o espelhamento de banco de dados, é possível usar certificados de servidor para eliminar a necessidade de um controlador de domínio.

##Outros serviços da plataforma Azure
Serviços de nuvem do Azure são compilados no Azure, portanto, se beneficiam dos recursos da plataforma descritos anteriormente para se recuperar de falhas locais. Em alguns casos, há ações específicas que você pode tomar para aumentar a disponibilidade para seu cenário específico.

###Barramento de Serviço
Para atenuar uma interrupção temporária do Barramento de Serviço do Azure, considere a criação de uma fila durável do lado do cliente. Isso usa temporariamente um mecanismo de armazenamento local alternativo para armazenar mensagens que não podem ser adicionadas à fila de Barramento de Serviço. O aplicativo pode decidir como lidar com as mensagens armazenadas temporariamente depois que o serviço for restaurado. Para obter mais informações, consulte [Práticas recomendadas para melhorias de desempenho usando o sistema de mensagens agenciado do Barramento de Serviço](../service-bus/service-bus-performance-improvements.md). Para obter mais informações, consulte [Barramento de Serviço (recuperação de desastre)](./resiliency-technical-guidance-recovery-loss-azure-region.md#service-bus).

###Serviços Móveis
Há duas considerações de disponibilidade para os Serviços Móveis do Azure. Primeiro, faça regularmente o backup do Banco de Dados SQL do Azure associado ao seu serviço móvel. Além disso, faça backup de seus scripts do serviço móvel. Para obter mais informações, consulte [Recuperar o serviço móvel em caso de desastre](../mobile-services/mobile-services-disaster-recovery.md). Se os Serviços Móveis apresentarem uma interrupção temporária, talvez você precise usar temporariamente um datacenter alternativo do Azure. Para obter mais informações, consulte [Serviços Móveis (recuperação de desastre)](./resiliency-technical-guidance-recovery-loss-azure-region.md#mobile-services).

###HDInsight
Os dados associados ao HDInsight são armazenados por padrão no Armazenamento de Blobs do Azure, que tem as propriedades de alta disponibilidade e durabilidade especificadas pelo Armazenamento do Azure. O processamento de vários nós associado aos trabalhos do Hadoop MapReduce é feito em um HDFS (Sistema de Arquivos Distribuído Hadoop) transitório que é provisionado quando requerido pelo HDInsight. Os resultados de um trabalho MapReduce também são armazenados por padrão no Armazenamento de Blobs do Azure, para que os dados processados sejam duráveis e permaneçam altamente disponíveis depois que o cluster Hadoop for desprovisionado. Para obter mais informações, consulte [HDInsight (recuperação de desastre)](./resiliency-technical-guidance-recovery-loss-azure-region.md#hdinsight).

##Listas de verificação: falhas locais
 
##Lista de verificação de serviços de nuvem
  1. Examinar a seção [Serviços de Nuvem](#cloud-services) deste documento
  2. Configurar pelo menos duas instâncias para cada função
  3. Manter o estado no armazenamento durável, não em instâncias de função
  4. Tratar corretamente o evento StatusCheck
  5. Encapsular alterações relacionadas em transações, quando possível
  6. Verificar se as tarefas de função de trabalho são idempotentes e reinicializáveis
  7. Continuar a invocar operações até que tenham êxito
  8. Considerar estratégias de dimensionamento automático

##Lista de verificação de máquinas virtuais
  1. Examinar a seção [Máquinas Virtuais](#virtual-machines) deste documento
  2. Não usar a unidade D: para o armazenamento persistente
  3. Agrupar computadores em uma camada de serviço em um conjunto de disponibilidade
  4. Configurar o balanceamento de carga e investigações opcionais
 
##Lista de verificação de armazenamento
  1. Examinar a seção [Armazenamento](#storage) deste documento
  2. Usar várias contas de armazenamento quando dados ou largura de banda exceder as cotas

##Lista de verificação de Banco de Dados SQL
  1. Examinar a seção [Banco de Dados SQL](#sql-database) deste documento
  2. Implementar uma política de repetição para tratar de erros transitórios
  3. Usar o particionamento/fragmentação como uma estratégia de escalonamento horizontal
  
##Lista de verificação do SQL Server em máquinas virtuais
  1. Examinar a seção [SQL Server em máquinas virtuais](#sql-server-on-virtual-machines) deste documento
  2. Seguir as recomendações anteriores para máquinas virtuais
  3. Usar recursos de alta disponibilidade do SQL Server, como AlwaysOn
  
##Lista de verificação do Barramento de Serviço
  1. Examinar a seção [Barramento de Serviço](#service-bus) deste documento
  2. Considerar a criação de uma fila durável do lado do cliente como um backup

##Lista de verificação do HDInsight
  1. Examinar a seção [HDInsight](#hdinsight) deste documento
  2. Nenhuma etapa adicional de disponibilidade necessária para falhas locais
 
##Próximas etapas
Este artigo faz parte de uma série que tem como foco [Orientações técnicas de resiliência do Azure](./resiliency-technical-guidance.md). O próximo artigo desta série se concentra em [recuperação de uma interrupção do serviço em toda uma região](./resiliency-technical-guidance-recovery-loss-azure-region.md).

<!---HONumber=AcomDC_0608_2016-->