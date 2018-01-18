---
title: "Exibir e usar o modelo do Azure Resource Manager de uma máquina virtual | Microsoft Docs"
description: "Saiba como usar o modelo do Azure Resource Manager de uma máquina virtual para criar outras VMs"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: v-craic
ms.openlocfilehash: 97822d5fb11c5c106c67aaaab0b8972e1ec8deee
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Criar máquinas virtuais usando um modelo do Azure Resource Manager 

Quando você estiver criando uma máquina virtual (VM) em DevTest Labs por meio do [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), você pode exibir o modelo do Azure Resource Manager antes de salvar a VM. O modelo, em seguida, pode ser usado como base para criar mais VMs do laboratório com as mesmas configurações.

Este artigo descreve várias VMs vs. modelos do Resource Manager de VM única e mostra como exibir e salvar um modelo ao criar uma VM.

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Modelos do Resource Manager de várias VMs vs. de uma única VM
Há duas maneiras de criar VMs em DevTest Labs usando um modelo do Resource Manager: provisionar o recurso Microsoft.DevTestLab/labs/virtualmachines ou provisionar o recurso Microsoft.Commpute/virtualmachines. Cada um é usado em cenários diferentes e exige permissões diferentes.

- Modelos do Resource Manager que usam um tipo de recurso Microsoft.DevTestLab/labs/virtualmachines (conforme declarado na propriedade "recurso" no modelo) podem provisionar VMs de laboratório individuais. Nesse caso, cada VM aparece como um item individual na lista de máquinas virtuais do DevTest Labs:

   ![Lista de VMs com itens individuais na lista de máquinas virtuais do DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Este tipo de modelo do Resource Manager pode ser provisionado com o comando **New-AzureRmResourceGroupDeployment** do Azure PowerShell ou por meio do comando **az group deployment create** da CLI do Azure. Isso requer permissões de administrador, de modo que os usuários a quem uma função de usuário do DevTest Labs é atribuída não são capazes de realizar a implantação. 

- Modelos do Resource Manager que usam um tipo de recurso Microsoft.Compute/virtualmachines podem provisionar várias VMs como um único ambiente na lista de máquinas virtuais do DevTest Labs:

   ![Lista de VMs com itens individuais na lista de máquinas virtuais do DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   VMs no mesmo ambiente podem ser administradas conjuntamente e compartilham o mesmo ciclo de vida. Os usuários atribuídos a uma função de usuário do DevTest Labs podem criar ambientes usando esses modelos, contanto que o administrador tenha configurado o laboratório dessa forma.

O restante deste artigo aborda modelos do Resource Manager que usam Mirosoft.DevTestLab/labs/virtualmachines. Eles são usados por administradores de laboratório para automatizar a criação de VMs de laboratório (por exemplo, VMs reivindicáveis) ou a geração de imagem dourada (por exemplo, a fábrica de imagem).

O artigo [Práticas recomendadas para criação de modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) oferece muitas diretrizes e sugestões para lhe ajudar a criar modelos do Azure Resource Manager que são confiáveis e fáceis de usar.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Exibir e salvar o modelo do Azure Resource Manager de uma máquina virtual
1. Siga as etapas em [Criar sua primeira VM em um laboratório](devtest-lab-create-first-vm.md) para começar a criar uma máquina virtual.
1. Insira as informações necessárias para sua máquina virtual e adicione qualquer artefato que você desejar nessa VM.
1. Na parte inferior da janela de configurações Configurar, escolha **Exibir modelo ARM**.

   ![Botão Exibir modelo ARM](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Copie e salve o modelo do Resource Manager para usar posteriormente para criar outra máquina virtual.

   ![Modelo do Resource Manager para salvar para uso posterior](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Depois de salvar o modelo do Resource Manager, você deve atualizar a seção de parâmetros do modelo antes de você poder usá-lo. Você pode criar um parameter.json que personaliza apenas os parâmetros fora do modelo real do Resource Manager. 

![Personalizar parâmetros usando um arquivo JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

O modelo do Resource Manager já está pronto para usar para [criar uma VM](devtest-lab-create-environment-from-arm.md).

### <a name="next-steps"></a>Próximas etapas
* Aprenda como [Criar ambientes de várias VMs com modelos do Resource Manager](devtest-lab-create-environment-from-arm.md).
* [Implantar um modelo do Resource Manager para criar uma VM](devtest-lab-create-environment-from-arm.md#deploy-a-resource-manager-template-to-create-a-vm)
* Explore mais modelos do Resource Manager de início rápido para a automação do DevTest Labs do [repositório público do GitHub do DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
