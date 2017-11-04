---
title: "Rápido de pilha do Azure iniciar - criar uma máquina virtual do Windows"
description: "Rápido de pilha do Azure iniciar - criar uma VM do Windows usando o portal"
services: azure-stack
author: ErikjeMS
manager: byronr
ms.service: azure-stack
ms.topic: azure-stack
ms.date: 09/15/2017
ms.author: erikje
ms.custom: mvc
ms.openlocfilehash: abca538f28bbc0a8f3f00311ca1a69d196f10272
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-with-the-azure-stack-portal"></a>Criar uma máquina virtual do Windows com o portal de pilha do Azure

Você pode criar uma máquina virtual do Windows usando o portal de pilha do Azure. O portal é uma interface de usuário baseada em navegador onde você pode criar, configurar e gerenciar recursos.

## <a name="sign-in-to-the-azure-stack-portal"></a>Entre portal do Azure pilha

Entre portal do Azure pilha. O endereço do portal do Azure pilha depende de qual produto da pilha do Azure você está se conectando:

* Para o Kit de desenvolvimento do Azure pilha (ASDK), acesse: https://portal.local.azurestack.external.
* Para um sistema de pilha do Azure integrado, vá para a URL que o operador de pilha do Azure fornecido.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Clique em **novo** > **de computação** > **Windows Server 2016 Datacenter Eval** > **criar**. Se você não vir **Datacenter avaliação do Windows Server 2016** entrada, entre em contato com seu operador de pilha do Azure. Peça que eles adicioná-la para o marketplace conforme explicado a [adicionar a imagem de VM do Windows Server 2016 Marketplace do Azure pilha](../azure-stack-add-default-image.md) artigo. 
    ![](media/azure-stack-quick-windows-portal/image01.png)
2. Em **Noções básicas de**, digite um **nome**, **nome de usuário**, e **senha**. Escolha uma **Assinatura**. Criar um **grupo de recursos**, ou selecione um existente selecione um deles, um **local**e, em seguida, clique em **Okey**.

    ![](media/azure-stack-quick-windows-portal/image02.png)
3. Em **escolher um tamanho de**, clique em **D1 padrão** > **selecione**.
    ![](media/azure-stack-quick-windows-portal/image03.png)
4. Em **configurações**, aceite os padrões e clique em **Okey**.
    ![](media/azure-stack-quick-windows-portal/image04.png)
5. Em **resumo**, clique em **Okey** para criar a máquina virtual. 
    ![](media/azure-stack-quick-windows-portal/image05.png)
6. Para ver a nova máquina virtual, clique em **todos os recursos**, em seguida, procure a máquina virtual e clique em seu nome.
    ![](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisa mais da máquina virtual, exclua o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos da página de máquina virtual e clique em **excluir**.

## <a name="next-steps"></a>Próximas etapas
Esse início rápido, você implantou a máquina virtual do Windows simple. Para saber mais sobre as máquinas virtuais de pilha do Azure, continuar [considerações para máquinas virtuais no Azure pilha](azure-stack-vm-considerations.md).
