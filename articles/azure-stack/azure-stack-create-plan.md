---
title: Criar um plano no Azure Stack | Microsoft Docs
description: Como um administrador de nuvem, crie um plano que permite que os assinantes provisionar máquinas de virtuais.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: a2ac138228b7b54f486acb0f42975748136a8da7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759462"
---
# <a name="create-a-plan-in-azure-stack"></a>Criar um plano no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

[Planos de](azure-stack-key-features.md) são agrupamentos de um ou mais serviços e suas cotas. Como um provedor, você pode criar planos para oferecer a seus usuários. Por sua vez, os seus usuários assinam suas ofertas para usar os planos, serviços e cotas que elas incluem. Este exemplo mostra como criar um plano que inclui computação, rede e provedores de recursos de armazenamento. Esse plano oferece a capacidade de provisionar máquinas virtuais de assinantes.

## <a name="create-a-plan-1902-and-later"></a>Criar um plano (1902 e posterior)

1. Entrar para o [portal do administrador do Azure Stack](https://adminportal.local.azurestack.external).

2. Para criar um plano e oferta que os usuários podem assinar, selecione **+ criar um recurso**, em seguida, **oferece + planos**, em seguida, **plano**.
  
   ![Selecione um plano](media/azure-stack-create-plan/select-plan.png)

3. Uma interface do usuário com guias aparece, permitindo que você especifique o nome do plano, adicionar serviços e definir cotas para cada um dos serviços selecionados. Mais importante, você pode examinar os detalhes da oferta que você cria, antes de decidir criá-lo.

   Sob o **Noções básicas** guia da **novo plano** janela, insira um **nome de exibição** e um **nome do recurso**. O nome de exibição é o nome amigável do plano operadores podem ver. Observe que, no portal do administrador, detalhes do plano são visíveis somente para operadores.

   ![Especifique os detalhes](media/azure-stack-create-plan/plan-name.png)

4. Criar um novo **grupo de recursos**, ou selecione um existente, como um contêiner para o plano.

   ![Especifique o grupo de recursos](media/azure-stack-create-plan/resource-group.png)

5. Selecione o **Services** guia e, em seguida, selecione a caixa de seleção **Microsoft. Compute**, **Microsoft. Network**, e **Microsoft. Storage** .
  
   ![Selecionar Serviços](media/azure-stack-create-plan/services.png)

6. Selecione o **cotas** guia. Lado **Microsoft. Storage**, escolha a cota de padrão na lista suspensa ou selecione **criar novo** para criar uma cota personalizada.
  
   ![Cotas](media/azure-stack-create-plan/quotas.png)

7. Se você estiver criando uma nova cota, insira um **nome** para a cota e, em seguida, especifique os valores de cota. Selecione **Okey** para criar a cota.

   ![Nova cota](media/azure-stack-create-plan/new-quota.png)

8. Repita as etapas 6 e 7 para criar e atribuir as cotas para **Microsoft. Network** e **Microsoft. Compute**. Quando todos os três serviços têm cotas atribuídas, eles vai parecer com o exemplo a seguir.

   ![Atribuições de cota completa](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Selecione **revisar + criar** para revisar o plano. Examine todos os valores e cotas para garantir que eles estão corretos. Observe as setas de expansão à esquerda de cada par de cota do serviço. Um novo recurso permite que você expanda as cotas em planos selecionados, um de cada vez, para exibir os detalhes de cada cota em um plano e volte para fazer as edições necessárias.

   ![Criar o plano](media/azure-stack-create-plan/create.png)

10. Quando você estiver pronto, selecione **criar** para criar o plano.

11. Para ver o novo plano, selecione **planos**, em seguida, procure o plano e selecione seu nome. Se sua lista de recursos for longa, use **pesquisa** para localizar seu plano por nome.

## <a name="create-a-plan-1901-and-earlier"></a>Criar um plano (1901 e anterior)

1. Entrar para o [portal do administrador do Azure Stack](https://adminportal.local.azurestack.external).

2. Para criar um plano e oferta que os usuários podem assinar, selecione **+ criar um recurso**, em seguida, **oferece + planos**, em seguida, **plano**.
  
   ![Selecione um plano](media/azure-stack-create-plan/select-plan1901.png)

3. Sob **novo plano**, insira um **nome de exibição** e um **nome do recurso**. O nome de exibição é o nome amigável do plano do que os usuários podem ver. Somente o administrador pode ver o nome do recurso, quais os administradores usam para trabalhar com o plano como um recurso do Azure Resource Manager.

   ![Especifique os detalhes](media/azure-stack-create-plan/plan-name1901.png)

4. Criar um novo **grupo de recursos**, ou selecione um existente, como um contêiner para o plano.

   ![Especifique o grupo de recursos](media/azure-stack-create-plan/resource-group1901.png)

5. Selecione **Services** e, em seguida, selecione a caixa de seleção **Microsoft. Compute**, **Microsoft. Network**, e **Microsoft. Storage**. Em seguida, escolha **selecionar** para salvar a configuração. Caixas de seleção aparecem quando o mouse passa sobre cada opção.
  
   ![Selecionar Serviços](media/azure-stack-create-plan/services1901.png)

6. Selecione **cotas**, **Microsoft. Storage (local)** e, em seguida, escolha a cota padrão ou selecione **criar nova cota** para criar uma cota personalizada.
  
   ![Cotas](media/azure-stack-create-plan/quotas1901.png)

7. Se você estiver criando uma nova cota, insira um **nome** para a cota > especifique os valores de cota > selecionar **Okey**. O **criar cota** caixa de diálogo é fechada.

   ![Nova cota](media/azure-stack-create-plan/new-quota1901.png)

   Você, em seguida, selecione a nova cota que você criou. Selecionando a cota atribui a ele e fecha a caixa de diálogo de seleção.
  
   ![Atribuir a cota](media/azure-stack-create-plan/assign-quota1901.png)

8. Repita as etapas 6 e 7 para criar e atribuir as cotas para **Network (local)** e **Microsoft. Compute (local)**. Quando todos os três serviços têm cotas atribuídas, eles vai parecer com o exemplo a seguir.

   ![Atribuições de cota completa](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. Sob **cotas**, escolha **Okey**e, em **novo plano**, escolha **criar** para criar o plano.

    ![Criar o plano](media/azure-stack-create-plan/create1901.png)

10. Para ver seu novo plano, selecione **todos os recursos**, em seguida, procure o plano e selecione seu nome. Se sua lista de recursos for longa, use **pesquisa** para localizar seu plano por nome.

    ![Examinar o plano](media/azure-stack-create-plan/plan-overview1901.png)

## <a name="next-steps"></a>Próximas etapas

* [Criar uma oferta](azure-stack-create-offer.md)
