---
title: Azure Connect Governamental com o PowerShell | Microsoft Docs
description: "Informações sobre como conectar sua assinatura no Azure Governamental com o PowerShell"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: 47e5e535-baa0-457e-8c41-f9fd65478b38
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 3a0594fa639a2ac34c53b2263dbf7dd2410cf16a
ms.lasthandoff: 02/21/2017


---



# <a name="connect-to-azure-government-with-powershell"></a>Conectar-se ao Azure Governamental com o PowerShell
Para usar a CLI do Azure, você precisa se conectar ao Azure Governamental, em vez do Azure público. A CLI do Azure pode ser usada para gerenciar uma assinatura de grande porte por meio de script, ou para acessar os recursos que não estão disponíveis atualmente no Portal do Azure. Se você usou o PowerShell no Público do Azure, será basicamente a mesma coisa.  As diferenças do Azure Governamental são:

* A conexão com sua conta
* Os nomes das regiões

> [!NOTE]
> Se você ainda não usou o PowerShell, confira a [Introdução ao Azure PowerShell](/powershell/azureps-cmdlets-docs).
> 
> 

Quando você inicia o PowerShell, precisa que o Azure PowerShell se conecte ao Azure Governamental especificando um parâmetro de ambiente.  O parâmetro garante que o PowerShell está se conectando aos pontos de extremidade corretos.  A coleção de pontos de extremidade é determinada quando você entra na sua conta.  APIs diferentes exigem versões de opções do ambiente diferentes:

| Tipo de conexão | Command |
| --- | --- |
| [Comandos do Azure Active Directory (modelo de implantação clássico)](https://msdn.microsoft.com/library/dn708504.aspx) |`Add-AzureAccount -Environment AzureUSGovernment` |
| Comandos de [gerenciamento de recursos](https://msdn.microsoft.com/library/mt125356.aspx) |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| Comandos do [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) |`Connect-MsolService -AzureEnvironment UsGovernment` |
| [Comandos do Azure Active Directory (modelo de implantação do Resource Manager)](https://msdn.microsoft.com/library/azure/mt757189.aspx) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Linha de da Comando CLI do Azure](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

Você também pode usar a opção Ambiente durante a conexão com uma conta de armazenamento usando New-AzureStorageContext e especificando AzureUSGovernment.

### <a name="determining-region"></a>Determinando a região
Depois de se conectar, haverá mais uma diferença: as regiões usadas para um serviço de destino.  Cada nuvem do Azure tem regiões diferentes.  Você pode vê-las listadas na página de disponibilidade do serviço.  Normalmente, você usa a região no parâmetro Location para um comando.

Há um problema.  As regiões do Azure Governamental têm formatações diferente de seus nomes comuns:

| Nome comum | Comando |
| --- | --- |
| US Gov Virginia |USGov Virginia |
| US Gov Iowa |USGov Iowa |

> [!NOTE]
> Não existe espaço entre US e Gov ao usar o parâmetro Location.
> 
> 

Se você quiser validar as regiões disponíveis no Azure Governamental, poderá executar os seguintes comandos e imprimir a lista atual:

    Get-AzureLocation

Se estiver curioso sobre os ambientes disponíveis no Azure, poderá executar:

    Get-AzureEnvironment

