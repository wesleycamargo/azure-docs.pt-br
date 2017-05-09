---
title: "Criar ou modificar laboratórios automaticamente usando modelos do Azure Resource Manager com o PowerShell | Microsoft Docs"
description: "Saiba como usar modelos de Azure Resource Manager com o PowerShell para criar ou modificar laboratórios automaticamente em um laboratório DevTest"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 8ee0301a48265b91f951c96c3339a3c3e3bdb1cb
ms.contentlocale: pt-br
ms.lasthandoff: 04/27/2017


---

# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Criar ou modificar laboratórios automaticamente usando modelos do Azure Resource Manager e o PowerShell

Laboratórios DevTest fornece muitos modelos de Azure Resource Manager e scripts do PowerShell que podem ajudá-lo rapidamente e criar automaticamente os novos laboratórios ou modificar laboratórios existentes e implantar esses recursos.

Este artigo ajuda a orientar você durante o processo de usar esses modelos e scripts para automatizar a criação, modificação e implantação de seus laboratórios. Este artigo também mostra onde você pode encontrar mais informações sobre como usar o PowerShell para realizar algumas tarefas comuns nos laboratórios de DevTest.

## <a name="step-1-gather-your-templates-and-scripts"></a>Etapa 1: Coletar seus modelos e scripts
Você pode encontrar [modelos do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) e [scripts do PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/Scripts) pré-realizados em nosso [repositório Github](https://github.com/Azure/azure-devtestlab) público. Use-os como estão, ou personalize-os para suas necessidades e armazene-os em seu próprio [repositório particular do Git](devtest-lab-add-artifact-repo.md). 

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Etapa 2: Modificar o modelo do Azure Resource Manager
[Criar ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md) mostra como usar modelos do Azure Resource Manager em laboratórios de DevTest para definir a infra-estrutura e a configuração de sua solução do Azure e implantar repetidamente várias VMs em um estado consistente.

Por exemplo, se você criou uma nova rede virtual e deseja aplicá-la a todos os seus laboratórios existentes, você pode rapidamente fazer isso usando um modelo do Azure Resource Manager.

## <a name="step-3-deploy-resources-with-powershell"></a>Etapa 3: Implantar recursos com o PowerShell
Depois que você personalizou os modelos e scripts, siga as etapas necessárias para [implantar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). O artigo fornece informações gerais sobre como usar o Azure PowerShell com modelos do Azure Resource Manager para implantar os recursos no Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Tarefas comuns que você pode executar nos laboratórios de DevTest usando o PowerShell
Há várias outras tarefas comuns que você pode automatizar usando o PowerShell. As seções da documentação a seguir descrevem as etapas necessárias para executar essas tarefas.

* [Criar uma imagem personalizada de um arquivo VHD usando o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Carregar arquivo VHD na conta de armazenamento do laboratório usando o PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Adicionar um usuário externo a um laboratório usando o PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Criar uma função personalizada do laboratório usando o PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Próximas etapas
* Saiba como criar um [repositório gito](devtest-lab-add-artifact-repo.md) em que você armazenará seus modelos personalizados ou scripts.
* Explore os [modelos do Azure Resource Manager na galeria de modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates).

