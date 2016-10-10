<properties
   pageTitle="Backups de Banco de Dados SQL | Microsoft Azure"
   description="Saiba mais sobre backups de banco de dados interno do Banco de Dados SQL que permitem a você reverter um Banco de Dados SQL do Azure para um ponto anterior ou copiar um banco de dados para um novo banco de dados em uma região geográfica (por até 35 dias)."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="carlrab"/>

# Backups de Banco de Dados SQL

O Banco de Dados SQL cria backups de banco de dados automaticamente como parte do serviço de oferta, sem que exista nenhum requisito para aceitar e sem nenhum custo adicional. Use backups de banco de dados para restaurar um banco de dados para um ponto anterior no tempo. Este artigo explica os detalhes específicos do recurso de backup de banco de dados no Banco de Dados SQL.

## O que é um backup de banco de dados?  

Um backup de banco de dados é um arquivo que armazena informações sobre o estado do banco de dados em um ponto específico no tempo. O Banco de Dados SQL cria backups dos tipos [completo](https://msdn.microsoft.com/library/ms186289.aspx), [diferencial](https://msdn.microsoft.com/library/ms175526.aspx) e de [log de transações](https://msdn.microsoft.com/library/ms191429.aspx). Quando você restaura um banco de dados para um ponto no tempo, o serviço descobre quais backups precisam ser restaurados.

Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Para obter mais informações, veja [Visão geral sobre a continuidade dos negócios](sql-database-business-continuity.md).

## Armazenamento com redundância geográfica

O serviço de banco de dados SQL armazena arquivos de backup de banco de dados em uma conta de armazenamento com redundância geográfica com acesso de leitura (RA-GRS). O recurso RA-GRS do Armazenamento do Azure replica os arquivos de backup para um [data center emparelhado](../best-practices-availability-paired-regions.md). Essa replicação geográfica garante que você possa restaurar um banco de dados caso não seja possível acessar o backup de banco de dados da sua região do banco de dados primário. No exemplo a seguir, o Banco de Dados SQL cria backups do banco de dados na região Leste dos EUA e armazena-os em uma conta com RA-GRS. Em seguida, o Armazenamento do Azure realiza a replicação geográfica dos backups para um data center emparelhado na região Oeste dos EUA.

![restauração geográfica](./media/sql-database-geo-restore/geo-restore-1.png)

>[AZURE.NOTE] No armazenamento do Azure, o termo *replicação* refere-se a copiar arquivos de uma localização para outra. A *replicação de banco de dados* do SQL refere-se a manter vários bancos de dados secundários sincronizados com o banco de dados primário.

Para saber mais sobre:
- Armazenamento com redundância geográfica, veja [Replicação do Armazenamento do Azure](../storage/storage-redundancy.md).
- Armazenamento RA-GRS, veja [Armazenamento com redundância geográfica com acesso de leitura](../storage/storage-redundancy.md#read-access-geo-redundant-storage).

## Custos de backup de banco de dados

O Banco de Dados SQL do Microsoft Azure fornece até 200% de seu armazenamento máximo de banco de dados provisionado como armazenamento de backup, sem custo adicional. Por exemplo, se você tiver uma instância de banco de dados Standard com tamanho provisionado de 250 GB, você terá 500 GB de espaço de armazenamento para backup sem custo adicional. Se seu banco de dados exceder o armazenamento de backup fornecido, você poderá optar por reduzir o período de retenção entrando em contato com o suporte do Azure. Outra opção é pagar por armazenamento de backup extra, que é cobrado segundo a taxa padrão de RA-GRS (Armazenamento com Redundância Geográfica com Acesso de Leitura).

## Agendamento de backup de banco de dados

Todos os bancos de dados Basic, Standard e Premium são protegidos por backups automáticos. Os backups de banco de dados completos são realizados toda semana, os backups de banco de dados diferenciais são realizados por hora e os backups do log de transação são realizados a cada cinco minutos. O primeiro backup completo é agendado imediatamente após a criação de um banco de dados. Normalmente ele é concluído em 30 minutos, mas pode levar mais tempo quando o banco de dados tem um tamanho significativo. Por exemplo, o backup inicial pode levar mais tempo para um banco de dados restaurado ou uma cópia do banco de dados. Após o primeiro backup completo, todos os outros backups são agendados automaticamente e gerenciados de forma silenciosa em segundo plano. O tempo exato de backups de banco de dados de tipo completo e [diferencial](https://msdn.microsoft.com/library/ms175526.aspx) é determinado conforme ele faz o balanceamento da carga de trabalho geral do sistema.

## Período de retenção do backup de banco de dados

Cada backup de Banco de Dados SQL é retido por sete dias para Básico, 35 dias para Standard e 35 dias para Premium. Para obter mais informações sobre os recursos disponíveis em cada camada de serviço, veja [Camadas de serviço](sql-database-service-tiers.md).

### O que acontece com o período de retenção do meu ponto de restauração quando faço downgrade/atualização pela camada de serviço?

Após o downgrade para um nível de desempenho inferior, o período de retenção do ponto de restauração é imediatamente truncado para o período de retenção do nível desempenho do banco de dados atual. Se a camada de serviço for atualizada, o período de retenção começará a ser estendido somente depois que o banco de dados for atualizado. Por exemplo, se você fizer downgrade de um banco de dados para Básico, o período de retenção será alterado de 35 para sete dias. Imediatamente, todos os pontos de restauração com mais de sete dias deixarão de estar disponíveis. Quando você atualiza um banco de dados Standard ou Premium, o período de retenção começa com sete dias e cresce até alcançar 35 dias.

### De quanto tempo é o período de retenção para um banco de dados descartado? 

O período de retenção é determinado pela camada de serviço do banco de dados enquanto ele existiu ou pelo número de dias que o banco de existe, o que for menor.

> [AZURE.IMPORTANT] Se você excluir uma instância de servidor do Banco de Dados SQL do Azure, todos os bancos de dados que pertencem a essa instância também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído.


## Usos comuns para backups de banco de dados

O uso principal para backups de banco de dados é possibilitar a restauração de um banco de dados para um ponto no tempo dentro do período de retenção. Com um backup de banco de dados você pode restaurar um banco de dados para um ponto no tempo, restaurar um banco de dados excluído até o momento de sua exclusão ou restaurar um banco de dados para outra região geográfica.

- Para saber mais sobre a restauração de bancos de dados, veja [Restaurar um banco de dados com base em backups automatizados](sql-database-recovery-using-backups.md).

Você pode usar um backup de banco de dados para copiar um banco de dados para um SQL Server lógico em qualquer região geográfica. A cópia é transacionalmente consistente com o Banco de Dados SQL atual.

- Para saber mais sobre como copiar um banco de dados, veja [cópia de banco de dados](sql-database-copy.md).

Você também pode arquivar backups automatizados além do período de retenção, criando uma cópia de banco de dados que você [exporta para um arquivo BACPAC](sql-database-export.md). Uma vez que tiver o arquivo BACPAC, você poderá arquivá-lo para armazenamento de longo prazo e armazená-lo após o período de retenção. Ou então, poderá usar o BACPAC para transferir uma cópia do banco de dados para o SQL Server, seja ele local ou em uma VM (máquina virtual) do Azure.

- Para saber mais sobre o arquivamento de um backup de banco de dados, veja [cópia de banco de dados](sql-database-copy.md)


## Tópicos relacionados

### Cenários

- Para obter uma visão geral sobre a continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)

### Recursos

Para saber mais sobre:

- Restauração de um backup de banco de dados, veja [restaurar um banco de dados com base nos backups iniciados pelo serviço](sql-database-recovery-using-backups.md).
- Arquivar um backup de banco de dados, veja [cópia de banco de dados](sql-database-copy.md).
- Opções de recuperação mais rápidas, veja [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md).

<!-- ### Tasks -->

<!-- ### Tutorials -->

<!---HONumber=AcomDC_0928_2016-->