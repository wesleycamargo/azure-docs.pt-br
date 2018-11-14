---
title: Arquitetura de conectividade do Banco de Dados SQL do Azure | Microsoft Docs
description: Este documento explica a arquitetura de conectividade do Banco de Dados SQL do Microsoft Azure de dentro do Azure ou de fora do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 11/02/2018
ms.openlocfilehash: 11133a24f4446478dcc7f38ed50eb36de8843442
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978394"
---
# <a name="azure-sql-database-connectivity-architecture"></a>Arquitetura de Conectividade do Banco de Dados SQL do Azure

Este artigo explica a arquitetura de conectividade do Banco de Dados SQL do Azure e explica como os diferentes componentes funcionam para direcionar o tráfego para a instância do Banco de Dados SQL do Azure. Esses componentes de conectividade do Banco de Dados SQL do Azure funcionam para direcionar o tráfego de rede para o banco de dados do Azure com os clientes conectando-se de dentro e de fora do Azure. Este artigo também fornece exemplos de script para alterar o modo como ocorre a conectividade e as considerações relacionadas à alteração das configurações de conectividade padrões.

## <a name="connectivity-architecture"></a>Arquitetura de conectividade

O diagrama a seguir fornece uma visão geral de alto nível da arquitetura de conectividade do Banco de Dados SQL.

![visão geral da arquitetura](./media/sql-database-connectivity-architecture/architecture-overview.png)

As etapas a seguir descrevem como uma conexão é estabelecida com um banco de dados SQL do Azure por meio do software balanceador de carga (SLB) do Banco de Dados do Azure SQL e do gateway do Banco de Dados SQL do Azure.

- Os clientes se conectam ao SLB, que tem um endereço IP público e escuta na porta 1433.
- O SLB encaminha o tráfego para o gateway do Banco de Dados SQL do Azure.
- O gateway, dependendo da política de conexão efetiva, redireciona ou faz proxy do tráfego para o middleware de proxy correto.
- O middleware de proxy encaminha o tráfego para o banco de dados SQL do Azure apropriado.

> [!IMPORTANT]
> Cada um desses componentes possui proteção de ataque de negação de serviço distribuído (DDoS) interna na rede e na camada de aplicativo.

## <a name="connection-policy"></a>Política de Conexão

O Banco de Dados SQL do Azure oferece suporte às três opções a seguir para a configuração de diretiva de conexão de um servidor do Banco de Dados SQL:

- **Redirecionamento (recomendado):** Os clientes estabelecem conexões diretamente com o nó que hospeda o banco de dados. Para habilitar a conectividade, os clientes devem permitir regras de firewall de saída para todos os endereços IP do Azure na região (tente isso usando NSG (Network Security Groups) com [tags de serviço](../virtual-network/security-overview.md#service-tags)), não apenas os endereços IP do Gateway de Banco de Dados SQL do Azure. Como os pacotes vão diretamente para o banco de dados, a latência e o rendimento melhoraram o desempenho.
- **Proxy:** Nesse modo, todas as conexões são intermediadas por proxy pelos gateways do Banco de Dados SQL do Azure. Para habilitar a conectividade, o cliente deve ter regras de firewall de saída que permitam apenas os endereços IP do Gateway de Banco de Dados SQL do Azure (geralmente dois endereços IP por região). A escolha desse modo pode resultar em maior latência e menor rendimento, dependendo da natureza da carga de trabalho. Recomendamos enfaticamente a política de conexão de redirecionamento sobre a política de conexão de proxy para a menor latência e maior taxa de transferência.
- **Padrão:** Esta é a política de conexão em vigor em todos os servidores após a criação, a menos que você altere explicitamente a política de conexão para Proxy ou Redirect. A diretiva efetiva depende se as conexões se originam no Azure (Redirecionamento) ou fora do Azure (Proxy).

## <a name="connectivity-from-within-azure"></a>Conectividade de dentro do Azure

Se você estiver se conectando de dentro do Azure em um servidor criado após 10 de novembro de 2018, suas conexões terão uma política de conexão de **Redirecionar** por padrão. Uma política de **Redirecionamento** significa que conexões após a sessão TCP ter sido estabelecida com o banco de dados SQL do Azure, a sessão do cliente é redirecionada para o middleware do proxy com uma alteração do IP virtual de destino do gateway do Banco de Dados SQL do Azure para o middleware do proxy. Depois disso, todos os pacotes subsequentes fluem diretamente por meio do middleware do proxy, ignorando o gateway do Banco de Dados SQL do Azure. O diagrama a seguir ilustra esse fluxo de tráfego.

![visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

> [!IMPORTANT]
> Se você criou o Banco de Dados SQL do Microsoft Azure antes de 10 de novembro de 2018, sua política de conexão foi definida explicitamente como **Proxy**. Ao usar endpoints de serviço, é altamente recomendável alterar sua política de conexão para **Redirect** para permitir um melhor desempenho. Se você alterar sua política de conexão para **Redirecionar**, não será suficiente permitir a saída em seu NSG para IPs de gateway do Banco de Dados SQL do Azure listados abaixo, você deve permitir a saída para todos os IPs do Banco de Dados SQL do Azure. Isso pode ser feito com a ajuda de Marcas de Serviço do NSG (Grupos de Segurança de Rede). Para saber mais, confira [Marcas do Serviço](../virtual-network/security-overview.md#service-tags).

## <a name="connectivity-from-outside-of-azure"></a>Conectividade de fora do Azure

Se você estiver se conectando de fora do Azure, as conexões têm uma política de conexão de **Proxy** por padrão. Uma política de **Proxy** significa que a sessão TCP é estabelecida por meio do gateway do Banco de Dados SQL do Azure e todos os pacotes subsequentes fluem por meio do gateway. O diagrama a seguir ilustra esse fluxo de tráfego.

![visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Endereços IP de gateway do Banco de Dados SQL do Azure

Para se conectar a um banco de dados SQL do Azure a partir de recursos locais, você precisa permitir o tráfego de rede de saída para o gateway do Banco de Dados SQL do Azure para a sua região do Azure. As conexões vão somente por meio do gateway ao conectar-se no modo de Proxy, que é o padrão ao se conectar a partir de recursos locais.

A tabela a seguir lista os IPs primários e secundários do gateway do Banco de Dados SQL do Azure para todas as regiões de dados. Para algumas regiões, há dois endereços IP. Nessas regiões, o endereço IP primário é o endereço IP atual do gateway e o segundo endereço IP é um endereço IP de failover. O failover é o endereço para o qual podemos pode mover seu servidor para manter a alta disponibilidade do serviço. Para essas regiões, é recomendável que você permita a saída para ambos os endereços IP. O segundo endereço IP pertence à Microsoft e não escutará nenhum serviço até que ele seja ativado pelo Banco de Dados SQL do Azure para aceitar conexões.

| Nome da Região | Endereço IP primário | Endereço IP secundário |
| --- | --- |--- |
| Leste da Austrália | 191.238.66.109 | 13.75.149.87 |
| Sudeste da Austrália | 191.239.192.109 | 13.73.109.251 |
| Sul do Brasil | 104.41.11.5 | |
| Canadá Central | 40.85.224.249 | |
| Leste do Canadá | 40.86.226.166 | |
| Centro dos EUA | 23.99.160.139 | 13.67.215.62 |
| Leste da China 1 | 139.219.130.35 | |
| Leste da China 2 | 40.73.82.1 | |
| Norte da China 1 | 139.219.15.17 | |
| Norte da China 2 | 40.73.50.0 | |
| Ásia Oriental | 191.234.2.139 | 52.175.33.150 |
| Leste dos EUA 1 | 191.238.6.43 | 40.121.158.30 |
| Leste dos EUA 2 | 191.239.224.107 | 40.79.84.180 * |
| França Central | 40.79.137.0 | 40.79.129.1 |
| Alemanha Central | 51.4.144.100 | |
| Nordeste da Alemanha | 51.5.144.179 | |
| Centro da Índia | 104.211.96.159 | |
| Sul da Índia | 104.211.224.146 | |
| Oeste da Índia | 104.211.160.80 | |
| Leste do Japão | 191.237.240.43 | 13.78.61.196 |
| Oeste do Japão | 191.238.68.11 | 104.214.148.156 |
| Coreia Central | 52.231.32.42 | |
| Sul da Coreia | 52.231.200.86 |  |
| Centro-Norte dos EUA | 23.98.55.75 | 23.96.178.199 |
| Norte da Europa | 191.235.193.75 | 40.113.93.91 |
| Centro-Sul dos Estados Unidos | 23.98.162.75 | 13.66.62.124 |
| Sudeste da Ásia | 23.100.117.95 | 104.43.15.0 |
| Norte do Reino Unido | 13.87.97.210 | |
| Sul do Reino Unido 1 | 51.140.184.11 | |
| Sul do Reino Unido 2 | 13.87.34.7 | |
| Oeste do Reino Unido | 51.141.8.11 | |
| Centro-Oeste dos EUA | 13.78.145.25 | |
| Europa Ocidental | 191.237.232.75 | 40.68.37.158 |
| Oeste dos EUA 1 | 23.99.34.75 | 104.42.238.205 |
| Oeste dos EUA 2 | 13.66.226.202 | |
||||

\* **OBSERVAÇÃO:** o *Leste dos EUA 2* também tem um endereço IP terciário `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Alterar a política de conexão do Banco de Dados SQL do Azure

Para alterar a política de conexão de Banco de Dados SQL do Azure para um servidor de Banco de Dados SQL do Azure, use o comando [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

- Se a política de conexão for definida como **Proxy**, todo os pacotes de rede fluem por meio do gateway do Banco de Dados SQL do Azure. Para essa configuração, você precisa permitir a saída para o IP de gateway do Banco de Dados SQL do Azure. Usar uma configuração de **Proxy** resulta em uma latência maior do que aquela da configuração de **Redirecionamento**.
- Se a política de conexão está defininda como **Redirecionamento**, todos os pacotes de rede fluem diretamente para o proxy do middleware. Para essa configuração, você precisa permitir a saída para vários IPs.

## <a name="script-to-change-connection-settings-via-powershell"></a>Script para alterar as configurações de conexão via PowerShell

> [!IMPORTANT]
> Este script requer o [módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).
>

O script do PowerShell a seguir mostra como alterar a política de conexão.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <Subscription Name>

# Azure Active Directory ID
$tenantId = "<Azure Active Directory GUID>"
$authUrl = "https://login.microsoftonline.com/$tenantId"

# Subscription ID
$subscriptionId = "<Subscription GUID>"

# Create an App Registration in Azure Active Directory.  Ensure the application type is set to NATIVE
# Under Required Permissions, add the API:  Windows Azure Service Management API

# Specify the redirect URL for the app registration
$uri = "<NATIVE APP - REDIRECT URI>"

# Specify the application id for the app registration
$clientId = "<NATIVE APP - APPLICATION ID>"

# Logical SQL Server Name
$serverName = "<LOGICAL DATABASE SERVER - NAME>"

# Resource Group where the SQL Server is located
$resourceGroupName= "<LOGICAL DATABASE SERVER - RESOURCE GROUP NAME>"


# Login and acquire a bearer token
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$result = $AuthContext.AcquireToken(
"https://management.core.windows.net/",
$clientId,
[Uri]$uri,
[Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Auto
)

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

#Get current connection Policy
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method GET -Headers $authHeader

#Set connection policy to Proxy
$connectionType="Proxy" <#Redirect / Default are other options#>
$body = @{properties=@{connectionType=$connectionType}} | ConvertTo-Json

# Apply Changes
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Script para mudar as configurações de conexão pela CLI do Azure

> [!IMPORTANT]
> Este script requer a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

O script da CLI a seguir mostra como alterar a política de conexão.

```azurecli-interactive
<pre>
# Get SQL Server ID
sqlserverid=$(az sql server show -n <b>sql-server-name</b> -g <b>sql-server-group</b> --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $id

# Update connection policy
az resource update --ids $id --set properties.connectionType=Proxy

</pre>
```

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como alterar a política de conexão de Banco de Dados SQL do Azure para um servidor de Banco de Dados SQL do Azure, consulte [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Para obter mais informações sobre o comportamento de conexão do Banco de Dados SQL do Azure para clientes que usam ADO.NET 4.5 ou uma versão mais recente, consulte [Portas depois da 1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Para obter informações sobre a visão geral do desenvolvimento de aplicativos em geral, consulte [Visão Geral do Desenvolvimento de Aplicativos do Banco de Dados SQL](sql-database-develop-overview.md).
