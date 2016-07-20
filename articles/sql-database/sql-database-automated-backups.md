<properties
   pageTitle="Continuidade comercial em nuvem - Backup interno - Banco de Dados SQL | Microsoft Azure"
   description="Saiba mais sobre backups internos do Banco de Dados SQL que permitem a você reverter um Banco de Dados SQL do Azure para um ponto anterior ou copiar um banco de dados para um novo banco de dados em uma região geográfica (por até 35 dias)."
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
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# Backups automatizados do Banco de Dados SQL

O serviço Banco de Dados SQL do Azure protege todos os bancos de dados com um backup automatizado, que é mantido por sete dias para Basic, 35 dias para Standard e 35 dias para Premium. Consulte [Camadas de serviço](sql-database-service-tiers.md) para obter mais informações sobre os recursos disponíveis em cada camada de serviço.

Os backups de banco de dados são feitos automaticamente sem nenhuma necessidade de aceitação e sem encargos adicionais. Esses backups automáticos e a restauração pontual fornecem uma maneira sem custo e sem administração para proteger bancos de dados de corrupção ou de exclusão acidental, seja qual for a causa. Você pode usar esses backups automatizado para executar recuperações pontuais e restaurar um banco de dados excluído após uma corrupção ou exclusão de dados acidental.

## Custos do Backup Automatizado

O Banco de Dados SQL do Microsoft Azure fornece até 200% de seu armazenamento máximo de banco de dados provisionado em armazenamento de backup, sem custo adicional. Por exemplo, se você tiver uma instância de banco de dados Standard com tamanho provisionado de 250 GB, serão oferecidos a você 500 GB de espaço de armazenamento para backup sem custo adicional. Se seu banco de dados exceder o armazenamento de backup fornecido, é possível optar por reduzir o período de retenção ao entrar em contato com o Suporte do Azure ou pagar pelo armazenamento de backup adicional com a taxa padrão do Armazenamento com Redundância Geográfica com Acesso de Leitura (RA-GRS).

## Detalhes de backup automático

Todos os bancos de dados Basic, Standard e Premium são protegidos por backups automáticos. Os backups completos são feitos a cada semana, os backups diferenciais a cada dia e os backups de log a cada cinco minutos. O primeiro backup completo é agendado imediatamente após a criação de um banco de dados. Normalmente, isso é concluído em 30 minutos, mas pode demorar mais. Se um banco de dados já for grande, por exemplo, se for criado como o resultado de uma cópia ou de uma restauração de banco de dados de um banco de dados grande, então o primeiro backup completo poderá demorar mais para ser concluído. Após o primeiro backup completo, todos os outros backups são agendados automaticamente e gerenciados de forma silenciosa em segundo plano. O tempo exato dos backups completos e diferenciais é determinado pelo sistema para balancear a carga total.

## Redundância geográfica

Os arquivos de backup são armazenados em uma conta de armazenamento com redundância geográfica com acesso de leitura (RA-GRS) para garantir a disponibilidade para fins de recuperação de desastre. A seguir é mostrada a replicação geográfica de backups semanais e diários armazenados em uma conta de armazenamento com redundância geográfica com acesso de leitura (RA-GRS) para garantir a disponibilidade para fins de recuperação de desastre.

![restauração geográfica](./media/sql-database-geo-restore/geo-restore-1.png)

## Usando backups automatizados

Você pode [restaurar um banco de dados dos backups iniciados pelo serviço](sql-database-recovery-using-backups.md) durante seus [período de retenção](sql-database-service-tiers.md) para:

- Um novo banco de dados no mesmo servidor lógico recuperado para um ponto específico no tempo durante o período de retenção.
- Um banco de dados no mesmo servidor lógico recuperado para o tempo de exclusão de um banco de dados excluído.
- Um novo banco de dados em qualquer servidor lógico em qualquer região recuperado para os backups diários mais recentes no armazenamento de blobs com replicação geográfica (RA-GRS).

Você também pode usar os [backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md) para criar uma [cópia de banco de dados](sql-database-copy.md) em qualquer servidor lógico de qualquer região que seja transacionalmente consistente com o Banco de Dados SQL atual. Você pode usar a cópia do banco de dados e [exportar para um BACPAC](sql-database-export.md) para arquivar uma cópia transacionalmente consistente do banco de dados para armazenamento de longo prazo após o período de retenção ou para transferir uma cópia do banco de dados para uma instância local ou VM do Azure do SQL Server.

## O que acontece com o período de retenção do meu ponto de restauração quando faço downgrade/atualização pela camada de serviço?

Após o downgrade para um nível de desempenho inferior, o período de retenção do ponto de restauração é imediatamente truncado para o período de retenção do nível desempenho do banco de dados atual. Se a camada de serviço for atualizada, o período de retenção começará a ser estendido somente depois que o banco de dados for atualizado. Por exemplo, se for feito downgrade de um banco de dados, de P1 para S3, o período de retenção mudará de 35 dias para 35 dias imediatamente; todos os pontos de restauração antes de 35 dias não estarão mais disponíveis. Posteriormente, se ele for atualizado para P1 novamente, o período de retenção iniciará a partir de 35 dias e começará a ser construído até 35 dias.

## De quanto tempo é o período de retenção para um banco de dados descartado? 
O período de retenção é determinado pela camada de serviço do banco de dados enquanto ele existiu ou pelo número de dias que o banco de existe, o que for menor.

> [AZURE.IMPORTANT] Se você excluir uma instância de servidor do Banco de Dados SQL, todos os seus bancos de dados também serão excluídos e não poderão ser recuperados. No momento, não há suporte para restaurar um servidor excluído.

## Próximas etapas

- Para saber mais sobre como usar backups automatizados de recuperação, consulte [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
- Para saber mais sobre opções de recuperação mais rápidas, consulte [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- Para saber mais sobre como usar backups automatizados para arquivamento, consulte [cópia de banco de dados](sql-database-copy.md)
- Para obter uma visão geral sobre a continuidade de negócios, consulte [Visão geral da continuidade de negócios](sql-database-business-continuity.md)

<!---HONumber=AcomDC_0706_2016-->