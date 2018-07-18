---
title: Rápido de pilha do Azure iniciar - criar uma máquina virtual do Windows
description: Rápido de pilha do Azure iniciar - criar uma VM do Windows usando o portal
services: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 5776fc472483018eb2c9e4f8962d0b1e8bce8081
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Início rápido: criar uma máquina virtual do Windows server com o portal de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode criar uma máquina de virtual do Windows Server 2016 usando o portal de pilha do Azure. Siga as etapas neste artigo para criar e usar uma máquina virtual.

## <a name="sign-in-to-the-azure-stack-portal"></a>Entre portal do Azure pilha

Entre portal do Azure pilha. O endereço do portal do Azure pilha depende de qual produto da pilha do Azure você está se conectando:

* Para o Kit de desenvolvimento do Azure pilha (ASDK), acesse: https://portal.local.azurestack.external.
* Para um sistema de pilha do Azure integrado, vá para a URL que o operador de pilha do Azure fornecido.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Clique em **novo** > **de computação** > **Windows Server 2016 Datacenter Eval** > **criar**. Se você não vir **Datacenter avaliação do Windows Server 2016** entrada, entre em contato com seu operador de pilha do Azure. Peça que eles adicioná-la para o marketplace conforme explicado a [adicionar a imagem de VM do Windows Server 2016 Marketplace do Azure pilha](../azure-stack-add-default-image.md) artigo.

    ![Etapas para criar uma máquina virtual Windows no portal](media/azure-stack-quick-windows-portal/image01.png)
2. Em **Noções básicas de**, digite um **nome**, **nome de usuário**, e **senha**. Escolha uma **Assinatura**. Criar um **grupo de recursos**, ou selecione um existente selecione um deles, um **local**e, em seguida, clique em **Okey**.

    ![Definir as configurações básicas](media/azure-stack-quick-windows-portal/image02.png)
3. Em **escolher um tamanho de**, clique em **D1 padrão** > **selecione**.
    ![Escolha o tamanho da máquina virtual](media/azure-stack-quick-windows-portal/image03.png)
4. Em **configurações**, aceite os padrões e clique em **Okey**.
    ![Definir configurações de máquina virtual](media/azure-stack-quick-windows-portal/image04.png)
5. Em **resumo**, clique em **Okey** para criar a máquina virtual.
    ![Exibir resumo e criar a máquina virtual](media/azure-stack-quick-windows-portal/image05.png)
6. Para ver a nova máquina virtual, clique em **todos os recursos**, procure o nome da máquina virtual e, em seguida, clique em seu nome nos resultados da pesquisa.
    ![Consulte a máquina virtual](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar de usar a máquina virtual, exclua a máquina virtual e seus recursos. Para fazer isso, selecione o grupo de recursos na página de máquina virtual e clique em **excluir**.

## <a name="next-steps"></a>Próximas etapas

Esse início rápido, você implantado uma básica da máquina virtual do Windows Server. Para saber mais sobre as máquinas virtuais de pilha do Azure, continuar [considerações para máquinas virtuais no Azure pilha](azure-stack-vm-considerations.md).
