<properties
   pageTitle="Continuidade dos negócios na nuvem - restauração pontual | Microsoft Azure"
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

# Visão geral: restauração pontual do Banco de Dados SQL

O ponto de restauração pontual permite restaurar um banco de dados para um ponto anterior no tempo.

O serviço Banco de Dados SQL do Azure protege todos os bancos de dados com um backup automatizado, que é mantido por sete dias para Basic, 14 dias para Standard e 35 dias para Premium. A recuperação pontual é projetada para a recuperação de seu banco de dados a partir desses backups após a corrupção ou a exclusão acidental de dados.

Os backups de banco de dados são feitos automaticamente sem nenhuma necessidade de aceitação e sem encargos adicionais. Esses backups automáticos e a restauração pontual fornecem uma maneira sem custo e sem administração para proteger bancos de dados de corrupção ou de exclusão acidental, seja qual for a causa.


|Tarefa (Portal) | PowerShell | REST |
|:--|:--|:--|
| [Restaurar um banco de dados SQL para um ponto anterior no tempo](sql-database-point-in-time-restore-portal.md) | [PowerShell](sql-database-point-in-time-restore-powershell.md) | [REST (createMode=PointInTimeRestore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |
| [Restaurar um banco de dados SQL excluído](sql-database-restore-deleted-database-portal.md) | [PowerShell](sql-database-restore-deleted-database-powershell.md) | [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|



## Noções básicas sobre backups automáticos

Todos os bancos de dados Basic, Standard e Premium são protegidos por backups automáticos. Os backups completos são feitos a cada semana, os backups diferenciais a cada dia e os backups de log a cada cinco minutos. O primeiro backup completo é agendado imediatamente após a criação de um banco de dados. Normalmente, isso é concluído em 30 minutos, mas pode demorar mais. Se um banco de dados já for grande, por exemplo, se for criado como o resultado de uma cópia ou de uma restauração de banco de dados de um banco de dados grande, então o primeiro backup completo poderá demorar mais para ser concluído. Após o primeiro backup completo, todos os outros backups são agendados automaticamente e gerenciados de forma silenciosa em segundo plano. O tempo exato dos backups completos e diferenciais é determinado pelo sistema para balancear a carga total. Os arquivos de backup são armazenados em uma conta de armazenamento com redundância geográfica com acesso de leitura (RA-GRS) para garantir a disponibilidade para fins de recuperação de desastre.


## Tempo de recuperação para uma restauração pontual

O tempo necessário para restaurar um banco de dados depende de muitos fatores, incluindo o tamanho do banco de dados, o ponto no tempo e a quantidade de atividades que precisam ser reproduzidas para recriar o estado no ponto selecionado. Para um banco de dados muito grande e/ou ativo, a restauração pode levar várias horas. A restauração de um banco de dados sempre cria um novo banco de dados no mesmo servidor que o banco de dados original e, portanto, o banco de dados restaurado deverá receber um novo nome.


O banco de dados pode ser restaurado para qualquer nível de desempenho ou pool elástico. É preciso garantir que você tenha uma cota DTU suficiente no servidor ou no pool, tendo em mente que a restauração cria um novo banco de dados e que o nível de desempenho e a camada de serviço de banco de dados restaurado pode ser diferente do estado atual do banco de dados dinâmico. Uma vez concluído, o banco de dados restaurado é um banco de dados online normal e totalmente acessível, cobrado a taxas normais com base em seu nível de desempenho e na camada de serviço. Se você estiver restaurando o banco de dados para fins de recuperação, poderá tratar o banco de dados restaurado como um substituto para o banco de dados original ou usá-lo para recuperar dados e, em seguida, atualizar o banco de dados original. Se o banco de dados restaurado se destinar a ser um substituto do banco de dados original, você deverá verificar se o nível de desempenho e/ou a camada de serviço são adequados e dimensionar o banco de dados, se necessário. Você pode renomear o banco de dados original e, em seguida, dar ao banco de dados restaurado o nome original usando o comando ALTER DATABASE no T-SQL. Se você planeja recuperar os dados do banco de dados restaurado, precisará gravar e executar separadamente todos os scripts de recuperação de dados necessários. Embora a operação de restauração possa demorar muito para concluir, o banco de dados ficará visível na lista de banco de dados o tempo todo. Se você excluir o banco de dados durante a restauração, ele cancelará a operação e você não será cobrado.



## Restauração de um banco de dados recentemente excluído

Se você excluir um banco de dados Basic, Standard ou Premium, o backup final será retido para o período de retenção normal, permitindo que você restaure o banco de dados para o ponto em que ele foi excluído.

Para os bancos de dados excluídos, o ponto de restauração é fixo para a hora de exclusão do banco de dados. E, como antes, ao restaurar um banco de dados excluído, ele só poderá ser restaurado para o servidor que continha o banco de dados original. Lembre-se disso ao excluir um servidor, já que você não poderá restaurar os bancos de dados anteriormente localizados no servidor quando ele for excluído.

## Backup/restauração versus Cópia/exportação/importação

A restauração pontual é a abordagem recomendada para a recuperação de bancos de dados Basic, Standard e Premium de perda ou de corrupção de dados acidentais. O backup e restauração têm um custo inferior (não há nenhum encargo para backups, a menos que sejam excessivos, enquanto você é cobrado para a cópia do banco de dados necessário para garantir uma exportação consistente transacional e armazenar o arquivo BACPAC), nenhuma administração (os backups são automáticos, já que você deve gerenciar ou agendar a exportações) e têm um RPO melhor (você pode restaurar para um ponto específico no tempo com uma granularidade (um minuto) muito maior do que seria prático com a importação/exportação/cópia) e um tempo de recuperação melhor (a restauração de backups normalmente é muito mais rápida do que a importação, o que envolve as etapas para criar o esquema, desabilitar índices, carregar os dados e, em seguida, habilitar índices para cada tabela individualmente). Observe que copiar e exportar continua sendo a solução recomendada para arquivamento a longo prazo, além do período de retenção.


## Resumo

Os backups automáticos e a restauração pontual protegem seus bancos de dados contra a corrupção ou a exclusão acidental de dados. A solução de administração de custo zero está disponível com todos os bancos de dados SQL. O backup e a restauração fornecem uma melhoria significativa em relação à solução alternativa de importação/exportação/cópia para as necessidades de recuperação a curto prazo. Recomendamos que você use a restauração pontual como parte da sua estratégia de continuidade de negócios e use apenas a exportação como necessário para fins de migração de dados ou de arquivamento a longo prazo.



## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Restauração geográfica](sql-database-geo-restore.md)
- [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- [Criando aplicativos para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalizar seu Banco de Dados SQL do Azure recuperado](sql-database-recovered-finalize.md)

<!---HONumber=AcomDC_0511_2016-->