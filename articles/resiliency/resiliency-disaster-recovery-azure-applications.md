<properties
   pageTitle="Recuperação de Desastre para Aplicativos do Azure | Microsoft Azure"
   description="Visões gerais técnicas e informações detalhadas sobre como projetar aplicativos para recuperação de desastre no Microsoft Azure."
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
   ms.date="05/18/2016"
   ms.author="hanuk;jroth;aglick"/>

#Recuperação de desastre para aplicativos criados no Microsoft Azure

##Introdução à recuperação de desastre
Enquanto a alta disponibilidade trata do gerenciamento de falhas temporárias, a DR (recuperação de desastre) trata da perda catastrófica de funcionalidades de aplicativos. Por exemplo, considere um cenário em que uma região fica inativa. Nesse caso, você precisa ter um plano para executar seu aplicativo ou acessar seus dados fora da região do Azure em questão. A execução desse plano envolve pessoas, processos e aplicativos de suporte que permitem que o sistema funcione. Os responsáveis pelos negócios e pela tecnologia, que definem o modo operacional em caso de desastre, determinam o nível de funcionalidade do serviço durante um desastre. Ele pode ter várias formas: totalmente indisponível, parcialmente disponível (funcionalidade reduzida ou processamento com atraso) ou totalmente disponível.

##Recursos de recuperação de desastre do Azure
Assim como há as considerações sobre disponibilidade, o Azure tem [Orientações técnicas de resiliência do Azure](./resiliency-technical-guidance.md), criadas para dar suporte à recuperação de desastre. Também há uma relação entre alguns dos recursos de disponibilidade do Azure e a recuperação de desastre. Por exemplo, o gerenciamento de funções entre domínios de falha aumenta a disponibilidade de um aplicativo. Sem esse gerenciamento, uma falha de hardware sem tratamento se tornaria um cenário de "desastre". Sendo assim, a aplicação correta de muitos dos recursos e estratégias de disponibilidade deve ser vista como uma parte importante da blindagem de seu aplicativo contra desastres. No entanto, esta seção vai além de problemas de disponibilidade gerais e aborda eventos de desastre mais graves (e mais raros).

##Múltiplas regiões de datacenter
O Azure mantém datacenters em muitas regiões diferentes do mundo. Isso dá suporte a vários cenários de recuperação de desastre, como a replicação geográfica, fornecida pelo sistema, do Armazenamento do Azure para regiões secundárias. Isso também significa que você pode implantar, de forma fácil e com baixo custo, um serviço de nuvem em vários locais do mundo. Compare isso com o custo e a dificuldade de executar seus próprios datacenters em várias regiões. Implantar serviços e dados em várias regiões protege seu aplicativo contra grandes interrupções em uma única região.

##Gerenciador de Tráfego do Azure
Quando ocorre uma falha específica a uma região, você deve redirecionar o tráfego para serviços ou implantações em outra região. Este roteamento pode ser feito manualmente, mas é mais eficiente usar um processo automatizado. O ATM (Gerenciador de Tráfego do Azure) tem essa finalidade. Ele permite que você gerencie automaticamente o failover de tráfego de usuários para outra região em caso de falha da região primária. Como o gerenciamento do tráfego é uma parte importante da estratégia geral, é importante compreender os conceitos básicos do ATM.

No diagrama a seguir, os usuários se conectam a uma URL especificada para o ATM (__http://myATMURL.trafficmanager.net__) que abstrai as URLs do site real (__http://app1URL.cloudapp.net__ e \_\___http://app2URL.cloudapp.net__)). Dependendo de como você configurar os critérios de quando rotear os usuários, eles serão enviados para o site real correto quando a política determinar. As opções de política são round robin, desempenho ou failover. Neste artigo, abordaremos somente a opção de failover.

###Roteamento usando o Gerenciador de Tráfego do Microsoft Azure

![Roteamento usando o Gerenciador de Tráfego do Azure](./media/resiliency-disaster-recovery-azure-applications/routing-using-azure-traffic-manager.png "Roteamento usando o Gerenciador de Tráfego do Azure")

_Roteamento usando o Gerenciador de Tráfego do Azure_

Ao configurar o ATM, você fornecerá um novo prefixo DNS do Gerenciador de Tráfego. Trata-se do prefixo da URL que você fornecerá aos usuários para que eles acessem seu serviço. Agora, o ATM abstrai o balanceamento de carga um nível acima, e não no nível da região. O DNS do Gerenciador de Tráfego é mapeado para um CNAME para todas as implantações que gerencia.

No ATM, você especifica a prioridade das implantações para as quais os usuários serão roteados quando ocorrer uma falha. O ATM monitora os pontos de extremidade das implantações e observa quando a implantação primária falha. No momento da falha, o ATM analisará a lista priorizada de implantações e encaminhará os usuários para a próxima implantação da lista.

Enquanto o ATM decide para onde ir durante um failover, você pode decidir se o seu domínio de failover fica inativo ou ativo quando NÃO estiver no modo de failover. Essa funcionalidade não tem nada a ver com o Gerenciador de Tráfego do Azure. O ATM detecta uma falha no site primário e faz a substituição para o site de failover. O ATM faz isso independentemente de o site estar atendendo usuários ativamente ou não. Mais informações sobre domínios de failover inativos ou ativos podem ser encontradas em seções posteriores deste documento.

Para obter mais informações sobre o funcionamento do Gerenciador de Tráfego do Azure, consulte os links a seguir:

 * [Visão geral do Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md)
 * [Configurar o método de roteamento de failover](../traffic-manager/traffic-manager-configure-failover-routing-method.md)

##Cenários de desastre do Azure
As seções a seguir abrangem vários tipos diferentes de cenários de desastre. Interrupções de serviço que afetam toda a região não são a única causa de falhas que afetam todo o aplicativo. Design inadequado ou erros de administração também podem levar a interrupções. É importante levar em consideração as possíveis causas de uma falha durante as fases de design e teste de seu plano de recuperação. Um bom plano aproveita os recursos do Azure e os aperfeiçoa com estratégias específicas ao aplicativo. A resposta escolhida é determinada pela importância do aplicativo, pelo RPO e pelo RTO.

###Falha do aplicativo
Conforme mencionado anteriormente, os Recursos de Infraestrutura do Azure tratam de falhas resultantes do software do sistema operacional ou do hardware subjacente na máquina virtual do host. O Azure cria uma nova instância da função em um servidor operacional e a adiciona à rotação do balanceador de carga. Se o número de instâncias de função for maior do que um, o Azure passará o processamento para as outras instâncias de função em execução enquanto substitui o nó com falha.

Há erros graves de aplicativo que ocorrem independentemente de quaisquer falhas de hardware ou sistema operacional. O aplicativo pode falhar devido às exceções catastróficas causadas por problemas de integridade de dados ou lógica incorreta. Você precisa incorporar telemetria suficiente ao código para que um sistema de monitoramento possa detectar condições de falha e notificar um administrador do aplicativo. Um administrador com total conhecimento dos processos de recuperação de desastre pode tomar a decisão de invocar um processo de failover. Como alternativa, o administrador poderia simplesmente aceitar uma interrupção da disponibilidade para resolver os erros críticos.

###Dados corrompidos
O Azure armazena automaticamente os dados do Banco de Dados SQL do Azure e do Armazenamento do Azure três vezes, de forma redundante, em diferentes domínios de falha na mesma região. Se a replicação geográfica for usada, eles serão armazenados mais três vezes em uma região diferente. No entanto, se os usuários ou o aplicativo corromperem esses dados na cópia primária, eles serão replicados rapidamente para as outras cópias. Infelizmente, isso resulta em três cópias de dados corrompidos.

Para gerenciar a possível corrupção dos dados, você tem duas opções. Primeiro, você pode gerenciar uma estratégia de backup personalizada. Você pode armazenar seus backups no Azure ou localmente, dependendo de seus requisitos de negócios ou de normas governamentais. Também é possível usar a nova opção de Restauração Pontual para recuperação do banco de dados SQL. Para obter mais informações, consulte a seção sobre [estratégias de dados para recuperação de desastre](#data-strategies-for-disaster-recovery).

##Interrupção de rede
Quando partes da rede do Azure estiverem inacessíveis, é possível que você não consiga acessar seu aplicativo ou dados. Se uma ou mais instâncias de função estiverem indisponíveis devido a problemas de rede, o Azure aproveitará as instâncias disponíveis restantes do aplicativo. Se o aplicativo não conseguir acessar seus dados devido a uma interrupção de rede do Azure, será possível operar localmente em modo degradado usando dados armazenados em cache. Você precisa arquitetar a estratégia de recuperação de desastre para execução no modo degradado em seu aplicativo. Para alguns aplicativos, talvez isso não seja prático.

Outra opção é armazenar dados em um local alternativo até que a conectividade seja restaurada. Se o modo degradado não for uma opção, as opções restantes serão o tempo de inatividade do aplicativo ou failover para uma região alternativa. O design de um aplicativo que é executado em modo degradado é tanto uma decisão de negócios quanto técnica. Isso é discutido com mais detalhes na seção [funcionalidade degradada do aplicativo](#degraded-application-functionality).

##Falha de serviço dependente
O Azure fornece muitos serviços que podem passar por tempo de inatividade periodicamente. Considere o [Cache Redis do Azure](https://azure.microsoft.com/services/cache/) como um exemplo. Esse serviço multilocatário fornece recursos de cache para seu aplicativo. Será importante levar em consideração o que acontece em seu aplicativo se o serviço dependente ficar indisponível. Em muitos aspectos, esse cenário é semelhante ao cenário de interrupção da rede. No entanto, considerar cada serviço de forma independente resulta em potenciais melhorias no plano geral.

O Cache Redis do Azure fornece cache ao seu aplicativo de dentro da implantação do serviço de nuvem, o que traz benefícios em termos de recuperação de desastre. Primeiro, o serviço agora é executado em funções que são locais com relação à sua implantação. Portanto, você conseguirá monitorar e gerenciar melhor o status do cache como parte dos processos de gerenciamento geral do serviço de nuvem. Esse tipo de cache também apresenta novos recursos. Um dos novos recursos é a alta disponibilidade para dados armazenados em cache. Isso ajuda a preservar dados armazenados em cache caso um único nó falhe, mantendo cópias duplicadas em outros nós. Observe que a alta disponibilidade diminui a taxa de transferência e aumenta a latência devido à atualização da cópia secundária quando há gravações. Ela também dobra a quantidade de memória usada para cada item, portanto, planeje-se para isso. Este exemplo específico demonstra que cada serviço dependente pode ter recursos que melhoram a disponibilidade e a resistência geral a falhas catastróficas.

Com cada serviço dependente, você precisa entender as implicações de uma interrupção do serviço. No exemplo do cache, talvez seja possível acessar os dados diretamente por meio de um banco de dados até que você restaure seu cache. Esse seria um modo degradado em termos de desempenho, mas forneceria funcionalidade completa em termos de dados.

##Interrupção do serviço em toda uma região
As falhas anteriores foram, principalmente, falhas que poderiam ser gerenciadas dentro de uma mesma região do Azure. No entanto, você também deve se preparar para a possibilidade de que haja uma interrupção do serviço em toda a região. Se ocorrer uma interrupção do serviço em toda a região, as cópias dos seus dados que forem redundantes localmente não ficarão disponíveis. Se você tiver habilitado a replicação geográfica, haverá três cópias adicionais dos seus blobs e tabelas em uma região diferente. Se a Microsoft declarar a região como perdida, o Azure remapeará todas as entradas DNS para a região da replicação geográfica.

>[AZURE.NOTE]Lembre-se de que você não tem nenhum controle sobre esse processo e de que ele ocorrerá apenas em caso de interrupção do serviço em toda uma região. Por isso, você também deve contar com outras estratégias de backup específicas ao aplicativo para chegar ao nível mais alto de disponibilidade. Para obter mais informações, consulte a seção sobre [estratégias de dados para recuperação de desastre](#data-strategies-for-disaster-recovery).

##Interrupção do serviço em todo o Azure
No planejamento para desastres, você deve levar em consideração toda a gama de possíveis desastres. Uma das interrupções de serviço mais graves envolveria todas as regiões do Azure simultaneamente. Assim como acontece com outros tipos de interrupção de serviço, você pode optar por correr o risco de que haja um tempo de inatividade temporário caso a situação ocorra. Interrupções de serviço amplas que afetam várias regiões devem ser muito mais raras do que interrupções de serviço isoladas que envolvem serviços dependentes ou regiões únicas. No entanto, para alguns aplicativos críticos, você pode decidir se deve haver um plano de backup para esse cenário também. O plano para esse caso poderia incluir failover para serviços em uma [nuvem alternativa](#alternative-clouds) ou uma [solução híbrida local e na nuvem](#hybrid-on-premises-and-cloud-solutions).

##Funcionalidade degradada do aplicativo
Um aplicativo bem projetado normalmente usa um conjunto de módulos que se comunicam uns com os outros por meio da implementação de padrões de troca de informações com acoplamento pouco rígido. Um aplicativo adequado à recuperação de desastre requer, especificamente, a separação de tarefas no nível de módulo. Isso ocorre para impedir que a interrupção de um serviço dependente faça com que o aplicativo inteiro fique inativo. Por exemplo, considere o aplicativo de comércio eletrônico da Empresa Y. Os seguintes módulos podem constituir o aplicativo:

 * __Catálogo de produtos__: permite que os usuários naveguem pelos produtos
 * __Carrinho de compras__: permite aos usuários adicionem/removam produtos de seu carrinho de compras
 * __Status do pedido__: mostra o status de envio dos pedidos do usuário
 * __Envio de pedidos__: finaliza a sessão de compras enviando o pedido com o pagamento
 * __Processamento de pedidos__: valida o pedido com relação à integridade de dados e executa a verificação de disponibilidade de quantidade

Quando um serviço dependente de um módulo deste aplicativo falhar, como o módulo funcionará até que a parte em questão seja recuperada? Um sistema bem projetado implementa limites de isolamento por meio da separação de tarefas no momento do design e no momento da execução. Você pode categorizar cada falha como recuperável e não recuperável. Erros não recuperáveis desativarão o módulo, mas é possível atenuar erros recuperáveis com alternativas. Conforme discutido na seção sobre alta disponibilidade, você pode ocultar alguns problemas dos usuários tratando de falhas e adotando ações alternativas. Durante uma interrupção de serviço mais grave, o aplicativo pode ficar completamente indisponível. No entanto, uma terceira opção é continuar atendendo os usuários no modo degradado.

Por exemplo, se o banco de dados que hospeda pedidos ficar inativo, o módulo de Processamento de pedidos perderá a capacidade de processar transações de vendas. Dependendo da arquitetura, pode ser difícil ou impossível que as partes de Envio de pedidos e Processamento de pedidos do aplicativo continuem. Se o aplicativo não tiver sido projetado para lidar com esse cenário, o aplicativo inteiro poderá ficar offline.

No entanto, nesse mesmo cenário, é possível que os dados de produtos sejam armazenados em um local diferente. Nesse caso, o módulo de Catálogo de produtos ainda pode ser usado para exibir produtos. No modo degradado, o aplicativo continua disponível para os usuários para a funcionalidade disponível, como exibir o catálogo de produtos. Outras partes do aplicativo, entretanto, ficarão indisponíveis, como a realização de pedidos ou consultas de estoque.

Outra variação do modo degradado tem como foco o desempenho em vez dos recursos. Por exemplo, considere um cenário em que o catálogo de produtos estava sendo armazenado em cache com o Cache Redis do Azure. Se o armazenamento em cache ficasse indisponível, seria possível que o aplicativo fosse diretamente ao armazenamento do servidor para recuperar informações do catálogo de produtos. Mas esse acesso pode ser mais lento do que a versão armazenada em cache. Por isso, o desempenho do aplicativo ficaria degradado até que o serviço de cache fosse restaurado totalmente.

Decidir quanto de um aplicativo continuará funcionando em modo degradado é tanto uma decisão de negócios quando técnica. O aplicativo também deve decidir como informar os usuários sobre problemas temporários. Nesse exemplo, o aplicativo poderia permitir a exibição de produtos e até mesmo sua adição a um carrinho de compras. No entanto, quando o usuário tentasse fazer uma compra, o aplicativo o notificaria de que o módulo de vendas está inacessível temporariamente. Não é ideal para o cliente, mas evita a interrupção do serviço de todo o aplicativo.

##Estratégias de dados para recuperação de desastre
Lidar com dados corretamente é a área mais difícil de acertar em qualquer plano de recuperação de desastre. A restauração de dados também é a parte do processo de recuperação que geralmente leva mais tempo. Escolhas diferentes com relação aos modos de degradação levam a desafios difíceis em termos de recuperação de dados após uma falha e consistência após uma falha. Um dos fatores é a necessidade de restaurar ou manter uma cópia dos dados do aplicativo. Você usará esses dados para fins de referência e transacionais em um site secundário. Uma configuração local requer um processo de planejamento caro e longo para implementar uma estratégia de recuperação de desastre envolvendo várias regiões. De foma conveniente, a maioria dos provedores de nuvem, incluindo o Azure, permitem prontamente a implantação de aplicativos em várias regiões. Essas regiões são distribuídas geograficamente de forma que a interrupção do serviço em várias regiões deve ser extremamente rara. A estratégia para lidar com dados entre regiões é um dos fatores que contribuem para o sucesso de qualquer plano de recuperação de desastre.

As seções a seguir discutem as técnicas de recuperação de desastre relacionadas a backups de dados, dados de referência e dados transacionais.

##Backup e restauração
Backups regulares dos dados do aplicativo podem dar suporte a alguns cenários de recuperação de desastre. Diferentes recursos de armazenamento requerem diferentes técnicas.

Para as camadas Basic, Standard e Premium do Banco de Dados SQL, você pode aproveitar a restauração pontual para recuperar o banco de dados. Para obter mais informações, consulte [Visão geral: continuidade de negócios em nuvem e recuperação de desastre do banco de dados com o banco de dados SQL](../sql-database/sql-database-business-continuity.md). Outra opção é usar a Replicação Geográfica Ativa para o Banco de Dados SQL. Ela replica automaticamente alterações no banco de dados para bancos de dados secundários na mesma região ou até mesmo em uma região diferente do Azure. Isso oferece uma alternativa potencial para algumas das técnicas de sincronização de dados mais manuais apresentadas neste artigo. Para obter mais informações, consulte [Visão geral: Replicação Geográfica Ativa para o Banco de Dados SQL](../sql-database/sql-database-geo-replication-overview.md).

Você também pode usar uma abordagem mais manual para o backup e restauração. Use o comando DATABASE COPY para criar uma cópia do banco de dados. Você deve usar esse comando para obter um backup com consistência transacional. Você também pode aproveitar o serviço de importação/exportação do Banco de Dados SQL do Azure. Isso dá suporte à exportação de bancos de dados para arquivos BACPAC que são armazenados no Armazenamento de Blobs do Azure. A redundância interna do Armazenamento do Azure cria duas réplicas do arquivo de backup na mesma região. No entanto, a frequência de execução do processo de backup determina o RPO, que é a quantidade de dados que você pode perder em cenários de desastre. Por exemplo, você executa um backup no início de cada hora e o desastre ocorre dois minutos antes do início da hora. Você perde 58 minutos de dados que ocorreram após o último backup ter sido executado. Além disso, para se proteger de uma interrupção do serviço de toda a região, você deve copiar os arquivos BACPAC para uma região alternativa. Depois, você tem a opção de restaurar esses backups na região alternativa. Para obter mais detalhes, consulte [Visão geral: continuidade de negócios em nuvem e recuperação de desastre do banco de dados com o banco de dados SQL](../sql-database/sql-database-business-continuity.md).

Para o Armazenamento do Azure, você pode desenvolver seu próprio processo de backup personalizado ou usar uma das diversas ferramentas de backup de terceiros. Observe que há complexidades adicionais na maioria dos designs de aplicativos em que os recursos de armazenamento fazem referência entre si. Por exemplo, considere um Banco de Dados SQL que tem uma coluna com vínculo a um blob no Armazenamento do Azure. Se os backups não acontecerem simultaneamente, o banco de dados poderá ter o ponteiro voltado a um blob do qual não foi feito backup antes da falha. O plano de recuperação de desastre ou aplicativo deve implementar processos para tratar dessa inconsistência após uma recuperação.

##Padrão de dados de referência para a recuperação de desastre
Conforme mencionado anteriormente, dados de referência são dados somente leitura que dão suporte à funcionalidade do aplicativo. Normalmente, eles não mudam com muita frequência. Embora o backup e restauração seja um método para lidar com interrupções de serviço em toda uma região, o RTO é relativamente longo. Quando você implanta o aplicativo em uma região secundária, há algumas estratégias que melhoram o RTO para dados de referência.

Como os dados de referência mudam com pouca frequência, você pode melhorar o RTO mantendo uma cópia permanente dos dados de referência na região secundária. Isso elimina o tempo necessário para restaurar backups no caso de um desastre. Para atender aos requisitos de recuperação de desastre em várias regiões, você deve implantar o aplicativo e os dados de referência juntos em várias regiões. Conforme mencionado no [Padrão de dados de referência para alta disponibilidade](./resiliency-high-availability-azure-applications.md#reference-data-pattern-for-high-availability), você pode implantar dados de referência na própria função, no armazenamento externo ou em uma combinação de ambos. O modelo de implantação de dados de referência dentro do nó de computação atende implicitamente aos requisitos de recuperação de desastre. A implantação de dados de referência no Banco de Dados SQL requer que você implante uma cópia dos dados de referência em cada região. A mesma estratégia se aplica ao Armazenamento do Azure. Você deve implantar uma cópia de quaisquer dados de referência armazenados no Armazenamento do Azure nas regiões primária e secundária.

###Publicação de dados de referência em várias regiões

![Publicação de dados de referência nas regiões primária e secundária](./media/resiliency-disaster-recovery-azure-applications/reference-data-publication-to-both-primary-and-secondary-regions.png "Publicação de dados de referência nas regiões primária e secundária")

_Publicação de dados de referência nas regiões primária e secundária_

Conforme mencionado anteriormente, você precisa implementar suas próprias rotinas de backup específicas ao aplicativo para todos os dados, incluindo os dados de referência. Cópias com replicação geográfica entre regiões são usadas somente no caso de uma interrupção de serviço que afete toda uma região. Para evitar um tempo de inatividade estendido, implante as partes críticas dos dados do aplicativo na região secundária. Para ver um exemplo dessa topologia, consulte o [modelo ativo-passivo](#active-passive).

##Padrão de dados transacionais para a recuperação de desastre
A implementação de uma estratégia de modo de desastre totalmente funcional exige a replicação assíncrona dos dados transacionais para a região secundária. As janelas de tempo práticas em que a replicação pode ocorrer determinarão as características de RPO do aplicativo. Você ainda pode recuperar os dados perdidos da região primária durante a janela de replicação. Você também pode conseguir mesclar com a região secundária posteriormente.

Os exemplos de arquitetura a seguir fornecem algumas ideias sobre diferentes maneiras de lidar com dados transacionais em um cenário de failover. É importante observar que esses exemplos não são exaustivos. Por exemplo, locais de armazenamento intermediários, como filas, podem ser substituídos pelo Banco de Dados SQL do Azure. As próprias filas podem ser filas do Barramento de Serviço ou do Armazenamento do Azure (consulte [Filas do Azure e filas do Barramento de Serviço do Azure - comparadas e contrastadas](../service-bus/service-bus-azure-and-service-bus-queues-compared-contrasted.md)). Os destinos de armazenamento do servidor também podem variar, como tabelas do Azure em vez do Banco de Dados SQL. Além disso, pode haver funções de trabalho inseridas como intermediárias em várias etapas. O importante não é emular exatamente essas arquiteturas, mas levar em consideração várias alternativas na recuperação de dados transacionais e módulos relacionados.

Considere um aplicativo que usa filas do Armazenamento do Azure para manter dados transacionais. Isso permite que as funções de trabalho processem os dados transacionais no banco de dados do servidor em uma arquitetura desacoplada. Conforme discutido, isso requererá que as transações usem alguma forma de cache temporário se as funções front-end exigirem a consulta imediata desses dados. Dependendo do nível de tolerância a perda de dados, você pode optar por replicar as filas, o banco de dados ou todos os recursos de armazenamento. Com apenas a replicação do banco de dados, se a região primária ficar inativa, você ainda poderá recuperar os dados nas filas quando a região primária voltar. O diagrama a seguir mostra uma arquitetura em que o banco de dados do servidor é sincronizado entre regiões.

###Replicar dados transacionais para se preparar para a recuperação de desastre

![Replicar dados transacionais para se preparar para a recuperação de desastre](./media/resiliency-disaster-recovery-azure-applications/replicate-transactional-data-in-preparation-for-disaster-recovery.png "Replicar dados transacionais para se preparar para a Recuperação de desastre")

_Replicar dados transacionais para se preparar para a recuperação de desastre_

O maior desafio para implementar a arquitetura anterior é a estratégia de replicação entre regiões. O Azure fornece o serviço de Sincronização de Dados do SQL para esse tipo de replicação. No entanto, o serviço está em preview e não é recomendado para ambientes de produção. Para obter mais informações, consulte [Visão geral: continuidade de negócios em nuvem e recuperação de desastre do banco de dados com o banco de dados SQL](../sql-database/sql-database-business-continuity.md). Para aplicativos de produção, você deve investir em uma solução de terceiros ou criar sua própria lógica de replicação no código. Dependendo da arquitetura, a replicação pode ser bidirecional, o que também é mais complexo. Uma implementação potencial poderia fazer uso da fila intermediária no exemplo anterior. A função de trabalho que processa os dados para o destino de armazenamento final pode fazer a alteração na região primária e na secundária. Essas tarefas não são triviais e uma orientação completa para a criação de código de replicação vai além do escopo deste documento. A questão importante é que grande parte do seu tempo e seus testes deve se concentrar em como você replica seus dados para a região secundária. Processamento e testes adicionais devem ser realizados para garantir que os processos de failover e recuperação tratem corretamente de quaisquer inconsistências de dados ou transações duplicadas possíveis.

>[AZURE.NOTE]A maior parte deste documento se concentra na Plataforma como serviço. No entanto, há opções adicionais de replicação e disponibilidade para aplicativos híbridos que usam as Máquinas Virtuais do Azure. Esses aplicativos híbridos usam IaaS (Infraestrutura como serviço) para hospedar o SQL Server em máquinas virtuais no Azure. Isso permite abordagens tradicionais de disponibilidade no SQL Server, como Grupos de Disponibilidade AlwaysOn ou Envio de Logs. Algumas técnicas, como o AlwaysOn, funcionam entre servidores SQL locais e máquinas virtuais do Azure. Para saber mais, consulte [Alta disponibilidade e recuperação de desastres para o SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

Considere uma segunda arquitetura que opera em modo degradado. O aplicativo na região secundária desabilita toda a funcionalidade, como relatórios, BI ou filas de drenagem. Ele aceita apenas os tipos mais importantes de fluxos de trabalho transacionais, conforme definido pelos requisitos de negócios. O sistema captura as transações e as grava em filas. O sistema pode adiar o processamento de dados durante a fase inicial da interrupção do serviço. Se o sistema na região primária for reativado dentro da janela de tempo esperada, as funções de trabalho na região primária poderão drenar as filas. Esse processo elimina a necessidade de mesclar o banco de dados. Se a interrupção do serviço na região primária for além da janela tolerável, o aplicativo poderá iniciar o processamento das filas. Nesse cenário, o banco de dados na região secundária contém dados transacionais que devem ser mesclados após a reativação da região primária. O diagrama a seguir mostra essa estratégia para armazenar temporariamente dados transacionais até que a região primária seja restaurada.

###Modo de aplicativo degradado para captura de transação

![Modo de aplicativo degradado para captura de transação](./media/resiliency-disaster-recovery-azure-applications/degraded-application-mode-for-transaction-capture.png "Modo de aplicativo degradado para captura de transação")

_Modo de aplicativo degradado para captura de transação_

Para obter mais informações sobre técnicas de gerenciamento de dados para aplicativos resilientes do Azure, consulte [Failsafe: Guidance for Resilient Cloud Architectures (À prova de falhas: orientação para arquiteturas de nuvem resilientes)](https://channel9.msdn.com/Series/FailSafe).

##Topologias de implantação para recuperação de desastre
Prepare aplicativos críticos para a possibilidade de uma interrupção de serviço em toda a região. Você faz isso incorporando uma estratégia de implantação em várias regiões ao planejamento operacional. Implantações em várias regiões podem envolver processos profissionais de TI para publicar o aplicativo e os dados de referência na região secundária após um desastre. Se o aplicativo exigir um failover instantâneo, o processo de implantação poderá envolver uma configuração ativo/passivo ou ativo/ativo. Esse tipo de implantação tem instâncias existentes do aplicativo em execução na região alternativa. Conforme discutido, uma ferramenta de roteamento como o Gerenciador de Tráfego do Azure fornece serviços de balanceamento de carga no nível do DNS. Ela pode detectar interrupções de serviço e encaminhar os usuários para diferentes regiões quando for necessário.

Parte de uma recuperação de desastre bem-sucedida no Azure é planejar essa recuperação na solução desde o início. A nuvem oferece opções adicionais para se recuperar de falhas durante um desastre que não estão disponíveis em um provedor de hospedagem tradicional. Especificamente, você pode alocar, de maneira rápida e dinâmica, recursos para uma região diferente. Assim, você não pagará muito por recursos ociosos enquanto espera uma falha ocorrer.

As seções a seguir abrangem diferentes topologias de implantação para recuperação de desastre. Normalmente, a disponibilidade adicional acarreta maiores custos ou complexidade.

##Implantação em uma região
Uma implantação em uma região não é de fato uma topologia de recuperação de desastre, mas a finalidade é contrastar com as outras arquiteturas. Implantações em uma região são comuns para aplicativos no Azure. Não se trata, no entanto, de uma opção forte para um plano de recuperação de desastre. O diagrama a seguir mostra um aplicativo em execução em uma única região do Azure. Conforme discutido anteriormente, os Recursos de Infraestrutura do Azure e o uso de domínios de falha e atualização aumentam a disponibilidade do aplicativo dentro da região.

###Implantação em uma região

![Implantação em uma região](./media/resiliency-disaster-recovery-azure-applications/single-region-deployment.png "Implantação em uma região")

_Implantação em uma região_

Aqui fica aparente que o banco de dados é um único ponto de falha. Embora o Azure replique os dados em diferentes domínios de falha para réplicas internas, tudo isso ocorre na mesma região. Assim, não é possível resistir a uma falha catastrófica. Se a região ficar inativa, todos os domínios de falha ficarão inativos, o que inclui todas as instâncias de serviço e recursos de armazenamento.

Para todos os aplicativos, exceto pelos menos críticos, você deve desenvolver um plano para implantar o aplicativo em várias regiões. Você também deve levar em consideração as restrições de custo e RTO ao avaliar qual topologia de implantação usar.

Agora, vamos ver padrões específicos para dar suporte ao failover em regiões diferentes. Todos estes exemplos usam duas regiões para descrever o processo.

##Reimplantar
Neste padrão, somente a região primária tem aplicativos e bancos de dados em execução. A região secundária não está configurada para um failover automático. Assim, quando um desastre ocorrer, você precisará acionar todas as partes do serviço na nova região. Isso inclui carregar um serviço de nuvem no Azure, implantar os serviços de nuvem, restaurar os dados e modificar o DNS para redirecionar o tráfego.

Embora se trate da mais acessível entre as opções multirregião, ela tem as piores características de RTO. Nesse modelo, os backups de banco de dados e o pacote de serviço são armazenados localmente ou no armazenamento de blobs da região secundária. No entanto, você precisa implantar um novo serviço e restaurar os dados antes de retomar a operação. Mesmo que você automatize completamente a transferência de dados do armazenamento de backup, acionar o novo ambiente de banco de dados consome muito tempo. A parte mais cara da restauração é mover os dados do armazenamento de backup em disco para o banco de dados vazio na região secundária. No entanto, você precisa fazer isso para colocar o novo banco de dados em um estado operacional, uma vez que ele não é replicado.

A melhor abordagem é armazenar os pacotes de serviço no Armazenamento de Blobs do Azure na região secundária. Isso elimina a necessidade de carregar o pacote no Azure, que é o que acontece quando você implanta por meio de uma máquina de desenvolvimento local. Você pode implantar rapidamente os pacotes de serviço em um novo serviço de nuvem por meio do armazenamento de blobs usando scripts do PowerShell.

Essa opção só é prática para aplicativos não críticos que podem tolerar um RTO alto. Por exemplo, pode funcionar para um aplicativo que pode ficar inativo por várias horas, mas que precisa estar em execução novamente dentro de 24 horas.

###Reimplantar para uma região secundária do Azure

![Reimplantar para uma região secundária do Azure](./media/resiliency-disaster-recovery-azure-applications/redeploy-to-a-secondary-azure-region.png "Reimplantar para uma região secundária do Azure")

_Reimplantar para uma região secundária do Azure_

##Ativo-passivo
O padrão ativo-passivo é a opção preferida por muitas empresas. Esse padrão traz melhorias ao RTO com um aumento relativamente pequeno do custo com relação ao padrão de reimplantação. Nesse cenário, há novamente uma região principal e uma região secundária do Azure. Todo o tráfego vai para a implantação ativa na região primária. A região secundária está melhor preparada para recuperação de desastre, pois o banco de dados está sendo executado em ambas as regiões. Além disso, há um mecanismo de sincronização entre elas. Essa abordagem de espera pode envolver duas variações: uma abordagem apenas de banco de dados ou uma implantação completa na região secundária.

Na primeira variação do padrão ativo-passivo, somente a região primária tem um aplicativo de serviço de nuvem implantado. No entanto, diferente do padrão de reimplantação, as duas regiões são sincronizadas com o conteúdo do banco de dados (consulte a seção sobre [padrão de dados transacionais para a recuperação de desastre](#transactional-data-pattern-for-disaster-recovery)). Quando ocorre um desastre, há menos requisitos de ativação. Você iniciar o aplicativo na região secundária, muda cadeias de conexão para o novo banco de dados e altera as entradas DNS para redirecionar o tráfego.

Assim como o padrão de reimplantação, você já deve ter armazenado os pacotes de serviço no Armazenamento de Blobs do Azure na região secundária para uma implantação mais rápida. Diferente do padrão de reimplantação, você não sofre a maioria da sobrecarga que as operações de restauração de banco de dados requerem. O banco de dados já está pronto e em execução. Isso economiza um tempo significativo, o que faz desse um padrão de recuperação de desastre acessível e, portanto, o padrão mais popular.

####Ativo-passivo - somente banco de dados

![Ativo-passivo - somente banco de dados](./media/resiliency-disaster-recovery-azure-applications/active-passive-database-only.png "Ativo-passivo - somente banco de dados")

_Ativo-passivo - somente banco de dados_

Na segunda variação do padrão ativo-passivo, a região principal e a secundária têm, cada uma, uma implantação completa. Essa implantação inclui os serviços de nuvem e um banco de dados sincronizado. No entanto, somente a região primária processa ativamente solicitações de rede dos usuários. A região secundária se torna ativa apenas quando a região primária apresentar uma interrupção do serviço. Nesse caso, todas as novas solicitações de rede são encaminhadas para a região secundária. O Gerenciador de Tráfego do Azure pode gerenciar esse failover automaticamente.

O failover ocorre mais rapidamente do que na variação apenas de banco de dados porque os serviços já foram implantados. Esse padrão fornece um RTO muito baixo; a região secundária do failover deve estar pronta para entrar em atividade imediatamente após a falha da região primária.

Além do tempo de resposta mais rápido, esse padrão também tem a vantagem adicional de pré-alocar e implantar serviços de backup. Você não precisa se preocupar se uma região não tem espaço para alocar novas instâncias no caso de um desastre. Isso é importante se a sua região secundária do Azure está se aproximando da capacidade total. Não há nenhuma garantia (SLA) de que você poderá implantar instantaneamente diversos novos serviços de nuvem em qualquer região.

Para ter o tempo de resposta mais rápido com esse modelo, você precisa ter escalas (número de instâncias de função) semelhantes nas regiões primária e secundária. Apesar das vantagens, pagar por instâncias de computação não utilizadas é caro e pode não ser a opção financeira mais aconselhável. Por isso, é mais comum usar uma versão um pouco reduzida dos serviços de nuvem na região secundária. Dessa forma, você poderá fazer o failover rapidamente e escalar horizontalmente a implantação secundária se for necessário. Você deve automatizar o processo de failover para que, quando a região primária estiver inacessível, ativar instâncias adicionais dependendo da carga. Isso pode envolver o uso de um mecanismo de dimensionamento automático, como os [Conjuntos de Escala de Máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). O diagrama a seguir mostra o modelo em que as regiões primária e secundária contêm um serviço de nuvem totalmente implantado em um padrão de ativo-passivo.

###Ativo-passivo - réplica completa

![Ativo-passivo - réplica completa](./media/resiliency-disaster-recovery-azure-applications/active-passive-full-replica.png "Ativo-passivo - Réplica completa")

_Ativo-passivo - réplica completa_

##Ativo-ativo
Neste ponto, você provavelmente está prevendo a evolução dos padrões – diminuir o RTO aumenta os custos e a complexidade. A solução com padrão ativo-ativo na verdade interrompe essa tendência em termos de custo. Em um padrão de ativo-ativo, o banco de dados e os serviços de nuvem são totalmente implantados nas duas regiões. Diferente do modelo ativo-passivo, as duas regiões recebem tráfego de usuários. Esta opção gera o tempo de recuperação mais rápido. Os serviços já estão dimensionados para lidar com parte da carga em cada região. O DNS já está habilitado para usar a região secundária. Há uma complexidade adicional para determinar como encaminhar os usuários para a região apropriada. O agendamento em round robin pode ser possível. É mais provável que determinados usuários usem uma região específica onde reside a cópia primária de seus dados.

No caso de um failover, basta desabilitar o DNS para a região primária, o que encaminha todo o tráfego para a região secundária. Mesmo nesse modelo, há algumas variações. Por exemplo, o diagrama a seguir mostra um modelo em que a região primária possui a cópia mestra do banco de dados. Os serviços de nuvem nas duas regiões são gravados nesse banco de dados primário. A implantação secundária pode ler do banco de dados primário ou do replicado. A replicação neste exemplo ocorre de forma unidirecional.

####Ativo-ativo

![Ativo-ativo](./media/resiliency-disaster-recovery-azure-applications/active-active.png "Ativo-ativo")

_Ativo-ativo_

Há uma desvantagem na arquitetura com padrão ativo-ativo no diagrama anterior. A segunda região precisa acessar o banco de dados na primeira região porque a cópia mestra reside nele. O desempenho é reduzido significativamente quando você acessa dados fora de uma região. Em chamadas de banco de dados entre regiões, você precisa considerar algum tipo de estratégia de envio em lote para melhorar o desempenho dessas chamadas. Para obter mais informações, consulte [Como usar o envio em lote para melhorar o desempenho do aplicativo Banco de Dados SQL](../sql-database/sql-database-use-batching-to-improve-performance.md). Uma arquitetura alternativa pode envolver cada região acessando seu próprio banco de dados diretamente. Nesse modelo, seria necessário algum tipo de replicação bidirecional para sincronizar os bancos de dados em cada região.

No padrão ativo-ativo, talvez não sejam necessárias tantas instâncias na região primária quanto seriam necessárias no padrão ativo-passivo. Se você tivesse dez instâncias na região primária em uma arquitetura com padrão ativo-passivo, talvez precisasse de apenas cinco em cada região em uma arquitetura com padrão ativo-ativo. Agora, as duas regiões compartilham a carga. Isso poderia representar uma economia de custos em relação ao padrão ativo-passivo se você mantivesse uma espera passiva na região passiva, com dez instâncias esperando o failover.

Observe que, até que você restaure a região primária, a região secundária pode receber um aumento repentino de novos usuários. Se houver 10.000 usuários em cada servidor quando a região primária apresentar uma interrupção do serviço, a região secundária subitamente precisará lidar com 20.000 usuários. Regras de monitoramento na região secundária devem detectar esse aumento e dobrar as instâncias na região secundária. Para obter mais informações sobre isso, consulte a seção sobre [detecção de falhas](#failure-detection).

##Soluções híbridas locais e na nuvem
Uma estratégia adicional para a recuperação de desastres é criar um aplicativo híbrido que é executado localmente e na nuvem. Dependendo do aplicativo, a região primária pode ser qualquer um desses locais. Considere as arquiteturas anteriores e imagine a região primária ou secundária como local.

Há alguns desafios nessas arquiteturas híbridas. Primeiro, a maior parte deste documento abordou padrões de arquitetura de PaaS (Plataforma como serviço). Aplicativos típicos de PaaS no Azure dependem de construções específicas do Azure, como funções, serviços de nuvem e o Controlador de Malha. Criar uma solução local para esse tipo de aplicativo PaaS exigiria uma arquitetura consideravelmente diferente. Isso pode não ser viável do ponto de vista do gerenciamento ou do custo.

No entanto, uma solução híbrida para recuperação de desastre tem menos desafios para arquiteturas tradicionais que simplesmente foram movidas para a nuvem. Isso vale para arquiteturas que usam IaaS (Infraestrutura como serviço). Aplicativos de IaaS usam máquinas virtuais na nuvem que podem ter equivalentes diretos locais. O uso de redes virtuais também permite conectar computadores na nuvem a recursos de rede locais. Isso abre várias possibilidades que não são possíveis com aplicativos somente PaaS. Por exemplo, o SQL Server aproveitar soluções de recuperação de desastre como os Grupos de Disponibilidade AlwaysOn e o espelhamento de banco de dados. Para obter mais detalhes, consulte [Alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

Soluções de IaaS também oferecem um caminho mais fácil para aplicativos locais usarem o Azure como a opção de failover. Você pode ter um aplicativo totalmente funcional em uma região local existente. No entanto, o que acontecerá se você não tiver os recursos para manter uma região separada geograficamente para failover? Você pode optar por usar redes virtuais e máquinas virtuais para executar seu aplicativo no Azure. Defina processos que sincronizam os dados para a nuvem. A implantação do Azure se torna, então, a região secundária a ser usada para failover. A região primária permanece como o aplicativo local. Para obter mais informações sobre os recursos e arquiteturas de IaaS, consulte a [Documentação das Máquinas Virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/).

##Nuvens alternativas
Há situações em que quais até mesmo a robustez da nuvem da Microsoft pode não atender às regras ou políticas internas de conformidade exigidas por sua organização. Até mesmo a melhor preparação e o melhor design para implementar sistemas de backup durante um desastre não bastarão se houver uma interrupção global do serviço de um provedor de serviços de nuvem.

É recomendável comparar os requisitos de disponibilidade com o custo e a complexidade da maior disponibilidade. Faça uma análise de risco e definia o RTO e o RPO para sua solução. Se seu aplicativo não puder tolerar nenhum tempo de inatividade, poderá fazer sentido considerar outra solução de nuvem. A menos que a Internet inteira fique inativa simultaneamente, outra solução de nuvem ainda poderá estar disponível se o Azure ficar inacessível globalmente.

Assim como no cenário híbrido, as implantações de failover nas arquiteturas de recuperação de desastre anteriores também podem existir em outra solução de nuvem. Sites alternativos de recuperação de desastre de nuvem só devem ser usados para soluções com um RTO que permita muito pouco, se algum, tempo de inatividade. Observe que uma solução que usa um site de recuperação de desastre fora do Azure exigirá mais trabalho para ser configurado, desenvolvido, implantado e mantido. Também é mais difícil implementar práticas recomendadas em arquiteturas entre nuvens. Embora plataformas de nuvem tenham conceitos gerais semelhantes, as APIs e arquiteturas são diferentes. Se você optar por dividir sua recuperação de desastre entre diferentes plataformas, faria sentido criar camadas de abstração no design da solução. Se fizer isso, você não precisará desenvolver e manter duas versões diferentes do mesmo aplicativo para diferentes plataformas de nuvem em caso de desastre. Assim como no cenário híbrido, o uso das Máquinas Virtuais do Azure ou do Serviço de Contêiner do Azure pode ser mais fácil nesses casos que designs de PaaS específicos à nuvem.

##Automação
Alguns dos padrões que acabamos de discutir exigem ativação rápida de implantações offline, bem como restauração de partes específicas de um sistema. A automação, ou script, dá suporte à capacidade de ativar recursos sob demanda e implantar soluções rapidamente. Neste artigo, a automação relacionada à recuperação de desastre é equiparada com o [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx), mas a [API REST de Gerenciamento de Serviços](https://msdn.microsoft.com/library/azure/ee460799.aspx) também é uma opção. Desenvolver scripts ajuda a gerenciar as partes da recuperação de desastre que o Azure não trata de forma transparente. Isso tem a vantagem de gerar resultados consistentes a cada vez, o que minimiza a possibilidade de erro humano. Ter scripts de recuperação de desastre predefinidos também reduz o tempo para recriação de um sistema e suas partes constituintes em meio a um desastre. Você não quer tentar descobrir manualmente como restaurar seu site enquanto ele está inativo e perdendo dinheiro a cada minuto.

Depois de criar os scripts, teste-os diversas vezes do início ao fim. Após verificar sua funcionalidade básica, certifique-se de testá-los em uma [simulação de desastre](#disaster-simulation). Isso ajuda a revelar falhas nos scripts ou processos.

É uma prática recomendada da automação criar um repositório de scripts do PowerShell de recuperação de desastre do Azure (ou scripts da CLI). Maque-os e categorize-os claramente para facilitar a pesquisa. Designe uma pessoa para gerenciar o repositório e controlar a versão dos scripts. Documente-os bem, com explicações de parâmetros e exemplos de uso dos scripts. Certifique-se também de manter esta documentação em sincronia com as implantações do Azure. Isso destaca o propósito de ter uma pessoa responsável por todas as partes do repositório.

##Detecção de falhas
Para lidar corretamente com problemas de disponibilidade e recuperação de desastre, você precisa ser capaz de detectar e diagnosticar falhas. Você deve fazer um monitoramento avançado do servidor e da implantação para saber rapidamente quando um sistema ou suas partes ficarem inativos repentinamente. Ferramentas de monitoramento que examinam a integridade geral do serviço de nuvem e suas dependências podem fazer parte desse trabalho. Uma ferramenta da Microsoft é o [System Center 2016](https://www.microsoft.com/pt-BR/server-cloud/products/system-center-2016/). Outras ferramentas de terceiros também podem fornecer recursos de monitoramento. A maioria das soluções de monitoramento monitora os principais contadores de desempenho e a disponibilidade do serviço.

Embora essas ferramentas sejam vitais, elas não dispensam a necessidade do planejamento para detecção de falhas e dos relatórios em um serviço de nuvem. Você deve se planejar para usar adequadamente o diagnóstico do Azure. Contadores de desempenho personalizados ou entradas de log de eventos também podem fazer parte da estratégia geral. Isso fornece mais dados durante falhas para diagnosticar o problema rapidamente e restaurar todos os recursos. Também fornece métricas adicionais para as ferramentas de monitoramento usarem para determinar a integridade do aplicativo. Para obter mais informações, consulte [Habilitando o diagnóstico do Azure nos Serviços de Nuvem do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). Para uma discussão sobre como planejar um "modelo de integridade" geral, consulte [Failsafe: Guidance for Resilient Cloud Architectures (À prova de falhas: orientação para arquiteturas de nuvem resilientes)](https://channel9.msdn.com/Series/FailSafe).

##Simulação de desastre
Testes de simulação envolvem a criação de situações reais de pequeno porte no local de trabalho real para observar como os membros da equipe reagem. As simulações também mostram o quanto as soluções do plano de recuperação são eficazes. Execute as simulações de forma que os cenários criados não afetem negócios reais, embora ainda pareçam situações "reais".

Considere a possibilidade de criar um tipo de "meu de controle" no aplicativo para simular manualmente problemas de disponibilidade. Por exemplo, por meio de um controle suave, dispare exceções de acesso ao banco de dados para um módulo de ordenamento fazendo com que ele não funcione corretamente. Abordagens suaves semelhantes podem ser adotadas para outros módulos no nível da interface de rede.

Quaisquer problemas que tiverem sido tratados de forma inadequada serão destacados durante a simulação. Os cenários simulados devem ser completamente controláveis. Isso significa que, mesmo que o plano de recuperação pareça estar falhando, você pode restaurar a situação voltar ao normal sem danos significativos. Também é importante que você informe o gerenciamento de nível superior sobre quando e como os exercícios de simulação serão executados. Esse plano deve incluir informações sobre o tempo ou os recursos que poderão se tornar improdutivos durante a execução do teste de simulação. Ao sujeitar seu plano de recuperação de desastre a um teste, também é importante definir como o sucesso será medido.

Há várias outras técnicas que você pode usar para testar planos de recuperação de desastre. No entanto, a maioria deles são simplesmente versões alteradas dessas técnicas básicas. A principal motivação por trás dos testes é avaliar a viabilidade e a funcionalidade do plano de recuperação. Testes de recuperação de desastre se concentram em detalhes para descobrir furos no plano básico de recuperação.

##Próximas etapas
Este artigo faz parte de uma série de artigos com foco na [Recuperação de desastres e alta disponibilidade para aplicativos criados no Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). O artigo anterior desta série é [Alta disponibilidade para aplicativos baseados no Microsoft Azure](./resiliency-high-availability-azure-applications.md).

<!---HONumber=AcomDC_0525_2016-->