<properties
   pageTitle="Continuidade dos negócios em nuvem - recuperação de banco de dados - Banco de Dados SQL | Microsoft Azure"
   description="Saiba como o Banco de Dados SQL do Azure dá suporte para a continuidade dos negócios em nuvem e para a recuperação de banco de dados, além de ajudar a manter os aplicativos em nuvem críticos em execução."
   keywords="continuidade dos negócios, continuidade dos negócios em nuvem, recuperação de desastre do banco de dados, recuperação de banco de dados"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-bcdr"
   ms.date="06/09/2016"
   ms.author="carlrab"/>

# Visão geral: continuidade de negócios em nuvem e recuperação de desastre do banco de dados com o banco de dados SQL do Azure

> [AZURE.SELECTOR]
- [Restauração pontual](sql-database-point-in-time-restore.md)
- [Restaurar banco de dados excluído](sql-database-restore-deleted-database.md)
- [Restauração geográfica](sql-database-geo-restore.md)
- [Replicação geográfica ativa](sql-database-geo-replication-overview.md)
- [Cenários de continuidade dos negócios](sql-database-business-continuity-scenarios.md)

O Banco de dados SQL do Azure fornece um número de soluções de continuidade de negócios. A continuidade dos negócios é sobre como criar, implantar e executar aplicativos de forma que eles sejam resistentes a eventos com interrupção planejados ou não, que resultem em perda permanente ou temporária da capacidade dos aplicativos de realizar sua função de negócios. O intervalo de eventos não planejados varia de erros humanos a interrupções permanentes ou temporárias até desastres regionais, que podem causar perdas na instalação em larga escala em uma região do Azure específica. Os eventos planejados incluem a reimplantação de aplicativos em uma região diferente e atualizações de aplicativos. O objetivo de continuidade de negócios é que seu aplicativo continue a funcionar durante esses eventos com um impacto mínimo sobre a função de negócios.

Para discutir as soluções de continuidade nos negócios em nuvem do Banco de Dados SQL, existem vários conceitos com os quais você precisa estar familiarizado. Estes são:

* **Recuperação de desastres (DR):** um processo de restauração a função normal de negócios do aplicativo

* **Tempo de recuperação estimado (ERT):** a duração estimada para que o banco de dados esteja totalmente disponível depois de uma solicitação de restauração ou failover.

* **RTO (Objetivo de tempo de recuperação)**: o tempo máximo aceitável antes que o aplicativo se recupere totalmente após um evento de interrupção. RTO mede a perda máxima de disponibilidade durante as falhas.

* **RPO (Objetivo de ponto de recuperação)**: a quantidade máxima de últimas atualizações (intervalo de tempo) que o aplicativo pode perder no momento em que ele se recupera totalmente após o evento de interrupção. RPO mede a perda máxima de dados durante as falhas.


## Cenários de continuidade dos negócios em nuvem do Banco de Dados SQL

Os principais cenários a serem considerados ao planejar a continuidade dos negócios e a recuperação do banco de dados são os seguintes:

### Aplicativos de design para continuidade dos negócios

O aplicativo que estou criando é essencial para a minha empresa. Quero criá-lo e configurá-lo para ser capaz de sobreviver a um desastre regional de uma falha catastrófica do serviço. Conheço os requisitos de RPO e RTO para meu aplicativo e escolherei a configuração que atenda a esses requisitos.

### Recuperação de erro humano

Eu tenho direitos administrativos para acessar a versão de produção do aplicativo. Como parte do processo de manutenção regular eu cometi um erro e exclui alguns dados essenciais em produção. Desejo restaurar rapidamente os dados para minimizar o impacto do erro.

### Recuperação de uma interrupção

Eu estou executando o meu aplicativo em produção e recebo um alerta sugerindo que há uma grande interrupção na região em que está implantado. Eu quero iniciar o processo de recuperação para colocá-lo em uma região diferente para minimizar o impacto nos negócios.

### Executar análise de recuperação de desastres

Como a recuperação de uma interrupção realocará a camada de dados para uma região diferente, eu quero testar periodicamente o processo de recuperação e avaliar seu impacto sobre o aplicativo para estar preparado.

### Atualização de aplicativo sem tempo de inatividade

Eu estou liberando uma atualização importante do meu aplicativo. Ela envolve as alterações de esquema do banco de dados, implantação de procedimentos armazenados adicionais etc. Este processo implicará impedir que o usuário acesse o banco de dados Ao mesmo tempo, quero me certificar que a atualização não cause uma interrupção significativa das operações de negócios.

## Recursos de continuidade dos negócios do Banco de Dados SQL

A tabela a seguir lista os recursos de continuidade dos negócios do Banco de Dados SQL e mostra as diferenças entre as [camadas de serviço](sql-database-service-tiers.md):

| Recurso | Camada básica | Camada padrão |Camada premium
| --- |--- | --- | ---
| Ponto de restauração pontual | Qualquer ponto de restauração dentro de 7 dias | Qualquer ponto de restauração dentro de 14 dias | Qualquer ponto de restauração dentro de 35 dias
| Restauração geográfica | ERT < 12h, RPO < 1h | ERT < 12h, RPO < 1h | ERT < 12h, RPO < 1h
| Replicação geográfica ativa | ERT < 30s, RPO < 5s | ERT < 30s, RPO < 5s | ERT < 30s, RPO < 5s

Esses recursos são fornecidos para tratar dos cenários listados anteriormente. Consulte a seção [Projeto para continuidade dos negócios](sql-database-business-continuity-design.md) para obter orientação sobre como selecionar o recurso específico.

> [AZURE.NOTE] Os valores ERT e RPO são metas de engenharia e fornecem apenas diretrizes. Eles não são parte do [SLA para o Banco de Dados SQL](https://azure.microsoft.com/support/legal/sla/sql-database/v1_0/)


###Restauração pontual

A [recuperação pontual](sql-database-point-in-time-restore.md) foi projetada para retornar seu banco de dados para um ponto anterior no tempo. Ele usa os backups de banco de dados, backups incrementais e backups de log de transações que o serviço mantém automaticamente para cada banco de dados do usuário. Esse recurso está disponível para todas as camadas de serviço. Você pode voltar 7 dias com a básica, 14 dias com a padrão e 35 dias com a premium.

### Restauração geográfica

A [Restauração Geográfica](sql-database-geo-restore.md) também está disponível com bancos de dados Basic, Standard e Premium. Ele fornece a opção de recuperação padrão quando o banco de dados também estiver indisponível devido a um incidente na região onde seu banco de dados está hospedado. Semelhante ao ponto de restauração pontual, a restauração geográfica depende de backups de banco de dados no armazenamento do Azure com redundância geográfica. Ele restaura a partir da cópia de backup replicado geograficamente e, portanto, é resistente a falhas de armazenamento na região primária.

### Replicação geográfica ativa

A [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md) está disponível para todas as camadas de banco de dados. Ela foi criada para aplicativos que têm requisitos de recuperação mais agressivos do que a Restauração Geográfica pode oferecer. Com a replicação geográfica ativa, você pode criar até quatro secundários legíveis nos servidores em regiões diferentes. Você pode iniciar o failover para qualquer um dos secundários. Além disso, a Replicação Geográfica Ativa pode ser usada para permitir a [atualização do aplicativo ou os cenários de realocação](sql-database-manage-application-rolling-upgrade.md), bem como o balanceamento de carga para cargas de trabalho somente leitura.

## Escolhendo entre os recursos de continuidade dos negócios

Para criar seu aplicativo para continuidade dos negócios, é necessário que você responda às seguintes perguntas:

1. Qual recurso de continuidade dos negócios é adequado para proteger meu aplicativo contra interrupções?
2. Qual o nível de redundância e a topologia de replicação eu devo usar?

Para estratégias de recuperação detalhadas ao usar um pool elástico, confira [Estratégias de recuperação de desastre para aplicativos que usam o Pool Elástico do Banco de Dados SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

### Quando usar a restauração geográfica

A [Restauração Geográfica](sql-database-geo-restore.md) fornece a opção de recuperação padrão quando um banco de dados não estiver disponível devido a um incidente na região em que está hospedado. O Banco de Dados SQL fornece uma proteção básica integrada para cada banco de dados por padrão. Isso é feito ao fazer e armazenar os [backups de banco de dados](sql-database-automated-backups.md) no GRS (Armazenamento do Azure com Redundância Geográfica). Se você escolher esse método, nenhuma configuração especial ou alocação adicional de recursos é necessária. Você pode recuperar o banco de dados para qualquer região fazendo a restauração para um novo banco de dados usando esses backups com redundância geográfica automatizados.

Você deve usar a proteção interna caso seu aplicativo atenda aos seguintes critérios:

1. Ele não é considerado crítico. Ele não tem um SLA vinculado, portanto, o tempo de inatividade de 24 horas ou mais não resultará em responsabilidade financeira.
2. A taxa de alteração de dados é baixa (por exemplo, transações por hora). O RPO de 1 hora não resultará em uma grande perda de dados.
3. O aplicativo é de baixo custo e não é possível justificar o custo adicional de replicação geográfica 

> [AZURE.NOTE] A restauração geográfica não aloca previamente a capacidade de computação em nenhuma região específica para restaurar bancos de dados ativos do backup durante a interrupção. O serviço irá gerenciar a carga de trabalho associada às solicitações de restauração geográfica de maneira a minimizar o impacto sobre os bancos de dados existentes nessa região e suas demandas de capacidade terão prioridade. Portanto, o tempo de recuperação do banco de dados dependerá de quantos outros bancos de dados serão recuperados na mesma região ao mesmo tempo, bem como do tamanho do banco de dados, do número de log de transações, da largura de banda da rede, etc.

### Quando usar a replicação geográfica ativa

A [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md) permite a criação e a manutenção de bancos de dados (secundários) legíveis em uma região diferente do principal, mantendo-os atualizados usando um mecanismo de replicação assíncrona. Ele garante que seu banco de dados terá os dados e recursos de computação necessários para oferecer suporte às cargas de trabalho do aplicativo após a recuperação.

Você deve usar a Replicação Geográfica Ativa caso seu aplicativo atenda aos seguintes critérios:

1. Ele é de crítico. Ele tem um SLA vinculado com RTO e RPO agressivos. A perda de dados e a disponibilidade resultarão em responsabilidade financeira. 
2. A taxa de alteração de dados é alta (por exemplo, transações por minuto ou segundos). O RPO de 1 h associado à proteção padrão provavelmente resultará em perda de dados inaceitável.
3. O custo associado ao uso da replicação geográfica é significativamente menor do que a responsabilidade financeira potencial e as perdas associadas do negócio.


## Próximas etapas

- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [SQL Database automated backups (Backups automatizados do Banco de Dados SQL)](sql-database-automated-backups.md)
- Para aprender a usar os backups automatizados do Banco de Dados SQL do Azure a fim de realizar uma restauração pontual de um banco de dados, confira [Restauração pontual](sql-database-point-in-time-restore.md)
- Para aprender a usar os backups automatizados do Banco de Dados SQL do Azure a fim de restaurar um banco de dados excluído, confira [Restore deleted database (Restaurar um banco de dados excluído)](sql-database-restore-deleted-database.md)
- Para aprender a usar os backups automatizados do Banco de Dados SQL do Azure a fim de realizar uma restauração geográfica de um banco de dados, confira [Restauração geográfica](sql-database-geo-restore.md)
- Para aprender a configurar e usar a Replicação Geográfica Ativa para continuidade dos negócios, confira [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)

## Recursos adicionais

- [Recuperação de uma interrupção](sql-database-disaster-recovery.md)
- [Recuperar de erros de usuário](sql-database-user-error-recovery.md)
- [Executar uma análise de recuperação de desastre](sql-database-disaster-recovery-drills.md)
- [Gerenciar a segurança após a recuperação](sql-database-geo-replication-security-config.md)
- [Gerenciando a rolagem de atualizações de aplicativos na nuvem usando a Replicação Geográfica Ativa do Banco de Dados SQL](sql-database-manage-application-rolling-upgrade.md)
- [Projetando solução de nuvem para recuperação de desastre](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Estratégias de recuperação de desastres para aplicativos que usam o Pool Elástico do Banco de Dados SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Criando soluções de nuvem para recuperação de desastres usando replicação geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->