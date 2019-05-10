---
title: Configurar o ponto de extremidade público - banco de dados do SQL Azure gerenciado instância | Microsoft Docs
description: Saiba como configurar um ponto de extremidade público para a instância gerenciada
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: d3e68a5287e59c576f85491e6e5eba33fac080ca
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465144"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Configurar o ponto de extremidade público na instância gerenciada do banco de dados SQL

Ponto de extremidade público para um [instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) permite o acesso de dados a sua instância gerenciada de fora a [rede virtual](../virtual-network/virtual-networks-overview.md). É possível acessar a instância gerenciada dos serviços do Azure de multilocatário, como Power BI, serviço de aplicativo do Azure ou uma rede local. Usando o ponto de extremidade público em uma instância gerenciada, você não precisa usar uma VPN, que pode ajudar a evitar problemas de taxa de transferência VPN.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> - Habilitar o ponto de extremidade público para sua instância gerenciada no portal do Azure
> - Habilitar o ponto de extremidade público para sua instância gerenciada usando o PowerShell
> - Configurar o grupo de segurança de rede de instância gerenciada para permitir o tráfego para o ponto de extremidade público de instância gerenciada
> - Obter a cadeia de caracteres de conexão de ponto de extremidade público de instância gerenciada

## <a name="permissions"></a>Permissões

Devido à confidencialidade dos dados que estão em uma instância gerenciada, a configuração para habilitar o ponto de extremidade público de instância gerenciada exige um processo em duas etapas. Esta medida de segurança segue a separação das responsabilidades (SoD):

- Habilitar ponto de extremidade público em uma instância gerenciada precisa ser feito pelo administrador de instância gerenciada. O administrador da instância gerenciada pode ser encontrado no **visão geral** página do SQL gerenciada recursos de instância.
- Permitindo o tráfego usando um grupo de segurança de rede que precisa ser feito por um administrador de rede. Para obter mais informações, consulte [permissões de grupo de segurança de rede](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Habilitando o ponto de extremidade público para uma instância gerenciada no portal do Azure

1. Inicie o portal do Azure em <https://portal.azure.com/.>
1. Abra o grupo de recursos com a instância gerenciada e selecione o **instância gerenciada SQL** que você deseja configurar o ponto de extremidade público.
1. Sobre o **segurança** as configurações, selecione o **rede Virtual** guia.
1. Na página de configuração de rede Virtual, selecione **habilitar** e, em seguida, o **salvar** ícone para atualizar a configuração.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Habilitando o ponto de extremidade público para uma instância gerenciada usando o PowerShell

### <a name="enable-public-endpoint"></a>Habilitar o ponto de extremidade público

Execute os comandos do PowerShell a seguir. Substitua **id da assinatura** com sua ID de assinatura. Além disso, substitua **rg-name** com o grupo de recursos para sua instância gerenciada e substitua **mi nome** com o nome da sua instância gerenciada.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Desabilitar ponto de extremidade público

Para desabilitar o ponto de extremidade público usando o PowerShell, você executaria o seguinte comando (e também não se esqueça de fechar o NSG para a porta de entrada 3342 se você tiver configurado):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Permitir o tráfego de ponto de extremidade público no grupo de segurança de rede

1. Se você tiver a página de configuração da instância gerenciada ainda aberta, navegue até a **visão geral** guia. Caso contrário, vá até seu **instância gerenciada SQL** recursos. Selecione o **sub-rede da rede Virtual** link, que você será levado à página de configuração de rede Virtual.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Selecione o **subredes** guia no painel à esquerda de configuração da sua rede Virtual e anote o **grupo de segurança** para sua instância gerenciada.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Volte ao seu grupo de recursos que contém sua instância gerenciada. Você deve ver a **grupo de segurança de rede** nome mencionado acima. Selecione o nome para ir para a página de configuração do grupo de segurança de rede.

1. Selecione o **regras de segurança de entrada** guia, e **Add** uma regra que tem prioridade maior do que o **deny_all_inbound** regra com as seguintes configurações: </br> </br>

    |Configuração  |Valor sugerido  |Descrição  |
    |---------|---------|---------|
    |**Fonte**     |Qualquer endereço IP ou a marca de serviço         |<ul><li>Para serviços do Azure como o Power BI, selecione a marca de serviço de nuvem do Azure</li> <li>Para seu computador ou VM do Azure, use o endereço IP de NAT</li></ul> |
    |**Intervalos de porta de origem**     |*         |Deixar esta opção para * (qualquer) como portas de origem geralmente são alocado dinamicamente e como, imprevisível |
    |**Destino**     |Qualquer         |Deixar o destino como qualquer para permitir o tráfego para a sub-rede da instância gerenciada |
    |**Intervalos de porta de destino**     |3342         |Porta de destino de escopo para 3342, que é o ponto de extremidade TDS público instância gerenciada |
    |**Protocolo**     |TCP         |Gerenciado instância usa TCP protocolo para o protocolo TDS |
    |**Ação**     |PERMITIR         |Permitir tráfego de entrada para a instância gerenciada por meio do ponto de extremidade público |
    |**Prioridade**     |1300         |Verifique se essa regra é uma prioridade maior que o **deny_all_inbound** regra |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Porta 3342 é usada para o ponto de extremidade público conexões com a instância gerenciada e não podem ser alteradas neste momento.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Como obter a cadeia de caracteres de conexão de ponto de extremidade público de instância gerenciada

1. Navegue até a página de configuração de instância gerenciada do SQL que foi habilitada para o ponto de extremidade público. Selecione o **cadeias de caracteres de Conexão** guia sob o **configurações** configuração.
1. Observe que o nome de host do ponto de extremidade público é fornecido no formato < mi_name >. **público**. < dns_zone >. database.windows.net e que a porta usada para a conexão é 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [usar o banco de dados SQL a instância gerenciada com segurança com o ponto de extremidade público](sql-database-managed-instance-public-endpoint-securely.md).