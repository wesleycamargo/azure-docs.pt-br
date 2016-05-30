<properties
   pageTitle="Continuidade dos negócios em nuvem - recuperação de banco de dados | Microsoft Azure"
   description="Saiba como o Banco de Dados SQL do Azure dá suporte para a continuidade dos negócios em nuvem e para a recuperação de banco de dados, além de ajudar a manter os aplicativos em nuvem críticos em execução."
   keywords="continuidade dos negócios, continuidade dos negócios em nuvem, recuperação de desastre do banco de dados, recuperação de banco de dados"
   services="sql-database"
   documentationCenter=""
   authors="elfisher"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/10/2016"
   ms.author="elfish"/>

# Visão geral: continuidade de negócios em nuvem e recuperação de desastre do banco de dados com o banco de dados SQL

A continuidade dos negócios é sobre como criar, implantar e executar aplicativos de forma que eles sejam resistentes a eventos com interrupção planejados ou não, que resultem em perda permanente ou temporária da capacidade dos aplicativos de realizar sua função de negócios. O intervalo de eventos não planejados varia de erros humanos a interrupções permanentes ou temporárias até desastres regionais, que podem causar perdas na instalação em larga escala em uma região do Azure específica. Os eventos planejados incluem a reimplantação de aplicativos em uma região diferente e atualizações de aplicativos. O objetivo de continuidade de negócios é que seu aplicativo continue a funcionar durante esses eventos com um impacto mínimo sobre a função de negócios.

Para discutir as soluções de continuidade nos negócios em nuvem, existem vários conceitos com os quais você precisa estar familiarizado:

* **Recuperação de desastres (DR):** um processo de restauração a função normal de negócios do aplicativo

* **Tempo de recuperação estimado (ERT):** a duração estimada para que o banco de dados esteja totalmente disponível depois de uma solicitação de restauração ou failover.

* **Objetivo de tempo de recuperação (RTO)**: tempo máximo aceitável antes que o aplicativo se recupere totalmente após um evento de interrupção. RTO mede a perda máxima de disponibilidade durante as falhas.

* **Objetivo de ponto de recuperação (RPO)**: a quantidade máxima de últimas atualizações (intervalo de tempo) que o aplicativo pode perder no momento em que ele se recupera totalmente após o evento de interrupção. RPO mede a perda máxima de dados durante as falhas.


## Cenários de continuidade dos negócios em nuvem

A seguir estão os principais cenários a serem considerados ao planejar a continuidade dos negócios e a recuperação do banco de dados.

###Aplicativos de design para continuidade dos negócios

O aplicativo que estou criando é essencial para a minha empresa. Quero criá-lo e configurá-lo para ser capaz de sobreviver a um desastre regional de uma falha catastrófica do serviço. Conheço os requisitos de RPO e RTO para meu aplicativo e escolherei a configuração que atenda a esses requisitos.

###Recuperação de erro humano

Eu tenho direitos administrativos para acessar a versão de produção do aplicativo. Como parte do processo de manutenção regular eu cometi um erro e exclui alguns dados essenciais em produção. Desejo restaurar rapidamente os dados para minimizar o impacto do erro.

###Recuperação de uma interrupção

Eu estou executando o meu aplicativo em produção e recebo um alerta sugerindo que há uma grande interrupção na região em que está implantado. Eu quero iniciar o processo de recuperação para colocá-lo em uma região diferente para minimizar o impacto nos negócios.

###Executar análise de recuperação de desastres

Como a recuperação de uma interrupção realocará a camada de dados para uma região diferente, eu quero testar periodicamente o processo de recuperação e avaliar seu impacto sobre o aplicativo para estar preparado.

###Atualização de aplicativo sem tempo de inatividade

Eu estou liberando uma atualização importante do meu aplicativo. Ela envolve as alterações de esquema do banco de dados, implantação de procedimentos armazenados adicionais etc. Este processo implicará impedir que o usuário acesse o banco de dados Ao mesmo tempo, quero me certificar que a atualização não cause uma interrupção significativa das operações de negócios.

##Recursos da continuidade dos negócios

A tabela a seguir mostra as diferenças dos recursos de continuidade dos negócios em nuvem em todos os níveis de serviço:

| Recurso | Camada básica | Camada padrão |Camada premium
| --- |--- | --- | ---
| Ponto de restauração pontual | Qualquer ponto de restauração dentro de 7 dias | Qualquer ponto de restauração dentro de 14 dias | Qualquer ponto de restauração dentro de 35 dias
| Restauração geográfica | ERT < 12h, RPO < 1h | ERT < 12h, RPO < 1h | ERT < 12h, RPO < 1h
| Replicação geográfica ativa | ERT < 30s, RPO < 5s | ERT < 30s, RPO < 5s | ERT < 30s, RPO < 5s

Esses recursos são fornecidos para tratar dos cenários listados anteriormente. Consulte a seção [Projeto para continuidade dos negócios](sql-database-business-continuity-design.md) para obter orientação sobre como selecionar o recurso específico.

> [AZURE.NOTE] Os valores ERT e RPO são metas de engenharia e fornecem apenas diretrizes. Eles não são parte do [SLA para o Banco de Dados SQL](https://azure.microsoft.com/support/legal/sla/sql-database/v1_0/)


###Restauração Pontual

A [recuperação pontual](sql-database-point-in-time-restore.md) é projetada para retornar seu banco de dados para um ponto anterior no tempo. Ele usa os backups de banco de dados, backups incrementais e backups de log de transações que o serviço mantém automaticamente para cada banco de dados do usuário. Esse recurso está disponível para todas as camadas de serviço. Você pode voltar 7 dias com a básica, 14 dias com a padrão e 35 dias com a premium. Consulte [Recuperação de erro humano](sql-database-user-error-recovery.md) para obter detalhes sobre como usar o ponto de restauração pontual.

###Restauração geográfica

A [restauração geográfica](sql-database-geo-restore.md) também está disponível com bancos de dados Básico, Standard e Premium. Ele fornece a opção de recuperação padrão quando o banco de dados também estiver indisponível devido a um incidente na região onde seu banco de dados está hospedado. Semelhante ao ponto de restauração pontual, a restauração geográfica depende de backups de banco de dados no armazenamento do Azure com redundância geográfica. Ele restaura a partir da cópia de backup replicado geograficamente e, portanto, é resistente a falhas de armazenamento na região primária. Consulte [Recuperação de uma falha](sql-database-disaster-recovery.md) para obter detalhes sobre como usar a restauração geográfica.

###Replicação geográfica ativa

A [replicação geográfica ativa](sql-database-geo-replication-overview.md) está disponível para todas as camadas de banco de dados. Ela foi criada para aplicativos que têm requisitos de recuperação mais agressivos do que a Restauração Geográfica pode oferecer. Com a replicação geográfica ativa, você pode criar até quatro secundários legíveis nos servidores em regiões diferentes. Você pode iniciar o failover para qualquer um dos secundários. Além disso, a replicação geográfica ativa pode ser usada para suportar a atualização do aplicativo ou os cenários de realocação, bem como o balanceamento de carga para cargas de trabalho somente leitura. Consulte [Projeto para continuidade de negócios](sql-database-business-continuity-design.md) para obter detalhes sobre como [configurar a replicação geográfica](sql-database-geo-replication-portal.md) e [fazer failover para o banco de dados secundário](sql-database-geo-replication-failover-portal.md). Consulte [Atualização de aplicativo sem tempo de inatividade](sql-database-business-continuity-application-upgrade.md) para obter detalhes sobre como implementar a atualização de aplicativo sem tempo de inatividade.

<!---HONumber=AcomDC_0518_2016-->