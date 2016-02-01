<properties
	pageTitle="Replicação Geográfica Ativa para o Banco de Dados SQL do Azure"
	description="Este tópico explica a Replicação Geográfica Ativa para o Banco de Dados SQL e seus usos."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="10/21/2015"
	ms.author="jroth" />

# Replicação Geográfica Ativa para o Banco de Dados SQL do Azure

## Visão geral
O recurso de Replicação Geográfica Ativa implementa um mecanismo para fornecer a redundância de banco de dados na mesma região do Microsoft Azure ou em regiões diferentes (redundância geográfica). A Replicação Geográfica Ativa replica de forma assíncrona as transações confirmadas de um banco de dados para até quatro cópias do banco de dados em servidores diferentes. O banco de dados original se torna o banco de dados primário da cópia contínua. Cada cópia contínua é conhecida como um banco de dados secundário online. O banco de dados primário replica de forma assíncrona as transações confirmadas para cada um dos bancos de dados secundários online. Embora, em qualquer momento, os dados secundários online possam estar um pouco atrás do banco de dados primário, os dados secundários online têm a garantia de sempre serem transacionalmente consistentes com as alterações confirmadas no banco de dados primário. A replicação geográfica ativa dá suporte a até quatro secundários online, ou a até três secundários online e um secundário offline.

Um dos principais benefícios da Replicação Geográfica Ativa é que ela fornece uma solução de recuperação de desastre no nível do banco de dados. Com a Replicação Geográfica Ativa, é possível configurar um banco de dados de usuário na camada de serviço Premium para replicar as transações para bancos de dados em diferentes servidores do Banco de Dados SQL do Microsoft Azure em regiões iguais ou diferentes. A redundância entre regiões permite que aplicativos se recuperem da perda permanente de um datacenter causada por desastres naturais, falhas humanas catastróficas ou crimes.

Outro benefício importante é que os bancos de dados secundários online são legíveis. Portanto, um secundário online pode agir como um balanceador de carga para cargas de trabalho de leitura, como relatórios. Embora você possa criar um secundário online em uma região diferente para a recuperação de desastre, você também pode ter um secundário online na mesma região em um servidor diferente. Ambos os bancos de dados secundários online podem ser usados para equilibrar as cargas somente leitura quem atendem clientes distribuídos em várias regiões.

Entre outros cenários em que a Replicação Geográfica Ativa pode ser usada estão:

- **Migração de banco de dados**: você pode usar a Replicação Geográfica Ativa para migrar um banco de dados de um servidor para outro online com tempo de inatividade mínimo.
- **Atualizações de aplicativo**: você pode usar o secundário online como uma opção de failback.

Para alcançar uma continuidade dos negócios real, a adição de redundância entre datacenters ao armazenamento relacional é apenas parte da solução. A recuperação completa de um aplicativo (serviço) após uma falha catastrófica requer a recuperação de todos os componentes que constituem o serviço e todos os serviços dependentes. O software cliente (por exemplo, um navegador com um JavaScript personalizado), front-ends da Web, armazenamento e DNS são exemplos desses componentes. É fundamental que todos os componentes sejam resilientes às mesmas falhas e fiquem disponíveis dentro do RTO (objetivo de tempo de recuperação) de seu aplicativo. Portanto, você precisa identificar todos os serviços dependentes e entender as garantias e os recursos que eles fornecem. Em seguida, você deve tomar as medidas necessárias para garantir que seu serviço funcione durante o failover dos serviços dos quais ele depende. Para obter mais informações sobre como criar soluções para a recuperação de desastre, veja [Criando soluções de nuvem para a recuperação de desastre usando a Replicação Geográfica Ativa](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## Recursos da replicação geográfica ativa
O recurso de Replicação Geográfica Ativa fornece os seguintes recursos essenciais:

- **Replicação Assíncrona Automática**: depois que um banco de dados secundário online é propagado, as atualizações ao banco de dados primário são copiadas de forma assíncrona no banco de dados secundário online automaticamente. Isso significa que as transações são confirmadas no banco de dados primário antes de serem copiadas para o banco de dados secundário online. No entanto, após a propagação, o banco de dados secundário online é transacionalmente consistente em qualquer momento.
	>[AZURE.NOTE]A replicação assíncrona acomoda a latência que tipifica as redes de longa distância pelas quais os datacenters remotos são conectados.

- **Vários bancos de dados secundários online**: dois ou mais bancos de dados secundários online aumentam a redundância e proteção para o banco de dados primário e o aplicativo. Se existirem vários bancos de dados secundários online, o aplicativo permanecerá protegido mesmo se houver uma falha em um dos bancos de dados secundários online. Se houver apenas um banco de dados secundário online e ele falhar, o aplicativo será exposto a um risco maior até que um novo banco de dados secundário online seja criado.

- **Bancos de dados secundários online legíveis**: um aplicativo pode acessar um banco de dados secundário online para operações somente leitura usando as mesmas entidades de segurança usadas para acessar o banco de dados primário. Operações de cópia contínua no banco de dados secundário online têm precedência sobre o acesso ao aplicativo. Além disso, se as consultas no banco de dados secundário online causarem o bloqueio prolongado da tabela, as transações podem acabar falhando no banco de dados primário.

- **Encerramento controlado pelo usuário para failover**: antes de poder executar o failover de um aplicativo para um banco de dados secundário online, o relacionamento de cópia contínuo com o banco de dados primário deve ser encerrado. O encerramento de relacionamento de cópia contínuo exige uma ação explícita do aplicativo, um script administrativo ou uma ação manual por meio do portal. Após o encerramento, o banco de dados secundário online se torna um banco de dados autônomo. Ele se torna um banco de dados de leitura / gravação, a menos que o banco de dados primário era um banco de dados somente leitura. Duas formas de [Encerramento de um relacionamento de cópia contínuo](#termination-of-a-continuous-copy-relationship) são descritas mais adiante neste tópico.

>[AZURE.NOTE]Há suporte para a Replicação Geográfica Ativa apenas para bancos de dados na camada de serviço Premium. Isso se aplica aos bancos de dados primário e secundários online. O secundário online deve ser configurado para ter um nível de desempenho igual ou maior que o primário. Alterações nos níveis de desempenho do banco de dados primário não são replicadas automaticamente para os secundários. Todas as atualizações devem ser feitas nos bancos de dados secundários primeiro e, por fim, no primário. Para obter mais informações sobre como alterar os níveis de desempenho, veja [Alterar níveis de desempenho](sql-database-scale-up.md). Há duas razões principais pelas quais o secundário online deve ter pelo menos o mesmo tamanho que o primário. O banco de dados secundário deve ter capacidade suficiente para processar as transações replicadas na mesma velocidade que o primário. Se o secundário não tiver, no mínimo, a mesma capacidade para processar as transações de entrada, ele poderá ter mais latência e acabar afetando a disponibilidade do primário. Se o secundário não tiver a mesma capacidade que o primário, o failover poderá prejudicar o desempenho e a disponibilidade do aplicativo.

## Conceitos do relacionamento de cópia contínuo
Redundância de dados local e recuperação operacional são recursos padrão do Banco de Dados SQL do Azure. Cada banco de dados possui um banco de dados primário e dois bancos de dados de réplica locais que residem no mesmo datacenter, fornecendo alta disponibilidade nesse datacenter. Isso significa que os bancos de dados de Replicação Geográfica Ativa também têm réplicas redundantes. Os bancos de dados primários e secundários online têm duas réplicas secundárias. No entanto, a réplica primária para o banco de dados secundário é diretamente atualizada pelo mecanismo de cópia contínua e não pode aceitar atualizações iniciadas pelo aplicativo. A figura a seguir ilustra como a Replicação Geográfica Ativa estende a redundância de banco de dados em duas regiões do Azure. A região que hospeda o banco de dados primário é conhecida como a região primária. A região que hospeda o banco de dados secundário online é conhecida como a região secundária. Nesta figura, Norte da Europa é a região primária. Europa Ocidental é a região secundária.

![Relacionamento de cópia contínuo](./media/sql-database-active-geo-replication/continuous-copy-relationships.gif)

Se o banco de dados primário ficar indisponível, encerrar o relacionamento de cópia contínuo de um determinado banco de dados secundário online o tornará um banco de dados autônomo. O banco de dados secundário online herda o modo somente leitura/leitura- gravação do banco de dados primário que não é alterado pelo encerramento. Por exemplo, se o banco de dados primário for um banco de dados somente leitura, após o encerramento, o banco de dados secundário online se tornará um banco de dados somente leitura. Neste ponto, o aplicativo pode executar o failover e continuar usando o banco de dados secundário online. Para oferecer resiliência no caso de uma falha catastrófica do datacenter ou uma interrupção prolongada na região primária, pelo menos um banco de dados secundário online precisa residir em uma região diferente.

## Criando uma cópia contínua
Você só pode criar uma cópia contínua de um banco de dados existente. Criar uma cópia contínua de um banco de dados existente é útil para adicionar a redundância geográfica. Uma cópia contínua também pode ser criada para copiar um banco de dados existente em um servidor diferente do Banco de Dados SQL do Azure. Depois de criado, o banco de dados secundário é populado com os dados copiados do banco de dados primário. Este processo é conhecido como propagação. Após a conclusão da propagação, cada nova transação é replicada após ser confirmada no primário.

Para obter informações sobre como criar uma cópia contínua de um banco de dados existente, veja [Como habilitar a Replicação geográfica](sql-database-business-continuity-design.md#how-to-enable-geo-replication).

## Evitando a perda de dados críticos
Devido à alta latência das redes de longa distância, a cópia contínua usa um mecanismo de replicação assíncrona. Isso tornará a perda de alguns dados inevitável se ocorrer uma falha. No entanto, alguns aplicativos podem exigir nenhuma perda de dados. Para proteger essas atualizações críticas, um desenvolvedor de aplicativo pode chamar o procedimento de sistema [sp\_wait\_for\_database\_copy\_sync](https://msdn.microsoft.com/library/dn467644.aspx) imediatamente após a confirmação da transação. Chamar **sp\_wait\_for\_database\_copy\_sync** bloqueia o thread de chamada até que a última transação confirmada seja replicada para o banco de dados secundário online. O procedimento aguardará até que todas as transações na fila tenham sido confirmadas pelo banco de dados secundário online. **sp\_wait\_for\_database\_copy\_sync** é delimitado para um link de cópia contínua específica. Qualquer usuário com os direitos de conexão para o banco de dados primário pode chamar este procedimento.

>[AZURE.NOTE]O atraso causado por uma chamada de procedimento **sp\_wait\_for\_database\_copy\_sync** pode ser significativo. O atraso depende do comprimento da fila e da largura de banda disponível. Evite chamar esse procedimento, a menos que seja absolutamente necessário.

## Encerramento de um relacionamento de cópia contínuo
O relacionamento de cópia contínuo pode ser encerrado a qualquer momento. Encerrar um relacionamento de cópia contínuo não remove o banco de dados secundário. Há dois métodos para encerrar um relacionamento de cópia contínuo:

- O **Encerramento planejado** é útil para operações planejadas em que a perda de dados é inaceitável. Um encerramento planejado pode ser executado apenas no banco de dados primário, depois que o banco de dados secundário online for propagado. Em um encerramento planejado, todas as transações confirmadas no banco de dados primário são replicadas para o banco de dados secundário online primeiro e, em seguida, o relacionamento de cópia contínuo é encerrado. Isso evita a perda de dados no banco de dados secundário.
- O **Encerrado não planejado (forçado)** destina-se a responder à perda do banco de dados primário ou de um de seus bancos de dados secundários online. Um encerramento forçado pode ser executado no banco de dados primário ou secundário. Cada encerramento forçado resulta na perda irreversível do relacionamento de replicação entre o banco de dados primário e o banco de dados secundário online associado. Além disso, o encerramento forçado causa a perda de todas as transações que não foram replicadas do banco de dados primário. Um encerramento forçado encerra imediatamente o relacionamento de cópia contínuo. As transações em andamento não são replicadas para o banco de dados secundário online. Portanto, um encerramento forçado pode resultar em uma perda irreversível de transações que não foram replicadas do banco de dados primário.

>[AZURE.NOTE]Se o banco de dados primário tiver somente um relacionamento de cópia contínuo, após o encerramento, as atualizações ao banco de dados primário não serão mais protegidas.

Para obter mais informações sobre como encerrar um relacionamento de cópia contínuo, veja [Recuperar um Banco de Dados SQL do Azure de uma interrupção](sql-database-disaster-recovery.md).

## Próximas etapas
Para obter mais informações sobre a Replicação Geográfica Ativa e recursos adicionais de continuidade dos negócios do Banco de Dados SQL, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).

<!---HONumber=AcomDC_0121_2016-->