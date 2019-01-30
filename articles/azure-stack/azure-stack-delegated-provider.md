---
title: Delegando ofertas no Azure Stack | Microsoft Docs
description: Aprenda a colocar a outras pessoas responsável por criar ofertas e inscrição de usuários para você.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 1b22d465df12d313e05d3fd93437b461ef939ee7
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242477"
---
# <a name="delegate-offers-in-azure-stack"></a>Delegar ofertas no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Como o operador do Azure Stack, você geralmente deseja colocar a outras pessoas responsável por se inscrever usuários e criar assinaturas. Por exemplo, se você for um provedor de serviços, convém revendedores Inscrever clientes e gerenciá-los em seu nome. Ou, se você fizer parte de um grupo central de TI em uma empresa, você talvez queira delegar a inscrição do usuário para outra equipe de TI.

Delegação torna mais fácil acessar e gerenciar mais usuários que você pode fazer por conta própria, conforme mostrado na figura a seguir:

![Níveis de delegação](media/azure-stack-delegated-provider/image1.png)

Com a delegação, o provedor delegado gerencia uma oferta (oferta delegada) e clientes finais obter assinaturas sob essa oferta sem o envolvimento do administrador do sistema.

## <a name="understand-delegation-roles-and-steps"></a>Entender as etapas e as funções de delegação

### <a name="delegation-roles"></a>Funções de delegação

As seguintes funções são parte da delegação:

* O *operador do Azure Stack* gerencia a infraestrutura do Azure Stack e cria um modelo de oferta. O operador delega a outras pessoas para fornecer ofertas aos seus locatários.

* Os operadores do Azure Stack delegados são usuários com *proprietário* ou *Colaborador* direitos nas assinaturas chamado *delegada provedores*. Eles podem pertencer a outras organizações, como outros locatários do Azure Active Directory (Azure AD).

* *Os usuários* se inscrever para ofertas e usá-los para o gerenciamento de suas cargas de trabalho, criando VMs, armazenamento de dados e assim por diante.

### <a name="delegation-steps"></a>Etapas de delegação

Há duas etapas básicas para configurar a delegação:

1. **Criar uma assinatura de provedor delegada**: Inscrever-se um usuário a uma oferta que contém apenas o serviço de assinaturas. Os usuários que assinem essa oferta, em seguida, podem estender as ofertas delegadas a outros usuários assinando-os para as ofertas.

2. **Delegar uma oferta para o provedor delegado**: Essa oferta permite que o provedor delegado para criar assinaturas ou estender a oferta para seus usuários. O provedor de delegado pode levar a oferta e oferecê-lo a outros usuários.

A figura a seguir mostra as etapas para configurar a delegação:

![Criar o provedor delegado e habilitá-los inscrever usuários](media/azure-stack-delegated-provider/image2.png)

#### <a name="delegated-provider-requirements"></a>Requisitos de provedor delegada

Para atuar como um provedor de delegados, um usuário estabelece uma relação com o provedor de principal com a criação de uma assinatura. Esta assinatura identifica o provedor delegado como tendo o direito de apresentar as ofertas delegadas em nome do provedor principal.

Depois que essa relação é estabelecida, o operador do Azure Stack pode delegar uma oferta para o provedor de delegado. O provedor de delegado pode levar a oferta, renomeá-lo (mas não alterar seu conteúdo) e oferecê-lo aos seus clientes.

## <a name="delegation-walkthrough"></a>Instruções passo a passo de delegação

As seções a seguir fornecem instruções práticas para configurar um provedor de delegados, delegar uma oferta e verificar que os usuários podem se inscrever para a oferta delegada.

### <a name="set-up-roles"></a>Configurar funções

Para usar este passo a passo, você precisa de duas contas do AD do Azure, além de sua conta do operador do Azure Stack. Se você não tiver essas duas contas, você deve criá-los. As contas podem pertencer a qualquer usuário do Azure AD e são chamadas de provedor delegado e o usuário.

| **Função** | **Direitos organizacionais** |
| --- | --- |
| Provedor delegada |Usuário |
| Usuário |Usuário |

### <a name="identify-the-delegated-provider"></a>Identificar o provedor delegado

1. Entre no portal do administrador como um operador do Azure Stack.

1. Para criar uma oferta que permite que um usuário para se tornar um provedor de delegado:

    a.  [Criar um plano de](azure-stack-create-plan.md).
       Esse plano deve incluir somente o serviço de subscrição. Este artigo usa um plano chamado **PlanForDelegation** como exemplo.

   b.  [Criar uma oferta](azure-stack-create-offer.md) com base nesse plano. Este artigo usa uma oferta denominada **OfferToDP** como exemplo.

   c.  Adicionar o provedor delegado como um assinante para esta oferta selecionando **assinaturas**, em seguida, **adicionar**, em seguida, **nova assinatura de locatário**.

   ![Adicione o provedor delegado como um assinante](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Como com todas as ofertas do Azure Stack, você tem a opção de fazer a oferta de usuários públicos e permitindo se inscrever para ele, ou mantê-la privada e permitindo que o operador do Azure Stack gerencie a inscrição. Provedores delegados são, geralmente, um pequeno grupo. Você deseja controlar quem é admitido a ele, então manter esta oferta privada faz sentido na maioria dos casos.

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Operador de pilha do Azure cria a oferta delegada

A próxima etapa é criar o plano e oferta que você vai delegar e que seus usuários usarão. É uma boa ideia definir esta oferta, exatamente como você deseja que os usuários vejam, porque o provedor de delegado não é possível alterar os planos e cotas que ele inclui.

1. Como um operador do Azure Stack, [criar um plano](azure-stack-create-plan.md) e [uma oferta](azure-stack-create-offer.md) com base no plano. Este artigo usa uma oferta denominada **DelegatedOffer** como exemplo.

   > [!NOTE]
   > Esta oferta não precisa ser público, mas você pode torná-lo público. No entanto, na maioria dos casos você apenas deseja provedores delegados para ter acesso à oferta. Depois que você delega uma oferta em particular, conforme descrito nas etapas a seguir, o provedor de delegado tem acesso a ele.

2. Delegar a oferta. Vá para **DelegatedOffer**. Sob **as configurações**, selecione **provedores delegados**, em seguida, selecione **adicionar**.

3. Selecione a assinatura para o provedor de delegados na lista suspensa e, em seguida, selecione **delegado**.

   ![Adicionar um provedor delegado](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>Provedor delegada personaliza a oferta

Entrar no portal do usuário, como o provedor de delegado e, em seguida, crie uma nova oferta usando a oferta delegada como um modelo.

1. Selecione **+ criar um recurso**, em seguida, **planos + locatário oferece**, em seguida, selecione **oferecem**.

    ![Criar uma oferta](media/azure-stack-delegated-provider/image5.png)

2. Atribua um nome para a oferta. Este exemplo usa **ResellerOffer**. Selecione a oferta delegada no qual baseá-la e, em seguida, selecione **criar**.

   ![Atribua um nome](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >É importante entender que os provedores delegados só podem escolher ofertas são delegadas a eles. Eles não podem fazer alterações para as ofertas. Apenas um operador do Azure Stack pode alterar essas ofertas, por exemplo, alterando seus planos e cotas. Um provedor de delegado não constrói uma oferta de planos de base e planos de complemento.

3. O provedor de delegado pode tornar essas ofertas pública por meio do portal sua próprias URL. Para tornar a oferta público, selecione **navegue**e então **oferece**. Selecione a oferta e, em seguida, selecione **alterar estado**.

4. As ofertas delegadas públicas agora estão visíveis apenas por meio do portal do delegado. Para localizar e alterar essa URL:

     a.  Selecione **procurar**, em seguida, **todos os serviços**e, na **geral** categoria, selecione **assinaturas**. Selecione a assinatura de provedor delegada; Por exemplo, **DPSubscription**, em seguida, **propriedades**.

    b.  Copie o portal de URL para um local separado, como o bloco de notas.

    ![Selecione a assinatura de provedor delegada](media/azure-stack-delegated-provider/dpportaluri.png)  

   Você terminou a criação de uma oferta delegada como um provedor de delegado. Saia como o provedor delegado e feche a janela do navegador.

### <a name="sign-up-for-the-offer"></a>Inscreva-se a oferta

1. Em uma nova janela do navegador, vá para o portal de delegado URL que você salvou na etapa anterior. Entre no portal como um usuário.

   >[!NOTE]
   >As ofertas delegadas não são visíveis, a menos que você usar o portal de delegado.

1. No painel, selecione **obter uma assinatura**. Você verá que apenas as ofertas de delegados que foram criadas pelo provedor de delegados são apresentadas ao usuário.

   ![Exibir e selecionar ofertas](media/azure-stack-delegated-provider/image8.png)

O processo de delegar uma oferta é concluído. Agora um usuário pode se inscrever para esta oferta obtendo uma assinatura a ele.

## <a name="move-subscriptions-between-delegated-providers"></a>Mover assinaturas entre provedores delegados

Se necessário, uma assinatura pode ser movida entre as assinaturas de provedor delegada novos ou existentes que pertencem ao mesmo locatário do diretório. Isso é feito usando o cmdlet do PowerShell [movimentação AzsSubscription](/powershell/module/azs.subscriptions.admin).

Isso é útil quando:

* Integrar um novo membro da equipe que levarão a função de provedor delegada e você deseja atribuir a esse assinaturas de usuário membro de equipe que foram criados anteriormente na assinatura padrão do provedor.
* Você tiver várias assinaturas de provedores delegados no mesmo locatário do diretório (Azure Active Directory) e precisa migrar assinaturas de usuário entre eles. Esse cenário pode ser um caso em que um membro da equipe se movem entre equipes e sua assinatura deve ser alocado para a nova equipe.

## <a name="next-steps"></a>Próximas etapas

* [Provisionar uma máquina virtual](azure-stack-provision-vm.md)