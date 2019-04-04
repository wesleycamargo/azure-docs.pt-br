---
title: Implantar Kubernetes para usar contêineres do Azure Stack | Microsoft Docs
description: Saiba como implantar Kubernetes para usar contêineres com o Azure Stack.
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
ms.date: 02/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 77275ec274a9c76918874007cfe564eea09e6de5
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877186"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack"></a>Implantar Kubernetes para usar contêineres com o Azure Stack

*Aplicável a Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!Note]  
> Kubernetes no Azure Stack está em visualização. Um cenário desconectado do Azure Stack atualmente não há suporte pelo preview.

Você pode seguir as etapas neste artigo para implantar e configurar os recursos do Kubernetes em uma única operação coordenada. As etapas usam um modelo de solução do Azure Resource Manager. Você precisará para coletar as informações necessárias sobre a instalação do Azure Stack, gerar o modelo e, em seguida, implante a sua nuvem. O modelo do Azure Stack não usa o mesmo serviço AKS gerenciado oferecido no Azure global.

## <a name="kubernetes-and-containers"></a>Contêineres e Kubernetes

Você pode instalar o Kubernetes usando modelos do Gerenciador de recursos do Azure gerados pelo mecanismo do ACS no Azure Stack. [Kubernetes](https://kubernetes.io) é um sistema de código-fonte aberto para automatizar a implantação, dimensionamento e gerenciamento de aplicativos em contêineres. Um [contêiner](https://www.docker.com/what-container) está em uma imagem. A imagem de contêiner é semelhante a uma VM, no entanto, ao contrário de uma VM, o contêiner inclui apenas os recursos necessários para executar um aplicativo, como o código, o tempo de execução para executar o código, bibliotecas específicas e configurações.

Você pode usar o Kubernetes para:

- Desenvolva aplicativos massivamente escalonáveis, pode ser atualizados, que podem ser implantados em segundos. 
- Simplifique o design do seu aplicativo e melhorar sua confiabilidade por aplicativos diferentes do Helm. [Helm](https://github.com/kubernetes/helm) é uma ferramenta de empacotamento de software livre que ajuda a instalar e gerenciar o ciclo de vida de aplicativos Kubernetes.
- Monitorar e diagnosticar a integridade de seus aplicativos facilmente.

Você será cobrado somente para o uso de computação necessário para os nós que dão suporte a seu cluster. Para obter mais informações, consulte [uso e cobrança do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-billing-and-chargeback).

## <a name="deploy-kubernetes-to-use-containers"></a>Implantar Kubernetes para usar contêineres

As etapas para implantar um cluster Kubernetes no Azure Stack dependerá de seu serviço de gerenciamento de identidade. Verifique se a solução de gerenciamento de identidade usada pela sua instalação do Azure Stack. Contate o administrador do Azure Stack para verificar se o serviço de gerenciamento de identidade.

- **Active Directory do Azure (Azure AD)**  
Para obter instruções sobre como instalar o cluster ao usar o AD do Azure, consulte [implantar Kubernetes para o Azure Stack usando o Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md).

- **Active Directory Federated Services (AD FS)**  
Para obter instruções sobre como instalar o cluster ao usar o AD FS, consulte [implantar Kubernetes para o Azure Stack usando o Active Directory Federated Services (AD FS)](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Conectar-se ao seu cluster

Agora você está pronto para se conectar ao seu cluster. O mestre pode ser encontrado no seu grupo de recursos de cluster e é denominado `k8s-master-<sequence-of-numbers>`. Use um cliente SSH para se conectar ao mestre. No mestre, você pode usar **kubectl**, o cliente de linha de comando para gerenciar o cluster do Kubernetes. Para obter instruções, consulte [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Você também pode encontrar o **Helm** Gerenciador de pacotes úteis para instalar e implantar aplicativos no seu cluster. Para obter instruções sobre como instalar e usar o Helm com o cluster, consulte [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Próximas etapas

[Habilitar o painel do Kubernetes](azure-stack-solution-template-kubernetes-dashboard.md)

[Adicionar um Kubernetes no Marketplace (para o operador do Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Implantar Kubernetes no Azure Stack usando o Azure Active Directory (AD do Azure)](azure-stack-solution-template-kubernetes-azuread.md)

[Implantar Kubernetes no Azure Stack usando o Active Directory Federated Services (AD FS)](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes no Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
