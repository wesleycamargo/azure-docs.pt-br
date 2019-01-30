---
title: Criar uma oferta no Azure Stack | Microsoft Docs
description: Como um administrador de nuvem, saiba como criar uma oferta para seus usuários no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 230415b84f06beac549693d49055d7cb998163d8
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251445"
---
# <a name="create-an-offer-in-azure-stack"></a>Criar uma oferta no Azure Stack

[Oferece](azure-stack-key-features.md) são grupos de um ou mais planos que os provedores apresentam aos usuários, o que esses usuários podem comprar ou inscrever-se. Este artigo descreve como criar uma oferta que inclui o [plano que você criou](azure-stack-create-plan.md). Esta oferta fornece a capacidade de configurar máquinas virtuais (VMs) de assinantes.

1. Entrar para o [portal do administrador do Azure Stack](https://adminportal.local.azurestack.external) e selecione **+ criar um recurso**, em seguida, **planos + locatário oferece**e, em seguida, **oferecem**.

   ![Criar uma oferta](media/azure-stack-create-offer/image01.png)
  
2. Sob **nova oferta**, insira um **nome de exibição** e um **nome do recurso**e, em **grupo de recursos**, selecione **criar novos** ou **usar existente**. O nome de exibição é o nome amigável para a oferta. Esse nome amigável é a única informação sobre a oferta que os usuários veem quando eles assinem uma oferta. Use um nome intuitivo que ajuda os usuários a entender o que vem com a oferta. Somente o administrador pode ver o nome do recurso. Esse é o nome que os administradores usam para trabalhar com a oferta como um recurso do Gerenciador de Recursos do Azure.

   ![Nova oferta](media/azure-stack-create-offer/image01a.png)
  
3. Selecione **planos Base** para abrir o **planejar**. Selecione os planos que você deseja incluir na oferta e, em seguida, escolha **selecionar**. Para criar, selecione a oferta **criar**.

   ![Selecionar plano](media/azure-stack-create-offer/image02.png)
  
4. Depois de criar a oferta, você pode alterar seu estado. Ofertas devem ser feitas **pública** para que os usuários tenham a exibição completa com a assinatura. Ofertas podem ser:

   - **Público**: Visível para os usuários.
   - **Privado**: Só é visível para os administradores de nuvem. Essa configuração é útil ao preparar o plano ou oferta, ou se o administrador de nuvem deseja [criar cada assinatura para usuários](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Descomissionado**: Fechado para novos assinantes. O administrador da nuvem pode encerrar ofertas para evitar futuras assinaturas, mas deixar os assinantes atuais inalterados.

   > [!TIP]  
   > As alterações para a oferta não serão visíveis imediatamente para o usuário. Para ver as alterações, os usuários podem ter sair e entrar novamente para o portal do usuário para ver a nova oferta.

   Na tela de visão geral da oferta, selecione **estado de acessibilidade**. Escolha o estado em que você deseja usar (por exemplo, **pública**) e, em seguida, selecione **salvar**.

     ![Escolha o estado](media/azure-stack-create-offer/change-stage-1807.png)

     Como alternativa, selecione **alterar estado** e, em seguida, escolha um estado.

    ![Selecione o estado de acessibilidade](media/azure-stack-create-offer/change-stage-select-1807.png)

   > [!NOTE]
   > Você também pode usar o PowerShell para criar cotas, planos e ofertas padrão. Para obter mais informações, consulte [módulo do Azure Stack PowerShell 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).

## <a name="next-steps"></a>Próximas etapas

- [Criar assinaturas](azure-stack-subscribe-plan-provision-vm.md)
- [Provisionar uma máquina virtual](azure-stack-provision-vm.md)
