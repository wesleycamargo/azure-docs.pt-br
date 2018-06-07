---
title: Implantar um Cluster de Kubernetes a pilha do Azure | Microsoft Docs
description: Saiba como implantar um Kubernetes Cluster a pilha do Azure.
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
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 43c0b7c87f9ee1cd33da3d617747c11dc120e51a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823615"
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Implantar um cluster de Kubernetes a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

> [!Note]  
> O Kubernetes de serviços de contêiner do Azure (ACS) na pilha do Azure está em visualização privada. O operador de pilha do Azure será necessário solicitar acesso ao item Kubernetes Marketplace necessário para executar as instruções neste artigo.

O seguinte artigo analisa o uso de um modelo de solução de Gerenciador de recursos do Azure para implantar e provisionar os recursos para Kubernetes em uma única operação coordenada. Serão necessárias para coletar as informações necessárias sobre a instalação de pilha do Azure, gerar o modelo e, em seguida, implantar a sua nuvem.

## <a name="kubernetes-and-containers"></a>Kubernetes e contêineres

Você pode instalar Kubernetes usando modelos do Azure Resource Manager gerados pelo mecanismo de serviços de contêiner do Azure (ACS) na pilha do Azure. [Kubernetes](https://kubernetes.io) é um sistema de código-fonte aberto para automatizar a implantação, escala e o gerenciamento de aplicativos em contêineres. Um [contêiner](https://www.docker.com/what-container) está contida em uma imagem, semelhante a uma máquina virtual. Ao contrário de uma VM, a imagem de contêiner inclui apenas os recursos necessários para executar um aplicativo, como o código de tempo de execução para executar o código, bibliotecas específicas e configurações.

Você pode usar Kubernetes para:

- Desenvolva aplicativos altamente escalonáveis, atualizáveis, que podem ser implantados em segundos. 
- Simplificar o design do seu aplicativo e melhorar a sua confiabilidade por aplicativos diferentes do comando. [Helm](https://github.com/kubernetes/helm) é uma ferramenta de empacotamento de software livre que ajuda a instalar e gerenciar o ciclo de vida de aplicativos Kubernetes.
- Facilmente monitorar e diagnosticar a integridade de seus aplicativos com a escala e funcionalidade de atualização.

## <a name="prerequisites"></a>Pré-requisitos 

Para começar, verifique se você tem as permissões corretas e se a pilha do Azure está pronta.

1. Verifique se que você pode criar aplicativos em seu locatário do Azure Active Directory (AD do Azure). Essas permissões são necessárias para a implantação de Kubernetes.

    Para obter instruções sobre como verificar suas permissões, consulte [permissões Check do Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

2. Gere um público e privado par de chaves SSH a entrar para a VM do Linux na pilha do Azure. Ao criar o cluster, você precisará a chave pública.

    Para obter instruções sobre como gerar uma chave, consulte [geração de chave SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

3. Verifique se você tem uma assinatura válida no portal de locatário do Azure pilha, e que você tem suficiente IP público endereços disponíveis para adicionar novos aplicativos.

    O cluster não pode ser implantado para uma pilha de Azure **administrador** assinatura. Você deve usar uma assinatura do usuário * *. 

## <a name="create-a-service-principal-in-azure-ad"></a>Criar uma entidade de serviço no AD do Azure

1. Entrar no global [portal do Azure](http://portal.azure.com).
2. Verifique se você entrou usando locatário do Azure AD associado com a instância de pilha do Azure.
3. Criar um aplicativo Azure AD.

    a. Selecione **Active Directory do Azure** > **+ registros do aplicativo** > **novo registro de aplicativo**.

    b. Insira um **nome** do aplicativo.

    c. Selecione **aplicativo Web / API**.

    d. Digite `http://localhost` para o **URL de logon**.

    c. Clique em **Criar**.

4. Anote o **ID do aplicativo**. Você precisará da ID ao criar o cluster. A ID é referenciada como **ID da entidade de serviço cliente**.

5. Selecione **configurações** > **chaves**.

    a. Insira o **descrição**.

    b. Selecione **nunca expira** para **Expires**.

    c. Clique em **Salvar**. Certifique-se de observar a cadeia de caracteres de chave. Ao criar o cluster, você precisará de cadeia de caracteres de chave. A chave é referenciada como a **segredo de cliente da entidade de serviço**.



## <a name="give-the-service-principal-access"></a>Fornecer o serviço de acesso principal

Fornecer o serviço de acesso principal à sua assinatura para que a entidade de segurança possa criar recursos.

1.  Entrar para o [do portal de administração](https://adminportal.local.azurestack.external).

2. Selecione **mais serviços** > **assinaturas de usuário** > **+ adicionar**.

3. Selecione a assinatura que você criou.

4. Selecione **(IAM) do controle de acesso** > selecione **+ adicionar**.

5. Selecione o **proprietário** função.

6. Selecione o nome do aplicativo criado para o serviço principal. Você terá que digitar o nome na caixa de pesquisa.

7. Clique em **Salvar**.

## <a name="deploy-a-kubernetes-cluster"></a>Implantar um Cluster Kubernetes

1. Abra o [portal do Azure pilha](https://portal.local.azurestack.external).

2. Selecione **+ novo** > **de computação** > **Kubernetes Cluster**. Clique em **Criar**.

    ![Modelo de Solução de Implementação](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

3. Selecione **Noções básicas de** na criar o Cluster Kubernetes.

    ![Modelo de Solução de Implementação](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

2. Insira o **nome de usuário de administração VM Linux**. Nome de usuário para as máquinas virtuais de Linux que fazem parte do cluster Kubernetes e DVM.

3. Insira o **chave pública SSH** usado para autorização para todas as máquinas Linux criado como parte do cluster Kubernetes e DVM.

4. Insira o **do prefixo de DNS perfil mestre** que é exclusivo para a região. Isso deve ser um nome exclusivo de região, como `k8s-12345`. Tente escolhê-lo mesmo como o grupo de recursos nome como a melhor prática.

    > [!Note]  
    > Para cada cluster, use um prefixo DNS do perfil mestre novos e exclusivos.

5. Insira o **contagem de perfil do agente de Pool**. A contagem de contém o número de agentes no cluster. Pode haver de 1 a 4.

6. Insira o **ClientId entidade de serviço** isso é usado pelo provedor de nuvem do Azure de Kubernetes.

7. Insira o **segredo de cliente da entidade de serviço** que você criou ao criar o aplicativo de serviço principal.

8. Insira o **versão do provedor de nuvem Azure Kubernetes**. Essa é a versão para o provedor do Kubernetes Azure. A pilha do Azure libera um build Kubernetes personalizado para cada versão de pilha do Azure.

9. Selecione seu **assinatura** ID.

10. Digite o nome do novo grupo de recursos ou selecionar um grupo de recursos existente. O nome do recurso deve ser alfanumérico e minúsculas.

11. Selecione o **local** do grupo de recursos. Isso é a região que você escolher para a instalação de pilha do Azure.

### <a name="specify-the-azure-stack-settings"></a>Especifique as configurações de pilha do Azure

1. Selecione o **configurações de carimbo de pilha do Azure**.

    ![Modelo de Solução de Implementação](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

2. Insira o **Arm de ponto de extremidade de locatário**. Este é o ponto de extremidade do Gerenciador de recursos do Azure para conectar-se para criar o grupo de recursos para o cluster Kubernetes. Você precisará obter o ponto de extremidade de seu operador de pilha do Azure para um sistema integrado. Para o Azure pilha Development Kit (ASDK), você pode usar `https://management.local.azurestack.external`.

3. Insira o **ID de locatário** para o locatário. Se você precisar de ajuda para encontrar esse valor, consulte [obter ID de locatário](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). 

## <a name="connect-to-your-cluster"></a>Conectar ao cluster

Agora você está pronto para se conectar ao cluster. O mestre pode ser encontrado no seu grupo de recursos de cluster e é denominado `k8s-master-<sequence-of-numbers>`. Use um cliente SSH para conectar-se ao mestre. No mestre, você pode usar **kubectl**, o cliente de linha de comando Kubernetes para gerenciar o cluster. Para obter instruções, consulte [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Você também pode encontrar o **comando** úteis para a instalação e implantação de aplicativos para o cluster do Gerenciador de pacotes. Para obter instruções sobre como instalar e usar o comando com o cluster, consulte [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Próximas etapas

[Adicionar um Kubernetes Cluster no mercado (para o operador de pilha do Azure)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes no Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
