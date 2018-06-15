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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b1cc1fad6b0831bcf0bab5ba4f37b753c3cf33ca
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33207259"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Usar bancos de dados SQL na pilha do Microsoft Azure
Usar o adaptador de provedor de recursos do SQL Server para expor bancos de dados SQL como um serviço de [Azure pilha](azure-stack-poc.md). Depois de instalar o provedor de recursos e conectá-lo para uma ou mais instâncias do SQL Server, os usuários podem criar:
- Bancos de dados para aplicativos de nuvem nativo.
- Sites baseados em SQL.
- Cargas de trabalho baseados em SQL.
Você não precisa provisionar uma máquina virtual (VM) que hospeda o SQL Server cada vez.

O provedor de recursos não dá suporte a todos os recursos de gerenciamento de banco de dados do [banco de dados do SQL Azure](https://azure.microsoft.com/services/sql-database/). Por exemplo, pools de banco de dados Elástico e a capacidade de discagem o desempenho do banco de dados para cima e para baixo automaticamente não estão disponíveis. No entanto, o recurso oferece suporte ao provedor semelhante criar, ler, atualizar e excluir operações (CRUD). A API não é compatível com o banco de dados SQL.

## <a name="sql-resource-provider-adapter-architecture"></a>Arquitetura de adaptador de provedor de recursos do SQL
O provedor de recursos consiste em três componentes:

- **O adaptador de provedor de recursos do SQL VM**, que é uma máquina virtual do Windows que executa os serviços de provedor.
- **O provedor de recursos**, que processa solicitações de provisionamento e expõe recursos de banco de dados.
- **Servidores que hospedam o SQL Server**, que fornecem a capacidade para bancos de dados chamados de servidores de hospedagem.

Você deve criar uma (ou mais) de instâncias do SQL Server e/ou fornecer acesso às instâncias do SQL Server externas.

> [!NOTE]
> Servidores que estão instalados na pilha do Azure de hospedagem sistemas integrados devem ser criados de uma assinatura de locatário. Eles não não possível criar a assinatura do provedor padrão. Eles devem ser criados no portal de locatário ou uma sessão do PowerShell com uma entrada apropriada. Todos os servidores de hospedagem são passíveis de cobrança VMs e devem ter as licenças apropriadas. O administrador de serviço pode ser o proprietário da assinatura de locatário.


## <a name="next-steps"></a>Próximas etapas

[Implantar o provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
