---
title: Tornar as máquinas virtuais disponíveis para seus usuários de pilha do Azure | Microsoft Docs
description: Saiba como tornar as máquinas virtuais disponíveis na pilha do Azure
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
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 9329cb0dbfa24cf239b820573ef7f642cdca9103
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248152"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Tutorial: tornar máquinas virtuais disponíveis para seus usuários de pilha do Azure

Como um administrador de nuvem de pilha do Azure, você pode criar ofertas que seus usuários (às vezes chamados de locatários) podem se inscrever. Inscrevendo-se a uma oferta, os usuários podem consumir os serviços de pilha do Azure que fornece uma oferta.

Este tutorial mostra como criar uma oferta para uma máquina virtual e faça logon como um usuário para testar a oferta.

O que você aprenderá:

> [!div class="checklist"]
> * Criar uma oferta
> * Adicionar uma imagem
> * Testar a oferta

Na pilha do Azure, os serviços são fornecidos para usuários que usam assinaturas, ofertas e planos. Os usuários podem assinar várias ofertas. Uma oferta pode ter um ou mais planos e um plano pode ter um ou mais serviços.

![Planos, ofertas e assinaturas](media/azure-stack-key-features/image4.png)

Para obter mais informações, consulte [principais recursos e conceitos no Azure pilha](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Criar uma oferta

Ofertas são grupos de um ou mais planos provedores apresentam aos usuários para comprar ou assinar. O processo de criação de uma oferta tem várias etapas. Primeiro, você será solicitado a criar a oferta, um plano e, finalmente, as cotas.

1. [Entrar](azure-stack-connect-azure-stack.md) o portal como um administrador de nuvem e, em seguida, selecione **novo** > **oferece + planos** > **oferecem**.

   ![Nova oferta](media/azure-stack-tutorial-tenant-vm/image01.png)

2. Em **oferecem nova**, insira um **nome de exibição** e **nome do recurso**e, em seguida, selecione um novo ou existente **grupo de recursos**. O Nome de Exibição é o nome amigável da oferta. Somente o operador de nuvem pode ver o nome do recurso. Esse é o nome que os administradores usam para trabalhar com a oferta como um recurso do Gerenciador de Recursos do Azure.

   ![Nome de exibição](media/azure-stack-tutorial-tenant-vm/image02.png)

3. Selecione **Base planos**e no **plano** seção, selecione **adicionar** para adicionar um novo plano para a oferta.

   ![Adicionar um plano](media/azure-stack-tutorial-tenant-vm/image03.png)

4. No **novo plano** seção, preencha **nome de exibição** e **nome do recurso**. O nome de exibição é o nome amigável do plano do que os usuários veem. Somente o operador de nuvem pode ver o nome do recurso. É o nome que os operadores de nuvem usam para trabalhar com o plano como um recurso do Gerenciador de recursos do Azure.

   ![Nome de exibição do plano](media/azure-stack-tutorial-tenant-vm/image04.png)

5. Selecione **serviços**. Na lista de serviços, escolha **Microsoft. Compute**, **Network**, e **Microsoft**. Escolha **selecione** para adicionar esses serviços ao plano.

   ![Serviços do plano](media/azure-stack-tutorial-tenant-vm/image05.png)

6. Selecione **cotas**e, em seguida, selecione o primeiro serviço que você deseja criar uma cota para. Para uma cota de IaaS, use o exemplo a seguir como um guia para configurar cotas para os serviços de computação, rede e armazenamento.

   - Primeiro, crie uma cota para o serviço de computação. Na lista de namespace, selecione **Microsoft. Compute** e, em seguida, selecione **criar nova cota**.

     ![Criar nova cota](media/azure-stack-tutorial-tenant-vm/image06.png)

   - Em **criar cota**, insira um nome para a cota. Você pode alterar ou aceite os valores de cota que são mostradas para a cota que você está criando. Neste exemplo, podemos aceitar as configurações padrão e selecione **Okey**.

     ![Nome da cota](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Escolher **Microsoft. Compute** na lista de namespace e, em seguida, selecione a cota que você criou. Isso vincula a cota para o serviço de computação.

     ![Selecione cota](media/azure-stack-tutorial-tenant-vm/image08.png)

      Repita essas etapas para os serviços de rede e armazenamento. Quando terminar, selecione **Okey** na **cotas** para salvar todas as cotas.

7. Em **novo plano**, selecione **Okey**.

8. Em **plano**, selecione o novo plano e, em seguida, **selecione**.

9. Em **nova oferta**, selecione **criar**. Você verá uma notificação quando a oferta é criada.

10. No menu do painel, selecione **oferece** e, em seguida, escolher a oferta que você criou.

11. Selecione **alterar estado**e, em seguida, escolher **público**.

    ![Estado público](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Adicionar uma imagem

Antes de você pode provisionar máquinas virtuais, você deve adicionar uma imagem do Marketplace do Azure pilha. Você pode adicionar a imagem de sua escolha, incluindo as imagens de Linux do Azure Marketplace.

Se você estiver operando em um cenário conectado e se você tiver registrado sua instância de pilha do Azure com o Azure, em seguida, você pode baixar a imagem de VM do Windows Server 2016 do Azure Marketplace usando as etapas descritas no [Download marketplace itens do Azure para o Azure pilha](azure-stack-download-azure-marketplace-item.md) tópico.

Para obter informações sobre como adicionar itens diferentes para o marketplace, consulte [o Azure Marketplace de pilha](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testar a oferta

Agora que você criou uma oferta, você pode testá-lo. Você vai entrar como um usuário, assinar a oferta e, em seguida, adicionar uma máquina virtual.

1. **Assinar uma oferta**

   a. Entre no portal do usuário com uma conta de usuário e selecione o **obter uma assinatura** lado a lado.
   - Para um sistema integrado, a URL varia com base na região do operador e o nome de domínio externo e será no formato https://portal.&lt; *região*&gt;.&lt; *FQDN*&gt;.
   - Se você estiver usando o Kit de desenvolvimento de pilha do Azure, o endereço do portal é https://portal.local.azurestack.external.

   ![Obtenha uma assinatura](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. Em **obter uma assinatura**, insira um nome para sua assinatura do **nome de exibição** campo. Selecione **oferecem**e, em seguida, escolha uma das ofertas no **escolha uma oferta de** lista. Clique em **Criar**.

   ![Criar uma oferta](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Para exibir a assinatura, selecione **mais serviços**e, em seguida, selecione **assinaturas**. Selecione sua nova assinatura para ver os serviços que fazem parte da assinatura.

   >[!NOTE]
   >Após assinar uma oferta, você terá que atualizar o portal para ver os serviços que fazem parte da nova assinatura.

2. **Provisionar uma máquina virtual**

   Do portal do usuário, você pode provisionar uma máquina virtual usando a nova assinatura.

   a. Entrar no portal do usuário com uma conta de usuário.
      - Para um sistema integrado, a URL varia com base na região do operador e o nome de domínio externo e será no formato https://portal.&lt; *região*&gt;.&lt; *FQDN*&gt;.
   - Se você estiver usando o Kit de desenvolvimento de pilha do Azure, o endereço do portal é https://portal.local.azurestack.external.

   b.  No painel, selecione **novo** > **de computação** > **Datacenter avaliação do Windows Server 2016**e, em seguida, selecione **criar**.

   c. Em **Noções básicas de**, forneça as seguintes informações:
      - Insira um **nome**
      - Insira um **nome de usuário**
      - Insira um **senha**
      - Escolha uma **Assinatura**
      - Criar um **grupo de recursos** (ou selecione um existente.) 
      - Selecione **Okey** para salvar essas informações.

   d. Em **escolher um tamanho de**, selecione **A1 padrão**e, em seguida, **selecione**.  

   e. Em **configurações**, selecione **rede Virtual**.

   f. Em **rede virtual escolha**, selecione **criar novo**.

   g. Em **criar rede virtual**, aceite todos os padrões e selecione **Okey**.

   h. Selecione **Okey** na **configurações** para salvar a configuração de rede.

   ![Criar rede virtual](media/azure-stack-provision-vm/image04.png)

   i. Em **resumo**, selecione **Okey** para criar a máquina virtual.  

   j. Para ver a nova máquina virtual, selecione **todos os recursos**. Pesquisar para a máquina virtual e selecione seu nome nos resultados da pesquisa.

   ![Todos os recursos](media/azure-stack-provision-vm/image06.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar uma oferta
> * Adicionar uma imagem
> * Testar a oferta

Avançar para o próximo tutorial para aprender como:
> [!div class="nextstepaction"]
> [Disponibilizar os bancos de dados SQL para os usuários de pilha do Azure](azure-stack-tutorial-sql-server.md)