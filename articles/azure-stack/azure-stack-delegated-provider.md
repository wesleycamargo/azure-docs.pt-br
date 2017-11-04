---
title: Delegando ofertas na pilha do Azure | Microsoft Docs
description: "Saiba como colocar outras pessoas responsável por criar ofertas e os usuários de assinatura para você."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: 157f0207-bddc-42e5-8351-197ec23f9d46
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: alfredop
ms.openlocfilehash: 1a1f2789076b610ee557bde5981a331c55cc1c95
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="delegate-offers-in-azure-stack"></a>Delegar ofertas no Azure Stack

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Como o operador de pilha do Azure, você geralmente deseja colocar outras pessoas responsável por criar ofertas e usuários de assinatura. Por exemplo, se você for um provedor de serviço, convém revendedores Inscrever clientes e gerenciá-los em seu nome. Ou, se você é membro de um grupo central de TI em uma empresa, convém subsidiárias para inscrever-se os usuários sem a intervenção do usuário.

A delegação ajuda a essas tarefas, tornando possível acessar e gerenciar mais usuários que você pode diretamente. A ilustração a seguir mostra um nível de delegação, mas a pilha do Azure oferece suporte a vários níveis. Provedores de delegado (DPs) por sua vez podem delegar a outros provedores, até cinco níveis.

![Níveis de delegação](media/azure-stack-delegated-provider/image1.png)

Operadores de pilha do Azure podem delegar a criação de usuários de outros usuários e ofertas usando a funcionalidade de delegação.

## <a name="roles-and-steps-in-delegation"></a>Funções e as etapas de delegação
Para entender a delegação, tenha em mente que há três funções envolvidas:

* O *operador Azure pilha* gerencia a infraestrutura de pilha do Azure, cria um modelo de oferta e delega a outras pessoas a oferecem aos usuários.

* Os operadores do Azure pilha delegados são chamados *delegado provedores*. Eles podem pertencer a outras organizações, como outros usuários do Active Directory do Azure (AD do Azure).

* *Os usuários* inscrever-se para as ofertas e usá-los para gerenciar suas cargas de trabalho, criar máquinas virtuais, armazenamento de dados e assim por diante.

Conforme mostrado no gráfico a seguir, há duas etapas para configurar a delegação.

1. *Identificar os provedores de delegado*. Você pode fazer isso ao inscrever-se a uma oferta com base em um plano que contém apenas o serviço de assinaturas. Usuários que assinaram esta oferta de aquisição de algumas das funcionalidades do operador de pilha do Azure, incluindo a capacidade de ampliar as ofertas e entre os usuários para eles.

2. *Delegar uma oferta para o provedor de delegado*. Esta oferta funciona como um modelo para que o provedor de delegado pode oferecer. O provedor de delegado agora pode levar a oferta. Escolha um nome para ele (mas não altere seus serviços e cotas) e oferecê-lo aos clientes.

![Criar o provedor delegado e habilitá-los inscrever usuários](media/azure-stack-delegated-provider/image2.png)

Para atuar como provedores de delegado, os usuários precisam estabelecer uma relação com o provedor principal. Em outras palavras, eles precisam criar uma assinatura. Nesse cenário, essa assinatura identifica os provedores de delegados como tendo o direito de ofertas presentes em nome do provedor principal.

Depois que essa relação foi estabelecida, o operador de pilha do Azure pode delegar uma oferta para o provedor de delegado. O provedor de delegado agora é capaz de levar a oferta, renomeá-la (mas não alterar seu conteúdo) e oferecem a seus clientes.

As seções a seguir descrevem como estabelecer um provedor de delegado, delegar uma oferta e verifique se que os usuários podem inscrever-se para ele.

## <a name="set-up-roles"></a>Configurar funções

Para ver um provedor de delegado no trabalho, você precisa do Azure adicional contas do AD além de sua conta do operador de pilha do Azure. Se você não os tiver, crie duas contas. As contas podem pertencer a qualquer usuário do AD do Azure. Nós nos referimos a eles como o provedor de delegado e o usuário.

| **Função** | **Direitos organizacionais** |
| --- | --- |
| Provedor de delegados |Usuário |
| Usuário |Usuário |

## <a name="identify-the-delegated-providers"></a>Identificar os provedores de delegados
1. Entrar como um operador de pilha do Azure.

2. Crie a oferta que permite que os usuários se tornam delegados provedores:
   
   a.  [Criar um plano de](azure-stack-create-plan.md).
       Esse plano deve incluir somente o serviço de assinaturas. Neste artigo, usamos um plano chamado **PlanForDelegation**.
   
   b.  [Criar uma oferta](azure-stack-create-offer.md) com base nesse plano. Neste artigo, usamos uma oferta chamada **OfferToDP**.
   
   c.  Após a conclusão da criação da oferta, adicione o provedor delegado como um assinante para esta oferta. Isso é feito selecionando **assinaturas** > **adicionar** > **nova assinatura de locatário**.
   
   ![Adicione o provedor de delegado como um assinante](media/azure-stack-delegated-provider/image3.png)

> [!NOTE]
> Como com todas as ofertas de pilha do Azure, você tem a opção de fazer a oferta públicos e que ajuda os usuários se inscrever para ele, ou mantê-lo particular e permitindo que o operador de pilha do Azure gerenciar a inscrição. Provedores de delegados são normalmente um pequeno grupo. Você deseja controlar quem é alocada a ele, para que manter esta oferta privada faz sentido na maioria dos casos.
> 
> 

## <a name="azure-stack-operator-creates-the-delegated-offer"></a>Operador de pilha do Azure cria a oferta delegada

O provedor de delegado agora estabelecida. A próxima etapa é criar o plano e a oferta que você vai delegar e que os clientes usarão. É recomendável definir esta oferta exatamente como deseja que os clientes para vê-lo porque o provedor delegado não será possível alterar os planos e cotas inclui.

1. Como um operador de pilha do Azure, [criar um plano de](azure-stack-create-plan.md) e [uma oferta](azure-stack-create-offer.md) com base nele. Neste artigo, usamos uma oferta chamada **DelegatedOffer.**
   
   > [!NOTE]
   > Esta oferta não precisa ser público. Se você escolher, pode tornar público. Na maioria dos casos, no entanto, você precisará apenas provedores delegados para ter acesso a ele. Depois que você delega uma oferta privada, conforme descrito nas etapas a seguir, o provedor de delegado tem acesso a ele.
   > 
   > 
1. Delega a oferta. Vá para **DelegatedOffer.** Em seguida, no **configurações** painel, selecione **provedores delegado** > **adicionar**.

2. Selecione a assinatura para o provedor de delegado na caixa de listagem suspensa e, em seguida, selecione **delegado**.

> ![Adicionar um provedor de delegados](media/azure-stack-delegated-provider/image4.png)
> 
> 

## <a name="delegated-provider-customizes-the-offer"></a>Provedor de delegado personaliza a oferta

Entrar no portal do usuário, como o provedor de delegado. Crie uma nova oferta, usando a oferta delegada como um modelo.

1. Selecione **novo** > **locatário oferece + planos** > **oferecem**.

    ![Criar uma nova oferta](media/azure-stack-delegated-provider/image5.png)


1. Atribua um nome para a oferta. Aqui, escolhemos **ResellerOffer**. Selecione a oferta delegada nos quais baseá-la e, em seguida, selecione **criar**.
   
   ![Atribua um nome](media/azure-stack-delegated-provider/image6.png)

    >[!NOTE] 
    > Observe a diferença comparada para oferecer criação como experientes, o operador de pilha do Azure. O provedor de delegado não constrói a oferta de planos de base e planos de complemento. Eles só podem escolher entre ofertas que tenham sido delegadas a eles e não podem fazer alterações para essas ofertas.

1. Publique a oferta selecionando **procurar**e, em seguida, **oferece**. Selecione a oferta e, em seguida, selecione **alterar estado**.

2. O provedor de delegado expõe essas ofertas por meio de seu próprio portal URL. Essas ofertas estão visíveis apenas através do portal do delegado. Para localizar e alterar esta URL:
   
    a.  Selecione **procurar** > **mais serviços** >  **assinaturas**. Em seguida, selecione a assinatura do delegado de provedor. Em nosso caso, ele tem **DPSubscription** > **propriedades**.
   
    b.  Copie o portal de URL para um local separado, como o bloco de notas.
   
    ![Selecione a assinatura do delegado de provedor](media/azure-stack-delegated-provider/dpportaluri.png)  
   
   Agora você criou uma oferta delegada como um provedor de delegado. Saia como o provedor de delegado. Feche a janela de navegador que você usou.

## <a name="sign-up-for-the-offer"></a>Inscreva-se para a oferta
1. Em uma nova janela do navegador, vá para o portal de delegado URL que você salvou na etapa anterior. Entrar no portal como um usuário. 
   
   >[!NOTE]
   > Use o portal delegado para esta etapa. O delegado oferece caso contrário, não é visível.

2. No painel, selecione **obter uma assinatura**. Você verá que somente as ofertas delegadas que foram criadas pelo provedor de delegados são apresentadas ao usuário:

> ![Exibir e selecionar as ofertas](media/azure-stack-delegated-provider/image8.png)
> 
> 

O processo de delegação de oferta é concluído. O usuário agora pode inscrever para esta oferta obtendo uma assinatura para ele.

## <a name="multiple-tier-delegation"></a>Delegação de multicamadas

Delegação de multicamadas habilita o provedor delegado delegar a oferta para outras entidades. Isso permite, por exemplo, a criação de mais canais de revendedor, no qual o provedor que está gerenciando o Azure pilha delega uma oferta com um distribuidor. Esse distribuidor, por sua vez, delega para um revendedor. A pilha do Azure oferece suporte a até cinco níveis de delegação.

Para criar vários níveis de delegação de oferta, o provedor de delegado por sua vez delega a oferta ao próximo provedor. O processo é o mesmo para o provedor de delegados como era para o operador de pilha do Azure (consulte [operador de pilha do Azure cria a oferta delegada](#cloud-operator-creates-the-delegated-offer)).

## <a name="next-steps"></a>Próximas etapas
[Provisionar uma máquina virtual](azure-stack-provision-vm.md)

