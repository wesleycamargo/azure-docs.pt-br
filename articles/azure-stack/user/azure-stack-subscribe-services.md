---
title: Neste tutorial, você aprenderá a assinar uma oferta do Azure Stack | Microsoft Docs
description: Este tutorial mostra como criar uma nova assinatura para serviços do Azure Stack e testar a oferta com a criação de uma máquina virtual de teste.
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
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/13/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 4c7c149a9541af0417e7548c78ab3dc9a8084ec6
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247343"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Tutorial: criar e testar uma assinatura

Este tutorial mostra como criar uma assinatura que contém uma oferta e, em seguida, testá-lo. Para o teste, você entrar no portal do usuário do Azure Stack como administrador de nuvem, assine a oferta e, em seguida, criar uma máquina virtual.

> [!TIP]
> Mais uma mais avançada experiência de avaliação, você pode [criar uma assinatura para um determinado usuário](../azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) e faça logon como esse usuário no portal do usuário. 

Este tutorial mostra como assinar uma oferta do Azure Stack.

O que você aprenderá:

> [!div class="checklist"]
> * Assinar uma oferta 
> * Testar a oferta

## <a name="subscribe-to-an-offer"></a>Assinar uma oferta

Para assinar uma oferta como um usuário, você entrar no portal do usuário do Azure Stack para descobrir os serviços que tenham sido oferecidos pelo operador do Azure Stack.

1. Entrar no portal e selecione o usuário **obter uma assinatura**.

   ![Obtenha uma assinatura](media/azure-stack-subscribe-services/get-subscription.png)

2. No campo **Nome de Exibição** , digite um nome para sua assinatura. Em seguida, selecione **oferecem** para escolher uma das ofertas disponíveis na **escolher uma oferta** seção. Em seguida, selecione **Criar**.

   ![Criar uma oferta](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Agora, você deve atualizar o portal do usuário para começar a usar sua assinatura.

3. Para exibir a assinatura que você criou, selecione **todos os serviços**. Em seguida, na **gerais** categoria, selecione **assinaturas**e, em seguida, selecione sua nova assinatura. Depois de assinar uma oferta, atualize o portal para ver se os novos serviços foram incluídos como parte da nova assinatura. Neste exemplo, **máquinas virtuais** foi adicionado.

   ![Modo de exibição de assinatura](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>Testar a oferta

Enquanto estiver conectado ao portal do usuário, você pode testar a oferta Provisionando uma máquina virtual usando os novos recursos de assinatura. 

> [!NOTE]
> Este teste requer que uma VM do Windows Server 2016 Datacenter foi adicionada pela primeira vez no Marketplace do Azure Stack. 

1. Entrar no portal do usuário.

2. No portal do usuário, selecione **máquinas virtuais**, em seguida, **Add**, em seguida, **Windows Server 2016 Datacenter**e, em seguida, clique em **criar**.

3. No **Noções básicas** seção, digite um **nome**, **nome de usuário**, e **senha**, escolha um **assinatura**, criar uma **grupo de recursos** (ou selecione um existente) e, em seguida, selecione **Okey**.

4. No **escolher um tamanho** seção, selecione **padrão A1**e, em seguida, clique em **selecione**.  

5. No **as configurações** folha, aceite os padrões e selecione **Okey**.

6. No **resumo** seção, clique em **Okey** para criar a máquina virtual.  

7. Para ver sua nova máquina virtual, selecione **máquinas virtuais**, em seguida, procure a nova máquina virtual e clique em seu nome.

    ![Todos os recursos](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> A implantação de máquina virtual leva alguns minutos para ser concluída.


## <a name="next-steps"></a>Próximas etapas

O que você aprendeu neste tutorial:

> [!div class="checklist"]
> * Assinar uma oferta 
> * Testar a oferta


> [!div class="nextstepaction"]
> [Criar uma VM a partir de um modelo da comunidade](azure-stack-create-vm-template.md)