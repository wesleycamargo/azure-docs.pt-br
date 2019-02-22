---
title: Usar modelos do Azure Resource Manager no Azure Stack | Microsoft Docs
description: Saiba como usar modelos do Azure Resource Manager no Azure Stack para provisionar recursos.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 11/14/2018
ms.openlocfilehash: 98bc87dcaf229671647b201fe37eee114abad731
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648805"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Usar modelos do Gerenciador de Recursos do Azure no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode usar modelos do Azure Resource Manager para implantar e provisionar todos os recursos de seu aplicativo em uma única operação coordenada. Também é possível reimplantar modelos para fazer alterações aos recursos em um grupo de recursos.

Esses modelos podem ser implantados com o portal do Microsoft Azure Stack, o PowerShell, a linha de comando e o Visual Studio.

Os modelos de início rápido a seguir estão disponíveis no [GitHub](https://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Implantar o SharePoint Server (implantação de alta disponibilidade)

Use a extensão de DSC do PowerShell para [criar um farm do SharePoint Server 2013](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha) que inclui os seguintes recursos:

* Uma rede virtual
* Três contas de armazenamento
* Dois balanceadores de carga externos
* Uma máquina virtual (VM) configurada como um controlador de domínio para uma nova floresta com um único domínio
* Uma VM configurada como um servidor autônomo do SQL Server 2014
* Uma VM configurada como um farm do SharePoint Server 2013 de uma única máquina

## <a name="deploy-ad-non-high-availability-deployment"></a>Implantar o AD (não-alta--implantação de disponibilidade)

Use a extensão de DSC do PowerShell para [criar um servidor de controlador de domínio do AD](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha) que inclui os seguintes recursos:

* Uma rede virtual
* Uma conta de armazenamento
* Um balanceador externo de carga
* Uma máquina virtual (VM) configurada como um controlador de domínio para uma nova floresta com um único domínio

## <a name="deploy-adsql-non-high-availability-deployment"></a>Implantar o AD/SQL (não-alta--implantação de disponibilidade)

Use a extensão de DSC do PowerShell para [criar um servidor autônomo do SQL Server 2014](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha) que inclui os seguintes recursos:

* Uma rede virtual
* Duas contas de armazenamento
* Um balanceador externo de carga
* Uma máquina virtual (VM) configurada como um controlador de domínio para uma nova floresta com um único domínio
* Uma VM configurada como um servidor autônomo do SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Use a extensão do DSC do PowerShell para configurar um LCM (Gerenciador de Configurações Local) da máquina virtual existente e registrá-lo em um Servidor de Recepção do DSC da Conta de Automação do Azure.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Criar uma máquina virtual de uma imagem do usuário

[Criar uma máquina virtual de uma imagem de usuário personalizada](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-from-user-image). Esse modelo também implanta uma rede virtual (com o DNS), endereço IP público e uma interface de rede.

## <a name="basic-virtual-machine"></a>Máquina virtual básica

[Implantar uma VM Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm) que inclui uma rede virtual (com DNS), endereço IP público e uma interface de rede.

## <a name="cancel-a-running-template-deployment"></a>Cancelar uma implantação de modelo em execução

Para cancelar uma implantação de modelo em execução, use o [Stop-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment) cmdlet do PowerShell.

## <a name="next-steps"></a>Próximas etapas

* [Implantar modelos com o portal](azure-stack-deploy-template-portal.md)
* [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
