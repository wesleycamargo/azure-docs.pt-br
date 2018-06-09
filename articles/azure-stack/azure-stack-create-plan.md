---
title: Criar um plano na pilha do Azure | Microsoft Docs
description: Como um administrador de nuvem, crie um plano que permite que os assinantes provisionar, máquinas virtuais.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 1fa01d23108ce92fbd7c854442c0474b19395d25
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248696"
---
# <a name="create-a-plan-in-azure-stack"></a>Criar um plano no Azure Stack

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Os [planos](azure-stack-key-features.md) são agrupamentos de um ou mais serviços. Como um provedor, você pode criar planos para oferecer a seus usuários. Por sua vez, os usuários assinar suas ofertas para usar os planos e serviços que elas incluem. Este exemplo mostra como criar um plano que inclui a computação, rede e provedores de recursos de armazenamento. Esse plano oferece a capacidade para provisionar máquinas virtuais de assinantes.

1. Entrar no portal do administrador do Azure pilha (https://adminportal.local.azurestack.external).

2. Para criar um plano e a oferta que os usuários podem assinar, selecione **novo** > **oferece + planos** > **plano**.
  
   ![Selecione um plano](media/azure-stack-create-plan/select-plan.png)

3. Em **novo plano**, insira um **nome de exibição** e um **nome do recurso**. O nome de exibição é o nome amigável do plano do que os usuários podem ver. Somente o administrador pode ver o nome do recurso, quais os administradores usam para trabalhar com o plano como um recurso do Gerenciador de recursos do Azure.

   ![Especifique os detalhes](media/azure-stack-create-plan/plan-name.png)

4. Criar um novo **grupo de recursos**, ou selecione um existente, como um contêiner para o plano.

   ![Especifique o grupo de recursos](media/azure-stack-create-plan/resource-group.png)

5. Selecione **serviços** e, em seguida, selecione a caixa de seleção **Microsoft. Compute**, **Network**, e **Microsoft**. Em seguida, escolha **selecione** para salvar a configuração. Caixas de seleção aparecem quando o mouse passa sobre cada opção.
  
   ![Selecionar serviços](media/azure-stack-create-plan/services.png)

6. Selecione **cotas**, **(local) do Microsoft**e, em seguida, escolha a cota padrão ou selecione **criar nova cota** para criar uma cota personalizada.
  
   ![Cotas](media/azure-stack-create-plan/quotas.png)

7. Se você estiver criando uma nova cota, insira um **nome** para a cota > especifique os valores de cota > selecione **Okey**. O **criar cota** caixa de diálogo é fechada.

   ![Nova cota](media/azure-stack-create-plan/new-quota.png)

   Você, em seguida, selecione nova cota que você criou. Selecionando a cota atribui a ele e fecha a caixa de diálogo de seleção.
  
   ![Atribuir a cota](media/azure-stack-create-plan/assign-quota.png)

8. Repita as etapas 6 e 7 para criar e atribuir cotas para **Network (local)** e **Microsoft. Compute (local)**. Quando todos os três serviços tem cotas atribuídas, eles terão uma aparência semelhante ao exemplo seguinte.

   ![Atribuições de cota concluída](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Em **cotas**, escolha **Okey**e, em seguida, em **novo plano**, escolha **criar** para criar o plano.

    ![Criar o plano](media/azure-stack-create-plan/create.png)

10. Para ver o novo plano, selecione **todos os recursos**, em seguida, procure o plano e selecione seu nome. Se a lista de recursos for longa, use **pesquisa** para localizar seu plano por nome.

   ![Examinar o plano](media/azure-stack-create-plan/plan-overview.png)

## <a name="next-steps"></a>Próximas etapas

[Criar uma oferta](azure-stack-create-offer.md)
