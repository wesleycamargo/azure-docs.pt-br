---
title: Assinar uma oferta na pilha do Azure | Microsoft Docs
description: Criar assinaturas para ofertas na pilha do Azure
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/11/2018
ms.author: brenduns
ms.openlocfilehash: 9153649774a67533649fb62da83a3f50abd592da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295203"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Criar assinaturas para ofertas na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Depois que você [criar uma oferta](azure-stack-create-offer.md), os usuários precisam de uma assinatura para essa oferta antes que eles podem usá-lo. Há duas maneiras que os usuários podem obter assinados para uma oferta:

- Como um operador de nuvem, você pode criar uma assinatura de um usuário no portal do administrador. As assinaturas que você criar podem ser ofertas públicas e privadas.
- Como um usuário de locatário, você pode assinar uma oferta pública quando você usar o portal do usuário.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Criar uma assinatura como um operador de nuvem

Operadores de nuvem podem usar o portal de administração para criar uma assinatura para uma oferta para um usuário.  Você pode criar assinaturas de membros de seu próprio locatário de diretório.  Quando [multilocação](azure-stack-enable-multitenancy.md) está habilitado, você também pode criar assinaturas para os usuários de locatários de diretório adicionais.

Se não quiser que seus locatários ao criar suas próprias assinaturas, faça suas ofertas privada e, em seguida, criar assinaturas para seus locatários. Essa abordagem é comum ao integrar a pilha do Azure cobrança externa ou sistemas de catálogo de serviço.

Depois de criar uma assinatura para um usuário, podem entrar no portal do usuário e veja o que estiver se inscrito para a oferta.  

### <a name="to-create-a-subscription-for-a-user"></a>Para criar uma assinatura para um usuário

1. No portal de administração, acesse **assinaturas do usuário.**
2. Selecione **Adicionar**. Em **nova assinatura de usuário**, insira as seguintes informações:  

   - **Nome de exibição** – um nome amigável para identificar a assinatura que aparece como o *nome de assinatura de usuário*.
   - **Usuário** – especifique um usuário de um locatário de diretório disponíveis para essa assinatura. O nome de usuário é exibido como *proprietário*.  O formato do nome do usuário depende de sua solução de identidade. Por exemplo: 

     - **O AD do Azure:***&lt;user1 > @&lt;contoso.onmicrosoft.com >* 

     - **O AD FS:***&lt;user1 > @&lt;azurestack.local >* 

   - **Locatário do diretório** – selecione o locatário do diretório em que a conta de usuário pertence. Se você não tiver habilitado a multilocação, somente seu locatário de diretório local está disponível.

3. Selecione **oferecem**. Em **oferece**, escolha um **oferecem** para essa assinatura. Como você está criando a assinatura para um usuário, selecione **privada** como o estado de acessibilidade.

4. Selecione **criar** para criar a assinatura. Você verá a nova assinatura em **assinatura de usuário**. Quando o usuário entra portal do usuário poderão ver os detalhes da assinatura.

### <a name="to-make-an-add-on-plan-available"></a>Para disponibilizar um plano de complemento

Um operador de nuvem pode adicionar um complemento de plano para uma assinatura criada anteriormente a qualquer momento:

1. No portal de administração, selecione **mais serviços** > **assinaturas de usuário**. Selecione a assinatura que deseja alterar.

2. Selecione **complementos** e, em seguida, selecione **+ adicionar**.  

3. Em **Adicionar plano**, selecione o plano que você deseja como um complemento.

## <a name="create-a-subscription-as-a-user"></a>Criar uma assinatura como um usuário

Como um usuário, você pode entrar no portal do usuário para localizar e assinar ofertas públicas e planos de complemento para o seu locatário de diretório (organização).

>[!NOTE]
>Se seu ambiente de pilha do Azure oferece suporte a [multilocação](azure-stack-enable-multitenancy.md) também é possível assinar a oferta de um locatário de diretório remoto.

### <a name="to-subscribe-to-an-offer"></a>Para assinar uma oferta

1. [Entrar](azure-stack-connect-azure-stack.md) portal do usuário do Azure pilha (https://portal.local.azurestack.external) e selecione **obter uma assinatura**.

   ![Obtenha uma assinatura](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. Em **obter uma assinatura**, digite o nome amigável da assinatura em **nome de exibição**. Selecione **oferecem** e, em **escolha uma oferta de**, escolha uma oferta. Selecione **criar** para criar a assinatura.

   ![Criar uma oferta](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Após assinar uma oferta, atualize o portal para ver os serviços que fazem parte da nova assinatura.
4. Para ver a assinatura que você criou, selecione **mais serviços** e, em seguida, selecione **assinaturas**. Selecione a assinatura para ver os detalhes da assinatura.  

### <a name="to-subscribe-to-an-add-on-plan"></a>Para assinar um plano de complemento

Se uma oferta tem um plano de complemento, você pode adicionar esse plano à sua assinatura a qualquer momento.  

1. No portal do usuário, selecione **mais serviços** > **assinaturas**e, em seguida, selecione a assinatura que você deseja alterar. Se houver qualquer plano de complementos disponíveis, **+ Adicionar plano** está ativo e não há um bloco para **planos de complemento**.

   >[!NOTE]
   >Se **+ Adicionar plano** não está ativo, em seguida, não existem planos complemento da oferta associada à assinatura.

1. Selecione **+ Adicionar plano** ou **planos de complemento** lado a lado. Em **planos de complemento**, selecione o plano que você deseja adicionar.

## <a name="next-steps"></a>Próximas etapas

[Provisionar uma máquina virtual](azure-stack-provision-vm.md)
