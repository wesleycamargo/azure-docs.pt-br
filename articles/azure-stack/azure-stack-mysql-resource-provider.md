---
title: Usar bancos de dados MySQL como PaaS no Azure Stack | Microsoft Docs
description: Saiba como você pode implantar o provedor de recursos do MySQL e fornecer bancos de dados MySQL como um serviço no Azure Stack.
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
ms.openlocfilehash: 635948c28ffe5d5eaece372976e58d26e17214e3
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50084223"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Usar bancos de dados MySQL no Microsoft Azure Stack

Bancos de dados MySQL são comuns nos sites e dar suporte a várias plataformas do site. Por exemplo, você pode criar sites WordPress usando a plataforma de aplicativos Web como um complemento de serviço (PaaS).

Depois de implantar o provedor de recursos, você pode:

* Crie servidores MySQL e bancos de dados usando modelos de implantação do Azure Resource Manager.
* Fornece bancos de dados MySQL como um serviço.  

## <a name="mysql-resource-provider-adapter-architecture"></a>Arquitetura de adaptador do provedor de recursos do MySQL

O provedor de recursos tem os seguintes componentes:

* **A máquina de virtual adaptador (VM) do provedor de recursos de MySQL**, que é uma VM do Windows Server que está executando os serviços de provedor.
* **O provedor de recursos**, que processa as solicitações e acessa recursos de banco de dados.
* **Servidores que hospedam o servidor MySQL**, que fornecem a capacidade para bancos de dados que são chamados de servidores de hospedagem. Você pode criar instâncias do MySQL por conta própria, ou fornecer acesso às instâncias do MySQL externos. O [Galeria de início rápido do Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) tem um modelo de exemplo que você pode usar para:

  * Crie um servidor MySQL para você.
  * Baixe e implante um servidor MySQL do Azure Marketplace.

> [!NOTE]
> Servidores que estão instalados no Azure Stack de hospedagem sistemas integrados devem ser criados de uma assinatura de locatário. Eles não podem ser criados de assinatura do provedor padrão. Eles devem ser criados a partir do portal de locatário ou uma sessão do PowerShell com uma entrada apropriada. Todos os servidores de hospedagem são faturáveis VMs e devem ter licenças. O administrador de serviço pode ser o proprietário da assinatura do locatário.

### <a name="required-privileges"></a>Privilégios necessários

A conta do sistema deve ter os seguintes privilégios:

* **Banco de dados:** criar, descartar
* **Logon:** criar, definir, drop, conceder e revogar  

## <a name="next-steps"></a>Próximas etapas

[Implantar o provedor de recursos do MySQL](azure-stack-mysql-resource-provider-deploy.md)
