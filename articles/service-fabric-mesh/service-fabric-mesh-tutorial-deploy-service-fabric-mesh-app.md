---
title: Tutorial - Implantar um aplicativo de Malha do Service Fabric para a Malha do Service Fabric | Microsoft Docs
description: Saiba como publicar um aplicativo de Malha do Azure Service Fabric que consiste em um site do ASP.NET Core que se comunica com um serviço Web de back-end.
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
ms.date: 07/26/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 350749161260768071afbb47b854cb2e9184bd9d
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284720"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-web-application"></a>Tutorial: Implantar um aplicativo Web de Malha do Service Fabric

Este tutorial é a terceira parte de uma série e mostra como publicar um aplicativo Web de Malha do Azure Service Fabric diretamente do Visual Studio.

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Publicar o aplicativo no Azure.
> * Verificar o status da implantação do aplicativo
> * Ver todos os aplicativos atualmente implantados em sua assinatura
> * Ver os logs de aplicativo
> * Apagar os recursos usados pelo aplicativo.

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Compilar um aplicativo Web de Malha do Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Depurar o aplicativo localmente](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Publicar o aplicativo no Azure

Você aprenderá a criar um aplicativo de Malha do Azure Service Fabric com um front-end Web do ASP.NET e um serviço de back-end da API Web do ASP.NET Core. Em seguida, você depura o aplicativo no cluster de desenvolvimento local e publica o aplicativo no Azure. Quando tiver terminado, você terá um aplicativo de tarefas pendentes simples que demonstra como realizar uma chamada entre serviços em um aplicativo Web de Malha do Service Fabric.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Verifique se [configurou o ambiente de desenvolvimento](service-fabric-mesh-howto-setup-developer-environment-sdk.md), o que inclui a instalação do tempo de execução do Service Fabric, SDK, Docker e Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Faça o download do aplicativo de exemplo de tarefas pendentes

Se você não tiver compilado o aplicativo de exemplo de tarefas pendentes na [parte dois desta série de tutoriais](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md), faça o download do arquivo. Em uma janela de comando, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

O aplicativo está no diretório `src\todolistapp`.

## <a name="publish-to-azure"></a>Publicar no Azure

Para publicar o projeto de Malha do Service Fabric no Azure, clique com o botão direito do mouse em **ServiceFabricMeshApp** no Visual Studio e escolha **Publicar...**

A seguir, você verá uma caixa de diálogo **Publicar aplicativo do Service Fabric**.

![Caixa de diálogo de publicação de Malha do Service Fabric do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Escolha a conta e assinatura do Azure. Escolha um **Local**. Este artigo usa **Leste dos EUA**.

Em **Grupo de recursos**, escolha **\<Criar Novo Grupo de Recursos...>**. Uma caixa de diálogo é exibida no local onde você irá criar um novo grupo de recursos. Este artigo usa o local **Leste dos EUA** e nomeia o grupo **sfmeshTutorial1RG** (se sua organização tiver várias pessoas usando a mesma assinatura, escolha um nome de grupo exclusivo).  Pressione **Criar** para criar o grupo de recursos e retornar à caixa de diálogo de publicação.

![Caixa de diálogo do novo grupo de recursos de Malha do Service Fabric do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

De volta à caixa de diálogo **Publicar aplicativo do Service Fabric**, em **Registro de Contêiner do Azure**, escolha **\<Criar Novo Registro de Contêiner...>**. Na caixa de diálogo **Criar Registro de Contêiner**, use um nome exclusivo para o **Nome do registro de contêiner**. Especifique um **Local** (este tutorial usa **Leste dos EUA**). Escolha o **Grupo de recursos** que você criou na etapa anterior na lista suspensa, por exemplo, **sfmeshTutorial1RG**. Defina o **SKU** como **Básico** e, em seguida, pressione **Criar** para retornar à caixa de diálogo de publicação.

![Caixa de diálogo do novo grupo de recursos de Malha do Service Fabric do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

Se receber um erro informando que um provedor de recursos não foi registrado para a assinatura, você poderá registrá-lo. Primeiro, verifique se o provedor de recursos está disponível para a assinatura:

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

Se o provedor de registro do contêiner (`Microsoft.ContainerRegistry`) estiver disponível, registre-o no Powershell:

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

Na caixa de diálogo de publicação, pressione o botão **Publicar** para implantar o aplicativo do Service Fabric no Azure.

Ao publicar no Azure pela primeira vez, a imagem do docker é enviada para o ACR (Registro de Contêiner do Azure) e isso demora, dependendo do tamanho da imagem. Publicações subsequentes do mesmo projeto serão mais rápidas. É possível monitorar o andamento da implantação escolhendo o painel **Ferramentas do Service Fabric** na janela **Saída** do Visual Studio. Quando a implantação for concluída, a saída **Ferramentas do Service Fabric** exibirá o endereço IP e a porta do aplicativo na forma de uma URL.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Abra um navegador da Web e navegue até a URL para ver o site em execução no Azure.

## <a name="set-up-service-fabric-mesh-cli"></a>Configurar CLI da Malha do Service Fabric 
Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir as etapas restantes. Instale o módulo de extensão de CLI da Malha do Azure Service Fabric seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).


## <a name="check-application-deployment-status"></a>Verificar o status da implantação do aplicativo

Neste ponto, seu aplicativo já terá sido implantado. Você pode verificar o status usando o comando `app show`. 

O nome do aplicativo para o aplicativo do tutorial é `ServiceMeshApp`. Reúna os detalhes sobre o aplicativo com o seguinte comando:

```azurecli-interactive
az mesh app show --resource-group $rg --name ServiceMeshApp
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Ver todos os aplicativos atualmente implantados em sua assinatura

É possível usar o comando "lista de aplicativos" para obter uma lista dos aplicativos implantados em sua assinatura.

```cli
az mesh app list --output table
```

## <a name="see-the-application-logs"></a>Ver os logs de aplicativo

Examine os logs do aplicativo implantado:

```azurecli-interactive
az mesh code-package-log get --resource-group $rg --application-name ServiceMeshApp --service-name todoservice --replica-name 0 --code-package-name ServiceMeshApp
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua todos os recursos criados. Como um novo grupo de recursos foi criado para hospedar tanto os recursos do serviço de Malha do Service Fabric como o ACR, é possível excluir com segurança esse grupo de recursos, o que excluirá todos os recursos associados.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Como alternativa, é possível excluir o grupo de recursos [no portal](../azure-resource-manager/resource-group-portal.md#delete-resource-group-or-resources). 

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:
> [!div class="checklist"]
> * Publicar o aplicativo no Azure.
> * Verificar o status da implantação do aplicativo
> * Ver todos os aplicativos atualmente implantados em sua assinatura
> * Ver os logs de aplicativo
> * Apagar os recursos usados pelo aplicativo.

Agora que você concluiu a publicação de um aplicativo de Malha do Service Fabric no Azure, tente o seguinte:

* Explore o [exemplo de aplicativo de votação](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) para ver outro exemplo de comunicação de serviço a serviço.
* Leia [Recursos do Service Fabric](service-fabric-mesh-service-fabric-resources.md)
* Leia sobre o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest