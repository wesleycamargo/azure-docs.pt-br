<properties
   pageTitle="Saiba mais sobre Backups do Banco de Dados SQL | Microsoft Azure" 
   description="Saiba mais sobre backups de banco de dados interno do Banco de Dados SQL que permitem a você restaurar um Banco de Dados SQL do Azure para um ponto anterior ou copiar um banco de dados para um novo banco de dados em uma região geográfica (por até 35 dias)."
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


# <a name="learn-about-sql-database-backups"></a>Saiba mais sobre o Banco de Dados SQL

O Banco de Dados SQL cria um backup do banco de dados local a cada cinco minutos e usa RA-GRS (Armazenamento com redundância geográfica de acesso de leitura) do Azure para copiar alguns dos backups de banco de dados para outra região geográfica. Esses backups ocorrem **automaticamente e sem nenhum custo adicional**. Use backups de banco de dados local para [restaurar um banco de dados para um ponto no tempo](sql-database-point-in-time-restore-portal.md). Use backups com redundância geográfica para [restaurar o banco de dados para uma região geográfica diferente](sql-database-geo-restore-portal.md).  

>[AZURE.NOTE] Os backups locais e com redundância geográfica ocorrem automaticamente. Você não precisa fazer nada para que eles ocorram, e não há qualquer custo adicional. 

No diagrama a seguir, o Banco de Dados SQL está em execução na região Leste dos EUA. Ele cria um backup de banco de dados a cada cinco minutos, o qual ele armazena localmente no RA-GRS (armazenamento com redundância geográfica de acesso de leitura) do Azure. O Azure copia os backups do banco de dados em um datacenter emparelhado na região Oeste dos EUA.

![Restauração geográfica](./media/sql-database-geo-restore/geo-restore-1.png)

<!--## What is <feature>?" -->

## <a name="what-is-a-sql-database-backup?"></a>O que é um backup de Banco de Dados SQL?  

<!-- 
First sentence begins with "The <feature> is ..." followed by a definition of the feature. Provide a 1-2 paragraph intro to explain what the feature is, how it works, and the importance of the feature for solving business problems.
-->
Um backup de Banco de Dados SQL é um arquivo que armazena informações sobre o estado do banco de dados em um ponto específico no tempo. O Banco de Dados SQL usa a tecnologia do SQL Server para criar backups locais [completos](https://msdn.microsoft.com/library/ms186289.aspx), [diferenciais](https://msdn.microsoft.com/library/ms175526.aspx ) e de [log de transações](https://msdn.microsoft.com/library/ms191429.aspx). Os backups de log de transação ocorrem a cada cinco minutos, o que permite que você faça uma restauração pontual para o mesmo servidor que hospeda o banco de dados. Quando você restaura um banco de dados, o serviço descobre quais backups completos, diferenciais e de log de transações precisam ser restaurados.

>[AZURE.NOTE] O Banco de Dados SQL cria automaticamente backups locais do banco de dados e backups com redundância geográfica. Você não precisa fazer nada para que isso ocorra. Não há qualquer custo adicional.

Use o backup de banco de dados para:

- Restaurar um banco de dados para um ponto no tempo dentro do período de retenção. Com um backup de banco de dados você pode restaurar um banco de dados para um ponto no tempo, restaurar um banco de dados excluído até o momento de sua exclusão ou restaurar um banco de dados para outra região geográfica. Para executar uma restauração, veja [Restaurar um banco de dados de um backup de banco de dados](sql-database-recovery-using-backups.md).

- Copie um banco de dados para um SQL Server na mesma região ou em uma região diferente. A cópia é transacionalmente consistente com o Banco de Dados SQL atual. Para executar uma cópia, veja [Cópia de banco de dados](sql-database-copy.md).

- Arquive um backup de banco de dados além do período de retenção de backup. Para executar um arquivamento, [exporte um Banco de Dados SQL para um arquivo BACPAC](sql-database-export.md). Assim, você poderá arquivar o BACPAC em um armazenamento de longo prazo e armazená-lo após o período de retenção. Ou então, poderá usar o BACPAC para transferir uma cópia do banco de dados para o SQL Server, seja ele local ou em uma VM (máquina virtual) do Azure.

## <a name="backups-have-geographical-redundancy"></a>Backups têm redundância geográfica

O Banco de Dados SQL usa a [Replicação do Armazenamento do Azure](../storage/storage-redundancy.md) para fazer backup do banco de dados em um local geográfico diferente. Para fornecer armazenamento com redundância geográfica, o Banco de Dados SQL armazena arquivos uma conta [RA-GRS (Armazenamento com redundância geográfica de acesso de leitura)](../storage/storage-redundancy.md#read-access-geo-redundant-storage). O Azure replica os arquivos de backup para um [data center emparelhado](../best-practices-availability-paired-regions.md). Essa replicação geográfica garante que você possa restaurar um banco de dados caso não seja possível acessar o backup de banco de dados da sua região do banco de dados primário. 

>[AZURE.NOTE] No armazenamento do Azure, o termo *replicação* refere-se a copiar arquivos de uma localização para outra. A *replicação de banco de dados* do SQL refere-se a manter vários bancos de dados secundários sincronizados com o banco de dados primário. 


## <a name="how-much-backup-storage-is-included-at-no-cost?"></a>Quanto armazenamento de backup é incluído sem custo adicional?

O Banco de Dados SQL fornece até 200% de seu armazenamento máximo de banco de dados provisionado como armazenamento de backup, sem custo adicional. Por exemplo, se você tiver uma instância de banco de dados Standard com tamanho provisionado de 250 GB, você terá 500 GB de espaço de armazenamento para backup sem custo adicional. Se seu banco de dados exceder o armazenamento de backup fornecido, você poderá optar por reduzir o período de retenção entrando em contato com o suporte do Azure. Outra opção é pagar por armazenamento de backup extra, que é cobrado segundo a taxa padrão de RA-GRS (Armazenamento com Redundância Geográfica com Acesso de Leitura). 

## <a name="how-often-do-backups-happen?"></a>Com que frequência os backups ocorrem?

Os backups de banco de dados completos são realizados toda semana, os backups de banco de dados diferenciais são realizados por hora e os backups do log de transação são realizados a cada cinco minutos. O primeiro backup completo é agendado imediatamente após a criação de um banco de dados. Normalmente ele é concluído em 30 minutos, mas pode levar mais tempo quando o banco de dados tem um tamanho significativo. Por exemplo, o backup inicial pode levar mais tempo para um banco de dados restaurado ou uma cópia do banco de dados. Após o primeiro backup completo, todos os outros backups são agendados automaticamente e gerenciados de forma silenciosa em segundo plano. O tempo exato de backups de banco de dados de tipo completo e [diferencial](https://msdn.microsoft.com/library/ms175526.aspx) é determinado conforme ele faz o balanceamento da carga de trabalho geral do sistema. 

## <a name="how-long-do-you-keep-my-backups?"></a>Por quanto tempo meus backups são armazenados?

Cada backup do Banco de Dados SQL tem um período de retenção que se baseia na [camada de serviço](sql-database-service-tiers.md) do banco de dados. O período de retenção para um banco de dados na:

- Camada de serviço Básico é de sete dias.
- Camada de serviço Standard é de 35 dias.
- Camada de serviço Premium é de 35 dias.


Se você fizer o downgrade do banco de dados das camadas de serviço Standard ou Premium para Básico, os backups serão salvos por sete dias. Todos os backups existentes com mais de sete dias não estarão mais disponíveis. 

Se você atualizar seu banco de dados da camada de serviço Básico para Standard ou Premium, o Banco de Dados SQL manterá os backups existentes até tenham 35 dias. Isso armazena os backups novos à medida que eles ocorrem durante 35 dias.
 
Se você excluir um banco de dados, o Banco de Dados SQL manterá os backups da mesma maneira que faria para um banco de dados online. Por exemplo, vamos supor que você exclui um banco de dados Básico que tenha um período de retenção de sete dias. Um backup com quatro dias será salvo por mais três dias.

> [AZURE.IMPORTANT]Se você excluir o SQL Server do Azure que hospeda Bancos de Dados SQL, todos os bancos de dados que pertencem a esse servidor também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído.

## <a name="next-steps"></a>Próximas etapas

Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Para ver como os backups de banco de dados podem se encaixar em sua estratégia de negócios, veja [Visão geral da continuidade de negócios](sql-database-business-continuity.md).





<!--HONumber=Oct16_HO2-->


