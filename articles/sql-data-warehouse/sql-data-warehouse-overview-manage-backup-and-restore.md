<properties
   pageTitle="Backup e restauração do banco de dados no SQL Data Warehouse do Azure (Visão Geral) | Microsoft Azure"
   description="Visão geral das opções de backup e restauração para recuperar um banco de dados no SQL Data Warehouse do Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>


# Backup e restauração do banco de dados no SQL Data Warehouse do Azure (Visão Geral)

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-overview-manage-backup-and-restore.md)
- [Portal](sql-data-warehouse-manage-backup-and-restore-tasks-portal.md)
- [PowerShell](sql-data-warehouse-manage-backup-and-restore-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-backup-and-restore-tasks-rest-api.md)

Este artigo apresenta as noções básicas de planejamento de continuidade de negócios e recuperação de desastres com o SQL Data Warehouse. Os data warehouses são o repositório central de informações das empresas, tendo um papel fundamental nas operações diárias de análise e business intelligence em todos os níveis da organização. Portanto, é essencial que seu data warehouse seja confiável e permita a recuperação e a operação contínua. Este artigo mostrará, especificamente, como o SQL Data Warehouse permite a recuperação após desastres e erros de usuário usando a Restauração de Banco de dados e a Restauração Geográfica.

## Planejando a recuperação 

Os planos de continuidade de negócios e recuperação de desastres precisam ser otimizados para:

**RTO (Objetivo de Tempo de Recuperação):** o tempo máximo aceitável para que o banco de dados se recupere totalmente depois de um evento de interrupção, isto é, perda máxima de disponibilidade durante falhas.

**RPO (Objetivo de Ponto de Recuperação):** a janela de tempo máximo aceitável de atualizações perdidas quando o banco de dados se recupera de um evento de interrupção, isto é, perda máxima de dados durante falhas.

**ERT (Tempo de Recuperação Estimado):** a duração estimada para que o banco de dados esteja totalmente disponível depois de uma solicitação de restauração.

Para obter mais informações, siga para [Visão geral da continuidade dos negócios no Banco de dados SQL do Azure][].

## Cenários de recuperação

**Recuperação de falhas de infraestrutura:** esse cenário refere-se à recuperação depois de problemas de infraestrutura, como falhas de disco, etc. Um cliente deseja garantir a continuidade de negócios com uma infraestrutura altamente disponível e tolerante a falhas.

**Recuperação de erros do usuário:** esse cenário refere-se à recuperação depois da corrupção ou exclusão de dados involuntária ou acidental. No caso em que um usuário modifica ou exclui dados involuntária ou acidentalmente, um cliente deseja garantir a continuidade de negócios com a restauração do banco de dados para um determinado ponto anterior.

**DR (Recuperação de Desastres):** esse cenário refere-se à recuperação após uma grande catástrofe. No cenário em que um evento de interrupção, como um desastre natural ou uma interrupção regional, faz com que o banco de dados fique indisponível, um cliente deseja recuperar o banco de dados em uma região diferente para continuar as operações de negócios.


## Funcionalidades de backup e restauração

Vamos observar como o SQL Data Warehouse aumenta a confiabilidade do seu banco de dados e permite a recuperação e operação contínua nos cenários mencionados acima.


### Redundância de dados

O SQL Data Warehouse separa a computação e o armazenamento. Todos os seus dados são gravados diretamente no RA-GRS (Armazenamento do Azure com redundância geográfica). O armazenamento com redundância geográfica replica seus dados para uma região secundária a centenas de quilômetros da região primária. Em ambas as regiões, primária e secundária, seus dados são replicados três vezes cada um, entre domínios de falha e domínios de atualização separados. Isso garante a durabilidade dos dados, mesmo no caso de uma interrupção regional completa ou um desastre que cause indisponibilidade das regiões. Para saber mais sobre o Armazenamento com Redundância Geográfica com Acesso de Leitura, leia [Opções de redundância de armazenamento do Azure][].

### Restauração do banco de dados

A restauração do banco de dados foi desenvolvida para restaurar seu banco de dados para um determinado ponto anterior. O serviço do Azure SQL Data Warehouse protege todos os bancos de dados com instantâneos automáticos de armazenamento pelo menos a cada oito horas e os mantêm por sete dias para fornecer a você um conjunto distinto de pontos de restauração. Esses backups são armazenados no Armazenamento do Azure RA-GRS e, portanto, com redundância geográfica por padrão. Os recursos de backup e restauração automáticos fornecem uma maneira que não exige administração para proteger bancos de dados contra corrupção ou exclusão acidentais. Para saber mais sobre a restauração de banco de dados, consulte [Tarefas de backup e restauração][].

### Restauração geográfica

A Restauração Geográfica foi desenvolvida para recuperar o banco de dados no caso em que ele se torna indisponível devido a um evento de interrupção. Como o backup tem redundância geográfica, ele pode ser usado para recuperar um banco de dados mesmo que este esteja inacessível devido a uma interrupção. O banco de dados restaurado pode ser criado em qualquer servidor em qualquer região do Azure. Além de recuperar de uma interrupção, a restauração geográfica também pode ser usada em outros cenários, como na migração ou na cópia do banco de dados para um servidor ou região diferente.

**Quando iniciar a recuperação** A operação de recuperação exige a alteração da cadeia de conexão SQL no momento da recuperação e pode resultar em perda permanente de dados. Portanto, deve ser feita somente quando houver a probabilidade de a interrupção durar mais do que o RTO do aplicativo. Use os seguintes pontos de dados para declarar que a recuperação é garantida:

- Falha de conectividade permanente no banco de dados.
- Seu Portal do Azure mostra um alerta sobre um incidente na região com grande impacto.


## Próximas etapas
Para outras tarefas de gerenciamento importantes, consulte [Visão geral de gerenciamento][];

<!--Image references-->

<!--Article references-->
[Opções de redundância de armazenamento do Azure]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Tarefas de backup e restauração]: sql-data-warehouse-backup-and-restore-tasks-portal.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md
[Visão geral de gerenciamento]: sql-data-warehouse-overview-management.md
[Visão geral da continuidade dos negócios no Banco de dados SQL do Azure]: ../sql-database/sql-database-business-continuity.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->