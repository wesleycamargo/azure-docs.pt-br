<properties 
   pageTitle="Criando Soluções de Nuvem para Recuperação de Desastres Usando a Replicação Geográfica do Banco de Dados SQL | Microsoft Azure"
   description="Aprenda a projetar sua solução de nuvem para recuperação de desastres escolhendo o padrão de failover correto."
   services="sql-database"
   documentationCenter="" 
   authors="anosov1960" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/29/2016"
   ms.author="sashan"/>

# Estratégias de recuperação de desastres para aplicativos que usam o Pool Elástico do Banco de Dados SQL 

Ao longo dos anos, aprendemos que os serviços de nuvem não são à prova de falhas e que incidentes catastróficos devem ocorrer. O Banco de Dados SQL fornece uma série de recursos para permitir a continuidade dos negócios do seu aplicativo quando esses incidentes ocorrerem. Os pools elásticos e bancos de dados independentes oferecem suporte ao mesmo tipo de recursos de recuperação de desastres. Este artigo descreve diversas estratégias de recuperação de desastres para pools elásticos que aproveitam esses recursos de continuidade de negócios do Banco de Dados SQL.

Para os fins deste artigo, usaremos o padrão de aplicativo ISV SaaS canônico:

<i>Um aplicativo Web baseado em nuvem moderno provisiona um banco de dados SQL para cada usuário final. O ISV tem um grande número de clientes e, portanto, usa vários bancos de dados, conhecidos como bancos de dados de locatário. Como os bancos de dados do locatário normalmente têm padrões de atividade imprevisíveis, o ISV usa um pool elástico para tornar o custo do banco de dados bastante previsível por longos períodos de tempo. O pool elástico também simplifica o gerenciamento de desempenho quando há picos de atividade de usuário. Além dos bancos de dados do locatário, o aplicativo também usa vários bancos de dados para gerenciar perfis de usuário, segurança, coletar padrões de uso, etc. A disponibilidade de locatários individuais não afeta a disponibilidade do aplicativo como um todo. No entanto, a disponibilidade e o desempenho dos bancos de dados de gerenciamento são essenciais para o funcionamento do aplicativo. Se os bancos de dados de gerenciamento estiverem offline, todo o aplicativo estará offline.</i>

No restante do artigo, discutiremos os vários cenários relacionados e definiremos uma estratégia de recuperação de desastres para cada caso.

## Cenário 1

<i>Sou uma startup e dependo muito do custo das coisas. Quero simplificar a implantação e o gerenciamento do aplicativo e desejo ter um SLA limitado para clientes individuais. No entanto, quero garantir que o aplicativo como um todo nunca fique offline.</i>

Para satisfazer o requisito de simplicidade, você deveria implantar todos os bancos de dados do locatário em um pool elástico na região do Azure de sua escolha e implantar os bancos de dados de gerenciamento como bancos de dados autônomos replicados geograficamente. Na recuperação de desastres de locatários, use a restauração geográfica, que é fornecida sem custo adicional. Para garantir a disponibilidade dos bancos de dados de gerenciamento, eles deverão ser replicados geograficamente em outra região (etapa 1). O custo da configuração da recuperação de desastres neste cenário é igual ao custo total dos bancos de dados secundários. Essa configuração está ilustrada no diagrama a seguir.

![A figura 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

No caso de uma interrupção na região primária, as etapas de recuperação para colocar seu aplicativo online estão ilustradas no diagrama a seguir.

- Faça o failover imediatamente dos bancos de dados de gerenciamento para a região da recuperação de desastres (2). 
- Altere a cadeia de conexão do aplicativo para apontar para a região da recuperação de desastres. Todas as novas contas e bancos de dados de locatário serão criados na região da recuperação de desastres. Os clientes existentes verão seus dados como temporariamente indisponíveis.
- Crie o pool elástico com a mesma configuração do pool original (3). 
- Use a restauração geográfica para criar cópias dos bancos de dados de locatário (4). Você pode cogitar disparar as restaurações individuais pelas conexões do usuário final ou usar outro esquema de prioridade específica do aplicativo.

Neste momento, seu aplicativo está novamente online na região da recuperação de desastres, mas alguns clientes acessarão seus dados com algum atraso.

![Figura 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Se a interrupção foi temporária, é possível que a região primária seja recuperada pelo Azure antes de todas as restaurações serem concluídas na região da recuperação de desastres. Nesse caso, você deve organizar a mudança do aplicativo novamente para a região primária. O processo usará as etapas ilustradas no diagrama a seguir.
 
- Cancele todas as solicitações de restauração geográfica pendentes.   
- Faça o failover dos bancos de dados de gerenciamento para a região primária (5). Observação: após a recuperação da região, os antigos primários se tornarão secundários automaticamente. Agora, eles alternarão funções novamente. 
- Mude a cadeia de conexão do aplicativo para apontar novamente para a região primária. Agora, todas as novas contas e bancos de dados de locatário serão criados na região primária. Alguns clientes existentes verão seus dados como temporariamente indisponíveis.   
- Defina todos os bancos de dados no pool da recuperação de desastres como somente leitura a fim de garantir que eles não poderão ser modificados na região da recuperação de desastres (6). 
- Para cada banco de dados no pool da recuperação de desastres que foi alterado desde a recuperação, renomeie ou exclua os bancos de dados correspondentes no pool primário (7). 
- Copie os bancos de dados atualizados do pool da recuperação de desastres para o pool primário (8). 
- Exclua o pool da recuperação de desastres (9)

Neste momento, seu aplicativo estará online na região primária com todos os bancos de dados de locatário disponíveis no pool primário.

![A figura 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

A principal **vantagem** dessa estratégia é o baixo custo da redundância da camada de dados. Os backups são usados automaticamente pelo serviço Banco de Dados SQL sem a necessidade de nova codificação do aplicativo e sem nenhum custo adicional. O custo é incorrido apenas quando os bancos de dados elásticos são restaurados. O **porém** é que a recuperação completa de todos os bancos de dados de locatário levará um tempo significativo. Dependerá do número total de restaurações que você iniciar na região da recuperação de desastres e do tamanho geral dos bancos de dados do locatário. Mesmo se você priorizar a restauração de alguns locatários sobre outros, você estará competindo com todos as outras restaurações iniciadas na mesma região já que o serviço arbitrará e restringirá para minimizar o impacto geral nos bancos de dados dos clientes existentes. Além disso, a recuperação dos bancos de dados do locatário não poderá ser iniciada até que o novo pool elástico na região da recuperação de desastres seja criado.

## Cenário 2

<i>Tenho um aplicativo SaaS maduro com ofertas de serviço em camadas e diferentes SLAs para clientes de avaliação e para clientes pagantes. Para clientes de avaliação, preciso reduzir o custo tanto quanto possível. Os clientes de teste podem ter um tempo de inatividade, mas quero reduzir sua probabilidade. Para clientes pagantes, qualquer tempo de inatividade é um risco. Portanto, quero garantir que os clientes pagantes possam sempre acessar seus dados.</i>

Para dar suporte a esse cenário, você deverá separar os locatários de avaliação dos locatários pagos colocando-os em pools elásticos separados. Os clientes de avaliação teriam um menor eDTU por locatário e SLA inferior com tempo de recuperação mais longo. Os clientes pagantes estariam em um pool com maior eDTU por locatário e um SLA mais alto. Para garantir o menor tempo de recuperação, os bancos de dados de locatário dos clientes pagantes devem ser replicados geograficamente. Essa configuração está ilustrada no diagrama a seguir.

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Como no primeiro cenário, os bancos de dados de gerenciamento estarão muito ativos. Portanto, use um banco de dados autônomo replicado geograficamente para ele (1). Isso vai garantir o desempenho previsível para novas assinaturas de clientes, atualizações de perfil e outras operações de gerenciamento. A região em que residem os bancos de dados de gerenciamento primários será a região primária e a região em que residem os bancos de dados de gerenciamento secundários será a região da recuperação de desastres.

Os bancos de dados de locatário dos clientes pagantes terão bancos de dados ativos no pool "pago" provisionado na região primária. Você deve provisionar um pool secundário com o mesmo nome na região da recuperação de desastres. Cada locatário seria replicado geograficamente para o pool secundário (2). Isso permitirá a recuperação rápida de todos os bancos de dados do locatário usando o failover.

Se uma interrupção na região primária ocorre, as etapas de recuperação para colocar o aplicativo online estão ilustradas no seguinte diagrama:

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

- Faça o failover imediato dos bancos de dados de gerenciamento para a região da recuperação de desastres (3).
- Altere a cadeia de conexão do aplicativo para apontar para a região da recuperação de desastres. Agora, todas as novas contas e bancos de dados de locatário serão criados na região da recuperação de desastres. Os clientes de avaliação existentes verão seus dados como temporariamente indisponíveis.
- Faça o failover do bancos de dados de locatário pago para o pool na região da recuperação de desastres para restaurar sua disponibilidade imediatamente (4). Como o failover é uma alteração rápida no nível dos metadados, você pode considerar uma otimização em que os failovers individuais são disparados sob demanda pelas conexões do usuário final. 
- Se o tamanho do pool secundário eDTU era menor do que o primário porque os bancos de dados secundários precisavam apenas da capacidade de processar os logs de alteração enquanto eram secundários, você deve aumentar imediatamente a capacidade do pool agora para acomodar a carga de trabalho completa de todos os locatários (5). 
- Crie um novo pool elástico com o mesmo nome e a mesma configuração na região da recuperação de desastres para os bancos de dados dos clientes de avaliação (6). 
- Depois de criar o pool dos clientes de avaliação, use a restauração geográfica para restaurar os bancos de dados de locatário de avaliação individuais no novo pool (7). Você pode cogitar disparar as restaurações individuais pelas conexões do usuário final ou usar outro esquema de prioridade específica do aplicativo.

Neste momento, seu aplicativo está online novamente na região da recuperação de desastres. Todos os clientes pagantes têm acesso aos dados, ao passo que o clientes de avaliação acessarão seus dados com atraso.

Quando a região primária é recuperada pelo Azure *depois* de você ter restaurado o aplicativo na região da recuperação de desastres, é possível continuar a executar o aplicativo nessa região ou optar por fazer o failback para a região primária. Se a região primária for recuperada *antes de* o processo de failover ser concluído, você deverá considerar fazer o failback imediatamente. O failback executará as etapas ilustradas no seguinte diagrama:
 
![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

- Cancele todas as solicitações de restauração geográfica pendentes.   
- Faça o filover dos bancos de dados de gerenciamento (8). Após a recuperação da região, o antigo primário se tornou secundário automaticamente. Agora, ele se tornará primário novamente.  
- Faça o failover dos bancos de dados de locatário pago (9). Da mesma forma, após a recuperação da região, os antigos primários se tornaram secundários automaticamente. Agora, eles se tornarão primários novamente. 
- Defina os bancos de dados de avaliação restaurados que foram alterados na região da recuperação de desastres como somente leitura (10).
- Para cada banco de dados no pool da recuperação de desastres dos clientes de avaliação alterados desde a recuperação, renomeie ou exclua o banco de dados correspondente no pool primário dos clientes de avaliação (11). 
- Copie os bancos de dados atualizados do pool da recuperação de desastres para o pool primário (12). 
- Exclua o pool da recuperação de desastres (13). 

> [AZURE.NOTE] A operação de failover é assíncrona. Para minimizar o tempo de recuperação, é importante que você execute o comando de failover dos bancos de dados de locatário em lotes de pelo menos 20 bancos de dados.

A principal **vantagem** dessa estratégia é que ela fornece o SLA mais alto para os clientes pagantes. Ela também garante que as novas avaliações sejam desbloqueadas assim que o pool da recuperação de desastres de avaliação seja criado. O **porém** é que essa configuração aumentará o custo total de bancos de dados do locatário pelo custo do pool da recuperação de desastres secundário para clientes pagos. Além disso, se o pool secundário tem um tamanho diferente, os clientes pagantes verão um menor desempenho depois do failover até que a atualização de pool na região da recuperação de desastres seja concluída.

## Cenário 3

<i>Eu tenho um aplicativo SaaS maduro com ofertas de serviço em camadas. Quero oferecer um SLA muito agressivo para meus clientes pagos e minimizar o risco do impacto quando ocorrerem paralisações porque mesmo uma breve interrupção pode causar a insatisfação do cliente. É essencial que os clientes pagantes sempre possam acessar seus dados. As avaliações são gratuitas e não há oferta de SLA durante o período de avaliação. </i>

Para dar suporte a esse cenário, você deve ter três pools elásticos separados. Dois pools de tamanhos iguais com altos eDTUs por banco de dados devem ser provisionados em duas regiões diferentes para conter os bancos de dados de locatário dos clientes pagos. O terceiro pool que contém os locatários de avaliação teria eDTUs por banco de dados inferiores e seria provisionado em uma das duas regiões.

Para garantir o menor tempo de recuperação durante as interrupções, os bancos de dados de locatário de clientes pagantes deverão ser replicados geograficamente com 50% dos bancos de dados primários em cada uma das duas regiões. Da mesma forma, cada região teria 50% dos bancos de dados secundários. Dessa forma, se uma região estivesse offline, apenas 50% dos bancos de dados dos clientes pagos seriam afetados e teriam de fazer failover. Os outros bancos de dados permaneceriam intactos. Essa configuração é ilustrada no diagrama abaixo:

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Como nos cenários anteriores, os bancos de dados de gerenciamento estarão muito ativos e, portanto, você deve configurá-los como bancos de dados autônomos replicados geograficamente (1). Isso vai garantir o desempenho previsível de novas assinaturas de clientes, atualizações de perfil e outras operações de gerenciamento. A região A seria a região primária para os bancos de dados de gerenciamento e a região B seria usada para a recuperação dos bancos de dados de gerenciamento.

Os bancos de dados de locatário dos clientes pagantes também serão replicados geograficamente, mas com primários e secundários divididos entre a região A e a região B (2). Dessa forma, os bancos de dados primários do locatário afetados pela interrupção podem fazer failover para a outra região e ficar disponíveis. A outra metade dos bancos de dados de locatário não será afetada.

O diagrama a seguir ilustra as etapas de recuperação em caso de falha na região A.

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

- Faça imediatamente o failover dos bancos de dados de gerenciamento para a região B (3).
- Altere a cadeia de conexão do aplicativo para apontar para os bancos de dados de gerenciamento na região B. Modifique os bancos de dados de gerenciamento para garantir que as novas contas e bancos de dados de locatário sejam criados na região B e que os bancos de dados existentes do inquilino estejam lá também. Os clientes de avaliação existentes verão seus dados como temporariamente indisponíveis.
- Faça o failover dos bancos de dados de locatário pago para o pool 2 na região B para restaurar sua disponibilidade (4). Como o failover é uma alteração rápida no nível dos metadados, você pode considerar uma otimização em que os failovers individuais são disparados sob demanda pelas conexões do usuário final. 
- A partir de agora, o pool 2 conterá apenas bancos de dados primários. A carga de trabalho total no pool aumentará e, portanto, você deverá aumentar seu tamanho eDTU (5). 
- Crie um novo pool elástico com o mesmo nome e a mesma configuração na região B para bancos de dados dos clientes de avaliação (6). 
- Depois de criar o pool, use a restauração geográfica para restaurar os bancos de dados de locatário de avaliação individuais no pool (7). Você pode cogitar disparar as restaurações individuais pelas conexões do usuário final ou usar outro esquema de prioridade específica do aplicativo.


> [AZURE.NOTE] A operação de failover é assíncrona. Para minimizar o tempo de recuperação, é importante que você execute o comando de failover dos bancos de dados de locatário em lotes de pelo menos 20 bancos de dados.

Neste ponto, seu aplicativo está novamente online na região B. Todos os clientes pagantes terão acesso aos dados, ao passo que os clientes de avaliação acessarão seus dados com atraso.

Quando a região A for recuperada, você precisará decidir se deseja usar a região B para clientes de avaliação ou fazer o failback usando o pool de clientes de avaliação na região A. Um critério poderia ser a % dos bancos de dados de locatário de avaliação modificados desde a recuperação. Independentemente dessa decisão, você precisará balancear novamente os locatários pagos entre dois pools. O diagrama a seguir ilustra o processo quando os bancos de dados de locatário de avaliação fazem failback para a região A.
 
![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

- Cancele todas as solicitações de restauração geográfica pendentes para o pool da recuperação de desastres de avaliação.   
- Faça o failover do banco de dados de gerenciamento (8). Após a recuperação da região, o antigo primário se tornou secundário automaticamente. Agora, ele se tornará primário novamente.  
- Escolha quais bancos de dados de locatário pago farão failback para o pool 1 e iniciarão o failover para seus secundários (9). Após a recuperação da região, todos os bancos de dados no pool 1 se tornaram secundários automaticamente. Agora, 50% deles se tornarão primários novamente. 
- Reduza o tamanho do pool 2 para o eDTU original (10).
- Defina todos os bancos de dados de avaliação na região B como somente leitura (11).
- Para cada banco de dados no pool da recuperação de desastres de avaliação que foi alterado desde a recuperação, renomeie ou exclua os bancos de dados correspondentes no pool primário de avaliação (12). 
- Copie os bancos de dados atualizados do pool da recuperação de desastres para o pool primário (13). 
- Excluir o pool da recuperação de desastres (14) 

As principais **vantagens** dessa estratégia são:
- Ela dá suporte ao SLA mais agressivo para os clientes pagantes porque faz com que uma interrupção não possa afetar mais de 50% dos bancos de dados de locatário. 
- Ela faz com que as novas avaliações sejam desbloqueadas assim que a trilha do pool da recuperação de desastres for criada durante a recuperação. 
- Ela permite um uso mais eficiente da capacidade do pool, já que 50% dos bancos de dados secundários nos pools 1 e 2 são garantidamente menos ativos que os bancos de dados primários.

Os principais **poréns** são:
- As operações CRUD em relação aos bancos de dados de gerenciamento terão menor latência para os usuários finais conectados à região A que para os usuários finais conectados à região B, já que elas serão executadas em relação ao primário dos bancos de dados.
- Ele requer um design mais complexo do banco de dados de gerenciamento. Por exemplo, cada registro de locatário precisa ter uma marca de local que tem que ser alterada durante o failover e o failback.  
- Os clientes pagantes podem sentir um desempenho menor que o normal até que a atualização do pool na região B seja concluída. 

## Resumo

Este artigo aborda as estratégias de recuperação de desastres para a camada de banco de dados usada por um aplicativo ISV SaaS multilocatário. A escolha da estratégia deve ser baseada nas necessidades do aplicativo, como o modelo de negócios, o SLA que você deseja oferecer aos seus clientes, restrições orçamentárias, etc. Cada estratégia descrita descreve as vantagens e os poréns para que você possa tomar uma decisão consciente. Além disso, seu aplicativo específico provavelmente incluirá outros componentes do Azure. Você deve revisar suas diretrizes de continuidade de negócios e coordenar a recuperação da camada de banco de dados com outros componentes do aplicativo. Para saber mais sobre o gerenciamento da recuperação de aplicativos de banco de dados no Azure, consulte [Criação de soluções de nuvem para a recuperação de desastres](./sql-database-designing-cloud-solutions-for-disaster-recovery.md).

As páginas abaixo ajudarão você a saber mais sobre as operações específicas necessárias para implementar cada um dos cenários neste artigo:

- [Adicionar banco de dados secundário](https://msdn.microsoft.com/library/azure/mt603689.aspx) 
- [Fazer failover do banco de dados para o secundário](https://msdn.microsoft.com/library/azure/mt619393.aspx)
- [Banco de dados de restauração geográfica](https://msdn.microsoft.com/library/azure/mt693390.aspx) 
- [Remover banco de dados](https://msdn.microsoft.com/library/azure/mt619368.aspx)
- [Copiar banco de dados](https://msdn.microsoft.com/library/azure/mt603644.aspx)

<!---HONumber=AcomDC_0504_2016-->