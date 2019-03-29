---
title: 'PowerShell: Habilitar TDE com BYOK – Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs'
description: Saiba como configurar um Instância Gerenciada do SQL do Azure para começar a usar a TDE (Transparent Data Encryption) com BYOK para criptografia em repouso usando o PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/27/2019
ms.openlocfilehash: 6181183b1455d5ca38ab9bbd37102cb3bc091b3c
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622086"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Gerenciar Transparent Data Encryption em uma Instância Gerenciada usando sua própria chave do Azure Key Vault

Este script do PowerShell configura a TDE (Transparent Data Encryption) no cenário Bring Your Own Key para Instância Gerenciada do SQL do Azure usando uma chave do Azure Key Vault. Para saber mais sobre a TDE com suporte BYOK (Bring Your Own Key), visite [TDE com Bring Your Own Key para SQL do Azure](../transparent-data-encryption-byok-azure-sql.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o AZ PowerShell 1.4.0 ou posterior. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Outros exemplos de script do PowerShell para Banco de Dados SQL podem ser encontrados nos [scripts do PowerShell para Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).
