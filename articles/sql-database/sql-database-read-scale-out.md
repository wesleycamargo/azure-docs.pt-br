---
title: Banco de Dados SQL do Microsoft Azure - consultas de leitura em réplicas | Microsoft Docs
description: O Banco de Dados SQL do Microsoft Azure fornece a capacidade de balancear a carga de cargas de trabalho somente leitura usando a capacidade de réplicas somente leitura - chamadas de Expansão de Leitura.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 03/28/2019
ms.openlocfilehash: d9ad859ef24b51dc337dc23281d2fe4e1eada1e6
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619884"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Usar réplicas somente leitura para cargas de trabalho de consulta somente leitura de balanceamento de carga

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é compatível com o banco de dados SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

**Read Scale-Out** permite que você faça o balanceamento de carga das cargas de trabalho somente leitura do Banco de Dados SQL do Azure usando a capacidade de uma réplica somente leitura.

Cada banco de dados na camada Premium ([modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md)) ou na camada Comercialmente Crítico ([modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md)) é provisionado automaticamente com várias réplicas AlwaysON para oferecer suporte ao SLA de disponibilidade. Isso é ilustrado pelo diagrama a seguir.

![Réplicas somente leitura](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

As réplicas secundárias são provisionadas com o mesmo tamanho de computação como a réplica primária. O recurso **Escala de leitura** permite que você balanceie a carga de cargas de trabalho somente leitura do Banco de Dados SQL do Microsoft Azure usando a capacidade de uma das réplicas somente leitura em vez de compartilhar a réplica de leitura-gravação. Dessa forma, a carga de trabalho somente leitura serão isoladas da carga de trabalho principal de leitura/gravação e não afetarão o desempenho. O recurso destina-se aos aplicativos que incluem cargas de trabalho somente leitura logicamente separadas, como análises e, portanto, poderiam obter benefícios de desempenho usando essa capacidade adicional sem nenhum custo extra.

Para usar o recurso de expansão de leitura com um determinado banco de dados, é necessário ativá-lo ao criar o banco de dados ou posteriormente, alterando a configuração usando o PowerShell invocando o [AzSqlDatabase conjunto](/powershell/module/az.sql/set-azsqldatabase) ou o [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlets ou por meio de REST API do Azure Resource Manager usando o [bancos de dados – criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) método.

Após a expansão de leitura está habilitada para um banco de dados, aplicativos que se conectam ao banco de dados serão direcionados pelo gateway para a réplica de leitura / gravação ou para uma réplica somente leitura desse banco de dados de acordo com o `ApplicationIntent` propriedades configuradas no cadeia de caracteres de conexão do aplicativo. Para obter informações sobre a propriedade `ApplicationIntent`, consulte [Especificando a intenção do aplicativo](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Se a expansão de leitura está desabilitada ou você definir a propriedade ReadScale em uma camada de serviço sem suporte, todas as conexões são direcionadas para a réplica de leitura-gravação, independentemente da propriedade `ApplicationIntent`.

> [!NOTE]
> Consulta dados Store, eventos estendidos, SQL Profiler e recursos de auditoria não têm suporte nas réplicas somente leitura. 
## <a name="data-consistency"></a>Consistência de dados

Um dos benefícios das réplicas é que as réplicas estão sempre no estado transacionalmente consistente, mas em diferentes momentos, pode haver uma pequena latência entre diferentes réplicas. A Expansão de leitura oferece suporte a consistência em nível de sessão. Isso significa que, se a sessão somente leitura se reconectar após um erro de conexão causado pela indisponibilidade da réplica, ela pode ser redirecionada para uma réplica que não esteja 100% atualizada com a réplica de leitura-gravação. Da mesma forma, se um aplicativo grava dados usando uma sessão de leitura / gravação e imediatamente o lê usando uma sessão somente leitura, é possível que as atualizações mais recentes não são visíveis imediatamente na réplica. A latência é causada por uma operação de refazer do log de transação assíncrona.

> [!NOTE]
> As latências de replicação na região são baixas e essa situação é rara.

## <a name="connect-to-a-read-only-replica"></a>Conectar-se a uma réplica somente leitura

Quando você habilita a expansão de leitura para um banco de dados, a opção `ApplicationIntent` na cadeia de conexão fornecida pelo cliente determina se a conexão é roteada para a réplica de gravação ou para uma réplica somente leitura. Especificamente, se o valor `ApplicationIntent` é `ReadWrite` (o valor padrão), a conexão será direcionada para a réplica de leitura-gravação do banco de dados. Isso é idêntico ao comportamento existente. Se o valor de `ApplicationIntent` é `ReadOnly`, a conexão é roteada para uma réplica somente leitura.

Por exemplo, a seguinte cadeia de conexão conecta o cliente a uma réplica somente leitura (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Qualquer uma das seguintes cadeias de conexão conecta o cliente a uma réplica de leitura-gravação (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Verifique se uma conexão está pronta para réplica somente leitura

Você pode verificar se você está conectado a uma réplica somente leitura ao executar a consulta a seguir. Ela retornará READ_ONLY quando conectado a uma réplica somente leitura.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> Em um determinado momento qualquer apenas uma das réplicas de AlwaysON fica acessível por sessões somente leitura.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Monitorando e Solucionando problemas de réplica somente leitura

Quando conectado a uma réplica somente leitura, você pode acessar as métricas de desempenho usando o `sys.dm_db_resource_stats` DMV. Para acessar as estatísticas do plano de consulta, use o `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` e `sys.dm_exec_sql_text` DMVs.

> [!NOTE]
> O DMV `sys.resource_stats` no banco de dados mestre lógico retorna dados de armazenamento e de utilização de CPU da réplica primária.


## <a name="enable-and-disable-read-scale-out"></a>Ativar e desativar o Escalonamento de Leitura

A Expansão de Leitura está habilitada por padrão em [Instância Gerenciada](sql-database-managed-instance.md) Camada Comercialmente Crítico. Ele deve ser habilitado explicitamente no [banco de dados colocado no servidor do Banco de Dados SQL](sql-database-servers.md) e nas camadas Premium e Comercialmente Crítico. Os métodos para habilitar e desabilitar expansão de leitura é descrita aqui.

### <a name="powershell-enable-and-disable-read-scale-out"></a>PowerShell: Ativar e desativar o Escalonamento de Leitura

Gerenciar a Expansão de leitura no Microsoft Azure PowerShell requer a versão de dezembro de 2016 do Microsoft Azure PowerShell ou mais recente. Para a versão mais recente do PowerShell, consulte [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Habilitar ou desabilitar a escala horizontal de leitura no Azure PowerShell invocando o [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet e passando o valor desejado – `Enabled` ou `Disabled` – para o `-ReadScale` parâmetro. Como alternativa, você pode usar o [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) para criar um novo banco de dados com escala de leitura habilitado.

Por exemplo, para habilitar a escala de leitura para um banco de dados existente (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Para desabilitar a escala de leitura para um banco de dados existente (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Para criar um novo banco de dados com escala de leitura habilitada (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

### <a name="rest-api-enable-and-disable-read-scale-out"></a>API REST: Ativar e desativar o Escalonamento de Leitura

Para criar um banco de dados com expansão de leitura habilitada, ou para habilitar ou desabilitar a escala de leitura para um banco de dados existente, crie ou atualize a entidade de banco de dados correspondente com a propriedade `readScale` definida como `Enabled` ou `Disabled` como na solicitação de exemplo abaixo.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Enabled"
   }
}
```

Para obter mais informações, consulte [Bancos de dados - Criar ou Atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Usando o TempDB na réplica somente leitura

O banco de dados TempDB não é replicado para as réplicas somente leitura. Cada réplica tem sua própria versão do banco de dados TempDB é criado quando a réplica é criada. Ele garante que o TempDB é atualizável e pode ser modificado durante a execução da consulta. Se sua carga de trabalho somente leitura depende do uso de objetos de TempDB, você deve criar esses objetos como parte de seu script de consulta. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Usar escala de leitura com bancos de dados replicados geograficamente

Se você estiver usando leitura expansão carregar saldo somente leitura cargas de trabalho em um banco de dados com replicação geográfica (por exemplo, como um membro de um grupo de failover), certifique-se de que a leitura scale-out está habilitado no principal e os bancos de dados de secundários replicado geograficamente. Essa configuração garantirá que a mesma experiência de balanceamento de carga continuará quando seu aplicativo se conecta ao novo primário após o failover. Se estiver se conectando ao banco de dados secundário geograficamente replicado com escala de leitura habilitada, suas sessões com `ApplicationIntent=ReadOnly` serão roteadas para uma das réplicas da mesma forma que roteamos conexões no banco de dados primário.  As sessões sem `ApplicationIntent=ReadOnly` serão roteadas para a réplica primária do secundário replicado geograficamente, que também é somente leitura. Como o banco de dados secundário replicado geograficamente tem um ponto de extremidade diferente do banco de dados primário, historicamente para acessar o secundário não era necessário configurar o `ApplicationIntent=ReadOnly`. Para garantir a compatibilidade com versões anteriores, o `sys.geo_replication_links` DMV mostra `secondary_allow_connections=2` (qualquer conexão de cliente é permitida).

> [!NOTE]
> Não há suporte para o round-robin ou qualquer outra com balanceamento de carga roteamento entre réplicas de banco de dados secundário locais.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como usar o PowerShell para definir a leitura de escala horizontal, consulte o [AzSqlDatabase conjunto](/powershell/module/az.sql/set-azsqldatabase) ou o [New AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlets.
- Para obter informações sobre como usar a API REST para definir a escala de leitura, consulte [Bancos de dados - Criar ou Atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).
