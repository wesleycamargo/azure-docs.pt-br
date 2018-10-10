---
title: Adicionar Kubernetes para o Azure Stack Marketplace | Microsoft Docs
description: Saiba como adicionar o Kubernetes no Azure Stack Marketplace.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 9250e38eadb9d9b1e8bf0c09fa1a9a165e5e69f1
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886255"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Adicionar Kubernetes para o Azure Stack Marketplace

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!note]  
> Kubernetes no Azure Stack está em visualização.

Você pode oferecer Kubernetes como um item do Marketplace para seus usuários. Os usuários podem implantar Kubernetes em uma única operação coordenada.

O seguinte artigo examinar usando um modelo do Azure Resource Manager para implantar e provisionar os recursos para um cluster de Kubernetes autônomo. O item do Marketplace de Cluster do Kubernetes 0.3.0 que requer a versão do Azure Stack 1808. Antes de começar, verifique suas configurações de locatário do Azure global e o Azure Stack. Colete as informações necessárias sobre o Azure Stack. Adicione recursos necessários para seu locatário e o Azure Stack Marketplace. O cluster depende de um servidor do Ubuntu, script personalizado e os itens de Kubernetes, seja no marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Criar um plano, uma oferta e uma assinatura

Crie um plano, uma oferta e uma assinatura para o item do Marketplace do Kubernetes. Você também pode usar um plano existente e oferecem.

1. Entrar para o [portal de administração.](https://adminportal.local.azurestack.external)

1. Crie um plano que o plano base. Para obter instruções, consulte [criar um plano no Azure Stack](azure-stack-create-plan.md).

1. Crie uma oferta. Para obter instruções, consulte [criar uma oferta no Azure Stack](azure-stack-create-offer.md).

1. Selecione **oferece**e localize a oferta que você criou.

1. Selecione **visão geral** na folha da oferta.

1. Selecione **alterar estado**. Selecione **público**.

1. Selecione **+ criar um recurso** > **planos e oferece** > **assinatura** para criar uma nova assinatura.

    a. Insira um **nome de exibição**.

    b. Insira um **usuário**. Use conta do Azure AD associada ao locatário.

    c. **Descrição do provedor**

    d. Defina a **locatário do diretório** ao locatário do Azure AD para o Azure Stack. 

    e. Selecione **oferecem**. Selecione o nome da oferta que você criou. Anote a ID da assinatura.

## <a name="add-an-ubuntu-server-image"></a>Adicionar uma imagem de servidor do Ubuntu

Adicione a seguinte imagem do Ubuntu Server no Marketplace:

1. Entrar para o [portal de administração](https://adminportal.local.azurestack.external).

1. Selecione **todos os serviços**e, em seguida, sob o **administração** categoria, selecione **gerenciamento Marketplace**.

1. Selecione **+ adicionar do Azure**.

1. Digite `UbuntuServer`.

1. Selecione a versão mais recente do servidor com o seguinte perfil:
    - **Publicador**: Canonical
    - **Oferecer**: UbuntuServer
    - **SKU**: 16.04-LTS

1. Selecione **baixar.**

## <a name="add-a-custom-script-for-linux"></a>Adicionar um script personalizado para Linux

Adicione o Kubernetes do Marketplace:

1. Abra o [portal de administração](https://adminportal.local.azurestack.external).

1. Selecione **todos os serviços** e, em seguida, sob o **administração** categoria, selecione **gerenciamento Marketplace**.

1. Selecione **+ adicionar do Azure**.

1. Digite `Custom Script for Linux`.

1. Selecione o script com o seguinte perfil:
    - **Oferecer**: Script personalizado para Linux 2.0
    - **Versão**: 2.0.6
    - **Publicador**: Microsoft Corp

    > [!Note]  
    > Mais de uma versão de Script personalizado para Linux pode ser listada. Você precisará adicionar a versão que corresponde ao. O Kubernetes requer a versão exata do item.

1. Selecione **baixar.**


## <a name="add-kubernetes-to-the-marketplace"></a>Adicionar o Kubernetes no Marketplace

1. Abra o [portal de administração](https://adminportal.local.azurestack.external).

1. Selecione **todos os serviços** e, em seguida, sob o **administração** categoria, selecione **gerenciamento Marketplace**.

1. Selecione **+ adicionar do Azure**.

1. Digite `Kubernetes`.

1. Selecione `Kubernetes Cluster`.

1. Selecione **baixar.**

    > [!note]  
    > Ele pode levar cinco minutos para que o item do marketplace aparecer no Marketplace.

    ![kubernetes](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Atualizar ou remover o Kubernetes 

Ao atualizar o item de Kubernetes, você precisará remover o item que está no Marketplace. Em seguida, você pode seguir as instruções neste artigo para adicionar o Kubernetes no Marketplace.

Para remover o item de Kubernetes:

1. Conecte-se ao Azure Stack com o PowerShell como um operador. Para obter instruções, consulte [conectar-se ao Azure Stack com o PowerShell como um operador](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-admin).

2. Localize o item atual do Kubernetes Cluster na Galeria.

    ```PowerShell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Anote o nome do item atual, como `Microsoft.AzureStackKubernetesCluster.0.2.0`

4. Use o seguinte cmdlet do PowerShell para remover o item:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Próximas etapas

[Implantar um Kubernetes no Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Visão geral da oferta de serviços no Azure Stack](azure-stack-offer-services-overview.md)