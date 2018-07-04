---
title: Banco de Dados SQL do Microsoft Azure - consultas de leitura em réplicas | Microsoft Docs
description: O Banco de Dados SQL do Microsoft Azure fornece a capacidade de balancear a carga de cargas de trabalho somente leitura usando a capacidade de réplicas somente leitura - chamadas de Expansão de Leitura.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: sashan
ms.openlocfilehash: 7b504306e32f97a0392239f9e6adc6c460848580
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059995"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads-preview"></a>Usar réplicas somente leitura para balancear a carga de cargas de trabalho de consulta somente leitura (visualização)

**Expansão de leitura** permite que você balanceie a carga de cargas de trabalho somente leitura do Banco de Dados SQL do Microsoft Azure usando a capacidade de réplicas somente leitura. 

## <a name="overview-of-read-scale-out"></a>Visão geral da expansão de leitura

Cada banco de dados na camada Premium ([modelo de compra com base em DTU](sql-database-service-tiers-dtu.md)) ou na camada Comercialmente Crítico ([modelo de compra com base em vCore (versão prévia)](sql-database-service-tiers-vcore.md)) é provisionado automaticamente com várias réplicas AlwaysON para oferecer suporte ao SLA de disponibilidade. Essas réplicas são provisionadas com o mesmo nível de desempenho que a réplica de leitura-gravação usada pelas conexões de banco de dados regulares. O recurso **Escala de leitura** permite que você balanceie a carga de cargas de trabalho somente leitura do Banco de Dados SQL do Microsoft Azure usando a capacidade de uma das réplicas somente leitura em vez de compartilhar a réplica de leitura-gravação. Dessa forma, a carga de trabalho somente leitura serão isoladas da carga de trabalho principal de leitura/gravação e não afetarão o desempenho. O recurso destina-se aos aplicativos que incluem cargas de trabalho somente leitura logicamente separadas, como análises e, portanto, poderiam obter benefícios de desempenho usando essa capacidade adicional sem nenhum custo extra.

Para usar o recurso Expansão de leitura com um determinado banco de dados, você deve habilitá-lo ao criar o banco de dados ou posteriormente, alterando a configuração usando o PowerShell invocando os cmdlets [et-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) ou [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) ou por meio da API REST do Azure Resource Manager usando o método [Bancos de dados - Criar ou Atualizar](/rest/api/sql/databases/createorupdate). 

Após a Expansão de leitura ser habilitada para um banco de dados, aplicativos que se conectam ao banco de dados serão direcionados para a réplica de leitura-gravação ou para uma réplica somente leitura desse banco de dados de acordo com a propriedade `ApplicationIntent` configurada na cadeia de conexão do aplicativo. Para obter informações sobre a propriedade `ApplicationIntent`, consulte [Especificando a intenção do aplicativo](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Se a expansão de leitura está desabilitada ou você definir a propriedade ReadScale em uma camada de serviço sem suporte, todas as conexões são direcionadas para a réplica de leitura-gravação, independentemente da propriedade `ApplicationIntent`.

> [!NOTE]
> Durante a visualização, o Repositório de Dados de Consultas e os Eventos Estendidos não têm suporte nas réplicas somente leitura.

## <a name="data-consistency"></a>Consistência de dados

Um dos benefícios do Always On é que as réplicas sempre estão no estado transacionalmente consistente, mas em diferentes pontos no tempo pode haver breve latência entre as réplicas diferentes. A Expansão de leitura oferece suporte a consistência em nível de sessão. Isso significa que, se a sessão somente leitura se reconectar após um erro de conexão causado pela indisponibilidade da réplica, ela pode ser redirecionada para uma réplica que não esteja 100% atualizada com a réplica de leitura-gravação. Da mesma forma, se um aplicativo grava dados usando uma sessão de leitura-gravação e imediatamente o lê usando uma sessão somente leitura, é possível que as atualizações mais recentes não fiquem visíveis imediatamente. Isso ocorre porque a refeitura do log de transação para as réplicas é assíncrona.

> [!NOTE]
> As latências de replicação na região são baixas e essa situação é rara.


## <a name="connecting-to-a-read-only-replica"></a>Conectar-se a uma réplica somente leitura

Quando você habilita a expansão de leitura para um banco de dados, a opção `ApplicationIntent` na cadeia de conexão fornecida pelo cliente determina se a conexão é roteada para a réplica de gravação ou para uma réplica somente leitura. Especificamente, se o valor `ApplicationIntent` é `ReadWrite` (o valor padrão), a conexão será direcionada para a réplica de leitura-gravação do banco de dados. Isso é idêntico ao comportamento existente. Se o valor `ApplicationIntent` é `ReadOnly`, a conexão é roteada para uma réplica legível.

Por exemplo, a seguinte cadeia de conexão conecta o cliente a uma réplica somente leitura (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Qualquer uma das seguintes cadeias de conexão conecta o cliente a uma réplica de leitura-gravação (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Você pode verificar se você está conectado a uma réplica somente leitura ao executar a consulta a seguir. Ela retornará READ_ONLY quando conectado a uma réplica somente leitura.


```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```
> [!NOTE]
> Em um determinado momento qualquer apenas uma das réplicas de AlwaysON fica acessível por sessões somente leitura.

## <a name="enable-and-disable-read-scale-out-using-azure-powershell"></a>Habilitar e desabilitar a Expansão de leitura usando o Microsoft Azure PowerShell

Gerenciar a Expansão de leitura no Microsoft Azure PowerShell requer a versão de dezembro de 2016 do Microsoft Azure PowerShell ou mais recente. Para a versão mais recente do PowerShell, consulte [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Habilitar ou desabilitar a escala leitura no Azure PowerShell invocando o cmdlet [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) e passando o valor desejado – `Enabled` ou `Disabled` – para o parâmetro `-ReadScale`. Como alternativa, você pode usar o cmdlet [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) para criar um novo banco de dados com escala de leitura habilitada.

Por exemplo, para habilitar a escala de leitura para um banco de dados existente (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Para desabilitar a escala de leitura para um banco de dados existente (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Para criar um novo banco de dados com escala de leitura habilitada (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```powershell
New-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

## <a name="enabling-and-disabling-read-scale-out-using-the-azure-sql-database-rest-api"></a>Habilitar e desabilitar Expansão de leitura usando a API REST do Banco de Dados SQL do Azure

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

Para obter mais informações, consulte [Bancos de dados - Criar ou Atualizar](/rest/api/sql/databases/createorupdate).

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Usar escala de leitura com bancos de dados replicados geograficamente

Se estiver usando escala de leitura para balancear a carga de cargas de trabalho somente leitura em um banco de dados que é replicado geograficamente (por exemplo, um membro de um grupo de failover), verifique se a escala de leitura está habilitada nos bancos de dados primário e secundário replicado geograficamente. Isso garantirá o mesmo efeito de balanceamento de carga quando seu aplicativo se conectar ao novo primário após o failover. Se estiver se conectando ao banco de dados secundário geograficamente replicado com escala de leitura habilitada, suas sessões com `ApplicationIntent=ReadOnly` serão roteadas para uma das réplicas da mesma forma que roteamos conexões no banco de dados primário.  As sessões sem `ApplicationIntent=ReadOnly` serão roteadas para a réplica primária do secundário replicado geograficamente, que também é somente leitura. Como o banco de dados secundário replicado geograficamente tem um ponto de extremidade diferente do banco de dados primário, historicamente para acessar o secundário não era necessário configurar o `ApplicationIntent=ReadOnly`. Para garantir a compatibilidade com versões anteriores, o `sys.geo_replication_links` DMV mostra `secondary_allow_connections=2` (qualquer conexão de cliente é permitida).

> [!NOTE]
> Durante a versão prévia, não realizamos rodízio nem qualquer outro roteamento balanceado de carga entre as réplicas locais do banco de dados secundário. 


## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como usar o PowerShell para definir a escala de leitura, consulte os cmdlets [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) ou [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).
- Para obter informações sobre como usar a API REST para definir a escala de leitura, consulte [Bancos de dados - Criar ou Atualizar](/rest/api/sql/databases/createorupdate).
