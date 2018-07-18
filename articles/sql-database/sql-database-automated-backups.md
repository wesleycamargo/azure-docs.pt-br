---
title: Backups do Banco de Dados SQL do Azure automáticos e com redundância geográfica | Microsoft Docs
description: O Banco de dados SQL cria automaticamente um backup de banco de dados local a cada poucos minutos e usa o armazenamento com redundância geográfica de acesso de leitura do Azure para redundância geográfica.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.workload: Active
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 37bbbf8ea5a5d8439b300d0740e4f1a048e98e91
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32189061"
---
# <a name="learn-about-automatic-sql-database-backups"></a>Saiba mais sobre backups automáticos de Banco de Dados SQL

O Banco de Dados SQL cria automaticamente backups do banco de dados e usa o RA-GRS (armazenamento com redundância geográfica de acesso de leitura) do Azure para fornecer redundância geográfica. Esses backups são criados automaticamente e sem nenhum custo adicional. Você não precisa fazer nada para que isso ocorra. Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Se você desejar manter backups em seu próprio contêiner de armazenamento, configure uma política de retenção de backup de longo prazo. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

## <a name="what-is-a-sql-database-backup"></a>O que é um backup de Banco de Dados SQL?

O Banco de Dados SQL usa tecnologia SQL Server para criar backups [completos](https://msdn.microsoft.com/library/ms186289.aspx), [diferenciais](https://msdn.microsoft.com/library/ms175526.aspx) e de [log de transações](https://msdn.microsoft.com/library/ms191429.aspx) para finalidades de PITR ( Recuperação Pontual). Os backups de log de transações geralmente ocorrem a cada 5 a 10 minutos, com a frequência baseada no nível de desempenho e na quantidade de atividade do banco de dados. Os backups de log de transações, com os backups completos e diferenciais, permitem restaurar um banco de dados para um ponto no tempo específico e para o mesmo servidor que hospeda o banco de dados. Quando você restaura um banco de dados, o serviço descobre quais backups completos, diferenciais e de log de transações precisam ser restaurados.


Use esses backups para:

* Restaurar um banco de dados para um ponto no tempo dentro do período de retenção. Essa operação criará um novo banco de dados no mesmo servidor do banco de dados original.
* Restaure um banco de dados excluído para a hora em que ele foi excluído ou em qualquer momento dentro do período de retenção. O banco de dados excluído pode ser restaurado apenas no mesmo servidor em que o banco de dados original foi criado.
* Restaure um banco de dados para outra região geográfica. Isso permite a recuperação de um desastre geográfico quando você não consegue acessar o servidor nem o banco de dados. Ele cria um novo banco de dados em qualquer servidor existente do mundo. 
* Restaure um banco de dados de um backup de longo prazo específico se o banco de dados tiver sido configurado com uma política de retenção de longo prazo. Isso permite restaurar uma versão antiga do banco de dados para atender a uma solicitação de conformidade ou para executar uma versão antiga do aplicativo. Consulte [Retenção de longo prazo](sql-database-long-term-retention.md).
* Para executar uma restauração, consulte [Restaurar um banco de dados de backups](sql-database-recovery-using-backups.md).

> [!NOTE]
> No armazenamento do Azure, o termo *replicação* refere-se a copiar arquivos de uma localização para outra. A *replicação de banco de dados* do SQL refere-se a manter vários bancos de dados secundários sincronizados com o banco de dados primário. 
> 

## <a name="how-often-do-backups-happen"></a>Com que frequência os backups ocorrem?
Os backups de banco de dados completos ocorrem semanalmente, os backups de banco de dados diferenciais geralmente ocorrem em horários determinados e os backups de log de transações geralmente ocorrem a cada 5 a 10 minutos. O primeiro backup completo é agendado imediatamente após a criação de um banco de dados. Normalmente ele é concluído em 30 minutos, mas pode levar mais tempo quando o banco de dados tem um tamanho significativo. Por exemplo, o backup inicial pode levar mais tempo para um banco de dados restaurado ou uma cópia do banco de dados. Após o primeiro backup completo, todos os outros backups são agendados automaticamente e gerenciados de forma silenciosa em segundo plano. O tempo exato de todos os backups de banco de dados é determinado pelo serviço do Banco de Dados SQL, pois ele equilibra a carga de trabalho geral do sistema. 

A replicação geográfica do armazenamento de backup ocorre com base no agendamento de replicação do Armazenamento do Azure.

## <a name="how-long-do-you-keep-my-backups"></a>Por quanto tempo meus backups são armazenados?
Cada backup de Banco de Dados SQL tem um período de retenção com base na camada de serviço do banco de dados e faz a diferenciação entre o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e [modelo de compra baseado em vCore (versão prévia)](sql-database-service-tiers-vcore.md). 


### <a name="database-retention-for-dtu-based-purchasing-model"></a>Retenção de banco de dados para o modelo de compra baseado em DTU.
O período de retenção para um banco de dados no modelo de compra baseado em DTU depende da camada de serviço. O período de retenção para um banco de dados para a:

* A camada de serviço Básico é de 7 dias.
* Camada de serviço Standard é de 35 dias.
* Camada de serviço Premium é de 35 dias.
* A camada de uso geral é configurável com um máximo de 35 dias (7 dias por padrão)*
* A camada Comercialmente Crítico (versão prévia) é configurável com um máximo de 35 dias (7 dias por padrão)*

\* Durante a versão prévia, o período de retenção de backups não é configurável e é fixado em 7 dias.

Se você converter um banco de dados com retenção de backups mais longa em um banco de dados com retenção mais curta, todos os backups existentes mais antigos que o período de retenção da camada de destino não estarão mais disponíveis.

Se você fizer upgrade de um banco de dados com um período de retenção mais curto para um banco de dados com um período mais longo, o Banco de Dados SQL manterá os backups existentes até que o período de retenção mais longo seja atingido. 

Se você excluir um banco de dados, o Banco de Dados SQL manterá os backups da mesma maneira que faria para um banco de dados online. Por exemplo, vamos supor que você exclui um banco de dados Básico que tenha um período de retenção de sete dias. Um backup com quatro dias será salvo por mais três dias.

> [!IMPORTANT]
> Se você excluir o SQL Server do Azure que hospeda Bancos de Dados SQL, todos os bancos de dados que pertencem a esse servidor também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído.

### <a name="database-retention-for-the-vcore-based-purchasing-model-preview"></a>Retenção de banco de dados para o modelo de compra baseado em vCore (versão prévia)

O armazenamento para backups de banco de dados é alocado para dar suporte aos recursos PITR (Recuperação Pontual) e LTR (Retenção em Longo Prazo) do Banco de Dados SQL. Esse armazenamento é alocado separadamente para cada banco de dados e cobrado como dois encargos separados por banco de dados. 

- **PITR**: os backups de banco de dados individuais são copiados para o armazenamento RA-GRS automaticamente. O tamanho do armazenamento aumenta dinamicamente conforme os novos backups são criados.  O armazenamento é usado por backups completos semanais, backups diferenciais diários e backups de log de transações copiados a cada 5 minutos. O consumo de armazenamento depende da taxa de alteração do banco de dados e do período de retenção. É possível configurar um período de retenção separado para cada banco de dados entre 7 e 35 dias. Um valor de armazenamento mínimo igual a 1x de tamanho de dados é fornecido sem nenhum custo adicional. Para a maioria dos bancos de dados, esse valor é suficiente para armazenar 7 dias de backups. Para obter mais informações, consulte [Restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore)
- **LTR**: o Banco de Dados SQL oferece a opção de configurar a retenção em longo prazo de backups completos por até 10 anos. Se a política de LTR estiver habilitada, esses backups serão armazenados no armazenamento RA-GRS automaticamente, mas você poderá controlar com que frequência os backups serão copiados. Para atender a diferentes requisitos de conformidade, é possível selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuais. Essa configuração definirá quanto armazenamento será usado para os backups de LTR. É possível usar a calculadora de preços de LTR para estimar o custo do armazenamento LTR. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

## <a name="how-to-extend-the-backup-retention-period"></a>Como estender o período de retenção de backup?

Se o aplicativo exigir que os backups estejam disponíveis por um período de tempo maior que o período máximo de retenção de backup do PITR, você poderá configurar uma política de retenção de backup de longo prazo para bancos de dados individuais (política LTR). Isso permite que você estenda o período de retenção interno de no máximo 35 dias para até 10 anos. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

Depois de adicionar a política LTR a um banco de dados usando API ou Portal do Azure, os backups semanais completos do banco de dados serão automaticamente copiados para um contêiner de armazenamento RA-GRS separado para retenção de longo prazo (armazenamento LTR). Caso seu banco de dados esteja criptografado com TDE, os backups serão criptografados automaticamente em repouso. O Banco de Dados SQL excluirá automaticamente os backups expirados com base no carimbo de data/hora e na política LTR. Depois que a política estiver configurada, não será necessário gerenciar o agendamento de backup nem preocupar-se com a limpeza dos arquivos antigos. Você pode usar o Portal do Azure ou PowerShell para exibir, restaurar ou excluir esses backups.

## <a name="are-backups-encrypted"></a>Os backups são criptografados?

Quando a TDE está habilitada para um banco de dados SQL do Azure, os backups também são criptografados. Todos os novos bancos de dados SQL do Azure são configurados com TDE habilitada por padrão. Para obter mais informações sobre a TDE, confira [Transparent Data Encryption com o Banco de Dados SQL do Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="are-the-automatic-backups-compliant-with-gdpr"></a>Os backups automáticos são compatíveis com GDPR?
Se o backup contiver dados pessoais, que estão sujeitos ao GDPR (Regulamento Geral sobre a Proteção de Dados), você deverá aplicar medidas de segurança aprimoradas para proteger os dados contra acesso não autorizado. Para estar em conformidade com o GDPR, é necessário ter uma maneira de gerenciar as solicitações de dados dos proprietários de dados sem precisar acessar os backups.  Para backups de curto prazo, uma solução pode ser reduzir a janela de backup para menos de 30 dias, que é o tempo permitido para concluir as solicitações de acesso a dados.  Se backups de longo prazo forem necessários, é recomendável armazenar apenas dados "pseudônimos" em backups. Por exemplo, se os dados sobre uma pessoa precisarem ser excluídos ou atualizados, não será necessário excluir ou atualizar os backups existentes. Você pode encontrar mais informações sobre as melhores práticas do GDPR em [Governança de dados para conformidade com GDPR](https://info.microsoft.com/DataGovernanceforGDPRCompliancePrinciplesProcessesandPractices-Registration.html).

## <a name="next-steps"></a>Próximas etapas

- Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL do Azure, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
- Para restaurar para um determinado ponto no tempo usando o Portal do Azure, consulte [Restaurar um banco de dados para um ponto no tempo usando o Portal do Azure](sql-database-recovery-using-backups.md).
- Para restaurar para um determinado ponto no tempo usando o PowerShell, consulte [Restaurar um banco de dados para um ponto no tempo usando o PowerShell](scripts/sql-database-restore-database-powershell.md).
- Para configurar, gerenciar e restaurar de retenção de longo prazo de backups automatizados em um cofre dos Serviços de Recuperação do Azure usando o Portal do Azure, consulte [Gerenciar retenção de backup de longo prazo usando o Portal do Azure](sql-database-long-term-backup-retention-configure.md).
- Para configurar, gerenciar e restaurar de retenção de longo prazo de backups automatizados em um cofre dos Serviços de Recuperação do Azure usando o PowerShell, consulte [Gerenciar retenção de backup de longo prazo usando o PowerShell](sql-database-long-term-backup-retention-configure.md).
