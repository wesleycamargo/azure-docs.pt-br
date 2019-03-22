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
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 59c69477e120facec1fbf132ce7017ca21aa8748
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092609"
---
# <a name="create-an-offer-in-azure-stack"></a>Criar uma oferta no Azure Stack

[Oferece](azure-stack-key-features.md) são grupos de um ou mais planos que os provedores apresentam aos usuários, o que esses usuários podem comprar ou inscrever-se. Este artigo descreve como criar uma oferta que inclui o [plano que você criou](azure-stack-create-plan.md). Esta oferta fornece a capacidade de configurar máquinas virtuais (VMs) de assinantes.

## <a name="create-an-offer-1902-and-later"></a>Criar uma oferta (1902 e posterior)

1. Entrar para o [portal do administrador do Azure Stack](https://adminportal.local.azurestack.external) e selecione **+ criar um recurso**, em seguida, **oferece + planos**e, em seguida, **oferecem**.

   ![Criar uma oferta](media/azure-stack-create-offer/offers.png)

2. Uma interface do usuário com guias aparece, permitindo que você definir o nome da oferta e adicionar existente ou criar novos planos de base e planos de complemento. Mais importante, você pode examinar os detalhes da oferta que você cria, antes de decidir criá-lo.

   No **Noções básicas** guia, em **nova oferta**, insira um **nome de exibição** e um **nome do recurso**e, em **recursos Grupo**, selecione **criar novo** ou **usar existente**. O nome de exibição é o nome amigável para a oferta. Esse nome amigável é a única informação sobre a oferta que os usuários veem quando eles assinem uma oferta no portal do usuário. Use um nome intuitivo que ajuda os usuários a entender o que vem com a oferta. Somente o administrador pode ver o nome do recurso. Esse é o nome que os administradores usam para trabalhar com a oferta como um recurso do Gerenciador de Recursos do Azure. Nessa guia, você também pode optar por tornar esta oferta público ou mantenha-o privado, o que é o padrão. Você pode [alterar o estado público ou privado da oferta](#change-the-state-of-an-offer) mais tarde, bem.

   ![Nova oferta](media/azure-stack-create-offer/new-offer.png)
  
3. Selecione o **planos Base** guia. Selecione o (s) que você deseja incluir na oferta.

   ![Selecionar plano](media/azure-stack-create-offer/select-plan.png)

4. Neste ponto, você pode criar um plano de complemento para modificar o plano de base, mas isso é opcional. Vamos criar um plano de complemento no próximo artigo [planos de complemento do Azure Stack](create-add-on-plan.md).

5. Selecione o **revisar + criar** guia. Examine o resumo da oferta para garantir que todos os valores estão corretos. A interface permite que você expanda as cotas nos escolhido planos, um de cada vez, para exibir os detalhes de cada cota em um plano e voltar para fazer as edições necessárias.

6. Selecione **criar** para criar a oferta.

   ![Examinar e criar](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>Alterar o estado de uma oferta

Depois de criar a oferta, você pode alterar seu estado. Ofertas devem ser feitas **pública** para que os usuários tenham a exibição completa com a assinatura. Ofertas podem ser:

- **Público**: Visível para os usuários.
- **Privado**: Só é visível para os administradores de nuvem. Essa configuração é útil ao preparar o plano ou oferta, ou se o administrador de nuvem deseja [criar cada assinatura para usuários](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
- **Descomissionado**: Fechado para novos assinantes. O administrador da nuvem pode encerrar ofertas para evitar futuras assinaturas, mas deixar os assinantes atuais inalterados.

  > [!TIP]  
  > As alterações para a oferta não serão visíveis imediatamente para o usuário. Para ver as alterações, os usuários podem ter sair e entrar novamente para o portal do usuário para ver a nova oferta.

Há duas maneiras de alterar o estado de uma oferta:

1. Na **todos os recursos**, selecione o nome da oferta. Sobre o **visão geral** tela para a oferta, selecione **alterar o estado**. Escolha o estado em que você deseja usar (por exemplo, **pública**).

   ![Escolha o estado](media/azure-stack-create-offer/change-state.png)

2. Na **todos os recursos**, selecione o nome da oferta. Em seguida, selecione **configurações da oferta**. Escolha o estado em que você deseja usar (por exemplo, **pública**), em seguida, selecione **salvar**.

   ![Selecione o estado de acessibilidade](media/azure-stack-create-offer/offer-settings.png)

## <a name="create-an-offer-1901-and-earlier"></a>Criar uma oferta (1901 e anterior)

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
