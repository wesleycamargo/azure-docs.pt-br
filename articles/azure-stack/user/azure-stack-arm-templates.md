---
title: Usar modelos do Gerenciador de recursos do Azure na pilha do Azure | Microsoft Docs
description: Saiba como usar modelos do Gerenciador de recursos do Azure na pilha do Azure para provisionar recursos.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 9c4d538f77ae056163fd17aa547162a4ad3eff63
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301671"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Usar modelos do Gerenciador de Recursos do Azure no Azure Stack

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode usar modelos do Gerenciador de recursos do Azure para implantar e provisionar todos os recursos para seu aplicativo em uma única operação coordenado. Você também pode reutilizar modelos para fazer alterações em recursos em um grupo de recursos.

Esses modelos podem ser implantados com o portal do Microsoft Azure Stack, o PowerShell, a linha de comando e o Visual Studio.

Os modelos de início rápido a seguir estão disponíveis em [GitHub](http://aka.ms/azurestackgithub).

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Implantar o SharePoint Server (implantação de alta disponibilidade)

Use a extensão de DSC do PowerShell para criar um farm do SharePoint Server 2013 que inclui os seguintes recursos:

* Uma rede virtual
* Três contas de armazenamento
* Dois balanceadores de carga externos
* Uma máquina virtual (VM) configurada como um controlador de domínio para uma nova floresta com um único domínio
* Uma VM configurada como um servidor autônomo do SQL Server 2014
* Uma VM configurada como um farm do SharePoint Server 2013 de um computador

## <a name="deploy-ad-non-high-availability-deployment"></a>Implantar o AD (não-alta-disponibilidade-implantação)

Use a extensão de DSC do PowerShell para criar um servidor de controlador de domínio do AD que inclui os seguintes recursos:

* Uma rede virtual
* Uma conta de armazenamento
* Um balanceador externo de carga
* Uma máquina virtual (VM) configurada como um controlador de domínio para uma nova floresta com um único domínio

## <a name="deploy-adsql-non-high-availability-deployment"></a>Implantar o AD/SQL (não-alta-disponibilidade-implantação)

Use a extensão de DSC do PowerShell para criar um servidor autônomo do SQL Server 2014 que inclui os seguintes recursos:

* Uma rede virtual
* Duas contas de armazenamento
* Um balanceador externo de carga
* Uma máquina virtual (VM) configurada como um controlador de domínio para uma nova floresta com um único domínio
* Uma VM configurada como um servidor autônomo do SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Use a extensão do DSC do PowerShell para configurar um LCM (Gerenciador de Configurações Local) da máquina virtual existente e registrá-lo em um Servidor de Recepção do DSC da Conta de Automação do Azure.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Criar uma máquina virtual de uma imagem do usuário

Crie uma máquina virtual de uma imagem personalizada do usuário. Esse modelo também implanta uma rede virtual (com o DNS), endereço IP público e uma interface de rede.

## <a name="basic-virtual-machine"></a>Básica da máquina virtual

Implante uma VM do Windows que inclui uma rede virtual (com o DNS), o endereço IP público e uma interface de rede.

## <a name="cancel-a-running-template-deployment"></a>Cancelar uma implantação de modelo em execução

Para cancelar uma implantação de modelo em execução, use o `Stop-AzureRmResourceGroupDeployment` cmdlet do PowerShell.

## <a name="next-steps"></a>Próximas etapas

* [Implantar modelos com o portal](azure-stack-deploy-template-portal.md)
* [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
