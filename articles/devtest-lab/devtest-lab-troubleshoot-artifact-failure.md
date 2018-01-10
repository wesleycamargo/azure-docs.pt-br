---
title: "Diagnosticar falhas de artefato na máquina virtual do Azure DevTest Labs | Microsoft Docs"
description: Saiba como solucionar problemas de falhas de artefato no Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: v-craic
ms.openlocfilehash: 6c03ce8f91ec688e32e379f1284767db9a45920c
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnosticar falhas de artefato no laboratório 
Depois de criar um artefato, você pode verificar se ele foi bem-sucedido ou falhou. Os logs de artefato no Azure DevTest Labs fornecem informações que você pode usar para diagnosticar uma falha de artefato. Você tem duas opções para exibir as informações de logs do artefato para uma VM do Windows:

* No portal do Azure
* Na VM

> [!NOTE]
> Para garantir que falhas sejam corretamente identificadas e explicadas, é importante que o artefato tenha a estrutura correta. Para saber mais sobre como construir corretamente um artefato, confira [Criar artefatos personalizados](devtest-lab-artifact-author.md). Para ver um exemplo de um artefato corretamente estruturado, verifique o artefato [Testar tipos de parâmetro](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Solucionar falhas de artefato usando o Portal do Azure

1. No Portal do Azure, na lista de recursos, selecione seu laboratório.
2. Escolha a VM do Windows que inclui o artefato que você deseja investigar.
3. No painel esquerdo, em **GERAL**, selecione **Artefatos**. É exibida uma lista de artefatos associados a essa VM. O nome e o status do artefato são indicados.

   ![Status do artefato](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Escolha um artefato que mostra um status de **Falha**. O artefato é aberto. Uma mensagem de extensão que inclui detalhes sobre a falha do artefato é mostrada.

   ![Mensagem de erro do artefato](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Solucionar falhas de artefato de dentro da máquina virtual

1. Entre na VM que contém o artefato que você deseja diagnosticar.
2. Navegue até C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, em que *1.9* é o número de versão da Extensão de Script Personalizado do Azure.

   ![O arquivo de status](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Abra o arquivo de **status**.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas
* [Ingresse uma VM em um domínio do Active Directory existente usando um modelo do Resource Manager no DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Próximas etapas
* Saiba como [adicionar um repositório Git a um laboratório](devtest-lab-add-artifact-repo.md).

