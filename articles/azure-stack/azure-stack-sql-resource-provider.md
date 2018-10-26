---
title: Usando bancos de dados SQL no Azure Stack | Microsoft Docs
description: Saiba como você pode implantar os bancos de dados SQL como um serviço no Azure Stack e os passos rápidos para implantar o adaptador de provedor de recursos do SQL Server.
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
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 3d608843ef31a1ed665fcb1fd90b822f34f77fdd
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086345"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Usar bancos de dados SQL no Microsoft Azure Stack

Usar o adaptador de provedor de recursos do SQL Server para oferecer bancos de dados SQL como um serviço do [do Azure Stack](azure-stack-poc.md). Depois de instalar o provedor de recursos e conectá-lo a uma ou mais instâncias do SQL Server, você e seus usuários podem criar:

- Bancos de dados para aplicativos nativos de nuvem.
- Sites que usam o SQL.
- Cargas de trabalho que usam SQL.

O provedor de recursos não fornece o banco de dados de capacidades de gerenciamento do [banco de dados SQL](https://azure.microsoft.com/services/sql-database/). Por exemplo, os pools Elásticos que alocam automaticamente os recursos não são suportados. No entanto, a recurso provedor dá suporte a semelhante cria, ler, atualizar e excluir operações (CRUD) em um banco de dados do SQL Server. 

## <a name="sql-resource-provider-adapter-architecture"></a>Arquitetura de adaptador de provedor de recursos do SQL

O provedor de recursos consiste dos seguintes componentes:

- **A máquina de virtual adaptador (VM) do provedor de recursos SQL**, que é uma VM do Windows Server que executa os serviços de provedor.
- **O provedor de recursos**, que processa as solicitações e acessa recursos de banco de dados.
- **Servidores que hospedam o SQL Server**, que fornecem a capacidade para bancos de dados chamado servidores de hospedagem.

Você deve criar pelo menos uma instância do SQL Server ou fornecem acesso às instâncias do SQL Server externos.

> [!NOTE]
> Servidores que estão instalados no Azure Stack de hospedagem sistemas integrados devem ser criados de uma assinatura de locatário. Eles não podem ser criados de assinatura do provedor padrão. Eles devem ser criados a partir do portal de locatário ou usando o PowerShell com a entrada apropriada. Todos os servidores de hospedagem são faturáveis máquinas de virtuais e devem ter licenças. O administrador de serviço pode ser o proprietário da assinatura do locatário.

## <a name="next-steps"></a>Próximas etapas

[Implantar o provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
