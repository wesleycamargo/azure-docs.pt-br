---
title: Instância Gerenciada do Banco de Dados SQL do Azure configurando a VNET/sub-rede existente | Microsoft Docs
description: Este tópico descreve como configurar uma VNet (rede virtual) e a sub-rede existentes em que você pode implantar a Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 53aba5192ddf57598965fcfe0db5f2b18423c7e9
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344870"
---
# <a name="configure-an-existing-vnet-for-azure-sql-database-managed-instance"></a>Configurar uma VNet existente para a Instância Gerenciada do Banco de Dados SQL do Azure

A Instância Gerenciada do Banco de Dados SQL do Azure deve ser implantada dentro da [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md) do Azure e da sub-rede dedicada somente a Instâncias Gerenciadas. Você poderá usar a VNET e a sub-rede existentes se elas estiverem configuradas conforme os [requisitos de VNet da Instância Gerenciada](sql-database-managed-instance-connectivity-architecture.md#network-requirements). 

Se você tiver uma nova sub-rede que ainda não foi configurada, não tiver certeza de que a sub-rede está alinhada com os [requisitos](sql-database-managed-instance-connectivity-architecture.md#network-requirements) ou quiser verificar se a sub-rede ainda está em conformidade com os [requisitos de rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements) depois algumas alterações que você fez, valide e modifique sua rede usando o script explicado nesta seção. 

  > [!Note]
  > Só é possível criar uma Instância Gerenciada nas redes virtuais do Resource Manager. Não há suporte para VNETs do Azure implantadas usando o modelo de implantação Clássico. Calcule o tamanho da sub-rede seguindo as diretrizes na seção [Determinar o tamanho da sub-rede para Instâncias Gerenciadas](#determine-the-size-of-subnet-for-managed-instances), pois a sub-rede não pode ser redimensionada depois de você implantar os recursos dentro dela.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Validar e modificar uma rede virtual existente 

Se você quer criar uma Instância Gerenciada dentro de uma sub-rede existente, recomendamos o seguinte script do PowerShell para preparar a sub-rede:
```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```
A preparação da sub-rede é feita em três etapas simples:

1. Validar – a sub-rede e a rede virtual selecionadas são validadas em relação aos requisitos de rede da Instância Gerenciada.
2. Confirmar – o usuário vê um conjunto de alterações que precisam ser feitas para preparar a sub-rede para a implantação da Instância Gerenciada e é solicitado a dar consentimento.
3. Preparar – Rede virtual e sub-rede são configuradas corretamente.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [O que é uma Instância Gerenciada](sql-database-managed-instance.md)
- Para um tutorial que mostra como criar uma VNet, criar uma Instância Gerenciada e restaurar um banco de dados a partir de um backup de banco de dados, consulte [Criar uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-get-started.md).
- Para problemas de DNS, veja [Configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md).
