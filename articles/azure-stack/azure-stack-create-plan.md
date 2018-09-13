---
title: Criar um plano no Azure Stack | Microsoft Docs
description: Como um administrador de nuvem, crie um plano que permite que os assinantes provisionar máquinas de virtuais.
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
ms.date: 09/12/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 0795c3d833133e2881f3c1ba0ae56584a229a31f
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44721238"
---
# <a name="create-a-plan-in-azure-stack"></a>Criar um plano no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Os [planos](azure-stack-key-features.md) são agrupamentos de um ou mais serviços. Como um provedor, você pode criar planos para oferecer a seus usuários. Por sua vez, os seus usuários assinam suas ofertas para usar os planos e serviços incluídos nelas. Este exemplo mostra como criar um plano que inclui computação, rede e provedores de recursos de armazenamento. Esse plano oferece a capacidade de provisionar máquinas virtuais de assinantes.

1. Entrar no portal do administrador do Azure Stack (https://adminportal.local.azurestack.external).

2. Para criar um plano e oferta que os usuários podem assinar, selecione **+ criar um recurso** > **oferece + planos** > **plano**.
  
   ![Selecione um plano](media/azure-stack-create-plan/select-plan.png)

3. Sob **novo plano**, insira um **nome de exibição** e um **nome do recurso**. O nome de exibição é o nome amigável do plano do que os usuários podem ver. Somente o administrador pode ver o nome do recurso, quais os administradores usam para trabalhar com o plano como um recurso do Azure Resource Manager.

   ![Especifique os detalhes](media/azure-stack-create-plan/plan-name.png)

4. Criar um novo **grupo de recursos**, ou selecione um existente, como um contêiner para o plano.

   ![Especifique o grupo de recursos](media/azure-stack-create-plan/resource-group.png)

5. Selecione **Services** e, em seguida, selecione a caixa de seleção **Microsoft. Compute**, **Microsoft. Network**, e **Microsoft. Storage**. Em seguida, escolha **selecionar** para salvar a configuração. Caixas de seleção aparecem quando o mouse passa sobre cada opção.
  
   ![Selecionar serviços](media/azure-stack-create-plan/services.png)

6. Selecione **cotas**, **Microsoft. Storage (local)** e, em seguida, escolha a cota padrão ou selecione **criar nova cota** para criar uma cota personalizada.
  
   ![Cotas](media/azure-stack-create-plan/quotas.png)

7. Se você estiver criando uma nova cota, insira um **nome** para a cota > especifique os valores de cota > selecionar **Okey**. O **criar cota** caixa de diálogo é fechada.

   ![Nova cota](media/azure-stack-create-plan/new-quota.png)

   Você, em seguida, selecione a nova cota que você criou. Selecionando a cota atribui a ele e fecha a caixa de diálogo de seleção.
  
   ![Atribuir a cota](media/azure-stack-create-plan/assign-quota.png)

8. Repita as etapas 6 e 7 para criar e atribuir as cotas para **Network (local)** e **Microsoft. Compute (local)**. Quando todos os três serviços têm cotas atribuídas, eles vai parecer com o exemplo a seguir.

   ![Atribuições de cota completa](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Sob **cotas**, escolha **Okey**e, em **novo plano**, escolha **criar** para criar o plano.

    ![Criar o plano](media/azure-stack-create-plan/create.png)

10. Para ver seu novo plano, selecione **todos os recursos**, em seguida, procure o plano e selecione seu nome. Se sua lista de recursos for longa, use **pesquisa** para localizar seu plano por nome.

   ![Examinar o plano](media/azure-stack-create-plan/plan-overview.png)

## <a name="next-steps"></a>Próximas etapas

[Criar uma oferta](azure-stack-create-offer.md)
