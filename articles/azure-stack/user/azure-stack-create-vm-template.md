---
title: Neste tutorial, você cria uma VM de pilha do Azure usando um modelo | Microsoft Docs
description: Descreve como usar o ASDK para criar uma VM usando um modelo predefinido e um modelo personalizado do GitHub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/13/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 9ccdea6ca0bf46bbc19e5a7e19ac2d3527138345
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241773"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Tutorial: criar uma VM usando um modelo da comunidade

Como um usuário ou operador do Azure Stack, você pode criar uma VM usando [modelos de início rápido do GitHub personalizados](https://github.com/Azure/AzureStack-QuickStart-Templates) em vez de implantar um manualmente no marketplace do Azure Stack.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Usar modelos de início rápido do Azure Stack 
> * Criar uma VM usando um modelo personalizado do GitHub
> * Inicie o minikube e instalar um aplicativo

## <a name="azure-stack-quickstart-templates"></a>Modelos de início rápido do Azure Stack

Modelos de início rápido do Azure Stack são armazenados na [repositório de modelos de início rápido do Azure Stack público](https://github.com/Azure/AzureStack-QuickStart-Templates) no GitHub. Esse repositório contém modelos de implantação do Azure Resource Manager que foram testados com o Microsoft Azure Stack desenvolvimento ASDK (Kit). Você pode usá-los para tornar mais fácil para você avaliar o Azure Stack e use o ambiente de ASDK. 

Ao longo do tempo muitos usuários GitHub contribuíram para o repositório, resultando em uma coleção de modelos de implantação de mais de 400. Esse repositório é um ótimo ponto de partida para obter uma melhor compreensão de como você pode implantar vários tipos de ambientes para o Azure Stack. 

>[!IMPORTANT]
> Alguns desses modelos são criados por membros da comunidade e não pela Microsoft. Cada modelo é licenciado sob um contrato de licença de seu proprietário e não da Microsoft. A Microsoft não é responsável por esses modelos e não avalia sua segurança, compatibilidade ou desempenho. Modelos da comunidade não têm suporte em qualquer serviço ou programa de suporte da Microsoft e são disponibilizados "Como estão," sem garantias de qualquer tipo.

Se você quiser contribuir com seus modelos do Azure Resource Manager para o GitHub, verifique sua contribuição para o [AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates) repositório. Para saber mais sobre esse repositório e como Contribuir com ele, consulte o [arquivo Leiame](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md). 

## <a name="create-a-vm-using-a-custom-github-template"></a>Criar uma VM usando um modelo personalizado do GitHub

Neste tutorial de exemplo, o [101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) modelo de início rápido do Azure Stack é usado para implantar uma máquina de virtual do Ubuntu 16.04 na pilha do Azure executando o Minikube para gerenciar um cluster Kubernetes.

Minikube é uma ferramenta que torna mais fácil de executar o Kubernetes localmente. Minikube executa um cluster do Kubernetes de nó único dentro de uma VM, permitindo que você experimente o Kubernetes ou desenvolver com ele diárias. Ele dá suporte a um cluster de Kubernetes simple, um nó em execução em uma VM do Linux. Minikube é a maneira mais rápida e mais simples de obter um cluster Kubernetes totalmente funcional em execução. Ele permite aos desenvolvedores desenvolver e testar suas implantações de aplicativo baseado no Kubernetes em suas máquinas locais. Em termos de arquitetura, a VM Minikube executa mestre e componentes do agente do nó localmente:

- Componentes do nó mestre como servidor de API, Agendador, e [etcd Server](https://coreos.com/etcd/) são executados em um único processo do Linux chamado LocalKube.
- Componentes do agente do nó são executados dentro de contêineres do docker, exatamente como eles seriam executados em um nó de agente normal. De um ponto de vista de implantação do aplicativo, não há nenhuma diferença entre quando o aplicativo é implantado em um Minikube ou em um cluster Kubernetes regular.

Esse modelo instala os seguintes componentes:

- VM do Ubuntu 16.04 LTS
- [Docker-CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> A imagem de VM do Ubuntu (Ubuntu Server 16.04 LTS neste exemplo) deve já ter sido adicionada para o Azure Stack marketplace antes de executar essas etapas.

1.  Selecione **+ criar um recurso**, em seguida, **personalizado**, em seguida, **implantação de modelo**.

    ![](media/azure-stack-create-vm-template/1.PNG) 

2. Selecione **Editar modelo**.

    ![](media/azure-stack-create-vm-template/2.PNG) 

3.  Selecione **modelo de início rápido**.

    ![](media/azure-stack-create-vm-template/3.PNG)

4. Selecione **101-vm-linux-minikube** dos modelos disponíveis usando o **selecionar um modelo** lista suspensa e, em seguida, clique **Okey**.  

    ![](media/azure-stack-create-vm-template/4.PNG)

5. Se você quiser fazer modificações ao modelo JSON, você pode fazer isso. Se não, ou ao concluir, selecione **salve** para fechar o **Editar modelo** caixa de diálogo.

    ![](media/azure-stack-create-vm-template/5.PNG) 

6.  Selecione **parâmetros**, preencha ou modifique os campos disponíveis conforme o necessário e, em seguida, clique em **Okey**. Escolha a assinatura para usar, crie ou escolha um nome de grupo de recursos existente e, em seguida, selecione **criar** para iniciar a implantação de modelo.

    ![](media/azure-stack-create-vm-template/6.PNG)

7. Escolha a assinatura para usar, crie ou escolha um nome de grupo de recursos existente e, em seguida, selecione **criar** para iniciar a implantação de modelo.

    ![](media/azure-stack-create-vm-template/7.PNG)

8. A implantação do grupo de recursos leva vários minutos para criar a VM personalizada com base no modelo. Você pode monitorar o status da instalação por meio de notificações e de propriedades do grupo de recursos. 

    ![](media/azure-stack-create-vm-template/8.PNG)

    >[!NOTE]
    > A VM será executado quando a implantação for concluída. 

## <a name="start-minikube-and-install-an-application"></a>Inicie o Minikube e instalar um aplicativo

Agora que a VM do Linux foi criada com êxito, você pode entrar para iniciar o Minikube e instalar um aplicativo. 

1. Depois que a implantação for concluída, selecione **Connect** para exibir o endereço IP público que será usado para conectar-se à VM do Linux. 

    ![](media/azure-stack-create-vm-template/9.PNG)

2. Em um prompt de comando com privilégios elevados, execute **mstsc.exe** para abrir a Conexão de área de trabalho remota e conecte-se para o endereço IP público de VM do Linux descoberto na etapa anterior. Quando solicitado a entrar no xRDP, use as credenciais que você especificou ao criar a VM.

    ![](media/azure-stack-create-vm-template/10.PNG)

3. Abra o emulador de Terminal e insira os seguintes comandos para iniciar o Minikube:

    ```shell
    sudo minikube start --vm-driver=none
    sudo minikube addons enable dashboard
    sudo minikube dashboard --url
    ```

    ![](media/azure-stack-create-vm-template/11.PNG)

4. Abra o navegador da web e visite o endereço do painel de Kubernetes. Parabéns, agora você tem um trabalho totalmente instalação Kubernetes usando Minikube!

    ![](media/azure-stack-create-vm-template/12.PNG)

5. Para implantar um aplicativo de exemplo, visite a página de documentação oficial do Kubernetes e ignore a seção "Criar Cluster do Minikube", pois já tiver criado uma acima. Vá para a seção "Criar seu aplicativo Node. js" em https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Saiba mais sobre modelos de início rápido do Azure Stack 
> * Criar uma VM usando um modelo personalizado do GitHub
> * Inicie o minikube e instalar um aplicativo

