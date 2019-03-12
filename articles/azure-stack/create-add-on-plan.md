---
title: Neste artigo, você aprenderá a atualizar planos e ofertas do Azure Stack | Microsoft Docs
description: Este artigo descreve como exibir e modificar planos e ofertas do Azure Stack existentes.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 00bb17eadfee32e9b0d006ac76bb8e1cd614f13e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780381"
---
# <a name="azure-stack-add-on-plans"></a>Planos de complemento de pilha do Azure

Como um operador do Azure Stack, você cria planos de complemento para modificar uma [plano de base](azure-stack-create-plan.md) quando você deseja oferecer serviços adicionais ou estender *computador*, *armazenamento*, ou *rede* cotas além a oferta inicial do plano base. Planos de complemento modificar o plano de base e as extensões opcionais que os usuários podem escolher assinar.

Há ocasiões em que a combinação de tudo em um único plano é ideal. Outras vezes, você talvez queira ter uma base de plano e oferta, em seguida, os serviços adicionais por meio de planos de complemento. Por exemplo, você poderia optar por oferecer serviços de IaaS como parte de um plano de base, com todos os serviços de PaaS, tratados como planos de complemento.

Outro motivo para usar planos de complemento é ajudar a monitorar o uso de recursos. Para fazer isso, você pode começar com um plano de base que inclui cotas relativamente pequenas (dependendo dos serviços necessários). Em seguida, como os usuários atingem a capacidade, eles seriam sejam alertados de que eles consumiu a alocação de recursos com base em seu plano atribuído. A partir daí, os usuários podem selecionar um plano de complemento que fornece os recursos adicionais.

> [!NOTE]
> Quando você não desejar usar um plano de complemento para estender uma cota, você também pode optar por [editar a configuração original da cota](azure-stack-quota-types.md#edit-a-quota).

Quando você adiciona um plano de complemento a uma assinatura de oferta existente, os recursos adicionais podem levar até uma hora para aparecer.

Planos de complemento são criados, modificando uma oferta existente.

## <a name="create-an-add-on-plan-1902-and-later"></a>Criar um plano de complemento (1902 e posterior)

1. Entre no portal do administrador do Azure Stack como um administrador de nuvem.
2. Siga as mesmas etapas usadas para [criar um novo plano base](azure-stack-create-plan.md) para criar um novo plano de oferta de serviços que não foram anteriormente oferecidos.
3. No portal do administrador, clique em **oferece** e, em seguida, selecione a oferta a ser atualizada com um plano de complemento.

   ![Criar plano de complemento](media/create-add-on-plan/add-on1.png)

4. Role até a parte inferior das propriedades de oferta e selecione **planos de complemento**. Clique em **Adicionar**.

    ![Criar plano de complemento](media/create-add-on-plan/add-on2.png)

5. Selecione o plano para adicionar. Neste exemplo, o plano é chamado **storageaccounts 20**. Depois de selecionar o plano, clique em **selecionar** para adicionar o plano à oferta. Você deve receber uma notificação de que o plano foi adicionado com êxito para a oferta.

    ![Criar plano de complemento](media/create-add-on-plan/add-on3.png)

6. Examine a lista de planos de complemento incluída na oferta para verificar se o novo plano de complemento está listado.

    [![Criar plano de complemento](media/create-add-on-plan/add-on4.png "criar plano de complemento")](media/create-add-on-plan/add-on4lg.png#lightbox)

## <a name="create-an-add-on-plan-1901-and-earlier"></a>Criar um plano de complemento (1901 e anterior)

1. Entre no portal do administrador do Azure Stack como um administrador de nuvem.
2. Siga as mesmas etapas usadas para [criar um novo plano base](azure-stack-create-plan.md) para criar um novo plano de oferta de serviços que não foram anteriormente oferecidos. Neste exemplo, o Cofre de chaves (**Microsoft. keyvault**) serviços serão incluídos no novo plano.
3. No portal do administrador, clique em **oferece** e, em seguida, selecione a oferta a ser atualizada com um plano de complemento.

   ![Criar plano de complemento](media/create-add-on-plan/1.PNG)

4. Role até a parte inferior das propriedades de oferta e selecione **planos de complemento**. Clique em **Adicionar**.

    ![Criar plano de complemento](media/create-add-on-plan/2.PNG)

5. Selecione o plano para adicionar. Neste exemplo, o plano é chamado **plano do Cofre de chave**. Depois de selecionar o plano, clique em **selecionar** para adicionar o plano à oferta. Você deve receber uma notificação de que o plano foi adicionado com êxito para a oferta.

    ![Criar plano de complemento](media/create-add-on-plan/3.PNG)

6. Examine a lista de planos de complemento incluída na oferta para verificar se o novo plano de complemento está listado.

    ![Criar plano de complemento](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>Próximas etapas

* [Criar uma oferta](azure-stack-create-offer.md)