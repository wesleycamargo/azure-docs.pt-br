<properties
   pageTitle="Continuidade dos negócios na nuvem - recuperação pontual - Banco de Dados SQL | Microsoft Azure"
   description="Saiba mais sobre a Restauração Pontual, que permite que você reverta um Banco de Dados SQL do Azure para um ponto anterior no tempo (até 35 dias)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/10/2016"
   ms.author="sstein"/>

# Visão geral: recuperação pontual do Banco de Dados SQL

> [AZURE.SELECTOR]
- [Visão geral](sql-database-point-in-time-restore.md)
- [Portal do Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

A recuperação pontual permite que você restaure um banco de dados para um ponto anterior usando [backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md). Você pode restaurar para um ponto anterior usando o [portal do Azure](sql-database-point-in-time-restore-portal.md), o [PowerShell](sql-database-point-in-time-restore-powershell.md) ou a [API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

O banco de dados pode ser restaurado para qualquer nível de desempenho ou pool elástico. É preciso garantir que você tenha uma cota DTU suficiente no servidor ou no pool, tendo em mente que a restauração cria um novo banco de dados e que o nível de desempenho e a camada de serviço de banco de dados restaurado pode ser diferente do estado atual do banco de dados dinâmico. Uma vez concluído, o banco de dados restaurado é um banco de dados online normal e totalmente acessível, cobrado a taxas normais com base em seu nível de desempenho e na camada de serviço. Se você estiver restaurando o banco de dados para fins de recuperação, poderá tratar o banco de dados restaurado como um substituto para o banco de dados original ou usá-lo para recuperar dados e, em seguida, atualizar o banco de dados original. Se o banco de dados restaurado se destinar a ser um substituto do banco de dados original, você deverá verificar se o nível de desempenho e/ou a camada de serviço são adequados e dimensionar o banco de dados, se necessário. Você pode renomear o banco de dados original e, em seguida, dar ao banco de dados restaurado o nome original usando o comando ALTER DATABASE no T-SQL. Se você planeja recuperar os dados do banco de dados restaurado, precisará gravar e executar separadamente todos os scripts de recuperação de dados necessários. Embora a operação de restauração possa demorar muito para concluir, o banco de dados ficará visível na lista de banco de dados o tempo todo. Se você excluir o banco de dados durante a restauração, ele cancelará a operação e você não será cobrado.

## Tempo de recuperação para uma restauração pontual

O tempo necessário para restaurar um banco de dados depende de muitos fatores, incluindo o tamanho do banco de dados, o ponto no tempo e a quantidade de atividades que precisam ser reproduzidas para recriar o estado no ponto selecionado. Para um banco de dados muito grande e/ou ativo, a restauração pode levar várias horas. A restauração de um banco de dados sempre cria um novo banco de dados no mesmo servidor que o banco de dados original e, portanto, o banco de dados restaurado deverá receber um novo nome.

## Backup/restauração versus Cópia/exportação/importação

A restauração pontual é a abordagem recomendada para a recuperação de bancos de dados Basic, Standard e Premium de perda ou de corrupção de dados acidentais. O backup e restauração têm um custo inferior (não há nenhum encargo para backups, a menos que sejam excessivos, enquanto você é cobrado para a cópia do banco de dados necessário para garantir uma exportação consistente transacional e armazenar o arquivo BACPAC), nenhuma administração (os backups são automáticos, já que você deve gerenciar ou agendar a exportações) e têm um RPO melhor (você pode restaurar para um ponto específico no tempo com uma granularidade (um minuto) muito maior do que seria prático com a importação/exportação/cópia) e um tempo de recuperação melhor (a restauração de backups normalmente é muito mais rápida do que a importação, o que envolve as etapas para criar o esquema, desabilitar índices, carregar os dados e, em seguida, habilitar índices para cada tabela individualmente). Observe que copiar e exportar continua sendo a solução recomendada para arquivamento a longo prazo, além do período de retenção.


## Resumo

Os backups automáticos e a restauração pontual protegem seus bancos de dados contra a corrupção ou a exclusão acidental de dados. A solução de administração de custo zero está disponível com todos os bancos de dados SQL. O backup e a restauração fornecem uma melhoria significativa em relação à solução alternativa de importação/exportação/cópia para as necessidades de recuperação a curto prazo. Recomendamos que você use a restauração pontual como parte da sua estratégia de continuidade de negócios e use apenas a exportação como necessário para fins de migração de dados ou de arquivamento a longo prazo.


## Próximas etapas

- [Finalizar seu Banco de Dados SQL do Azure recuperado](sql-database-recovered-finalize.md)
- [Recuperação pontual usando o portal do Azure](sql-database-point-in-time-restore-portal.md)
- [Recuperação pontual usando](sql-database-point-in-time-restore-powershell.md)
- [Recuperação pontual usando a API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)


## Recursos adicionais

- [Restaurar um banco de dados excluído](sql-database-restore-deleted-database.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Restauração geográfica](sql-database-geo-restore.md)
- [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- [Criando aplicativos para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->