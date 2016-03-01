<properties
   pageTitle="Planejando a continuidade de negócios no SQL Data Warehouse | Microsoft Azure"
   description="Visão geral de continuidade de negócios no SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/17/2016"
   ms.author="sahajs;barbkess;sonyama"/>


# Planejando a continuidade de negócios no SQL Data Warehouse

Este artigo apresenta as noções básicas de planejamento de continuidade de negócios e recuperação de desastres com o SQL Data Warehouse. Os data warehouses são o repositório central de informações das empresas, tendo um papel fundamental nas operações diárias de análise e business intelligence em todos os níveis da organização. Portanto, é essencial que seu data warehouse seja confiável e permita a recuperação e a operação contínua. Este artigo mostrará, especificamente, como o SQL Data Warehouse permite a recuperação após desastres e erros de usuário usando a Restauração de Banco de dados e a Restauração Geográfica.

## Conceitos

Os planos de continuidade de negócios e recuperação de desastres precisam ser otimizados para:

**RTO (Objetivo de Tempo de Recuperação):** o tempo máximo aceitável para que o banco de dados se recupere totalmente depois de um evento de interrupção, isto é, perda máxima de disponibilidade durante falhas.

**RPO (Objetivo de Ponto de Recuperação):** a janela de tempo máximo aceitável de atualizações perdidas quando o banco de dados se recupera de um evento de interrupção, isto é, perda máxima de dados durante falhas.

**ERT (Tempo de Recuperação Estimado):** a duração estimada para que o banco de dados esteja totalmente disponível depois de uma solicitação de restauração.

## Cenários de continuidade dos negócios

**Recuperação de falhas de infraestrutura:** esse cenário refere-se à recuperação depois de problemas de infraestrutura, como falhas de disco, etc. Um cliente deseja garantir a continuidade de negócios com uma infraestrutura altamente disponível e tolerante a falhas.

**Recuperação de erros do usuário:** esse cenário refere-se à recuperação depois da corrupção ou exclusão de dados involuntária ou acidental. No caso em que um usuário modifica ou exclui dados involuntária ou acidentalmente, um cliente deseja garantir a continuidade de negócios com a restauração do banco de dados para um determinado ponto anterior.

**DR (Recuperação de Desastres):** esse cenário refere-se à recuperação após uma grande catástrofe. No cenário em que um evento de interrupção, como um desastre natural ou uma interrupção regional, faz com que o banco de dados fique indisponível, um cliente deseja recuperar o banco de dados em uma região diferente para continuar as operações de negócios.


## Recursos da continuidade dos negócios

Vamos observar como o SQL Data Warehouse aumenta a confiabilidade do seu banco de dados e permite a recuperação e operação contínua nos cenários mencionados acima.


### Redundância de dados

Uma vez que o SQL Data Warehouse separa computação e armazenamento, todos os seus dados são gravados diretamente no RA-GRS (Armazenamento do Azure com redundância geográfica). O armazenamento com redundância geográfica replica seus dados para uma região secundária a centenas de quilômetros da região primária. Em ambas as regiões, primária e secundária, seus dados são replicados três vezes cada um, entre domínios de falha e domínios de atualização separados. Isso garante a durabilidade dos dados, mesmo no caso de uma interrupção regional completa ou um desastre que cause indisponibilidade das regiões. Para saber mais sobre o Armazenamento com Redundância Geográfica com Acesso de Leitura, leia [Opções de redundância de armazenamento do Azure][].

### Restauração do banco de dados

A restauração do banco de dados foi desenvolvida para restaurar seu banco de dados para um determinado ponto anterior. O serviço do Azure SQL Data Warehouse protege todos os bancos de dados com instantâneos automáticos de armazenamento pelo menos a cada oito horas e os mantêm por sete dias para fornecer a você um conjunto distinto de pontos de restauração. Esses backups são armazenados no Armazenamento do Azure RA-GRS e, portanto, com redundância geográfica por padrão. Os recursos de backup e restauração automáticos são gratuitos, além de não terem nenhum custo e não exigirem administração para proteger os bancos de dados contra corrupção ou exclusão acidentais. Para saber mais sobre restauração de banco de dados, consulte [Recuperar após erro do usuário][].

### Restauração geográfica

A Restauração Geográfica foi desenvolvida para recuperar o banco de dados no caso em que ele se torna indisponível devido a um evento de interrupção. Você pode contatar o suporte para restaurar um banco de dados usando um backup com redundância geográfica de modo a criar um novo banco de dados em qualquer região do Azure. Como o backup tem redundância geográfica, ele pode ser usado para recuperar um banco de dados mesmo que este esteja inacessível devido a uma interrupção. O recurso de Restauração Geográfica não tem custos adicionais.


## Próximas etapas
Para saber mais sobre os recursos de continuidade de negócios de outras edições do Banco de Dados SQL, leia a [Visão geral de continuidade de negócios do Banco de Dados SQL][].

<!--Image references-->

<!--Article references-->
[business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md
[Opções de redundância de armazenamento do Azure]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Visão geral de continuidade de negócios do Banco de Dados SQL]: ../sql-database/sql-database-business-continuity.md
[Recuperar após erro do usuário]: sql-data-warehouse-business-continuity-recover-from-user-error.md

<!--MSDN references-->
[Create database restore request]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: http://msdn.microsoft.com/library/azure/dn509562.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0218_2016-->