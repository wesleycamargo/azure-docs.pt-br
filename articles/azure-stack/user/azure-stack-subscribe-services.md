---
title: Neste tutorial, você aprenderá a assinar uma oferta de pilha do Azure | Microsoft Docs
description: Este tutorial mostra como criar uma nova assinatura para serviços de pilha do Azure e testar a oferta ao criar uma máquina virtual de teste.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 96b82ede71e7957105dce25096d7873ee876211f
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238336"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Tutorial: criar e testar uma assinatura
Este tutorial mostra como criar uma assinatura que contém uma oferta e, em seguida, testá-lo. Para o teste, faça logon no portal do usuário de pilha do Azure como um administrador de nuvem, assinar a oferta e, em seguida, criar uma máquina virtual.

> [!TIP]
> Para mais obter uma experiência avaliação mais avançada, você pode [criar uma assinatura para um determinado usuário](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) e, em seguida, faça logon como esse usuário no portal do usuário. 

Este tutorial mostra como assinar uma oferta de pilha do Azure.

O que você aprenderá:

> [!div class="checklist"]
> * Assinar uma oferta 
> * Testar a oferta

## <a name="subscribe-to-an-offer"></a>Assinar uma oferta
Para assinar uma oferta como um usuário, você precisa fazer logon no portal do usuário de pilha do Azure para descobrir os serviços que oferecida pelo operador pilha do Azure.

1. Faça logon no portal do usuário e clique em **obter uma assinatura**.

   ![Obtenha uma assinatura](media/azure-stack-subscribe-services/get-subscription.png)

2. No campo **Nome de Exibição** , digite um nome para sua assinatura. Em seguida, clique em **oferecem** para selecionar uma das ofertas disponíveis no **escolha uma oferta de** seção e, em seguida, clique em **criar**.

   ![Criar uma oferta](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Agora, você deve atualizar o portal do usuário para começar a usar sua assinatura.

3. Para exibir a assinatura que você acabou de criar, clique em **mais serviços**, clique em **assinaturas**, em seguida, clique em nova assinatura. Após assinar uma oferta, atualize o portal para ver se os novos serviços foram incluídos como parte da nova assinatura. Neste exemplo, **máquinas virtuais** foi adicionado.

   ![Exibição de assinatura](media/azure-stack-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>Testar a oferta
Enquanto estiver conectado ao portal do usuário, você pode testar a oferta Provisionando uma máquina virtual usando os novos recursos de assinatura. 

> [!NOTE]
> Este teste requer que uma VM do Datacenter do Windows Server 2016 foi adicionada pela primeira vez no mercado de pilha do Azure. 

1. Faça logon portal do usuário.

2. No portal do usuário, clique em **máquinas virtuais** > **adicionar** > **Datacenter do Windows Server 2016**e, em seguida, clique em **criar** .

3. No **Noções básicas de** seção, digite um **nome**, **nome de usuário**, e **senha**, escolha um **assinatura**, criar um **grupo de recursos** (ou selecione um existente) e, em seguida, clique em **Okey**.

4. No **escolher um tamanho de** seção, clique em **A1 padrão**e, em seguida, clique em **selecione**.  

5. Na folha configurações, aceite os padrões e clique em **Okey**.

6. No **resumo** seção, clique em **Okey** para criar a máquina virtual.  

7. Para ver a nova máquina virtual, clique em **máquinas virtuais**, em seguida, procure a nova máquina virtual e clique em seu nome.

    ![Todos os recursos](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> Implantação da máquina virtual levará alguns minutos para ser concluída.


## <a name="next-steps"></a>Próximas etapas

O que você aprendeu neste tutorial:

> [!div class="checklist"]
> * Assinar uma oferta 
> * Testar a oferta


> [!div class="nextstepaction"]
> [Criar uma máquina virtual de um modelo de comunidade](azure-stack-create-vm-template.md)