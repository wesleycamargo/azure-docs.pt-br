---
title: Criar um plano na pilha do Azure | Microsoft Docs
description: "Como um administrador de nuvem, crie um plano que permite que os assinantes provisionar, máquinas virtuais."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 30759dca746fd7fd02653556cb105f419f5bf854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-plan-in-azure-stack"></a>Criar um plano no Azure Stack

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Os [planos](azure-stack-key-features.md) são agrupamentos de um ou mais serviços. Como um provedor, você pode criar planos para oferecer a seus usuários. Por sua vez, os usuários assinar suas ofertas para usar os planos e serviços que elas incluem. Este exemplo mostra como criar um plano que inclui a computação, rede e provedores de recursos de armazenamento. Esse plano oferece a capacidade para provisionar máquinas virtuais de assinantes.

1. Entrar no portal do administrador do Azure pilha (https://adminportal.local.azurestack.external). Insira as credenciais para a conta que você criou na etapa 5 do [executar o script PowerShell](azure-stack-run-powershell-script.md) seção.

2. Para criar um plano e a oferta que os usuários podem se inscrever, clique em **novo** > **locatário oferece + planos** > **plano**.

   ![](media/azure-stack-create-plan/image01.png)
3. No **novo plano** folha, preencha **nome de exibição** e **nome do recurso**. O nome de exibição é o nome amigável do plano do que os usuários veem. Somente o administrador pode ver o Nome do Recurso. Esse é o nome que os administradores usam para trabalhar com o plano como um recurso do Gerenciador de Recursos do Azure.

   ![](media/azure-stack-create-plan/image02.png)
4. Criar um novo **grupo de recursos**, ou selecione um existente, como um contêiner para o plano.

   ![](media/azure-stack-create-plan/image02a.png)
5. Clique em **serviços**, selecione **Microsoft. Compute**, **Network**, e **Microsoft**e, em seguida, clique em **Selecionar**.

   ![](media/azure-stack-create-plan/image03.png)
6. Clique em **cotas**, clique em **(local) do Microsoft**e, em seguida, selecione a cota padrão ou clique em **criar nova cota** para personalizar a cota.

   ![](media/azure-stack-create-plan/image04.png)
7. Se você estiver criando uma nova cota, insira um nome para a cota > definir os valores de cota > clique **Okey** > clique no nome da nova cota.

   ![](media/azure-stack-create-plan/image06.png)
8. Clique em **Network (local)**e, em seguida, selecione a cota padrão ou clique em **criar nova cota** para personalizar a cota.

    ![](media/azure-stack-create-plan/image07.png)
9. Se você estiver criando uma nova cota, digite um nome para a cota > definir os valores de cota > clique **Okey** > clique no nome da nova cota.

    ![](media/azure-stack-create-plan/image08.png)
10. Clique em **Microsoft. Compute (local)**e, em seguida, selecione a cota padrão ou clique em **criar nova cota** para personalizar a cota.

    ![](media/azure-stack-create-plan/image09.png)
11. Se você estiver criando uma nova cota, digite um nome para a cota > definir os valores de cota > clique **Okey** > clique no nome da nova cota.

    ![](media/azure-stack-create-plan/image10.png)
12. No **cotas** folha, clique em **Okey**e, em seguida, no **novo plano** folha, clique em **criar** para criar o plano.

    ![](media/azure-stack-create-plan/image11.png)
13. Para ver o novo plano, clique em **todos os recursos**, em seguida, procure o plano e clique em seu nome.

    ![](media/azure-stack-create-plan/image12.png)

### <a name="next-steps"></a>Próximas etapas
[Criar uma oferta](azure-stack-create-offer.md)
