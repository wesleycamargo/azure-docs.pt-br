---
title: PowerShell para alias de DNS para SQL do Azure| Microsoft Docs
description: "Os cmdlets do PowerShell, como New-AzureRMSqlServerDNSAlias, permitem que você redirecione novas conexões de cliente para um servidor de Banco de Dados SQL diferente, sem a necessidade de alterar qualquer configuração de cliente."
keywords: banco de dados sql dns
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.service: sql-database
ms.custom: 
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: PowerShell
ms.topic: article
ms.date: 01/31/2018
ms.reviewer: dmalik;amagarwa;maboja
ms.author: genemi
ms.openlocfilehash: 9d4aa9ddc0d77556e1511ea0a7914d94a227895f
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell para alias do DNS para o Banco de Dados SQL do Azure

Este artigo fornece um script do PowerShell que demonstra como você gerencia um alias de DNS para o Banco de Dados SQL do Azure. O script executa os seguintes cmdlets que realizam as seguintes ações:


Os cmdlets usados no exemplo de código são os seguintes:
- [New-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): cria um novo alias de DNS no sistema de serviço do Banco de Dados SQL do Azure. O alias refere-se ao servidor de Banco de Dados SQL do Azure 1.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): obtém e lista todos os aliases de DNS que são atribuídos ao servidor do Banco de Dados SQL 1.
- [Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): modifica o nome do servidor ao qual o alias está configurado para fazer referência, de servidor 1 para servidor do Banco de Dados SQL 2.
- [Remove-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): remove o alias do DNS do servidor do Banco de Dados SQL 2, usando o nome do alias.


Os cmdlets do PowerShell anteriores foram adicionados ao módulo **AzureRm.Sql** a partir da versão 5.1.1.




#### <a name="dns-alias-in-connection-string"></a>Alias de DNS na cadeia de conexão

Para se conectar a um servidor de Banco de Dados SQL do Azure específico, um cliente, como o SSMS (SQL Server Management Studio), pode fornecer o nome do alias de DNS em vez do nome real do servidor. Na cadeia de caracteres de servidor de exemplo a seguir, o alias *any-unique-alias-name* substitui o primeiro nó delimitado por pontos na cadeia de caracteres de servidor de quatro nós:
- Cadeia de caracteres de servidor de exemplo: `any-unique-alias-name.database.windows.net`.



## <a name="prerequisites"></a>pré-requisitos

Se você deseja executar o script de demonstração do PowerShell fornecido neste artigo, os seguintes pré-requisitos se aplicam:

- Uma assinatura e uma conta do Azure. Para uma avaliação gratuita, clique em [https://azure.microsoft.com/free/][https://azure.microsoft.com/free/].

- Módulo do Azure PowerShell, com o cmdlet **New-AzureRMSqlServerDNSAlias**.
    - Para instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell][install-azurerm-ps-84p].
    - Execute `Get-Module -ListAvailable AzureRM;` em powershell\_ise.exe, para localizar a versão.

- Dois servidores de Banco de Dados SQL do Azure.

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
    Login-AzureRmAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzureRmSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzureRMSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzureRMSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```


#### <a name="actual-console-output-from-the-powershell-example"></a>Saída de console real do exemplo do PowerShell

A seguinte saída do console foi copiada e colada de uma execução real.


```
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

[install-azurerm-ps-84p]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps

[dns-alias-overview-37v]: dns-alias-overview.md

