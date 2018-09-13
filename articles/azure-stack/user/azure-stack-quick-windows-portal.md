---
title: Rápido de pilha do Azure iniciar - criar uma máquina virtual do Windows
description: Rápido do Azure da pilha de início - criar uma VM do Windows usando o portal
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/12/2018
ms.author: mabrigg
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: e4e3fdbdd3bc9eb982f993a9be60ba0812c68a9d
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713725"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Guia de início rápido: criar uma máquina virtual do Windows server com o portal do Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode criar uma máquina virtual Windows Server 2016 por meio do portal do Azure Stack. Siga as etapas neste artigo para criar e usar uma máquina virtual.

> [!NOTE]  
> As imagens de tela neste artigo são atualizadas para corresponder à interface do usuário que é apresentado com a versão do Azure Stack 1808. 1808 adiciona suporte para usar *discos gerenciados* além dos discos não gerenciados. Se você usar uma versão anterior, algumas imagens, como seleção de disco, será diferente do que é exibido neste artigo.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Entrar no portal do Azure Stack

Entrar no portal do Azure Stack. O endereço do portal do Azure Stack dependendo de qual produto do Azure Stack você está se conectando:

* Para o Azure Stack desenvolvimento ASDK (Kit), vá para: https://portal.local.azurestack.external.
* Para um sistema integrado do Azure Stack, vá para a URL que o operador do Azure Stack fornecida.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Clique em **+ criar um recurso** > **computação** > **Windows Server 2016 Datacenter – pagamento-como-uso**  >   **Criar**. Se você não vir **Windows Server 2016 Datacenter – pagamento-como-uso** entrada, entre em contato com seu operador do Azure Stack. Peça que eles adicioná-lo no Marketplace conforme explicado a [adicionar a imagem de VM do Windows Server 2016 para o Azure Stack marketplace](../azure-stack-add-default-image.md) artigo.

    ![Etapas para criar uma máquina virtual do Windows no portal](media/azure-stack-quick-windows-portal/image01.png)
2. Sob **Noções básicas**, digite um **nome**, **nome de usuário**, e **senha**. Escolha uma **Assinatura**. Criar uma **grupo de recursos**, ou selecione um existente, selecione um **local**e, em seguida, clique em **Okey**.

    ![Definir as configurações básicas](media/azure-stack-quick-windows-portal/image02.png)
3. Sob **tamanho** selecionar **Standard D1**e, em seguida, clique em **selecione**.  
    ![Escolha o tamanho da máquina virtual](media/azure-stack-quick-windows-portal/image03.png)

4. Sobre o **configurações** de página, faça as alterações desejadas para os padrões.
   - A partir da versão do Azure Stack 1808, você pode configurar **armazenamento** onde você pode optar por usar *discos gerenciados*. Antes da versão 1808 apenas discos não gerenciados podem ser usados.  
   ![Definir as configurações de máquina virtual](media/azure-stack-quick-windows-portal/image04.png)  
   Quando as configurações estiverem prontas, selecione **Okey** para continuar.

5. Sob **resumo**, clique em **Okey** para criar a máquina virtual.
    ![Exibir resumo e criar a máquina virtual](media/azure-stack-quick-windows-portal/image05.png)

6. Para ver sua nova máquina virtual, clique em **todos os recursos**, procure o nome da máquina virtual e, em seguida, clique em seu nome nos resultados da pesquisa.
    ![Consulte a máquina virtual](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado de usar a máquina virtual, exclua a máquina virtual e seus recursos. Para fazer isso, selecione o grupo de recursos na página de máquina virtual e clique em **excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina de virtual básica do Windows Server. Para saber mais sobre máquinas virtuais do Azure Stack, continue [considerações para máquinas virtuais no Azure Stack](azure-stack-vm-considerations.md).
