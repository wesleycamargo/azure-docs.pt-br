---
title: Usar bancos de dados MySQL como PaaS na pilha do Azure | Microsoft Docs
description: Saiba como você pode implantar o provedor de recursos MySQL e fornecer os bancos de dados MySQL como um serviço na pilha do Azure.
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
ms.openlocfilehash: 24ba595413cde07c420a94de234d7926e0eb0e7f
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310048"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Usar bancos de dados MySQL na pilha do Microsoft Azure

Você pode implantar o provedor de recursos MySQL API para usar bancos de dados MySQL implantados na pilha do Azure. Para obter mais informações sobre a API de provedor de recursos, consulte [Windows Azure Pack MySQL recurso provedor referência da API REST](https://msdn.microsoft.com/library/dn528442.aspx).

Bancos de dados MySQL são comuns em sites e oferecer suporte a várias plataformas de site. Por exemplo, você pode criar sites de WordPress usando a plataforma de aplicativos Web como um complemento de serviço (PaaS).

Depois de implantar o provedor de recursos, você pode:

* Crie servidores MySQL e bancos de dados usando modelos de implantação do Gerenciador de recursos do Azure.
* Forneça os bancos de dados MySQL como um serviço.  

## <a name="mysql-resource-provider-adapter-architecture"></a>Arquitetura de adaptador do provedor de recursos do MySQL

O provedor de recursos tem os seguintes componentes:

* **A máquina de virtual adaptador (VM) do provedor de recursos de MySQL**, que é uma VM do Windows Server que está executando os serviços de provedor.
* **O provedor de recursos**, que processa as solicitações e acesse recursos de banco de dados.
* **Servidores que hospedam o servidor MySQL**, que fornecem a capacidade para bancos de dados que são chamados de servidores de hospedagem. Você pode criar instâncias do MySQL por conta própria, ou fornecer acesso externo instâncias do MySQL. O [Galeria de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) tem um modelo de exemplo que você pode usar para:

  * Crie um servidor MySQL para você.
  * Baixe e implante um servidor MySQL no Azure Marketplace.

> [!NOTE]
> Servidores que estão instalados na pilha do Azure de hospedagem sistemas integrados devem ser criados de uma assinatura de locatário. Eles não não possível criar a assinatura do provedor padrão. Eles devem ser criados no portal de locatário ou uma sessão do PowerShell com uma entrada apropriada. Todos os servidores de hospedagem são faturáveis VMs e devem ter as licenças. O administrador de serviço pode ser o proprietário da assinatura de locatário.

### <a name="required-privileges"></a>Privilégios necessários

A conta do sistema deve ter os seguintes privilégios:

* **Banco de dados:** criar, descartar
* **Logon:** criar, definir, drop, conceder, revogar  

## <a name="next-steps"></a>Próximas etapas

[Implantar o provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md)
