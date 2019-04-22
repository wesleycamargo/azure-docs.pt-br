---
title: PowerShell para alias de DNS para SQL do Azure| Microsoft Docs
description: Cmdlets do PowerShell, como novo AzSqlServerDNSAlias permitem que você redirecione novas conexões de cliente para um servidor de banco de dados SQL diferente, sem precisar tocar qualquer configuração de cliente.
keywords: banco de dados sql dns
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi,amagarwa,maboja, jrasnick
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 17b712afff293dba8c353767fc326761bcb53ba3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57860730"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell para alias do DNS para o Banco de Dados SQL do Azure

Este artigo fornece um script do PowerShell que demonstra como você gerencia um alias de DNS para o Banco de Dados SQL do Azure. O script executa os seguintes cmdlets que realizam as seguintes ações:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é compatível com o banco de dados SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Os cmdlets usados no exemplo de código são os seguintes:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Cria um novo alias do DNS no sistema de serviços do Banco de Dados SQL do Azure. O alias refere-se ao servidor do Banco de Dados SQL do Azure 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Obtém e lista todos os aliases do DNS atribuídos ao servidor de BD SQL 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modifica o nome do servidor para o qual o alias está configurado, do servidor 1 ao servidor de BD SQL 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remove o alias do DNS do servidor de BD SQL 2, usando o nome do alias.

## <a name="dns-alias-in-connection-string"></a>Alias de DNS na cadeia de conexão

Para se conectar a um servidor do Banco de Dados SQL do Azure específico, um cliente, como o SSMS (SQL Server Management Studio), pode fornecer o nome do alias de DNS em vez do nome real do servidor. Na cadeia de caracteres de servidor de exemplo a seguir, o alias *any-unique-alias-name* substitui o primeiro nó delimitado por pontos na cadeia de caracteres de servidor de quatro nós:

- Cadeia de caracteres de servidor de exemplo: `any-unique-alias-name.database.windows.net`.

## <a name="prerequisites"></a>Pré-requisitos

Se você deseja executar o script de demonstração do PowerShell fornecido neste artigo, os seguintes pré-requisitos se aplicam:

- Uma assinatura e uma conta do Azure. Para uma avaliação gratuita, clique em [https://azure.microsoft.com/free/][https://azure.microsoft.com/free/].
- Módulo do PowerShell do Azure, com o cmdlet **New-AzSqlServerDNSAlias**.
  - Para instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell][install-Az-ps-84p].
  - Execute `Get-Module -ListAvailable Az;` em powershell\_ise.exe, para localizar a versão.
- Dois servidores do Banco de Dados SQL do Azure.

## <a name="code-example"></a>Exemplo de código

Os exemplos de código do PowerShell a seguir começam atribuindo valores literais a diversas variáveis. Para executar o código, primeiro você deve editar todos os valores de espaço reservado para corresponderem a valores reais do seu sistema. Ou você pode apenas estudar o código. E a saída do console do código também será fornecida.

```powershell
################################################################
###    Assign prerequisites.                                 ###
################################################################
cls;

$SubscriptionName             = '<EDIT-your-subscription-name>';
[string]$SubscriptionGuid_Get = '?'; # The script assigns this value, not you.

$SqlServerDnsAliasName = '<EDIT-any-unique-alias-name>';

$1ResourceGroupName = '<EDIT-rg-1>';  # Can be same or different than $2ResourceGroupName.
$1SqlServerName     = '<EDIT-sql-1>'; # Must differ from $2SqlServerName.

$2ResourceGroupName = '<EDIT-rg-2>';
$2SqlServerName     = '<EDIT-sql-2>';

# Login to your Azure subscription, first time per session.
Write-Host "You must log into Azure once per powershell_ise.exe session,";
Write-Host "  thus type 'yes' only the first time.";
Write-Host " ";
$yesno = Read-Host '[yes/no]  Do you need to log into Azure now?';
if ('yes' -eq $yesno)
{
    Connect-AzAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```

### <a name="actual-console-output-from-the-powershell-example"></a>Saída de console real do exemplo do PowerShell

A seguinte saída do console foi copiada e colada de uma execução real.

```powershell
You must log into Azure once per powershell_ise.exe session,
  thus type 'yes' only the first time.

[yes/no]  Do you need to log into Azure now?: yes


Environment           : AzureCloud
Account               : gm@acorporation.com
TenantId              : 72f988bf-1111-1111-1111-111111111111
SubscriptionId        : 45651c69-2222-2222-2222-222222222222
SubscriptionName      : mysubscriptionname
CurrentStorageAccount :

[1] Assign a DNS alias to SQL DB server 1.
[2] Get the DNS alias that is assigned to SQL DB server 1.
[3] Move the DNS alias from 1 to SQL DB server 2.
[4] Remove the DNS alias from SQL DB server 2.
ResourceGroupName ServerName         ServerDNSAliasName
----------------- ----------         ------------------
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-2       gm-sqldb-dns-2     unique-alias-name-food


[C:\windows\system32\]
>>
```

## <a name="next-steps"></a>Próximas etapas

Para obter uma explicação completa sobre o recurso Alias de DNS para o Banco de Dados SQL, consulte [Alias de DNS para o Banco de Dados SQL do Azure][dns-alias-overview-37v].

<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-Az-ps-84p]: https://docs.microsoft.com/powershell/azure/install-az-ps

[dns-alias-overview-37v]: dns-alias-overview.md
