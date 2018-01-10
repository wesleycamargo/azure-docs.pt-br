---
title: "Definir configurações de imagem do Azure Marketplace no Azure DevTest Labs | Microsoft Docs"
description: Configure quais imagens do Azure Marketplace podem ser usadas ao criar uma VM no Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: v-craic
ms.openlocfilehash: a3b52bb8db0bcd46badb15d4bc65b85977faaadc
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Definir configurações de imagem do Azure Marketplace no Azure DevTest Labs
O DevTest Labs dá suporte à criação de VMs com base em imagens do Azure Marketplace, dependendo de como você tiver configurado o uso de imagens do Azure Marketplace em seu laboratório. Este artigo mostra como especificar quais imagens (caso haja alguma) do Azure Marketplace podem ser usadas durante a criação de VMs em um laboratório. Isso garante que sua equipe tenha acesso apenas às imagens do Marketplace de que precisam. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Selecionar quais imagens do Azure Marketplace são permitidas durante a criação de uma VM
1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado. 
4. Na folha do laboratório, selecione **Configuração e Políticas**.
5. Na folha **Configurações e políticas** do laboratório, em **Bases da Máquina Virtual**, selecione **Imagens do Marketplace**.
6. Especifique se você deseja que todas as imagens qualificadas do Azure Marketplace estejam disponíveis para uso como uma base de uma nova VM. Se você selecionar **Sim**, todas as imagens do Azure Marketplace que atenderem a todos os critérios a seguir serão permitidas no laboratório:
   
   * A imagem cria uma única VM **e**
   * A imagem usa o Azure Resource Manager para provisionar VMs **e**
   * A imagem não exige a compra de um plano de licenciamento extra
     
    Se você não quiser permitir qualquer imagem, ou se quiser especificar quais imagens poderão ser usadas, selecione **Não**.
     
     ![Opção para permitir que todas as imagens do Marketplace sejam usadas como imagens base para VMs](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Se você tiver selecionado **Não** na etapa anterior, a caixa de seleção **Imagens permitidas/Selecionar todas** será habilitada. 
   Você pode usar essa opção junto com a caixa de pesquisa para marcar ou desmarcar rapidamente todos os itens exibidos na lista.
   * Selecione as imagens do Azure Marketplace que você deseja permitir para a criação da VM individualmente, marcando a caixa de seleção correspondente de cada imagem.
   * Não selecione nada na lista se não quiser permitir que as imagens do Azure Marketplace sejam usadas no laboratório.
   
    ![Você pode especificar quais imagens do Marketplace podem ser usadas como imagens base para VMs](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
Depois de configurar como as imagens do Azure Marketplace são permitidas durante a criação de uma VM, a próxima etapa será [adicionar uma VM ao seu laboratório](devtest-lab-add-vm.md).

