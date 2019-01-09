---
title: Assinar uma oferta no Azure Stack | Microsoft Docs
description: Criar assinaturas para ofertas no Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: sethm
ms.openlocfilehash: 7de32573ac6c0d084be3fdd6ff2c3641559fc31f
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118810"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Criar assinaturas de ofertas no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Depois que você [criar uma oferta](azure-stack-create-offer.md), os usuários precisam de uma assinatura para essa oferta antes que eles podem usá-lo. Há duas maneiras que os usuários podem obter uma assinatura para uma oferta:

- Como um operador de nuvem, você pode criar uma assinatura para um usuário de dentro do portal do administrador. As assinaturas que você cria podem ser para as ofertas públicas e privadas.
- Como um usuário de locatário, você pode assinar uma oferta pública quando você usa o portal do usuário.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Criar uma assinatura como um operador de nuvem

Operadores de nuvem podem usar o portal de administração para criar uma assinatura para uma oferta para um usuário. Você pode criar assinaturas para membros de seu próprio locatário do diretório. Quando [multilocação](azure-stack-enable-multitenancy.md) é habilitado, você também pode criar assinaturas para usuários em locatários de diretório adicionais.

Se não quiser que os seus locatários para criar suas próprias assinaturas, verifique suas ofertas privadas e, em seguida, criar assinaturas para seus locatários. Essa abordagem é comum ao integrar o Azure Stack com cobrança externa ou sistemas do catálogo de serviço.

Depois de criar uma assinatura para um usuário, podem entrar no portal do usuário e ver que eles se inscreveu para a oferta.  

### <a name="to-create-a-subscription-for-a-user"></a>Para criar uma assinatura para um usuário

1. No portal de administração, acesse **assinaturas do usuário.**
2. Selecione **Adicionar**. Sob **nova assinatura de usuário**, insira as seguintes informações:  

   - **Nome de exibição** – um nome amigável para identificar a assinatura que é exibido como o *nome da assinatura de usuário*.
   - **Usuário** – especificar um usuário de um locatário de diretório disponível para esta assinatura. O nome de usuário é exibido como *proprietário*.  O formato do nome do usuário depende de sua solução de identidade. Por exemplo: 

     - **Azure AD:** `<user1>@<contoso.onmicrosoft.com>`

     - **AD FS:** `<user1>@<azurestack.local>`

   - **Locatário de diretório** – selecione o locatário de diretório em que a conta de usuário pertence. Se você não tiver habilitado a multilocação, somente seu locatário de diretório local está disponível.

3. Selecione **oferecem**. Sob **oferece**, escolha um **oferecem** para esta assinatura. Como você está criando a assinatura para um usuário, selecione **privada** como o estado de acessibilidade.

4. Selecione **criar** para criar a assinatura. A nova assinatura aparece sob **assinatura de usuário**. Quando o usuário entra portal do usuário, eles podem ver os detalhes da assinatura.

### <a name="to-make-an-add-on-plan-available"></a>Para disponibilizar um plano de complemento

Um operador de nuvem pode adicionar um plano para uma assinatura criada anteriormente a qualquer momento:

1. No portal de administração, selecione **todos os serviços** e, em seguida, sob o **recursos ADMINISTRATIVOS** categoria, selecione **assinaturas do usuário**. Selecione a assinatura que deseja alterar.

2. Selecione **complementos** e, em seguida, selecione **+ adicionar**.  

3. Sob **Adicionar plano**, selecione o plano que você deseja como um complemento.

## <a name="create-a-subscription-as-a-user"></a>Criar uma assinatura como um usuário

Como um usuário, você pode entrar no portal do usuário para localizar e assinar públicas ofertas e planos de complemento para seu locatário de diretório (organização).

>[!NOTE]
>Se seu ambiente do Azure Stack dá suporte a [multilocação](azure-stack-enable-multitenancy.md), também é possível assinar a oferta de um locatário de diretório remoto.

### <a name="to-subscribe-to-an-offer"></a>Para assinar uma oferta

1. [Entrar no](azure-stack-connect-azure-stack.md) para o [portal do usuário do Azure Stack](https://portal.local.azurestack.external) e selecione **obter uma assinatura**.

   ![Obtenha uma assinatura](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. Sob **obter uma assinatura**, digite o nome amigável da assinatura no **nome de exibição**. Selecione **oferecem** e, em **escolher uma oferta**, escolher uma oferta. Selecione **criar** para criar a assinatura.

   ![Criar uma oferta](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Depois de assinar uma oferta, atualize o portal para ver quais serviços fazem parte da nova assinatura.

4. Para ver a assinatura que você criou, selecione **todos os serviços** e, em seguida, sob o **gerais** categoria, selecione **assinaturas**. Selecione a assinatura para ver os detalhes da assinatura.  

### <a name="to-subscribe-to-an-add-on-plan"></a>Para assinar um plano de complemento

Se uma oferta tem um plano de complemento, você pode adicionar esse plano à sua assinatura a qualquer momento.  

1. No portal do usuário, selecione **todos os serviços**. Em seguida, sob o **gerais** categoria, selecione **assinaturas**e, em seguida, selecione a assinatura que você deseja alterar. Se houver qualquer plano de complemento disponível, **+ Adicionar plano** está ativa e não há um bloco para **planos de complemento**. 

   Se **+ Adicionar plano** não estiver ativo, em seguida, não há planos de complemento para a oferta associada a essa assinatura.

1. Selecione **+ Adicionar plano** ou o **planos de complemento** lado a lado. Sob **planos de complemento**, selecione o plano que você deseja adicionar.

## <a name="next-steps"></a>Próximas etapas

- [Provisionar uma máquina virtual](azure-stack-provision-vm.md)
