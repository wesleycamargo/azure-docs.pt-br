---
title: Delegando ofertas no Azure Stack | Microsoft Docs
description: Aprenda a colocar a outras pessoas responsável por criar ofertas e inscrição de usuários para você.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 157f0207-bddc-42e5-8351-197ec23f9d46
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: 161a17f2360767dacc4f418a078c695d7cb8daa7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449957"
---
# <a name="delegate-offers-in-azure-stack"></a>Delegar ofertas no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Como o operador do Azure Stack, você geralmente deseja colocar a outras pessoas responsável por criar ofertas e inscrever-se de que os usuários. Por exemplo, se você for um provedor de serviços, convém revendedores Inscrever clientes e gerenciá-los em seu nome. Ou, se você fizer parte de um grupo central de TI em uma empresa, você talvez queira delegar a entrada do usuário até outra equipe de TI.

Delegação torna mais fácil acessar e gerenciar mais usuários que você pode fazer por conta própria. A ilustração a seguir mostra um nível de delegação, mas o Azure Stack dá suporte a mais de um nível. Provedores delegados (DPs) podem delegar a outros provedores, até cinco níveis.

![Níveis de delegação](media/azure-stack-delegated-provider/image1.png)

## <a name="understand-delegation-roles-and-steps"></a>Entender as etapas e as funções de delegação

### <a name="delegation-roles"></a>Funções de delegação

As seguintes funções são parte da delegação:

* O *operador do Azure Stack* gerencia a infraestrutura do Azure Stack e cria um modelo de oferta. O operador delega a outras pessoas para fornecer ofertas aos seus usuários.

* Os operadores do Azure Stack delegados são chamados *delegada provedores*. Eles podem pertencer a outras organizações, como outros usuários do Active Directory (Azure AD).

* *Os usuários* se inscrever para ofertas e usá-los para o gerenciamento de suas cargas de trabalho, criando VMs, armazenamento de dados e assim por diante.

### <a name="delegation-steps"></a>Etapas de delegação

Há duas etapas básicas para configurar a delegação:

1. *Criar um provedor delegado* inscrevendo-se um usuário a uma oferta com base em um plano que tem apenas o serviço de assinaturas. Os usuários que assinem essa oferta, em seguida, podem estender as ofertas e inscreva-se de que os usuários para as ofertas.

1. *Delegar uma oferta para o provedor delegado*. Esta oferta é um modelo para o que o provedor de delegado pode oferecer. O provedor de delegado pode levar a oferta e oferecê-lo a outros usuários.

Elemento gráfico a seguir mostra as etapas para configurar a delegação.

![Criar o provedor delegado e habilitá-los inscrever usuários](media/azure-stack-delegated-provider/image2.png)

**Requisitos de provedor delegada**

Para atuar como um provedor de delegados, um usuário precisa para estabelecer uma relação com o provedor de principal com a criação de uma assinatura. Esta assinatura identifica o provedor delegado como tendo o direito de ofertas presentes em nome do provedor principal.

Depois que essa relação é estabelecida, o operador do Azure Stack pode delegar uma oferta para o provedor de delegado. O provedor de delegado pode levar a oferta, renomeá-lo (mas não alterar seu conteúdo) e oferecê-lo aos seus clientes.

## <a name="delegation-walkthrough"></a>Instruções passo a passo de delegação

As seções a seguir fornecem instruções práticas para configurar um provedor de delegados, delegar uma oferta e verificar que os usuários podem se inscrever para a oferta delegada.

### <a name="set-up-roles"></a>Configurar funções

Para usar este passo a passo, você precisa de duas contas do AD do Azure, além de sua conta do operador do Azure Stack. Se você não tiver essas duas contas, você precisa criá-los. As contas podem pertencer a qualquer usuário do Azure AD e são chamadas de provedor delegado e o usuário.

| **Função** | **Direitos organizacionais** |
| --- | --- |
| Provedor delegada |Usuário |
| Usuário |Usuário |

### <a name="identify-the-delegated-provider"></a>Identificar o provedor delegado

1. Entre no portal do administrador como um operador do Azure Stack.

1. Para criar uma oferta que permite que um usuário para se tornar um provedor de delegado:

   a.  [Criar um plano de](azure-stack-create-plan.md).
       Esse plano deve incluir somente o serviço de assinaturas. Este artigo usa um plano chamado **PlanForDelegation** como exemplo.

   b.  [Criar uma oferta](azure-stack-create-offer.md) com base nesse plano. Este artigo usa uma oferta denominada **OfferToDP** como exemplo.

   c.  Adicionar o provedor delegado como um assinante para esta oferta selecionando **inscrições** > **Add** > **nova assinatura de locatário**.

   ![Adicione o provedor delegado como um assinante](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Como com todas as ofertas do Azure Stack, você tem a opção de fazer a oferta de usuários públicos e permitindo se inscrever para ele, ou mantê-la privada e permitindo que o operador do Azure Stack gerencie a inscrição. Provedores delegados são, geralmente, um pequeno grupo. Você deseja controlar quem é admitido a ele, então manter esta oferta privada faz sentido na maioria dos casos.

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Operador de pilha do Azure cria a oferta delegada

A próxima etapa é criar o plano e oferta que você vai delegar e que seus usuários usarão. É uma boa ideia definir esta oferta, exatamente como você deseja que os usuários para vê-lo porque o provedor de delegado não é possível alterar os planos e cotas que ele inclui.

1. Como um operador do Azure Stack, [criar um plano](azure-stack-create-plan.md) e [uma oferta](azure-stack-create-offer.md) com base no plano. Este artigo usa uma oferta denominada **DelegatedOffer** como exemplo.

   > [!NOTE]
   > Esta oferta não precisa ser público, mas você pode torná-lo público se desejar. No entanto, na maioria dos casos você apenas deseja provedores delegados para ter acesso à oferta. Depois que você delega uma oferta em particular, conforme descrito nas etapas a seguir, o provedor de delegado tem acesso a ele.

1. Delegar a oferta. Vá para **DelegatedOffer**. Sob **as configurações**, selecione **provedores delegados** > **Add**.

1. Selecione a assinatura para o provedor de delegados na lista suspensa e, em seguida, selecione **delegado**.

   ![Adicionar um provedor delegado](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>Provedor delegada personaliza a oferta

Entrar no portal do usuário, como o provedor de delegado e, em seguida, crie uma nova oferta usando a oferta delegada como um modelo.

1. Selecione **novos** > **locatário oferece + planos** > **oferecem**.

    ![Criar uma nova oferta](media/azure-stack-delegated-provider/image5.png)

1. Atribua um nome para a oferta. Este artigo usa **ResellerOffer** como exemplo. Selecione a oferta delegada no qual baseá-la e, em seguida, selecione **criar**.

   ![Atribua um nome](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >É importante entender que, ao contrário de um operador do Azure Stack, um provedor de delegado não construa uma oferta de planos de base e planos de complemento. Provedores delegados só podem escolher ofertas são delegadas a eles, eles não podem fazer alterações para as ofertas.

1. Tornar a oferta público selecionando **navegue**e então **oferece**. Selecione a oferta e, em seguida, selecione **alterar estado**.

1. O provedor delegado expõe essas ofertas por meio do portal sua próprias URL. Essas ofertas são visíveis apenas por meio do portal do delegado. Para localizar e alterar essa URL:

    a.  Selecione **navegue** > **mais serviços** > **assinaturas**. Em seguida, selecione a assinatura de provedor delegada. Por exemplo, **DPSubscription** > **propriedades**.

    b.  Copie o portal de URL para um local separado, como o bloco de notas.

    ![Selecione a assinatura de provedor delegada](media/azure-stack-delegated-provider/dpportaluri.png)  

   Você terminou a criação de uma oferta delegada como um provedor de delegado. Saia como o provedor delegado e feche a janela do navegador que você está usando.

### <a name="sign-up-for-the-offer"></a>Inscreva-se a oferta

1. Em uma nova janela do navegador, vá para o portal de delegado URL que você salvou na etapa anterior. Entre no portal como um usuário.

   >[!NOTE]
   >As ofertas delegadas não são visíveis, a menos que você usar o portal de delegado.

1. No painel, selecione **obter uma assinatura**. Você verá que apenas as ofertas de delegados que foram criadas pelo provedor de delegados são apresentadas ao usuário.

   ![Exibir e selecionar ofertas](media/azure-stack-delegated-provider/image8.png)

O processo de delegar uma oferta é concluído. Agora um usuário pode se inscrever para esta oferta obtendo uma assinatura para ela.

## <a name="multiple-tier-delegation"></a>Delegação de várias camadas

A delegação de camadas múltiplas permite que um provedor delegado delegar a oferta para outras entidades. Por exemplo criar mais profundo revendedor canais where:

* O provedor que está gerenciando o Azure Stack delega uma oferta com um distribuidor.
* Os delegados de distribuidor com um revendedor.

Para criar vários níveis de delegação de oferta, o provedor delegado delega a oferta para o provedor seguinte. O processo é o mesmo para o provedor delegado, como era para o operador do Azure Stack. Para obter mais informações, consulte [operador do Azure Stack cria a oferta delegada](#cloud-operator-creates-the-delegated-offer).

## <a name="next-steps"></a>Próximas etapas

[Provisionar uma máquina virtual](azure-stack-provision-vm.md)