---
title: "Neste tutorial, você cria uma VM de pilha do Azure usando um modelo | Microsoft Docs"
description: Descreve como usar o ASDK para criar uma VM usando um modelo de predfined e um modelo personalizado do GitHub.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: ec44fc879abfe2a457e80f27db972ac4d7570dbd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Tutorial: criar uma VM usando um modelo de comunidade
Como um usuário ou o operador de pilha do Azure, você pode criar uma VM usando [modelos personalizados de início rápido GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates) em vez de implantar uma manualmente a partir do mercado de pilha do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Saiba mais sobre modelos de início rápido da pilha do Azure 
> * Criar uma VM usando um modelo personalizado do GitHub
> * Iniciar minikube e instalar um aplicativo

## <a name="learn-about-azure-stack-quickstart-templates"></a>Saiba mais sobre modelos de início rápido da pilha do Azure
Modelos de início rápido de pilha do Azure são armazenados no [repositório público de modelos de início rápido de AzureStack](https://github.com/Azure/AzureStack-QuickStart-Templates) no GitHub. Esse repositório contém modelos de implantação do Gerenciador de recursos do Azure que foram testados com o Microsoft Azure pilha Development Kit (ASDK). Você pode usá-los para tornar mais fácil para você avaliar a pilha do Azure e usar o ambiente ASDK. 

Ao longo do tempo muitos usuários do GitHub contribuíram para o repositório, resultando em uma grande coleção de modelos de implantação de mais de 400. Esse repositório é um excelente ponto de partida para obter um melhor entendimento de como você pode implantar vários tipos de ambientes a pilha do Azure. 

>[!IMPORTANT]
> Alguns desses modelos são criados por membros da comunidade e não pela Microsoft. Cada modelo é licenciado sob um contrato de licença por seu proprietário, não são da Microsoft. A Microsoft não é responsável por esses modelos e não verifica a segurança, compatibilidade ou desempenho. Comunidade modelos não têm suporte em qualquer serviço ou programa de suporte da Microsoft e são disponibilizados "Como estão" sem garantias de qualquer tipo.

Se você quiser contribuir seus modelos do Azure Resource Manager GitHub, você deve fazer sua contribuição para o [repositório de modelos de início rápido do azure](https://github.com/Azure/AzureStack-QuickStart-Templates).

Para saber mais sobre o repositório do GitHub e como contribuem para ele, consulte o [arquivo Leiame de repositório](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md). 


## <a name="create-a-vm-using-a-custom-github-template"></a>Criar uma VM usando um modelo personalizado do GitHub
Neste tutorial de exemplo, o [101-vm linux minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) modelo de início rápido do Azure pilha é usado para implantar uma máquina virtual Ubuntu 16.04 AzureStack executando Minikube para gerenciar um cluster kubenetes.

Minikube é uma ferramenta que torna mais fácil executar Kubernetes localmente. Minikube executa um cluster Kubernetes um único nó em uma VM para usuários que desejam testar Kubernetes ou desenvolver com ele diárias. Ele dá suporte a uma simples, o cluster de Kubernetes de um nó em execução em uma VM do Linux. É a maneira mais rápida e mais simples de obter um cluster Kubernetes totalmente funcional em execução. Ela permite aos desenvolvedores desenvolver e testar suas implantações de aplicativo com base em Kubernetes em seus computadores locais. Em termos de arquitetura, Minikube VM executa mestre e componentes do agente do nó local:
- Componentes do nó do mestre, como servidor de API, Agendador e etcd Server são executados em um único processo de Linux chamado LocalKube.
- Componentes do agente do nó são executados dentro de contêineres do docker, exatamente como eles seriam executado em um nó de agente normal. Do ponto de vista do aplicativo implantação, não há nenhuma diferença quando o aplicativo é implantado em um cluster de Kubernetes regular ou de Minikube.

Este modelo instala os seguintes componentes:

- Ubuntu 16.04 LTS VM
- [Docker-CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> A imagem de VM Ubuntu (Ubuntu Server 16.04 LTS neste exemplo) já deve ter sido [adicionado Marketplace do Azure pilha](asdk-marketplace-item.md) antes de começar a estas etapas.

1.  Clique em **+ novo** > **personalizado** > **implantação de modelo**.

    ![](media/asdk-create-vm-template/1.PNG) 

2. Clique em **Editar modelo**.

   ![](media/asdk-create-vm-template/2.PNG) 

3.  Clique em **Quickstart modelo**.

       ![](media/asdk-create-vm-template/3.PNG)

4. Selecione **101-vm linux minikube** de modelos disponíveis usando o **selecionar um modelo de** suspensa lista e, em seguida, clique em **Okey**.  

   ![](media/asdk-create-vm-template/4.PNG)

5. Se você quiser fazer modificações ao modelo de JSON, você pode fazer isso, se não, ou ao concluir, clique em **salvar** para fechar a caixa de diálogo Editar modelo.

   ![](media/asdk-create-vm-template/5.PNG) 

6.  Clique em **parâmetros**, preencha ou modifique os campos disponíveis conforme o necessário e, em seguida, clique em **Okey**. Escolha a assinatura para usar, criar ou escolher um nome de grupo de recursos existente e, em seguida, clique em **criar** para iniciar a implantação de modelo.

       ![](media/asdk-create-vm-template/6.PNG)

7. Escolha a assinatura para usar, criar ou escolher um nome de grupo de recursos existente e, em seguida, clique em **criar** para iniciar a implantação de modelo.

   ![](media/asdk-create-vm-template/7.PNG)

8. A implantação do grupo de recursos leva vários minutos para criar a máquina virtual com base em modelo personalizada. Você pode monitorar o status da instalação por meio de notificações e de propriedades do grupo de recursos. 

   ![](media/asdk-create-vm-template/8.PNG)

   >[!NOTE]
   > A máquina virtual serão executados quando a implantação for concluída. 

## <a name="start-minikube-and-install-an-application"></a>Iniciar minikube e instalar um aplicativo
Agora que a VM do Linux foi criado com êxito, você pode fazer logon iniciar minikube e instalar um aplicativo. 

1. Após a conclusão da implantação, clique em **conectar** para exibir o endereço IP público que será usado para conectar-se à VM do Linux. 

   ![](media/asdk-create-vm-template/9.PNG)

2. Em um prompt de comando elevado, execute **mstsc.exe** para abrir a Conexão de área de trabalho remota e se conectar ao endereço IP público de Linux VM descoberto na etapa anterior. Quando solicitado a fazer logon no xRDP, use as credenciais que você especificou ao criar a máquina virtual.

   ![](media/asdk-create-vm-template/10.PNG)

3. Abra o emulador de Terminal e insira comandos para iniciar minikube a seguir:

    >    `sudo minikube start --vm-driver=none`
    >   
    >    `sudo minikube addons enable dashboard`
    >    
    >    `sudo minikube dashboard --url`

   ![](media/asdk-create-vm-template/11.PNG)

4. Abra o navegador da web e visite o endereço do painel kubernetes. Parabéns, você agora tem uma totalmente kubernetes instalação em funcionamento usando minikube!

   ![](media/asdk-create-vm-template/12.PNG)

5. Se você gostaria de implantar um aplicativo de exemplo, visite a página de documentação oficial do kubernetes, ignorar a seção "Criar o Cluster de Minikube" como você já tiver criado uma acima. Basta ir para a seção "Criar seu aplicativo Node. js" em https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Saiba mais sobre modelos de início rápido da pilha do Azure 
> * Criar uma VM usando um modelo personalizado do GitHub
> * Iniciar minikube e instalar um aplicativo


> [!div class="nextstepaction"]
> [Saiba mais sobre as tarefas de avaliação avançadas](asdk-advanced-eval.md)