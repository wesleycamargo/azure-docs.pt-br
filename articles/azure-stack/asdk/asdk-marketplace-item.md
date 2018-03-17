---
title: Adicionar um item do marketplace do Azure pilha do Azure | Microsoft Docs
description: "Descreve como adicionar uma imagem de máquina virtual do servidor Windows Azure Marketplace de pilha do Azure."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 25f179f825526e20377c0e94ccb38a788cadd898
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-azure"></a>Tutorial: adicionar um item do marketplace do Azure pilha do Azure

Como um operador de pilha do Azure, a primeira coisa que você precisa fazer para habilitar usuários para implantar uma máquina Virtual (VM) é adicionar uma imagem de VM para o marketplace de pilha do Azure. Por padrão, nada é publicado no mercado de pilha do Azure, mas você pode baixar itens da [uma lista curadoria de itens do marketplace do Azure](.\.\azure-stack-marketplace-azure-items.md) que foram previamente testados para executar na pilha do Azure. Use esta opção se você estiver operando em um cenário conectado e registrou sua instância de pilha do Azure com o Azure.

Neste tutorial, você adicionar a imagem de VM do Windows Server 2016 do marketplace do Azure Marketplace pilha do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um item da pilha do Windows Server VM do Azure marketplace
> * Verifique se que a imagem da VM está disponível 
> * A imagem VM de teste

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Instalar o [módulos do PowerShell do Azure compatíveis de pilha do Azure](asdk-post-deploy.md#install-azure-stack-powershell)
- Baixe o [ferramentas de pilha do Azure](asdk-post-deploy.md#download-the-azure-stack-tools)
- [Registrar o ASDK](asdk-register.md) com sua assinatura do Azure

## <a name="add-a-windows-server-vm-image"></a>Adicionar uma imagem de VM do Windows Server
Você pode adicionar uma imagem do Windows Server 2016 no mercado de pilha do Azure ao baixar a imagem do marketplace do Azure. Use esta opção se você estiver operando em um cenário conectado e você já tiver [registrado sua instância do Azure pilha](asdk-register.md) com o Azure.

1. Entrar para o [portal de administração ASDK](https://adminportal.local.azurestack.external).

2. Selecione **mais serviços** > **Marketplace gerenciamento** > **adicionar do Azure**. 

   ![Adicionar a partir do Azure](media/asdk-marketplace-item/azs-marketplace.png)

3. Localizar ou procurar o **Datacenter do Windows Server 2016** imagem.

4. Selecione o **Datacenter do Windows Server 2016** da imagem e, em seguida, selecione **baixar**.

   ![Baixar a imagem do Azure](media/asdk-marketplace-item/azure-marketplace-ws2016.png)


> [!NOTE]
> Leva aproximadamente uma hora para baixar a imagem VM e publicá-lo no mercado de pilha do Azure. 

Quando o download for concluído, a imagem será publicada e disponibilizada em **Marketplace gerenciamento**. A imagem também está disponível em **de computação** para criar novas máquinas virtuais.

## <a name="verify-the-marketplace-item-is-available"></a>Verifique se que o item do marketplace está disponível
Use estas etapas para verificar se a nova imagem VM está disponível no mercado de pilha do Azure.

1. Entrar para o [portal de administração ASDK](https://adminportal.local.azurestack.external).

2. Selecione **mais serviços** > **Marketplace gerenciamento**. 

3. Verifique se a imagem de VM do Datacenter do Windows Server 2016 foi adicionada com êxito.

   ![Imagem baixada do Azure](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>A imagem VM de teste
Como um operador de pilha do Azure, você pode usar o [portal do administrador](https://adminportal.local.azurestack.external) para criar um teste de VM para validar a imagem ter sido disponibilizada com êxito. 

1. Entrar para o [portal de administração ASDK](https://adminportal.local.azurestack.external).

2. Clique em **novo** > **de computação** > **Datacenter do Windows Server 2016** > **criar**.  
 ![Criar VM a partir da imagem do marketplace](media/asdk-marketplace-item/new-compute.png)

3. No **Noções básicas de** folha, insira as informações a seguir e, em seguida, clique em **Okey**:
  - **Name**: test-vm-1
  - **Nome de usuário**: AdminTestUser
  - **Password**: AzS-TestVM01
  - **Assinatura**: aceitar a assinatura do provedor de padrão
  - **Grupo de recursos**: rg de vm de teste
  - **Local**: local

4. No **escolher um tamanho de** folha, clique em **A1 padrão**e, em seguida, clique em **selecione**.  

5. No **configurações** folha, aceite os padrões e clique em **Okey**

6. No **resumo** folha, clique em **Okey** para criar a máquina virtual.  
> [!NOTE]
> Implantação da máquina virtual leva alguns minutos para ser concluída.

7. Para exibir a nova VM, clique em **máquinas virtuais**, em seguida, procure **teste 1 de vm** e clique em seu nome.
    ![Imagem VM de teste primeiro exibida](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>Limpar recursos
Depois de ter com êxito conectado à máquina virtual e verificar se a imagem de teste está funcionando corretamente, você deve excluir o grupo de recursos de teste. Isso liberará recursos limitados disponíveis para instalações de ASDK de nó único.

Quando não é mais necessário, exclua o grupo de recursos, a VM e todos os recursos relacionados seguindo estas etapas:

1. Entrar para o [portal de administração ASDK](https://adminportal.local.azurestack.external).
2. Clique em **grupos de recursos** > **rg de vm de teste** > **excluir grupo de recursos**.
3. Tipo **rg de vm de teste** como o nome do grupo de recursos e clique **excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Adicionar um item da pilha do Windows Server VM do Azure marketplace
> * Verifique se que a imagem da VM está disponível 
> * A imagem VM de teste

Avança para o próximo tutorial para aprender a criar uma oferta de pilha do Azure e o plano.

> [!div class="nextstepaction"]
> [Oferecer serviços do Azure IaaS de pilha](asdk-offer-services.md)