---
title: Banco de Dados SQL do Azure - consultas de leitura em réplicas | Microsoft Docs
description: O banco de dados do SQL Azure fornece a capacidade de balancear a carga de cargas de trabalho de somente leitura usando a capacidade de réplicas somente leitura - chamadas de expansão de leitura.
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
ms.date: 04/19/2019
ms.openlocfilehash: cbcdcfd151951334246a4e85d9f521a15bb6269d
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006141"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Usar réplicas somente leitura para cargas de trabalho de consulta somente leitura de balanceamento de carga

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é compatível com o banco de dados SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Como parte do [arquitetura de alta disponibilidade](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability), cada banco de dados na camada de serviço Premium, comercialmente crítico ou em hiperescala é provisionado automaticamente com uma réplica primária e várias réplicas secundárias. As réplicas secundárias são provisionadas com o mesmo tamanho de computação como a réplica primária. O **expansão de leitura** recurso permite que você a balancear a carga banco de dados SQL somente leitura cargas de trabalho usando a capacidade de uma das réplicas de somente leitura em vez de compartilhar a réplica de leitura / gravação. Dessa forma, a carga de trabalho somente leitura serão isoladas da carga de trabalho principal de leitura/gravação e não afetarão o desempenho. O recurso destina-se para os aplicativos que incluem logicamente separadas somente leitura cargas de trabalho, como análise. Eles poderiam obter benefícios de desempenho usando essa capacidade adicional sem nenhum custo extra.

O diagrama a seguir ilustra usando um banco de dados críticos de negócios.

![Réplicas somente leitura](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

O recurso de expansão de leitura está habilitado por padrão em Premium novo, críticos de negócios e bancos de dados em hiperescala. Se sua cadeia de caracteres de conexão do SQL está configurada com `ApplicationIntent=ReadOnly`, o aplicativo será redirecionado pelo gateway para uma réplica somente leitura desse banco de dados. Para obter informações sobre como usar o `ApplicationIntent` propriedade, consulte [especificando a intenção do aplicativo](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Se você quiser garantir que o aplicativo se conecta à réplica primária, independentemente do `ApplicationIntent` configuração na cadeia de caracteres de conexão SQL, você deve desabilitar explicitamente horizontal de leitura ao criar o banco de dados ou ao alterar sua configuração. Por exemplo, se você atualizar seu banco de dados da camada Standard ou de uso geral à camada Premium, comercialmente crítico ou em hiperescala e certifique-se de todas as suas conexões continuem indo para a réplica primária, desabilite a expansão de leitura. Para obter detalhes sobre como desativá-la, consulte [habilitar e desabilitar expansão de leitura](#enable-and-disable-read-scale-out).

> [!NOTE]
> Consulta dados Store, eventos estendidos, SQL Profiler e recursos de auditoria não têm suporte nas réplicas somente leitura. 

## <a name="data-consistency"></a>Consistência de dados

Um dos benefícios das réplicas é que as réplicas estão sempre no estado transacionalmente consistente, mas em diferentes momentos, pode haver uma pequena latência entre diferentes réplicas. A Expansão de leitura oferece suporte a consistência em nível de sessão. Isso significa, se a sessão somente leitura se reconectar após um erro de conexão causado pela indisponibilidade da réplica, ela poderá ser redirecionada para uma réplica que não seja 100% atualizada com a réplica de leitura / gravação. Da mesma forma, se um aplicativo grava dados usando uma sessão de leitura / gravação e imediatamente o lê usando uma sessão somente leitura, é possível que as atualizações mais recentes não são visíveis imediatamente na réplica. A latência é causada por uma operação de refazer do log de transação assíncrona.

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

Expansão de leitura está habilitada por padrão nas camadas de serviço Premium, críticos de negócios e em hiperescala. Expansão de leitura não pode ser habilitado em camadas de serviço Basic, Standard ou uso geral. Expansão de leitura é automaticamente desabilitada nos bancos de dados em hiperescala configurados com 0 réplicas. 

Você pode desabilitar e habilitar novamente a expansão de leitura em bancos de dados únicos e pool Elástico na camada de serviço Premium ou comercialmente crítico usando os seguintes métodos.

> [!NOTE]
> A capacidade de desabilitar a expansão de leitura é fornecida para compatibilidade com versões anteriores.

### <a name="azure-portal"></a>Portal do Azure

Você pode gerenciar a configuração de leitura Sacle-out na **configurar** folha banco de dados. 

### <a name="powershell"></a>PowerShell

Gerenciar a Expansão de leitura no Microsoft Azure PowerShell requer a versão de dezembro de 2016 do Microsoft Azure PowerShell ou mais recente. Para a versão mais recente do PowerShell, consulte [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Você pode desabilitar ou habilitar novamente a expansão de leitura no Azure PowerShell invocando o [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet e passando o valor desejado – `Enabled` ou `Disabled` – para o `-ReadScale` parâmetro. 

Para desabilitar a escala horizontal de leitura no banco de dados existente (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
Para desabilitar a escala horizontal de leitura em um novo banco de dados (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

Para habilitar novamente a escala horizontal de leitura no banco de dados existente (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>API REST

Para criar um banco de dados com leitura expansão desabilitado, ou para alterar a configuração para um banco de dados existente, use o seguinte método com o `readScale` propriedade definida como `Enabled` ou `Disabled` como na solicitação de exemplo abaixo.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Disabled"
   }
}
```

Para obter mais informações, consulte [Bancos de dados - Criar ou Atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Usando o TempDB na réplica somente leitura

O banco de dados TempDB não é replicado para as réplicas somente leitura. Cada réplica tem sua própria versão do banco de dados TempDB é criado quando a réplica é criada. Ele garante que o TempDB é atualizável e pode ser modificado durante a execução da consulta. Se sua carga de trabalho somente leitura depende do uso de objetos de TempDB, você deve criar esses objetos como parte de seu script de consulta. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Usar escala de leitura com bancos de dados replicados geograficamente

Se você estiver usando a expansão de leitura para o balanceamento de carga somente leitura cargas de trabalho em um banco de dados com replicação geográfica (por exemplo, como um membro de um grupo de failover), certifique-se de que expansão leitura está habilitado no principal e os bancos de dados de secundários replicado geograficamente. Essa configuração garantirá que a mesma experiência de balanceamento de carga continuará quando seu aplicativo se conecta ao novo primário após o failover. Se estiver se conectando ao banco de dados secundário geograficamente replicado com escala de leitura habilitada, suas sessões com `ApplicationIntent=ReadOnly` serão roteadas para uma das réplicas da mesma forma que roteamos conexões no banco de dados primário.  As sessões sem `ApplicationIntent=ReadOnly` serão roteadas para a réplica primária do secundário replicado geograficamente, que também é somente leitura. Porque o banco de dados secundário replicado geograficamente tem um ponto de extremidade diferente que o banco de dados primário, historicamente para acessar o secundário ele não era necessário definir `ApplicationIntent=ReadOnly`. Para garantir a compatibilidade com versões anteriores, o `sys.geo_replication_links` DMV mostra `secondary_allow_connections=2` (qualquer conexão de cliente é permitida).

> [!NOTE]
> Não há suporte para o round-robin roteamento ou quaisquer outros com balanceamento de carga entre réplicas de banco de dados secundário locais.

## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre a oferta em hiperescala do banco de dados SQL, consulte [camada de serviço em hiperescala](./sql-database-service-tier-hyperscale.md).
