---
title: Tornar as máquinas virtuais disponíveis para seus usuários do Azure Stack | Microsoft Docs
description: Saiba como disponibilizar as máquinas virtuais no Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 7c4f8fd3d7a4885893a126c63776a5e241dc0f91
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246527"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Tutorial: tornar as máquinas virtuais disponíveis para seus usuários do Azure Stack

Como um administrador de nuvem do Azure Stack, você pode criar ofertas que seus usuários (também conhecidos como locatários) podem assinar. Ao assinar uma oferta, os usuários podem consumir os serviços do Azure Stack que fornece uma oferta.

Este tutorial mostra como criar uma oferta para uma máquina virtual e, em seguida, entre como um usuário de teste a oferta.

O que você aprenderá:

> [!div class="checklist"]
> * Criar uma oferta
> * Adicionar uma imagem
> * Testar a oferta

No Azure Stack, os serviços são entregues aos usuários que usam assinaturas, ofertas e planos. Os usuários podem assinar várias ofertas. Uma oferta pode ter um ou mais planos, e um plano pode ter um ou mais serviços.

![As assinaturas, ofertas e planos](media/azure-stack-key-features/image4.png)

Para obter mais informações, consulte [principais recursos e conceitos no Azure Stack](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Criar uma oferta

As ofertas são grupos de um ou mais planos que os provedores apresentam aos usuários para comprar ou inscrever-se. O processo de criação de uma oferta tem várias etapas. Primeiro, você será solicitado a criar a oferta, em seguida, um plano e, finalmente, as cotas.

1. [Entrar no](azure-stack-connect-azure-stack.md) no portal como um administrador de nuvem e em seguida, selecione **+ criar um recurso** > **oferece + planos** > **oferecem**.

   ![Nova oferta](media/azure-stack-tutorial-tenant-vm/image01.png)

1. Na **nova oferta**, insira um **nome de exibição** e **nome do recurso**e, em seguida, selecione uma nova ou existente **grupo de recursos**. O nome de exibição é o nome amigável da oferta. Somente o operador de nuvem pode ver o nome do recurso que é o nome que os administradores usam para trabalhar com a oferta como um recurso do Azure Resource Manager.

   ![Nome de exibição](media/azure-stack-tutorial-tenant-vm/image02.png)

1. Selecione **planos Base**e, nas **plano** seção, selecione **Add** para adicionar um novo plano à oferta.

   ![Adicionar um plano](media/azure-stack-tutorial-tenant-vm/image03.png)

1. No **novo plano** seção, preencha **nome de exibição** e **nome do recurso**. O nome de exibição é o nome amigável do plano do que os usuários veem. Somente o operador de nuvem pode ver o nome do recurso que é o nome que os operadores de nuvem usam para trabalhar com o plano como um recurso do Azure Resource Manager.

   ![Nome de exibição do plano](media/azure-stack-tutorial-tenant-vm/image04.png)

1. Selecione **Services**. Na lista de serviços, escolha **Microsoft. Compute**, **Network**, e **Microsoft. Storage**. Escolher **selecionar** para adicionar esses serviços ao plano.

   ![Serviços do plano](media/azure-stack-tutorial-tenant-vm/image05.png)

1. Selecione **cotas**e, em seguida, selecione o primeiro serviço que você deseja criar uma cota para. Para uma cota de IaaS, use o exemplo a seguir como um guia para configurar as cotas para os serviços de computação, rede e armazenamento.

   - Primeiro, crie uma cota para o serviço de computação. Na lista de namespace, selecione **Microsoft. Compute** e, em seguida, selecione **criar nova cota**.

     ![Criar nova cota](media/azure-stack-tutorial-tenant-vm/image06.png)

   - Na **criar cota**, insira um nome para a cota. Você pode alterar ou aceitar qualquer um dos valores de cota que são mostrados para a cota que você está criando. Neste exemplo, podemos aceitar as configurações padrão e selecione **Okey**.

     ![Nome da cota](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Escolher **Microsoft. Compute** na lista de namespace e, em seguida, selecione a cota que você criou. Isso vincula a cota para o serviço de computação.

     ![Selecione cota](media/azure-stack-tutorial-tenant-vm/image08.png)

      Repita essas etapas para os serviços de rede e armazenamento. Quando tiver terminado, selecione **Okey** na **cotas** para salvar todas as cotas.

1. Na **novo plano**, selecione **Okey**.

1. Sob **plano**, selecione o novo plano e, em seguida **selecione**.

1. Na **nova oferta**, selecione **criar**. Você verá uma notificação quando a oferta é criada.

1. No menu do painel, selecione **oferece** e, em seguida, escolher a oferta que você criou.

1. Selecione **alterar estado**e, em seguida, escolha **público**.

    ![Estado público](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Adicionar uma imagem

Antes de você pode provisionar máquinas virtuais, você deve adicionar uma imagem no Marketplace do Azure Stack. Você pode adicionar a imagem de sua escolha, incluindo imagens do Linux, do Azure Marketplace.

Se você estiver operando em um cenário conectado e se você registrou sua instância do Azure Stack com o Azure, em seguida, você pode baixar a imagem de VM do Windows Server 2016 no Azure Marketplace usando as etapas descritas no [marketplace de Download itens do Azure para o Azure Stack](azure-stack-download-azure-marketplace-item.md) tópico.

Para obter informações sobre como adicionar itens diferentes no Marketplace, consulte [o Azure Stack Marketplace](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testar a oferta

Agora que você criou uma oferta, você pode testá-lo. Você deve entrar como um usuário, assine a oferta e, em seguida, adicionar uma máquina virtual.

1. **Assinar uma oferta**

    a. Entre no portal do usuário com uma conta de usuário e selecione o **obter uma assinatura** lado a lado.
   - Para um sistema integrado, a URL varia com base na região de seu operador e o nome de domínio externo e será no formato https://portal.&lt; *região*&gt;.&lt; *FQDN*&gt;.
   - Se você estiver usando o Kit de desenvolvimento do Azure Stack, o endereço do portal é https://portal.local.azurestack.external.

   ![Obtenha uma assinatura](media/azure-stack-tutorial-tenant-vm/image10.png)

   b. Na **obter uma assinatura**, insira um nome para sua assinatura na **nome de exibição** campo. Selecione **oferecem**e, em seguida, escolha uma das ofertas na **escolher uma oferta** lista. Selecione **Criar**.

   ![Criar uma oferta](media/azure-stack-tutorial-tenant-vm/image11.png)

   c. Para exibir a assinatura, selecione **todos os serviços**e, em seguida, sob o **gerais** categoria, selecione **assinaturas**. Selecione sua nova assinatura para ver quais serviços fazem parte da assinatura.

   >[!NOTE]
   >Depois de assinar uma oferta, você talvez precise atualizar o portal para ver quais serviços fazem parte da nova assinatura.

1. **Provisionar uma máquina virtual**

   A partir do portal do usuário, você pode provisionar uma máquina virtual usando a nova assinatura.

    a. Entrar no portal do usuário com uma conta de usuário.
      - Para um sistema integrado, a URL varia com base na região de seu operador e o nome de domínio externo e será no formato https://portal.&lt; *região*&gt;.&lt; *FQDN*&gt;.
   - Se você estiver usando o Kit de desenvolvimento do Azure Stack, o endereço do portal é https://portal.local.azurestack.external.

   b.  No painel, selecione **+ criar um recurso** > **computação** > **Windows Server 2016 Datacenter Eval**e, em seguida, selecione **Criar**.

   c. Na **Noções básicas de**, forneça as seguintes informações:
      - Insira um **nome**
      - Insira um **nome de usuário**
      - Insira um **senha**
      - Escolha uma **Assinatura**
      - Criar uma **grupo de recursos** (ou selecione um existente). 
      - Selecione **Okey** para salvar essas informações.

   d. No **escolher um tamanho**, selecione **padrão A1**e então **selecione**.  

   e. Na **as configurações**, selecione **rede Virtual**.

   f. Na **Escolher rede virtual**, selecione **criar novo**.

   g. Na **criar rede virtual**, aceite todos os padrões e selecione **Okey**.

   h. Selecione **Okey** na **configurações** para salvar a configuração de rede.

      i. Na **resumo**, selecione **Okey** para criar a máquina virtual.  

   j. Para ver a nova máquina virtual, selecione **todos os recursos**. Procure a máquina virtual e selecione seu nome dos resultados da pesquisa.

   
## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar uma oferta
> * Adicionar uma imagem
> * Testar a oferta

Avance para o próximo tutorial para aprender como:
> [!div class="nextstepaction"]
> [Disponibilizar os bancos de dados SQL para os usuários do Azure Stack](azure-stack-tutorial-sql-server.md)