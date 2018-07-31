---
title: Tutorial - Depurar um aplicativo Web da Malha do Azure Service Fabric | Microsoft Docs
description: Neste tutorial, depure um aplicativo da Malha do Azure Service Fabric em execução em seu cluster local.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: ad6812f25ee33bf723ed86d4ec32ca6898d01774
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186731"
---
# <a name="tutorial-debug-a-service-fabric-mesh-web-application"></a>Tutorial: Depurar um aplicativo Web da Malha do Service Fabric

Este tutorial é a segunda parte de uma série e mostra como depurar um aplicativo Web da Malha do Azure Service Fabric em seu cluster de desenvolvimento local.

Neste tutorial, você irá aprender:

> [!div class="checklist"]
> * O que acontece quando você compila um aplicativo da Malha do Azure Service Fabric
> * Como definir um ponto de interrupção para observar uma chamada de serviço a serviço

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Compilar um aplicativo da Malha do Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Depurar o aplicativo localmente
> * [Publicar o aplicativo no Azure](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Verifique se [configurou o ambiente de desenvolvimento](service-fabric-mesh-howto-setup-developer-environment-sdk.md), o que inclui a instalação do tempo de execução do Service Fabric, SDK, Docker e Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Faça o download do aplicativo de exemplo de tarefas pendentes

Se você não tiver criado o aplicativo de exemplo de tarefas pendentes na [parte um desta série de tutoriais](service-fabric-mesh-tutorial-create-dotnetcore.md), baixe-o. Em uma janela de comando, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

O aplicativo está no diretório `src\todolistapp`.

## <a name="build-and-debug-on-your-local-cluster"></a>Compilar e depurar em seu cluster local

Uma imagem do Docker será compilada e implantada automaticamente no cluster local assim que o projeto for carregado. Esse processo pode demorar um pouco. Para monitorar o progresso no painel de **Saída** do Visual Studio, defina a lista suspensa do painel Saída **Mostrar saída de:** como **Ferramentas do Service Fabric**.

Pressione **F5** para compilar e executar seu serviço localmente. Quando o projeto for executado e depurado localmente, o Visual Studio irá:

* Verificar se o Docker para Windows está em execução e definido para usar o Windows como o sistema operacional de contêiner.
* Baixar qualquer imagem base ausente do Docker. Essa parte pode demorar algum tempo
* Compilar (ou recompilar) a imagem do Docker usada para hospedar seu projeto de código.
* Implantar e executar o contêiner no cluster de desenvolvimento do Service Fabric local.
* Executar seus serviços e atingir qualquer ponto de interrupção definido por você.

Após a conclusão da implantação local, e quando o Visual Studio estiver executando seu aplicativo, uma janela do navegador será aberta com uma página da Web de exemplo padrão.

**Dicas de depuração**

* Se você receber o erro **Não há um cluster local do Service Fabric em execução**, verifique se o SLCM (Gerenciador de Cluster Local do Serviço) está em execução e clique com o botão direito no ícone do SLCM na barra de tarefas, depois clique em **Iniciar Cluster Local**. Depois de iniciado, retorne ao Visual Studio e pressione **F5**.
* Se você receber um erro **404** quando o aplicativo for iniciado, ele provavelmente significará que suas variáveis de ambiente em **service.yaml** estão incorretas. Certifique-se de que `ApiHostPort` e `ServiceName` estejam definidas corretamente de acordo com as instruções em [Criar variáveis de ambiente](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore#create-environment-variables).
* Se você receber erros de compilação em **service.yaml**, use espaços, e não guias, para recuar linhas. Além disso, por enquanto, você deve compilar o aplicativo usando a localidade de inglês.

### <a name="debug-in-visual-studio"></a>Depuração no Visual Studio

Ao depurar um aplicativo da Malha do Service Fabric no Visual Studio, você usa um cluster de desenvolvimento local do Service Fabric. Para ver como os itens de tarefas pendentes são recuperados do serviço de back-end, depure-os no método OnGet().
1. No projeto **WebFrontEnd**, abra **Páginas** > **Index.cshtml** > **Index.cshtml.cs** e defina um ponto de interrupção no método **Get** (linha 17).
2. No projeto **ToDoService**, abra **TodoController.cs** e defina um ponto de interrupção no método **OnGet** (linha 15).
3. Volte para o navegador e atualize a página. Você chegou ao ponto de interrupção no método `OnGet()` front-end da Web. Você pode inspecionar a variável `backendUrl` para ver como as variáveis de ambiente que você definiu no arquivo **service.yaml** são combinadas na URL usada para contatar o serviço de back-end.
4. Avance (F10) a chamada `client.GetAsync(backendUrl).GetAwaiter().GetResult())` e você atingirá o ponto de interrupção `Get()` do controlador. Nesse método, você pode ver como a lista de itens de tarefas pendentes é recuperada da lista na memória.
5. Quando você terminar, pare a depuração de seu projeto no Visual Studio pressionando **SHIFT+F5**.
 
## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:

> [!div class="checklist"]
> * O que acontece quando você compila um aplicativo da Malha do Azure Service Fabric
> * Como definir um ponto de interrupção para observar uma chamada de serviço a serviço

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Publicar um aplicativo Web de Malha do Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)