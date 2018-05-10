---
title: Adicionar um Cluster Kubernetes a pilha do Azure Marketplace | Microsoft Docs
description: Saiba como adicionar um Kubernetes Cluster a pilha do Azure Marketplace.
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
ms.date: 05/08/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: c66b0d7ea5ade90c6bb8f88006f2a09bd407deaa
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>Adicionar um Cluster Kubernetes a pilha do Azure Marketplace

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

> [!note]  
> O Kubernetes de serviços de contêiner do Azure (ACS) na pilha do Azure está em visualização privada. Para solicitar acesso ao item Kubernetes Marketplace necessário para executar as instruções neste artigo, [enviar uma solicitação para obter acesso](https://aka.ms/azsk8).

Você pode oferecer um Kubernetes Cluster como um item do Marketplace para seus usuários. Os usuários podem implantar Kubernetes em uma única operação coordenada.

O seguinte artigo examinar usando um modelo do Gerenciador de recursos do Azure para implantar e provisionar os recursos para um cluster de Kubernetes autônomo. Antes de começar, verifique as configurações de locatário do Azure global e a pilha do Azure. Colete as informações necessárias sobre a pilha do Azure. Adicione recursos necessários para seu locatário e a pilha do Azure Marketplace. O cluster depende de um servidor do Ubuntu, script personalizado e os itens de Kubernetes Cluster no marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Criar um plano, uma oferta e uma assinatura

Crie um plano, uma oferta e uma assinatura para o item do Marketplace de Cluster Kubernetes. Você também pode usar um plano existente e oferecem.

1. Entrar para o [do portal de administração.](https://adminportal.local.azurestack.external)

2. Crie um plano que o plano base. Para obter instruções, consulte [criar um plano na pilha do Azure](azure-stack-create-plan.md).

3. Crie uma oferta. Para obter instruções, consulte [criar uma oferta na pilha do Azure](azure-stack-create-offer.md).

4. Selecione **oferece**e localize a oferta que você criou.

5. Selecione **visão geral** na folha da oferta.

6. Selecione **alterar o estado**. Selecione **público**.

7. Selecione **+ novo** > **oferece e planos de** > **assinatura** para criar uma nova assinatura.

    a. Insira um **exibir nome**.

    b. Insira um **usuário**. Use a conta do AD do Azure associada ao seu locatário.

    c. **Descrição do provedor**

    d. Definir o **locatário de diretório** ao locatário do AD do Azure para a pilha do Azure. 

    e. Selecione **oferecem**. Selecione o nome da oferta que você criou. Anote a ID da assinatura.

## <a name="add-an-ubuntu-server-image"></a>Adicionar uma imagem de servidor do Ubuntu

Adicione a imagem a seguir Ubuntu Server Marketplace:

1. Entrar para o [do portal de administração](https://adminportal.local.azurestack.external).

2. Selecione **mais serviços** > **Marketplace gerenciamento**.

3. Selecione **+ adicionar do Azure**.

4. Digite `UbuntuServer`.

5. Selecione o servidor com o seguinte perfil:
    - **Publicador**: canônico
    - **Oferecer**: UbuntuServer
    - **SKU**: 16.04 LTS
    - **Versão**: 16.04.201802220

6. Selecione **baixar.**

## <a name="add-a-custom-script-for-linux"></a>Adicionar um script personalizado para Linux

Adicione o Cluster Kubernetes do Marketplace:

1. Abra o [do portal de administração](https://adminportal.local.azurestack.external).

2. Selecione **mais serviços** > **Marketplace gerenciamento**.

3. Selecione **+ adicionar do Azure**.

4. Digite `Custom Script for Linux`.

5. Selecione o script com o seguinte perfil:
    - **Oferecer**: Script personalizado para Linux 2.0
    - **Versão**: 2.0.3
    - **Publicador**: Microsoft Corp

6. Selecione **baixar.**


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>Adicionar o Kubernetes Cluster Marketplace

1. Abra o [do portal de administração](https://adminportal.local.azurestack.external).

2. Selecione **mais serviços** > **Marketplace gerenciamento**.

3. Selecione **+ adicionar do Azure**.

4. Digite `Kubernetes Cluster`.

5. Selecione `Kubernetes Cluster`.

6. Selecione **baixar.**

    > [!note]  
    > Ele pode levar cinco minutos para o item do marketplace aparecer no Marketplace.

    ![Cluster Kubernetes](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>Atualizar ou remover o Kubernetes Cluster 

Ao atualizar o item Kubernetes Cluster, você precisará remover o item que está no Marketplace. Em seguida, você pode seguir as instruções neste artigo para adicionar o Kubernetes Cluster Marketplace.

Para remover o item Kubernetes Cluster:

1. Observe o nome do item atual, como `Microsoft.AzureStackKubernetesCluster.0.1.0`

2. Conecte-se a pilha do Azure com o PowerShell.

3. Use o seguinte cmdlet do PowerShell para remover o item:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Próximas etapas

[Implantar um Cluster de Kubernetes a pilha do Azure](/user/azure-stack-solution-template-kubernetes-deploy.md)

[Visão geral da oferta de serviços na pilha do Azure](azure-stack-offer-services-overview.md)