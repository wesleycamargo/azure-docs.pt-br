---
title: Bancos de dados SQL na pilha do Azure | Microsoft Docs
description: Saiba como você pode implantar bancos de dados SQL como um serviço na pilha do Azure e as etapas rápidas para implantar o adaptador de provedor de recursos do SQL Server.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 55d0e51606e8768a01c0b5a7766dbafe24d97a0d
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307818"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Usar bancos de dados SQL na pilha do Microsoft Azure

Usar o adaptador de provedor de recursos do SQL Server API para expor bancos de dados SQL como um serviço de [Azure pilha](azure-stack-poc.md). Depois de instalar o provedor de recursos e conectá-lo para uma ou mais instâncias do SQL Server, os usuários podem criar:

- Bancos de dados para aplicativos de nuvem nativo.
- Sites que usam SQL.
- Cargas de trabalho que usam SQL.

O provedor de recursos não fornece o banco de dados de capacidade de gerenciamento de [banco de dados do SQL Azure](https://azure.microsoft.com/services/sql-database/). Por exemplo, pools Elásticos que alocam automaticamente os recursos não têm suporte. No entanto, o provedor de recurso suporta semelhante cria, ler, atualizar e excluir operações CRUD () em um banco de dados do SQL Server. Para obter mais informações sobre a API de provedor de recursos, consulte [Windows Azure Pack SQL Server Resource provedor referência da API REST](https://msdn.microsoft.com/library/dn528529.aspx).

>[!NOTE]
A API de provedor de recursos do SQL Server não é compatível com o banco de dados do SQL Azure.

## <a name="sql-resource-provider-adapter-architecture"></a>Arquitetura de adaptador de provedor de recursos do SQL

O provedor de recursos consiste dos seguintes componentes:

- **A máquina de virtual adaptador (VM) do provedor de recursos SQL**, que é uma VM do Windows Server que executa os serviços de provedor.
- **O provedor de recursos**, que processa as solicitações e acesse recursos de banco de dados.
- **Servidores que hospedam o SQL Server**, que fornecem a capacidade para bancos de dados chamados de servidores de hospedagem.

Você deve criar pelo menos uma instância do SQL Server ou fornecer acesso às instâncias do SQL Server externas.

> [!NOTE]
> Servidores que estão instalados na pilha do Azure de hospedagem sistemas integrados devem ser criados de uma assinatura de locatário. Eles não não possível criar a assinatura do provedor padrão. Eles devem ser criados no portal de locatário ou usando o PowerShell com o logon apropriado. Todos os servidores de hospedagem são máquinas virtuais de faturáveis e devem ter as licenças. O administrador de serviço pode ser o proprietário da assinatura de locatário.

## <a name="next-steps"></a>Próximas etapas

[Implantar o provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
