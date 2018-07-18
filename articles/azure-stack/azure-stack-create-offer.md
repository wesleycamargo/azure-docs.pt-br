---
title: Criar uma oferta na pilha do Azure | Microsoft Docs
description: Como um administrador de nuvem, saiba como criar uma oferta para os usuários na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2018
ms.author: brenduns
ms.openlocfilehash: 6a59d0c8144492ef907e5c395f05e4e8a16678a5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="create-an-offer-in-azure-stack"></a>Criar uma oferta no Azure Stack

[Oferece](azure-stack-key-features.md) são grupos de um ou mais planos provedores apresentam aos usuários para comprar ou assinar. Este documento mostra como criar uma oferta que inclui o [plano que você criou](azure-stack-create-plan.md) na última etapa. Esta oferta fornece a capacidade de provisionar máquinas virtuais de assinantes.

1. Entrar no portal do administrador do Azure pilha (https://adminportal.local.azurestack.external) > clique **novo** > **locatário oferece + planos** > **oferecem**.

   ![](media/azure-stack-create-offer/image01.png)
2. No **oferecem nova** painel, preencha **nome de exibição** e **nome do recurso**e, em seguida, selecione um novo ou existente **grupo de recursos**. O nome de exibição é o nome amigável da oferta. Esse nome amigável é a única informação sobre a oferta que os usuários veem ao inscrever-se. Portanto, certifique-se de usar um nome intuitivo que ajuda o usuário a entender o que vem com a oferta. Somente o administrador pode ver o Nome do Recurso. Esse é o nome que os administradores usam para trabalhar com a oferta como um recurso do Gerenciador de Recursos do Azure.

   ![](media/azure-stack-create-offer/image01a.png)
3. Clique em **Base planos** para abrir o **planejar** painel, selecione os planos que você deseja incluir na oferta e, em seguida, clique em **selecione**. Clique em **Criar** para criar a oferta.

   ![](media/azure-stack-create-offer/image02.png)
4. Depois de criar a oferta, você pode alterar seu estado. Ofertas devem ser feitas *pública* para os usuários a obter o modo de exibição completo quando eles se inscrever. Ofertas podem ser:
   - **Público**: visível para os usuários.
   - **Privada**: visível somente para administradores de nuvem. Útil ao projeto o plano ou a oferta, ou se o administrador de nuvem deseja [criar cada assinatura para usuários](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Desativados**: fechados para novos assinantes. O administrador de nuvem pode usar encerrado para evitar futuras assinaturas, mas deixe os assinantes atuais inalterados.

   > [!TIP]  
   > As alterações para a oferta não ficam imediatamente visíveis para o usuário. Para ver as alterações, os usuários podem ter logoff e logon novamente para o portal do usuário para ver a nova oferta. 

   Para alterar o estado da oferta: 

   - **Versão mais recente e 1803**:  
     Na folha de visão geral da oferta, clique em **estado acessibilidade**, selecione o estado que você deseja usar, como *pública*e, em seguida, clique em **salvar**. 
 
     ![Selecione o estado de acessibilidade](media/azure-stack-create-offer/change-state.png) 

     Como alternativa, depois de acessar uma oferta pode ir para **oferecem configurações**e, em seguida, selecione **estado acessibilidade** para alterar o estado. 

   - **Antes da versão 1803**:  
     Clique em **todos os recursos**, procure sua nova oferta, clique em nova oferta, clique em **alterar estado**e, em seguida, clique em **público**.

  
   > [!NOTE] 
   > Você também pode usar o PowerShell para criar cotas, planos e ofertas de padrão. Para obter mais informações, consulte [Leiame do administrador de serviço do Azure pilha](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).
   >


### <a name="next-steps"></a>Próximas etapas
[Criar assinaturas](azure-stack-subscribe-plan-provision-vm.md)      
[Provisionar uma máquina virtual](azure-stack-provision-vm.md)
