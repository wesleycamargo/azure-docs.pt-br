---
title: Escalar recursos de banco de dados individual – Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo descreve como escalar os recursos de computação e armazenamento disponíveis para um banco de dados individual no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/18/2019
ms.openlocfilehash: 471ded9cd94623929630155f1a3c613bf00576a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331803"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Escalar recursos de banco de dados individual no Banco de Dados SQL do Azure

Este artigo descreve como escalar os recursos de computação e armazenamento disponíveis para um banco de dados individual no Banco de Dados SQL do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é compatível com o banco de dados SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

## <a name="change-compute-size-vcores-or-dtus"></a>Altere o tamanho de computação (vCores ou DTUs)

Depois de escolher inicialmente o número de DTUs ou de vCores, você pode dimensionar um único banco de dados para cima ou para baixo dinamicamente com base na experiência real usando o [portal do Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [ PowerShell](/powershell/module/az.sql/set-azsqldatabase), o [CLI do Azure](/cli/azure/sql/db#az-sql-db-update), ou o [API REST](https://docs.microsoft.com/rest/api/sql/databases/update).

O vídeo a seguir mostra como alterar dinamicamente a camada de serviço e o tamanho de computação para aumentar os DTUs disponíveis para um banco de dados individual.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impacto da alteração de tamanho de computação da camada ou redimensionamento do serviço

Alterando o serviço de camada ou calcular o tamanho de um banco de dados envolve principalmente o serviço executando as seguintes etapas:

1. Criar nova instância de computação para o banco de dados  

    Uma nova instância de computação para o banco de dados é criada com a camada de serviço solicitado e o tamanho de computação. Para algumas combinações de camada de serviço e as alterações de tamanho de computação, uma réplica do banco de dados deve ser criada na nova instância de computação que envolve a cópia de dados e pode influenciar muito a latência geral. Independentemente disso, o banco de dados permanece online durante esta etapa e continuam de conexões ser direcionado para o banco de dados na instância de computação do original.

2. Alterne o roteamento de conexões para a nova instância de computação

    As conexões existentes com o banco de dados na instância original de computação são descartadas. Novas conexões são estabelecidas com o banco de dados na nova instância de computação. Para algumas combinações de camada de serviço e as alterações de tamanho de computação, os arquivos de banco de dados são desanexados e anexados novamente durante a troca.  Independentemente disso, a opção pode resultar em uma breve interrupção de serviço, quando o banco de dados não está disponível em geral para menos de 30 segundos e com frequência para apenas alguns segundos. Se houver de longa execução transações em execução quando as conexões são removidas, a duração dessa etapa pode levar mais tempo para recuperar transações anuladas. [Acelerada de recuperação de banco de dados](sql-database-accelerated-database-recovery.md) pode reduzir o impacto de anulação de transações de longa execução.

> [!IMPORTANT]
> Nenhum dado será perdido durante qualquer etapa no fluxo de trabalho.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latência de alterar o tamanho de computação da camada ou redimensionamento do serviço

A latência para alterar a camada de serviço ou redimensionar o tamanho de computação de um único banco de dados ou pool Elástico é parametrizada da seguinte maneira:

|Camada de serviço|Banco de dados único básico,</br>Standard (S0-S1)|Pool Elástico básico,</br>Standard (S2-S12), </br>E em hiperescala </br>Geral finalidade único banco de dados ou pool Elástico|Premium ou comercialmente crítica de banco de dados único ou pool Elástico|
|:---|:---|:---|:---|
|**Banco de dados único básico,</br> Standard (S0-S1)**|&bull; &nbsp;Latência de tempo constante, independente do espaço usado</br>&bull; &nbsp;Normalmente, menos de 5 minutos|&bull; &nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull; &nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|
|**Pool Elástico básico, </br>Standard (S2-S12) </br>e em hiperescala </br>único banco de dados de uso geral ou pool Elástico**|&bull; &nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull; &nbsp;Latência de tempo constante, independente do espaço usado</br>&bull; &nbsp;Normalmente, menos de 5 minutos|&bull; &nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|
|**Premium ou comercialmente crítica de banco de dados único ou pool Elástico**|&bull; &nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull; &nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull; &nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|

> [!TIP]
> Para monitorar operações em andamento, consulte: [Gerenciar operações usando a API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [Gerenciar operações usando a CLI](/cli/azure/sql/db/op), [Monitorar operações usando T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e esses dois comandos do PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>Cancelando alterações de camada de serviço ou operações de redimensionamento de computação

Uma camada de serviço alterar ou operação de redimensionamento pode ser cancelada de computação.

#### <a name="azure-portal"></a>Portal do Azure

Na folha de visão geral do banco de dados, navegue até **notificações** e clique no bloco indicando que há uma operação em andamento:

![Operação em andamento](media/sql-database-single-database-scale/ongoing-operations.png)

Em seguida, clique no botão rotulado **cancelar esta operação**.

![Cancelar a operação em andamento](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

Em um prompt de comando do PowerShell, defina as `$ResourceGroupName`, `$ServerName`, e `$DatabaseName`, e, em seguida, execute o seguinte comando:

```PowerShell
$OperationName = (az sql db op list --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --query "[?state=='InProgress'].name" --out tsv)
if(-not [string]::IsNullOrEmpty($OperationName))
    {
        (az sql db op cancel --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --name $OperationName)
        "Operation " + $OperationName + " has been canceled"
    }
    else
    {
        "No service tier change or compute rescaling operation found"
    }
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Considerações adicionais ao alterar tamanho da camada ou redimensionamento de computação de serviço

- Se você estiver atualizando para uma camada de serviço ou tamanho de computação maior, o tamanho máximo do banco de dados não aumentará, a menos que você especifique explicitamente um tamanho maior (tamanho máximo).
- Para fazer downgrade de um banco de dados, o espaço usado dele deve ter um tamanho menor do que o máximo permitido para a camada de serviço e o tamanho da computação de destino.
- Ao fazer o downgrade da camada **Premium** para a camada **Standard**, um custo de armazenamento extra será aplicado se (1) o tamanho máximo do banco de dados tiver suporte no tamanho da computação de destino e (2) o tamanho máximo ultrapassar a quantidade de armazenamento incluída de tamanho da computação de destino. Por exemplo, se um banco de dados P1 com um tamanho máximo de 500 GB for reduzido para S3, um custo de armazenamento extra será aplicado, pois S3 dá suporte a um tamanho máximo de 500 GB, e a quantidade de armazenamento incluído é somente de 250 GB. Assim, a quantidade de armazenamento extra será 500 GB – 250 GB = 250 GB. Para obter o preço do armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for menor do que a quantidade de armazenamento incluído, esse custo extra poderá ser evitado por meio da redução do tamanho máximo do banco de dados para a quantidade incluída.
- Ao atualizar um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, atualize seus bancos de dados secundários para a camada de serviço e o tamanho da computação desejados antes de atualizar o banco de dados primário (orientação geral para o melhor desempenho). Ao fazer upgrade para uma diferente, o upgrade do banco de dados secundário será exigido antes.
- Ao fazer downgrade de um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, faça downgrade dos seus bancos de dados primários para a camada de serviço e o tamanho da computação desejados antes de atualizar o banco de dados secundário (orientação geral para o melhor desempenho). Ao fazer o downgrade para uma edição diferente, antes será necessário fazer o downgrade do banco de dados primário.
- As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se você estiver fazendo downgrade para camada **Básica**, haverá um período de retenção de backup inferior. Consulte [Backups de Banco de Dados SQL do Azure](sql-database-automated-backups.md).
- As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.

### <a name="billing-during-compute-rescaling"></a>Faturamento durante o redimensionamento de computação

Você será cobrado pelas horas em que um banco de dados existir usando a camada de serviço mais alta mais o tamanho da computação aplicado durante essas horas, independentemente do uso ou se o banco de dados ficou ativo por menos de uma hora. Por exemplo, se você criar um banco de dados individual e o excluir depois de cinco minutos, sua fatura apresentará uma cobrança referente a uma hora de banco de dados.

## <a name="change-storage-size"></a>Alterar o tamanho de armazenamento

### <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

- O armazenamento pode ser provisionado até o limite de tamanho máximo com incrementos de 1 GB. O armazenamento de dados configurável mínimo é de 5 GB
- É possível provisionar o armazenamento para um único banco de dados aumentando ou diminuindo seu tamanho máximo usando o [Portal do Azure](https://portal.azure.com), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [CLI do Azure](/cli/azure/sql/db#az-sql-db-update) ou [API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- O banco de dados SQL automaticamente aloca 30% do armazenamento adicional para os arquivos de log e 32 GB por vCore para TempDB, mas não deve exceder 384 GB. O TempDB está localizado em um SSD anexado em todas as camadas de serviço.
- O preço do armazenamento para um único banco de dados é a soma das quantidades de armazenamento de dados e armazenamento de log multiplicada pelo preço unitário do armazenamento da camada de serviço. O custo de TempDB está incluído no preço de vCore. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

- O preço de DTU para um único banco de dados inclui uma determinada quantidade de armazenamento sem custo adicional. O armazenamento extra além da quantidade incluída pode ser provisionado mediante um custo adicional até o limite máximo de tamanho, em incrementos de 250 GB até 1 TB e, em seguida, em incrementos de 256 GB além de 1 TB. Para conhecer as quantidades de armazenamento incluídos e os limites máximos de tamanho, confira [Banco de dados individual: Tamanhos de armazenamento e tamanhos da computação](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- É possível provisionar o armazenamento extra para um único banco de dados aumentando seu tamanho máximo usando o [Portal do Azure](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [Transact-SQL](/powershell/module/az.sql/set-azsqldatabase), [PowerShell](/cli/azure/sql/db#az-sql-db-update), [CLI do Azure](https://docs.microsoft.com/rest/api/sql/databases/update) ou API REST.
- O preço do armazenamento extra para um único banco de dados é a quantidade de armazenamento extra multiplicada pelo preço unitário do armazenamento extra da camada de serviço. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Restrições de P11 e P15 quando o máximo de tamanho maior que 1 TB

Mais de 1 TB de armazenamento na camada Premium está atualmente disponível em todas as regiões, exceto: Leste da China, Norte da China, Alemanha Central, Nordeste da Alemanha, Centro-oeste dos EUA, regiões US DoD e US Government Central. Nessas regiões, o armazenamento máximo na camada Premium é limitado a 1 TB. As seguintes considerações e limitações se aplicam aos bancos de dados P11 e P15 com um tamanho máximo superior a 1 TB:

- Se o tamanho máximo para um banco de dados P11 ou P15 nunca foi definido como um valor maior que 1 TB, em seguida, ele apenas possível restaurado ou copiar um banco de dados P11 ou P15.  Subsequentemente, o banco de dados pode ser redimensionado para um tamanho de computação diferentes desde a quantidade de espaço alocado no momento da operação de redimensionamento não exceda os limites de tamanho máximo o novo tamanho de computação.
- Para cenários com replicação geográfica ativa:
  - Configurando um relacionamento de replicação geográfica: Se o banco de dados primário for P11 ou P15, os secundários também deverão ser P11 ou P15; os tamanhos da computação inferiores serão rejeitadas como secundários porque não são capazes de dar suporte a mais de 1 TB.
  - Atualizando o banco de dados primário em uma relação de replicação geográfica: Alterar o tamanho máximo para mais de 1 TB em um banco de dados primário disparará a mesma alteração no banco de dados secundário. As duas atualizações devem ser bem-sucedidas para que a alteração no primário entre em vigor. Há limitações de região para a opção superior a 1 TB. Se o secundário estiver em uma região que não oferece suporte a mais de 1 TB, o primário não será atualizado.
- Não há suporte para o uso do serviço de Importação/Exportação para carregar bancos de dados P11/P15 com mais de 1 TB. Use SqlPackage.exe para [importar](sql-database-import.md) e [exportar](sql-database-export.md) dados.

## <a name="next-steps"></a>Próximas etapas

Para saber os limites gerais de recursos, confira [Limites de recurso baseados em vCore do Banco de Dados SQL – bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e [Limites de recurso baseados em DTU do Banco de Dados SQL – pools elásticos](sql-database-dtu-resource-limits-single-databases.md).
