---
title: "Backups do Banco de Dados SQL – automático, com redundância geográfica | Microsoft Docs"
description: "O Banco de dados SQL cria automaticamente um backup de banco de dados local a cada poucos minutos e usa o armazenamento com redundância geográfica de acesso de leitura do Azure para redundância geográfica."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 3ee3d49d-16fa-47cf-a3ab-7b22aa491a8d
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2016
ms.author: sashan;carlrab;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: f6bb6e1c81cafe5f0e5c43c99ab15a0483742868


---
# <a name="learn-about-sql-database-backups"></a>Saiba mais sobre o Banco de Dados SQL
<!------------------
This topic is annotated with TEMPLATE guidelines for FEATURE TOPICS.

Metadata guidelines

pageTitle
    60 characters or less. Includes name of the feature - primary benefit. Not the same as H1. Its 60 characters or fewer including all characters between the quotes and the Microsoft Azure site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "SQL Database automatically creates a local database backup every few minutes and uses Azure read-access geo-redundant storage for geo-redundancy."

TEMPLATE GUIDELINES for feature topics

The Feature Topic is a one-pager (ok, sometimes longer) that explains a capability of the product or service. It explains what the capability is and characteristics of the capability.  

It is a "learning" topic, not an action topic.

DO explain this:
    � Definition of the feature terminology.  i.e., What is a database backup?
    � Characteristics and capabilities of the feature. (How the feature works)
    � Common uses with links to overview topics that recommend when to use the feature.
    � Reference specifications (Limitations and Restrictions, Permissions, General Remarks, etc.)
    � Next Steps with links to related overviews, features, and tasks.

DON'T explain this:
    � How to steps for using the feature (Tasks)
    � How to solve business problems that incorporate the feature (Overviews)

GUIDELINES for the H1 

    The H1 should answer the question "What is in this topic?" Write the H1 heading in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  

    To help people understand this is a learning topic and not an action topic, start the title with "Learn about ... "

    Heading must use an industry standard term. If your feature is a proprietary name like "elastic pools", use a synonym. For example:    "Learn about elastic pools for multi-tenant databases". In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

GUIDELINES for introduction

    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top key words that you are using throughout the article.The introduction should be brief and to the point of what the feature is, what it is used for, and what's in the article. 

    If the introduction is short enough, your article can pop to the top in Google Instant Answers.

    In this example:

Sentence #1 Explains what the article will cover, which is what the feature is or does. This is also the metadata description. 
    SQL Database automatically creates a database backup every five minutes and uses Azure read-access geo-redundant storage (RA-GRS) to provide geo-redundancy. 

Sentence #2 Explains why I should care about this.  
    Database backups are an essential part of any business continuity and disaster recovery strategy because they protect your data from accidental corruption or deletion.

-------------------->

O Banco de Dados SQL cria automaticamente backups do banco de dados e usa o RA-GRS (armazenamento com redundância geográfica de acesso de leitura) do Azure para fornecer redundância geográfica. Esses backups são criados automaticamente e sem nenhum custo adicional. Você não precisa fazer nada para que isso ocorra. Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Se você desejar manter backups em seu próprio contêiner de armazenamento, configure uma política de retenção de backup de longo prazo. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

<!-- This image needs work, so not putting it in right now.

This diagram shows SQL Database running in the US East region. It creates a database backup every five minutes, which it stores locally to Azure Read Access Geo-redundant Storage (RA-GRS). Azure uses geo-replication to copy the database backups to a paired data center in the US West region.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

-->

<!---------------
GUIDELINES for the first ## H2.

    The first ## describes what the feature encompasses and how it is used. It points to related task articles.

    For consistency, being the heading with "What is ... "
----------------->

## <a name="what-is-a-sql-database-backup"></a>O que é um backup de Banco de Dados SQL?
<!-- 
    Explains what a SQL Database backup is and answers an important question that people want to know.
-->


<!----------------- 
    Explains first component of the backup feature
------------------>

O Banco de Dados SQL usa a tecnologia do SQL Server para criar backups [completos](https://msdn.microsoft.com/library/ms186289.aspx), [diferenciais](https://msdn.microsoft.com/library/ms175526.aspx) e de [log de transações](https://msdn.microsoft.com/library/ms191429.aspx). Os backups de log de transações geralmente ocorrem a cada 5 a 10 minutos, com a frequência baseada no nível de desempenho e na quantidade de atividade do banco de dados. Os backups de log de transações, com os backups completos e diferenciais, permitem restaurar um banco de dados para um ponto no tempo específico e para o mesmo servidor que hospeda o banco de dados. Quando você restaura um banco de dados, o serviço descobre quais backups completos, diferenciais e de log de transações precisam ser restaurados.

<!--------------- 
    Explicit list of what to do with a local backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

Use esses backups para:

* Restaurar um banco de dados para um ponto no tempo dentro do período de retenção. Essa operação criará um novo banco de dados no mesmo servidor do banco de dados original.
* Restaure um banco de dados excluído para a hora em que ele foi excluído ou em qualquer momento dentro do período de retenção. O banco de dados excluído pode ser restaurado apenas no mesmo servidor em que o banco de dados original foi criado.
* Restaure um banco de dados para outra região geográfica. Isso permite a recuperação de um desastre geográfico quando você não consegue acessar o servidor nem o banco de dados. Ele cria um novo banco de dados em qualquer servidor existente do mundo. 
* Restaure um banco de dados de um backup específico armazenado no cofre dos Serviços de Recuperação do Azure. Isso permite restaurar uma versão antiga do banco de dados para atender a uma solicitação de conformidade ou para executar uma versão antiga do aplicativo. Consulte [Retenção de longo prazo](sql-database-long-term-retention.md).
* Para executar uma restauração, consulte [Restaurar um banco de dados de backups](sql-database-recovery-using-backups.md).

> [!TIP]
> Para ver um tutorial, consulte [Introdução ao backup e restauração para proteção e recuperação dos dados](sql-database-get-started-backup-recovery.md)
>

<!----------------- 
    Explains first component of the backup feature
------------------>

<!--------------- 
    Explicit list of what to do with a geo-redundant backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

> [!NOTE]
> No armazenamento do Azure, o termo *replicação* refere-se a copiar arquivos de uma localização para outra. A *replicação de banco de dados* do SQL refere-se a manter vários bancos de dados secundários sincronizados com o banco de dados primário. 
> 
> 

<!----------------
    The next ## H2's discuss key characteristics of how the feature works. The title is in conversational language and asks the question that will be answered.
------------------->
## <a name="how-much-backup-storage-is-included-at-no-cost"></a>Quanto armazenamento de backup é incluído sem custo adicional?
O Banco de Dados SQL fornece até 200% de seu armazenamento máximo de banco de dados provisionado como armazenamento de backup, sem custo adicional. Por exemplo, se você tiver uma instância de banco de dados Standard com tamanho provisionado de 250 GB, você terá 500 GB de espaço de armazenamento para backup sem custo adicional. Se seu banco de dados exceder o armazenamento de backup fornecido, você poderá optar por reduzir o período de retenção entrando em contato com o suporte do Azure. Outra opção é pagar por armazenamento de backup extra, que é cobrado segundo a taxa padrão de RA-GRS (Armazenamento com Redundância Geográfica com Acesso de Leitura). 

## <a name="how-often-do-backups-happen"></a>Com que frequência os backups ocorrem?
Os backups de banco de dados completos ocorrem semanalmente, os backups de banco de dados diferenciais geralmente ocorrem em horários determinados e os backups de log de transações geralmente ocorrem a cada 5 a 10 minutos. O primeiro backup completo é agendado imediatamente após a criação de um banco de dados. Normalmente ele é concluído em 30 minutos, mas pode levar mais tempo quando o banco de dados tem um tamanho significativo. Por exemplo, o backup inicial pode levar mais tempo para um banco de dados restaurado ou uma cópia do banco de dados. Após o primeiro backup completo, todos os outros backups são agendados automaticamente e gerenciados de forma silenciosa em segundo plano. O tempo exato de todos os backups de banco de dados é determinado pelo serviço do Banco de Dados SQL, pois ele equilibra a carga de trabalho geral do sistema. 

A replicação geográfica do armazenamento de backup ocorre com base no agendamento de replicação do Armazenamento do Azure.

## <a name="how-long-do-you-keep-my-backups"></a>Por quanto tempo meus backups são armazenados?
Cada backup do Banco de Dados SQL tem um período de retenção que se baseia na [camada de serviço](sql-database-service-tiers.md) do banco de dados. O período de retenção para um banco de dados na:

<!------------------

    Using a list so the information is easy to find when scanning.
------------------->

* A camada de serviço Básico é de 7 dias.
* Camada de serviço Standard é de 35 dias.
* Camada de serviço Premium é de 35 dias.

Se você fizer o downgrade do banco de dados das camadas de serviço Standard ou Premium para Básico, os backups serão salvos por sete dias. Todos os backups existentes com mais de sete dias não estarão mais disponíveis. 

Se você atualizar seu banco de dados da camada de serviço Básico para Standard ou Premium, o Banco de Dados SQL manterá os backups existentes até tenham 35 dias. Isso armazena os backups novos à medida que eles ocorrem durante 35 dias.

Se você excluir um banco de dados, o Banco de Dados SQL manterá os backups da mesma maneira que faria para um banco de dados online. Por exemplo, vamos supor que você exclui um banco de dados Básico que tenha um período de retenção de sete dias. Um backup com quatro dias será salvo por mais três dias.

> [!IMPORTANT]
> Se você excluir o SQL Server do Azure que hospeda Bancos de Dados SQL, todos os bancos de dados que pertencem a esse servidor também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído.
> 
> 

## <a name="how-to-extend-the-backup-retention-period"></a>Como estender o período de retenção de backup?
Caso seu aplicativo precise que os backups estejam disponíveis por um período mais longo, é possível estender o período de retenção interno, configurando a política de retenção de backup de longo prazo em bancos de dados individuais (política LTR). Isso permite que você estenda o período de retenção interno de 35 dias para até 10 anos. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

Depois de adicionar a política LTR a um banco de dados usando o portal do Azure ou uma API, os backups de banco de dados completos semanais serão copiados automaticamente para seu próprio Cofre do Serviço de Backup do Azure. Caso seu banco de dados esteja criptografado com TDE, os backups serão criptografados automaticamente em repouso.  O Cofre de Serviços excluirá automaticamente os backups expirados com base em seu carimbo de data/hora e na política LTR.  Portanto, você não precisa gerenciar o agendamento de backup nem se preocupar com a limpeza dos arquivos antigos. A API de restauração dá suporte aos backups armazenados no cofre, contanto que o cofre esteja na mesma assinatura do banco de dados SQL. Use o portal do Azure ou o PowerShell para acessar esses backups.

> [!TIP]
> Para ver um tutorial, consulte [Introdução ao backup e restauração para proteção e recuperação dos dados](sql-database-get-started-backup-recovery.md)
>

<!-------------------
OPTIONAL section
## Best practices 
--------------------->

<!-------------------
OPTIONAL section
## General remarks
--------------------->

<!-------------------
OPTIONAL section
## Limitations and restrictions
--------------------->

<!-------------------
OPTIONAL section
## Metadata
--------------------->

<!-------------------
OPTIONAL section
## Performance
--------------------->

<!-------------------
OPTIONAL section
## Permissions
--------------------->

<!-------------------
OPTIONAL section
## Security
--------------------->

<!-------------------
GUIDELINES for Next Steps

    The last section is Next Steps. Give a next step that would be relevant to the customer after they have learned about the feature and the tasks associated with it.  Perhaps point them to one or two key scenarios that use this feature.

    You don't need to repeat links you have already given them.
--------------------->

## <a name="next-steps"></a>Próximas etapas

- Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL do Azure, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
- Para restaurar para um ponto no tempo, consulte [restore database to a point in time](sql-database-point-in-time-restore.md) (Restaurar o Banco de Dados para um ponto no tempo anterior).
- Para exibir o ponto de restauração mais antigo dos backups de um banco de dados gerados pelo serviço, consulte [View oldest restore point](sql-database-view-oldest-restore-point.md) (Exibir o ponto de restauração mais antigo)
- Para configurar a retenção de longo prazo de backups automatizados em um cofre dos Serviços de Recuperação do Azure, consulte [Configure long-term backup retention](sql-database-configure-long-term-retention.md) (Configurar a retenção de backups de longo prazo)



<!--HONumber=Dec16_HO2-->


