---
title: "Neste tutorial, você aprenderá a pilha do Azure oferece e planos de atualização | Microsoft Docs"
description: Este artigo descreve como exibir e modificar as ofertas de pilha do Azure e planos existentes.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 3b5d2ab5e924f578f5838d11b0d2058aacf67dec
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-update-offers-and-plans"></a>Tutorial: oferece e planos de atualização
Como um operador de pilha do Azure, você cria planos que contêm os serviços desejados e cotas aplicáveis aos seus usuários para se inscrever. Essas *base planos* contêm os serviços de núcleo para ser oferecidos aos seus usuários e você só pode ter um plano de base por oferta. Se você precisar modificar sua oferta, você pode usar *planos de complemento* que permitem que você modifique o plano para estender o computador, armazenamento, ou cotas inicialmente oferecidas com o plano de base de rede. 

Embora combinando tudo em um único plano pode ser o ideal em alguns casos, convém ter uma base de plano e oferecer serviços adicionais usando planos de complemento. Por exemplo, você pode decidir oferecer serviços de IaaS como parte de um plano de base, com todos os serviços de PaaS tratados como planos de complemento. Planos também podem ser usados para controlar o consumo de recursos em seu ambiente ASDK limitado. Por exemplo, se desejar que seus usuários para estar atento ao seu uso de recursos, você pode ter um plano de base relativamente pequeno (dependendo dos serviços necessários) e como os usuários alcançar a capacidade, seria ser avisados que já que consumiram a alocação de recursos com base em seu plano atribuído. A partir daí, os usuários podem selecionar um plano de complementos disponíveis para obter recursos adicionais. 

> [!NOTE]
> Quando um usuário adiciona um complemento de plano para uma assinatura existente de oferta, os recursos adicionais podem levar uma hora exibida. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um plano de complemento 
> * Assinar o plano de complemento

## <a name="create-an-add-on-plan"></a>Criar um plano de complemento
**Planos de complemento** são criados por meio de modificação de uma oferta existente.

1. Entrar para o [portal do Azure pilha](https://adminportal.local.azurestack.external) como um administrador de nuvem.
2. Siga as mesmas etapas usadas anteriormente para [criar um plano de base](asdk-offer-services.md) para criar um novo plano de oferta de serviços que não foram oferecidos anteriormente. Neste exemplo, serviços de Cofre de chaves (Microsoft.KeyVault) serão incluídos no plano.
3. No portal do administrador, clique em **oferece** e, em seguida, selecione a oferta para ser atualizado com um plano de complemento.

   ![](media/asdk-update-offers/1.PNG)

4.  Role até a parte inferior das propriedades de oferta e selecione **planos de complemento**. Clique em **Adicionar**.
   
    ![](media/asdk-update-offers/2.PNG)

5. Selecione o plano para adicionar. Neste exemplo, o plano é chamado **plano de Cofre de chave**e, em seguida, clique em **selecione** para adicionar o plano para a oferta. Você deve receber uma notificação de que o plano foi adicionado com êxito para a oferta.
   
    ![](media/asdk-update-offers/3.PNG)

6. Examine a lista de complemento listados planos incluídos com a oferta para verificar se o complemento do novo plano.
   
    ![](media/asdk-update-offers/4.PNG)

## <a name="subscribe-to-the-add-on-plan"></a>Assinar o plano de complemento
Você precisa fazer logon no portal do usuário de pilha do Azure para estender uma assinatura de pilha do Azure existente com um plano de complemento.

Siga estas etapas para descobrir recursos adicionais que oferecida pelo operador pilha do Azure e adicionar um complemento de plano para uma oferta que você tenha assinado anteriormente.

1. Faça logon na [portal do usuário](https://portal.local.azurestack.external).
2. Para localizar a assinatura para estender com plano de complemento, clique em **mais serviços**, clique em **assinaturas**, em seguida, selecione sua assinatura.
   
    ![](media/asdk-update-offers/5.PNG)

3.  Na visão geral de assinatura, clique em **Adicionar plano**.
   
    ![](media/asdk-update-offers/6.PNG)

4. Na folha Adicionar plano, selecione o plano de complemento para adicionar à assinatura. Neste exemplo, **plano de Cofre de chave** está selecionado. Em seguida, você deve receber uma notificação de que o plano de complemento foi adquirido com êxito e que você precisa atualizar o portal para acessar a assinatura atualizada.
   
    ![](media/asdk-update-offers/7.PNG)

5. Por fim, examine os planos de complementos associados a assinatura para garantir que o plano de complemento foi adicionado com êxito.
   
    ![](media/asdk-update-offers/8.PNG)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um plano de complemento 
> * Assinar o plano de complemento

> [!div class="nextstepaction"]
> [Criar uma máquina virtual de um modelo](asdk-create-vm-template.md)

