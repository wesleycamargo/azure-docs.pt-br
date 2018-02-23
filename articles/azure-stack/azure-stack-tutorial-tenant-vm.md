---
title: "Tornar as máquinas virtuais disponíveis para seus usuários de pilha do Azure | Microsoft Docs"
description: "Tutorial para disponibilizar as máquinas virtuais na pilha do Azure"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2017
ms.author: brenduns
ms.reviewer: 
ms.custom: mvc
ms.openlocfilehash: d169a18df8916cfb8dd30b00248099a3d374aacc
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="make-virtual-machines-available-to-your-azure-stack-users"></a>Tornar as máquinas virtuais disponíveis para seus usuários de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Como um administrador de nuvem de pilha do Azure, você pode criar ofertas que seus usuários (às vezes chamados de locatários) podem se inscrever. Usando sua assinatura, os usuários podem consumir serviços da pilha do Azure.

Este artigo mostra como criar uma oferta e, em seguida, testá-lo. Para o teste, faça logon no portal como um usuário, assinar a oferta e, em seguida, criar uma máquina virtual usando a assinatura.

O que você aprenderá:

> [!div class="checklist"]
> * Criar uma oferta
> * Adicionar uma imagem
> * Testar a oferta


Na pilha do Azure, os serviços são fornecidos para usuários que usam assinaturas, ofertas e planos. Os usuários podem assinar várias ofertas. As ofertas podem ter um ou mais planos, e os planos podem ter um ou mais serviços.

![Planos, ofertas e assinaturas](media/azure-stack-key-features/image4.png)

Para obter mais informações, consulte [principais recursos e conceitos no Azure pilha](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Criar uma oferta

Agora você pode obter as coisas pronto para seus usuários. Quando você inicia o processo, você primeiro será solicitado a criar a oferta, um plano e, finalmente, as cotas.

3. **Criar uma oferta**

   Ofertas são grupos de um ou mais planos provedores apresentam aos usuários para comprar ou assinar.

   a. [Entrar](azure-stack-connect-azure-stack.md) para o portal como um administrador de nuvem e clique **novo** > **oferece + planos** > **oferecem**.
   ![nova oferta](media/azure-stack-tutorial-tenant-vm/image01.png)

   b. No **oferecem nova** seção, preencha **nome de exibição** e **nome do recurso**e, em seguida, selecione um novo ou existente **grupo de recursos**. O Nome de Exibição é o nome amigável da oferta. Somente o operador de nuvem pode ver o nome do recurso. Esse é o nome que os administradores usam para trabalhar com a oferta como um recurso do Gerenciador de Recursos do Azure.

   ![Nome de exibição](media/azure-stack-tutorial-tenant-vm/image02.png)

   c. Clique em **Base planos**e no **plano** seção, clique em **adicionar** para adicionar um novo plano para a oferta.

   ![Adicionar um plano](media/azure-stack-tutorial-tenant-vm/image03.png)

   d. No **novo plano** seção, preencha **nome de exibição** e **nome do recurso**. O nome de exibição é o nome amigável do plano do que os usuários veem. Somente o operador de nuvem pode ver o nome do recurso. É o nome que os operadores de nuvem usam para trabalhar com o plano como um recurso do Gerenciador de recursos do Azure.

   ![Nome de exibição do plano](media/azure-stack-tutorial-tenant-vm/image04.png)

   e. Clique em **serviços**, selecione **Microsoft. Compute**, **Network**, e **Microsoft**e, em seguida, clique em **Selecionar**.

   ![Serviços do plano](media/azure-stack-tutorial-tenant-vm/image05.png)

   f. Clique em **cotas**e, em seguida, selecione o primeiro para o qual você deseja criar uma cota de serviço. Para uma cota de IaaS, siga estas etapas para os serviços de computação, rede e armazenamento.

   Neste exemplo, vamos primeiro criar uma cota para o serviço de computação. Na lista de namespace, selecione o **Microsoft. Compute** namespace e, em seguida, clique em **criar nova cota**.
   
   ![Criar nova cota](media/azure-stack-tutorial-tenant-vm/image06.png)

   g. Sobre o **criar cota** seção, digite um nome para a cota e defina os parâmetros desejados para a cota e clique em **Okey**.

   ![Nome da cota](media/azure-stack-tutorial-tenant-vm/image07.png)

   h. Agora, para **Microsoft. Compute**, selecione a cota que você criou.

   ![Selecione cota](media/azure-stack-tutorial-tenant-vm/image08.png)

   Repita essas etapas para os serviços de rede e armazenamento e, em seguida, clique em **Okey** no **cotas** seção.

   i. Clique em **Okey** no **novo plano** seção.

   j. Sobre o **plano** seção, selecione o novo plano e clique em **selecione**.

   k. Sobre o **nova oferta** seção, clique em **criar**. Você verá uma notificação quando a oferta foi criada.

   l. No menu do painel, clique em **oferece** e, em seguida, clique na oferta que você criou.

   m. Clique em **alterar estado**e, em seguida, clique em **público**.

   ![Estado público](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Adicionar uma imagem

Antes de você pode provisionar máquinas virtuais, você deve adicionar uma imagem do Marketplace do Azure pilha. Você pode adicionar a imagem de sua escolha, incluindo as imagens de Linux do Azure Marketplace.

Se você estiver operando em um cenário conectado e se você tiver registrado sua instância de pilha do Azure com o Azure, em seguida, você pode baixar a imagem de VM do Windows Server 2016 do Azure Marketplace usando as etapas descritas no [Download marketplace itens do Azure para o Azure pilha](azure-stack-download-azure-marketplace-item.md) tópico.

Para obter informações sobre como adicionar itens diferentes para o marketplace, consulte [o Azure Marketplace de pilha](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testar a oferta

Agora que você criou uma oferta, você pode testá-lo. Faça logon como um usuário e assinar a oferta e, em seguida, adicionar uma máquina virtual.

1. **Assinar uma oferta**

   Agora você pode fazer logon no portal do como um usuário para assinar uma oferta.

   a. Faça logon portal do usuário como um usuário e clique em **obter uma assinatura**.
   - Para um sistema integrado, a URL varia com base na região do operador e o nome de domínio externo e será em https://portal o formato. &lt; *região*&gt;.&lt; *FQDN*&gt;.
   - Se você estiver usando o Kit de desenvolvimento de pilha do Azure, o endereço do portal é https://portal.local.azurestack.external.

   ![Obtenha uma assinatura](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. No **nome de exibição** campo, digite um nome para sua assinatura, clique em **oferecem**, clique em uma das ofertas no **escolha uma oferta de** seção e, em seguida, clique em  **Criar**.

   ![Criar uma oferta](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Para exibir a assinatura que você criou, clique em **mais serviços**, clique em **assinaturas**, em seguida, clique em nova assinatura.  

   Após assinar uma oferta, atualize o portal para ver os serviços que fazem parte da nova assinatura.

2. **Provisionar uma máquina virtual**

   Agora você pode fazer logon no portal do como um usuário para provisionar uma máquina virtual usando a assinatura. 

   a. Faça logon portal do usuário como um usuário.
      - Para um sistema integrado, a URL varia com base na região do operador e o nome de domínio externo e será em https://portal o formato. &lt; *região*&gt;.&lt; *FQDN*&gt;.
   - Se você estiver usando o Kit de desenvolvimento de pilha do Azure, o endereço do portal é https://portal.local.azurestack.external.

   b.  No painel, clique em **novo** > **de computação** > **Datacenter avaliação do Windows Server 2016**e, em seguida, clique em **criar**.

   c. No **Noções básicas de** seção, digite um **nome**, **nome de usuário**, e **senha**, escolha um **assinatura**, criar um **grupo de recursos** (ou selecione um existente) e, em seguida, clique em **Okey**.

   d. No **escolher um tamanho de** seção, clique em **A1 padrão**e, em seguida, clique em **selecione**.  

   e. No **configurações** seção, clique em **rede Virtual**. No **rede virtual escolha** seção, clique em **criar novo**. No **criar rede virtual** seção, aceite todos os padrões e clique em **Okey**. No **configurações** seção, clique em **Okey**.

   ![Criar rede virtual](media/azure-stack-provision-vm/image04.png)

   f. No **resumo** seção, clique em **Okey** para criar a máquina virtual.  

   g. Para ver a nova máquina virtual, clique em **todos os recursos**, em seguida, procure a máquina virtual e clique em seu nome.

    ![Todos os recursos](media/azure-stack-provision-vm/image06.png)

O que você aprendeu neste tutorial:

> [!div class="checklist"]
> * Criar uma oferta
> * Adicionar uma imagem
> * Testar a oferta

> [!div class="nextstepaction"]
> [Tornar disponível para os usuários do Azure pilha web, móveis e aplicativos de API](azure-stack-tutorial-app-service.md)