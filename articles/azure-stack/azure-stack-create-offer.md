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
ms.date: 06/07/2018
ms.author: brenduns
ms.openlocfilehash: e5b96a9464bf4d0e3b69d2f635da32c6648ce793
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247510"
---
# <a name="create-an-offer-in-azure-stack"></a>Criar uma oferta no Azure Stack

[Oferece](azure-stack-key-features.md) são grupos de um ou mais planos provedores apresentam aos usuários para comprar ou assinar. Este documento mostra como criar uma oferta que inclui o [plano que você criou](azure-stack-create-plan.md). Esta oferta fornece a capacidade de configurar máquinas virtuais de assinantes.

1. Entrar no portal do administrador do Azure pilha (https://adminportal.local.azurestack.external) e selecione **novo** > **locatário oferece + planos** > **oferecem**.

   ![Criar uma oferta](media/azure-stack-create-offer/image01.png)
  
2. Em **oferecem nova**, insira um **nome de exibição** e um **nome do recurso**e, em seguida, em **grupo de recursos**, selecione **criar novo** ou **usar existente**. O nome de exibição é o nome amigável da oferta. Esse nome amigável é a única informação sobre a oferta que os usuários veem quando assinar uma oferta. Use um nome intuitivo que ajuda os usuários a entender o que vem com a oferta. Somente o administrador pode ver o Nome do Recurso. Esse é o nome que os administradores usam para trabalhar com a oferta como um recurso do Gerenciador de Recursos do Azure.

   ![Nova oferta](media/azure-stack-create-offer/image01a.png)
  
3. Selecione **Base planos** para abrir o **planejar**. Selecione os planos que você deseja incluir na oferta e, em seguida, escolha **selecione**. Para criar a seleção de oferta **criar**.

   ![Selecionar plano](media/azure-stack-create-offer/image02.png)
  
4. Depois de criar a oferta, você pode alterar seu estado. Ofertas devem ser feitas *pública* para os usuários a obter o modo de exibição completo quando eles se inscrever. Ofertas podem ser:

   - **Público**: visível para os usuários.
   - **Privada**: visível somente para administradores de nuvem. Essa configuração é útil ao projeto o plano ou a oferta, ou se o administrador de nuvem deseja [criar cada assinatura para usuários](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Desativados**: fechados para novos assinantes. O administrador de nuvem pode usar encerrado para evitar futuras assinaturas, mas deixe os assinantes atuais afetados.

   > [!TIP]  
   > As alterações para a oferta não são imediatamente visíveis para o usuário. Para ver as alterações, os usuários talvez precise fazer logoff e logon novamente para o portal do usuário para ver a nova oferta.

   Para alterar o estado da oferta:

   - **Versão mais recente e 1803**:  
     Na visão geral da oferta, selecione **estado acessibilidade**. Escolha o estado que você deseja usar (por exemplo, *pública*) e, em seguida, selecione **salvar**.
 
     ![Selecione o estado de acessibilidade](media/azure-stack-create-offer/change-state.png)

     Como alternativa, depois de acessar uma oferta pode ir para **oferecem configurações**. Selecione **estado acessibilidade** para alterar o estado.

   - **Antes da versão 1803**:  
     Selecione **todos os recursos**, para sua nova oferta de pesquisa e, em seguida, selecione a nova oferta. Selecione **alterar estado**e, em seguida, selecione **público**.

   > [!NOTE]
   > Você também pode usar o PowerShell para criar cotas, planos e ofertas de padrão. Para obter mais informações, consulte [Leiame do administrador de serviço do Azure pilha](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).

## <a name="next-steps"></a>Próximas etapas

- [Criar assinaturas](azure-stack-subscribe-plan-provision-vm.md)
- [Provisionar uma máquina virtual](azure-stack-provision-vm.md)
