---
title: Diagnosticar falhas de artefato na VM do Azure DevTest Labs | Microsoft Docs
description: Saiba como solucionar problemas de falhas de artefato no DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: e4f2946d0ba0756f36622aded0e8594acabb9527
ms.contentlocale: pt-br
ms.lasthandoff: 08/24/2017

---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnosticar falhas de artefato no laboratório 
Depois de criar um artefato, você pode verificar se ele foi bem-sucedido ou falhou. Os logs de artefato no DevTest Labs fornecem informações que você pode usar para diagnosticar uma falha de artefato. Há duas maneiras diferentes de exibir as informações do log de artefatos para uma VM do Windows.

> [!NOTE]
> Para garantir que falhas sejam corretamente identificadas e explicadas, é importante que o artefato seja corretamente estruturado. Para saber mais sobre como construir corretamente um artefato, confira [Criar artefatos personalizados](devtest-lab-artifact-author.md). E para ver um exemplo de um artefato corretamente estruturado, verifique o artefato [Testar tipos de parâmetro](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

## <a name="troubleshoot-artifact-failures-using-the-azure-portal"></a>Solucionar falhas de artefato usando o Portal do Azure
Para usar o Portal do Azure a fim diagnosticar falhas durante a criação do artefato, siga estas etapas:

1. Na lista de recursos, selecione o laboratório.

2. Escolha a VM do Windows que inclui o artefato que deseja investigar.

3. No painel esquerdo, em **GERAL**, escolha **Artefatos**. Uma lista de artefatos associados a essa VM aparece, indicando o nome do artefato e seu status.

   ![Exemplo de repositório git de artefatos](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Escolha um artefato que mostra um status **de falha**. O artefato é aberto e mostra uma mensagem de extensão que inclui detalhes sobre a falha do artefato.

   ![Exemplo de repositório git de artefatos](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-vm"></a>Solucionar falhas de artefato dentro da VM
Para exibir os logs de artefato dentro da máquina virtual, siga estas etapas:

1. Faça logon na VM que contém o artefato que deseja diagnosticar.

2. Navegue até C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.9\Status em que "1.9 é o número de versão do CSE.

   ![Exemplo de repositório git de artefatos](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Abra o arquivo **status** para exibir informações que ajudem a diagnosticar falhas de artefato para essa VM.




[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas
* [Join a VM to existing AD Domain using a resource manager template in Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (Ingressar uma VM em um AD Domain existente usando um modelo do Resource Manager no Azure DevTest Labs)

## <a name="next-steps"></a>Próximas etapas
* Saiba como [adicionar um repositório Git a um laboratório](devtest-lab-add-artifact-repo.md).


