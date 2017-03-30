---
title: "Adicionar uma VM a um laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como adicionar uma máquina virtual a um laboratório no Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 052785aa225086e55a40c061765770b9eebaa154
ms.lasthandoff: 03/21/2017


---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Adicionar uma VM a um laboratório no Azure DevTest Labs
Você adiciona uma VM a um laboratório por meio de uma *base* que é uma [imagem personalizada](devtest-lab-create-template.md), uma [fórmula](devtest-lab-manage-formulas.md) ou [imagem do Marketplace](devtest-lab-configure-marketplace-images.md). Este tutorial orienta você pelo Portal do Azure para adicionar uma máquina virtual a um laboratório no DevTest Labs.

> [!NOTE]
> [Adicionar uma VM declarável](devtest-lab-add-claimable-vm.md) mostra como tornar a VM declarável para que ela esteja disponível para uso por qualquer usuário no laboratório.
>
>

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Etapas para adicionar uma VM a um laboratório no Azure DevTest Labs
1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório no qual você deseja criar a VM.  
1. Na folha **Visão geral** do laboratório, selecione **+ Adicionar**.  

    ![Botão Adicionar VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Na folha **Escolher uma base** , selecione uma base para a VM.
1. Na folha **Máquina virtual**, insira um nome para a nova máquina virtual na caixa de texto **Nome da máquina virtual**.

    ![Folha VM de Laboratório](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Insira um **Nome de Usuário** que receberá privilégios de administrador na máquina virtual.  
1. Se você quiser usar uma senha armazenada em seu [repositório secreto](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), selecione **Usar um segredo salvo** e especifique um valor de chave que corresponda ao seu segredo (senha). Caso contrário, digite uma senha no campo de texto rotulado **Digite um valor**.
1. O **tipo de disco de máquina virtual** determina que tipo de disco de armazenamento é permitido para as máquinas virtuais no laboratório.
1. Selecione **Tamanho da máquina virtual** e selecione um dos itens predefinidos que especificam os núcleos de processador, o tamanho da RAM e o tamanho do disco rígido da VM a ser criada.
1. Selecione **Artefatos** e, na lista de artefatos, selecione e configure os artefatos que você deseja adicionar à imagem base.
    **Observação:** se você for iniciante em Laboratórios de Desenvolvimento/Teste ou na configuração de artefatos, veja a seção [Adicionar um artefato existente a uma VM](./devtest-lab-add-vm-with-artifacts.md#add-an-existing-artifact-to-a-vm) e volte aqui quando terminar.
1. Selecione **Configurações avançadas** para configurar as opções de expiração e as opções de rede da VM.
1. Se quiser exibir ou copiar o modelo do Azure Resource Manager, veja a seção [Salvar modelo do Azure Resource Manager](./devtest-lab-add-vm-with-artifacts.md#save-azure-resource-manager-template) e retorne para cá quando terminar.
1. Selecione **Criar** para adicionar a VM especificada ao laboratório.
1. A folha do laboratório exibe o status da criação da VM; primeiro como **Criando** e como **Executando** após a inicialização da VM.

## <a name="next-steps"></a>Próximas etapas
* Após a criação da VM, você poderá se conectar à VM selecionando **Conectar** na folha da VM.
* Explorar a [galeria de modelos de Início Rápido do Azure Resource Manager do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)

