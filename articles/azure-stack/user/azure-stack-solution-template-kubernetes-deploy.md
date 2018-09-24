---
title: Implantar Kubernetes no Azure Stack | Microsoft Docs
description: Saiba como implantar o Kubernetes no Azure Stack.
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
ms.date: 09/25/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: a6e1acf3b9e69f32a8c175310134c534dbf8c561
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977529"
---
# <a name="deploy-kubernetes-to-azure-stack"></a>Implantar Kubernetes no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!Note]  
> Kubernetes no Azure Stack está em visualização. O operador do Azure Stack será necessário solicitar o acesso para o item do Marketplace de Cluster de Kubernetes necessário para executar as instruções neste artigo.

O artigo a seguir examina usando um modelo de solução do Azure Resource Manager para implantar e provisionar os recursos do Kubernetes em uma única operação coordenada. Você precisa coletar as informações necessárias sobre a instalação do Azure Stack, gerar o modelo e, em seguida, implante a sua nuvem. Observe que o modelo não é o mesmo gerenciadas serviço AKS oferecido no global Azure, mas quanto mais próximo ao serviço do ACS.

## <a name="kubernetes-and-containers"></a>Contêineres e Kubernetes

Você pode instalar o Kubernetes usando modelos do Gerenciador de recursos do Azure gerados pelo mecanismo do ACS no Azure Stack. [Kubernetes](https://kubernetes.io) é um sistema de código-fonte aberto para automatizar a implantação, dimensionamento e gerenciamento de aplicativos em contêineres. Um [contêiner](https://www.docker.com/what-container) está contida em uma imagem, semelhante a uma VM. Ao contrário de uma VM, a imagem de contêiner inclui apenas os recursos necessários para executar um aplicativo, como o código, o tempo de execução para executar o código, bibliotecas específicas e configurações.

Você pode usar o Kubernetes para:

- Desenvolva aplicativos massivamente escalonáveis, pode ser atualizados, que podem ser implantados em segundos. 
- Simplifique o design do seu aplicativo e melhorar sua confiabilidade por aplicativos diferentes do Helm. [Helm](https://github.com/kubernetes/helm) é uma ferramenta de empacotamento de software livre que ajuda a instalar e gerenciar o ciclo de vida de aplicativos Kubernetes.
- Com facilidade, monitorar e diagnosticar a integridade de seus aplicativos com a escala e funcionalidade de atualização.

## <a name="prerequisites"></a>Pré-requisitos 

Para começar, verifique se você tiver as permissões corretas e que o Azure Stack está pronto.

1. Verifique se que você pode criar aplicativos em seu locatário do Azure Active Directory (Azure AD). Você precisará dessas permissões para a implantação de Kubernetes.

    Para obter instruções sobre como verificar suas permissões, consulte [permissões do Active Directory do Azure Verifique](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

1. Gere um público e privado par de chaves SSH para entrar na VM do Linux no Azure Stack. Você precisará da chave pública ao criar o cluster.

    Para obter instruções sobre como gerar uma chave, consulte [geração de chave SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Verifique se você tiver uma assinatura válida no seu portal de locatário do Azure Stack e que você tem suficiente IP público endereços disponíveis para adicionar novos aplicativos.

    O cluster não pode ser implantado para Azure Stack **administrador** assinatura. Você deve usar uma assinatura do usuário * *. 

## <a name="create-a-service-principal-in-azure-ad"></a>Criar uma entidade de serviço no Azure AD

1. Entrar para global [portal do Azure](http://portal.azure.com).

1. Verifique se você entrou usando o locatário do AD do Azure associado à instância do Azure Stack. Você pode alternar sua entrada, clicando no ícone de filtro na barra de ferramentas do Azure.

    ![Selecione o seu locatário do AD](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. Criar um aplicativo Azure AD.

    a. Selecione **do Azure Active Directory** > **+ registros do aplicativo** > **novo registro de aplicativo**.

    b. Insira um **nome** do aplicativo.

    c. Selecione **aplicativo Web / API**.

    d. Insira `http://localhost` para o **URL de logon**.

    c. Clique em **Criar**.

1. Anote a **ID do aplicativo**. Ao criar o cluster, você precisará da ID. A ID é referenciada como **ID do cliente de entidade de serviço**.

1. Selecione **as configurações** > **chaves**.

    a. Insira o **descrição**.

    b. Selecione **nunca expira** para **Expires**.

    c. Clique em **Salvar**. Certifique-se de observar a cadeia de caracteres de chave. Você precisará de cadeia de caracteres chave ao criar o cluster. A chave é referenciada como a **segredo do cliente de entidade de serviço**.


## <a name="give-the-service-principal-access"></a>Conceder acesso à entidade de serviço

Conceder acesso à entidade de serviço à sua assinatura para que a entidade de segurança possa criar recursos.

1.  Entrar para o [portal do Azure Stack](https://portal.local.azurestack.external/).

1. Selecione **todos os serviços** > **assinaturas**.

1. Selecione a assinatura criada pelo seu operador para usar o Kubernetes Cluster.

1. Selecione **controle de acesso (IAM)** > selecione **+ adicionar**.

1. Selecione o **Colaborador** função.

1. Selecione o nome do aplicativo criado para seu serviço principal. Talvez você precise digitar o nome na caixa de pesquisa.

1. Clique em **Salvar**.

## <a name="deploy-a-kubernetes"></a>Implantar um Kubernetes

1. Abra o [portal do Azure Stack](https://portal.local.azurestack.external).

1. Selecione **+ criar um recurso** > **computação** > **Kubernetes Cluster**. Clique em **Criar**.

    ![Modelo de Solução de Implementação](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

1. Selecione **Noções básicas de** na criar Kubernetes.

    ![Modelo de Solução de Implementação](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Insira o **o nome de usuário de administrador VM Linux**. Nome de usuário para as máquinas virtuais de Linux que fazem parte do cluster Kubernetes e DVM.

1. Insira o **chave pública SSH** usado para autorização em todas as máquinas Linux criado como parte do cluster Kubernetes e DVM.

1. Insira o **prefixo de DNS do mestre de perfil** que é exclusivo para a região. Isso deve ser um nome exclusivo de região, como `k8s-12345`. Tente escolhê-lo mesmo que o grupo de recursos nomeie como melhor prática.

    > [!Note]  
    > Para cada cluster, use um prefixo DNS do mestre de perfil novos e exclusivos.

1. Insira o **contagem de perfil do agente de Pool**. A contagem de contém o número de agentes no cluster. Pode haver de 1 a 4.

1. Insira o **entidade de serviço ClientId** isso é usado pelo provedor de nuvem do Azure do Kubernetes.

1. Insira o **segredo do cliente de entidade de serviço** que você criou ao criar o aplicativo de serviço principal.

1. Insira o **versão do provedor de nuvem Azure Kubernetes**. Essa é a versão para o provedor do Azure do Kubernetes. O Azure Stack libera uma criação personalizada de Kubernetes para cada versão do Azure Stack.

1. Selecione suas **assinatura** ID.

1. Insira o nome de um novo grupo de recursos ou selecione um grupo de recursos. O nome do recurso precisa ser alfanuméricos e minúsculos.

1. Selecione o **local** do grupo de recursos. Essa é a região que você escolhe para sua instalação do Azure Stack.

### <a name="specify-the-azure-stack-settings"></a>Especifique as configurações do Azure Stack

1. Selecione o **configurações de carimbo de data / Azure Stack**.

    ![Modelo de Solução de Implementação](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

1. Insira o **ponto de extremidade Arm de locatário**. Esse é o ponto de extremidade do Azure Resource Manager para conectar-se para criar o grupo de recursos para o cluster Kubernetes. Você precisará obter o ponto de extremidade do seu operador do Azure Stack para um sistema integrado. Para o Azure Stack desenvolvimento ASDK (Kit), você pode usar `https://management.local.azurestack.external`.


## <a name="connect-to-your-cluster"></a>Conectar ao cluster

Agora você está pronto para se conectar ao seu cluster. O mestre pode ser encontrado no seu grupo de recursos de cluster e é denominado `k8s-master-<sequence-of-numbers>`. Use um cliente SSH para se conectar ao mestre. No mestre, você pode usar **kubectl**, o cliente de linha de comando para gerenciar o cluster do Kubernetes. Para obter instruções, consulte [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Você também pode encontrar o **Helm** Gerenciador de pacotes úteis para instalar e implantar aplicativos no seu cluster. Para obter instruções sobre como instalar e usar o Helm com o cluster, consulte [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Próximas etapas

[Adicionar um Kubernetes no Marketplace (para o operador do Azure Stack)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes no Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
