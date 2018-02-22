---
title: Criar uma VM de teste na pilha do Azure | Microsoft Docs
description: Saiba como configurar um teste de VM na pilha do Azure como um operador de nuvem.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: c86646e1-a12e-493f-b396-f17bfacd60c2
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 41096f68e5e7d9e31098d1e8919101418abe4c03
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-test-virtual-machine-in-azure-stack"></a>Criar uma máquina virtual de teste na pilha do Azure

*Aplica-se a: Kit de desenvolvimento de pilha do Azure*

Como um operador de pilha do Azure, você pode criar uma máquina virtual de teste para validar sua [Azure pilha](azure-stack-poc.md) implantação do Kit de desenvolvedor.

> [!NOTE]
> Antes de você pode provisionar máquinas virtuais, você deve [adicionar a imagem de avaliação do Windows Server 2016 Marketplace do Azure pilha](azure-stack-add-default-image.md).
> 
> 

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
1. No host do Kit de desenvolvimento de pilha do Azure, [entrar](azure-stack-connect-azure-stack.md) para o portal do administrador (`https://adminportal.local.azurestack.external`) e, em seguida, clique em **novo** > **de computação**  >  **Windows Server 2016 Datacenter Eval** > **criar**.  
2. No **Noções básicas de** folha, digite um **nome**, **nome de usuário**, e **senha**. Escolha uma **Assinatura**. Criar um **grupo de recursos**, ou selecione um existente e, em seguida, clique em **Okey**.  
3. No **escolher um tamanho de** folha, clique em **A1 padrão**e, em seguida, clique em **selecione**.  
4. No **configurações** folha, aceite os padrões e clique em **Okey**
5. No **resumo** folha, clique em **Okey** para criar a máquina virtual.  
6. Para ver a nova máquina virtual, clique em **todos os recursos**, em seguida, procure a máquina virtual e clique em seu nome.
    ![](media/azure-stack-provision-vm/image06.png)


## <a name="next-steps"></a>Próximas etapas
[Usando os portais de administrador e usuário na pilha do Azure](azure-stack-manage-portals.md)
