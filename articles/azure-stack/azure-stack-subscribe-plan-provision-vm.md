---
title: Assinar uma oferta na pilha do Azure | Microsoft Docs
description: Criar assinaturas para ofertas na pilha do Azure
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2018
ms.author: brenduns
ms.openlocfilehash: 9b35b497c264fab2b8352eda82fe6d4e1fc274e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Criar assinaturas para ofertas na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Depois que você [criar uma oferta](azure-stack-create-offer.md), os usuários precisam de uma assinatura para essa oferta antes que eles podem usá-lo.   
- Como um operador de nuvem, você pode criar uma assinatura de um usuário no portal do administrador.  As assinaturas que você criar podem ser ofertas públicas e privadas.
- Como um usuário de locatário, você pode assinar uma oferta pública quando você usar o portal do usuário.  

As seções a seguir fornecem orientação para os operadores de nuvem durante a criação de assinaturas para usuários e como assinar uma oferta como um usuário.

## <a name="create-a-subscription-as-a-cloud-operator"></a>Criar uma assinatura como um operador de nuvem
Operadores de nuvem podem usar o portal de administração para criar uma assinatura para uma oferta para um usuário.  Você pode criar assinaturas de membros de seu próprio locatário de diretório.  Quando [multilocação](azure-stack-enable-multitenancy.md) está habilitado, você também pode criar assinaturas para os usuários de locatários de diretório adicionais.

Você pode criar assinaturas para ofertas públicas e privadas.  Se não quiser que seus locatários ao criar suas próprias assinaturas, faça todas as suas ofertas privada e, em seguida, criar assinaturas em nome de seus locatários. Essa abordagem é comum ao integrar a pilha do Azure cobrança externa ou sistemas de catálogo de serviço.

Depois de criar uma assinatura para um usuário, esse usuário pode fazer logon no portal do usuário e encontrará que estão inscritos para a oferta.  

### <a name="to-create-the-subscription-for-a-user"></a>Para criar a assinatura para um usuário
1.  No portal de administração, acesse **assinaturas do usuário.**
2.  Selecione **adicionar** para abrir o **nova assinatura de usuário** painel. Aqui você pode especificar os seguintes detalhes:  

  a. **Nome de exibição** – um nome amigável para identificar a assinatura que aparece como o *nome de assinatura de usuário*.

  b. **Usuário** – especifique um usuário de um locatário de diretório disponíveis para essa assinatura. O nome de usuário é exibido como *proprietário*.  O formato do nome do usuário depende de sua solução de identidade. Por exemplo:    

     -  **O AD do Azure:***&lt;user1 > @&lt;contoso.onmicrosoft.com >* 

     -   **O AD FS:***&lt;user1 > @&lt;azurestack.local >*      

  c.    **Locatário do diretório** – selecione o locatário do diretório em que a conta de usuário pertence. Se você não tiver habilitado a multilocação, somente seu locatário de diretório local está disponível.

3.  Selecione **oferecem** para abrir o **oferece** painel e, em seguida, escolha um **oferecem** para essa assinatura. Porque você está criando uma assinatura para um usuário, você pode selecionar uma oferta pública ou privada.

4.  Selecione **criar** para criar a assinatura. O **assinaturas de usuário** agora, o painel exibe a nova assinatura.  Posteriormente, quando o usuário fizer logon no portal do usuário, eles podem exibir detalhes sobre essa assinatura.

### <a name="to-make-an-add-on-plan-available"></a>Para disponibilizar um plano de complemento  
Um operador de nuvem pode adicionar um complemento de plano para uma assinatura criada anteriormente a qualquer momento:   
1.  No portal de administração, vá para **mais serviços** > **assinaturas de usuário**e, em seguida, selecione a assinatura que você deseja alterar.   

2.  Selecione **complementos** > **adicionar** para abrir o **Adicionar plano** painel.  

3.  Selecione o plano que você deseja adicionar como um complemento.  O console, em seguida, exibe os planos associados à assinatura.




## <a name="create-a-subscription-as-a-user"></a>Criar uma assinatura como um usuário
Como um usuário, você pode entrar no portal do usuário para localizar e assinar ofertas públicas e planos de complemento de seu locatário de diretório (organização). Quando o ambiente de pilha do Azure dá suporte a [multilocação](azure-stack-enable-multitenancy.md) pode se inscrever para ofertas de um locatário de diretório remoto.

### <a name="to-subscribe-to-an-offer"></a>Para assinar uma oferta
1. [Entrar](azure-stack-connect-azure-stack.md) para o portal do usuário do Azure pilha (https://portal.local.azurestack.external) e clique em **obter uma assinatura**.

   ![Obtenha uma assinatura](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. No **nome de exibição** campo, digite um nome para sua assinatura, clique em **oferecem**, clique em uma das ofertas no **escolha uma oferta de** painel e clique  **Criar**.

   ![Criar uma oferta](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. Para exibir a assinatura que você criou, clique em **mais serviços**, clique em **assinaturas**, em seguida, clique em nova assinatura.  

Após assinar uma oferta, atualize o portal para ver os serviços que fazem parte da nova assinatura.

### <a name="to-subscribe-to-an-add-on-plan"></a>Para assinar um plano de complemento
Se uma oferta tem um plano de complemento, você pode adicionar esse plano à sua assinatura a qualquer momento.  

1. No portal do usuário, selecione **mais serviços** > **assinaturas**e, em seguida, selecione a assinatura que você deseja alterar.

2. Selecione **Adicionar plano** botão e, em seguida, selecione o plano de complemento que você deseja. Se **Adicionar plano** não está disponível, em seguida, não há nenhum plano de complemento da oferta associada à assinatura.



## <a name="next-steps"></a>Próximas etapas
[Provisionar uma máquina virtual](azure-stack-provision-vm.md)
