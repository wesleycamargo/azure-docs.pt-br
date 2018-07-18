---
title: Implantar um do Service Fabric em um cluster no Azure | Microsoft Docs
description: Saiba como implantar um aplicativo em um cluster no Visual Studio.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/28/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: f83ebcce68a7abe53d7b8eaeff5913a907e3df9a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344182"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Tutorial: Implantar um aplicativo do Service Fabric em um cluster no Azure

Este tutorial é a segunda parte de uma série e mostra como implantar um aplicativo do Azure Service Fabric em um novo cluster no Azure diretamente do Visual Studio.

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Criar um cluster no Visual Studio
> * Implantar um aplicativo em um cluster remoto usando o Visual Studio

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Criar um aplicativo .NET do Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * Implantar o aplicativo em um cluster remoto
> * [Adicionar um ponto de extremidade HTTPS a um serviço de front-end do ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Configurar CI/CD usando o Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurar monitoramento e diagnóstico para o aplicativo](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Instale o Visual Studio 2017](https://www.visualstudio.com/) e instale as cargas de trabalho de **desenvolvimento do Azure** e de **desenvolvimento para a Web e ASP.NET**.
* [Instalar o SDK do Service Fabric](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Baixar o aplicativo de exemplo Votação

Se você não tiver criado o aplicativo de exemplo Votação na [parte um desta série de tutoriais](service-fabric-tutorial-create-dotnet-app.md), poderá baixá-lo. Em uma janela de comando, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric

Agora que o aplicativo está pronto, você poderá implantá-lo no cluster diretamente por meio do Visual Studio. Um cluster do [Service Fabric](/service-fabric/service-fabric-deploy-anywhere.md) é um conjunto de computadores físicos ou virtuais conectados via rede, nos quais os microsserviços são implantados e gerenciados.

Você tem duas opções de implantação dentro do Visual Studio:

* Criar um cluster no Microsoft Azure a partir do Visual Studio. Essa opção permite que você crie um cluster seguro diretamente no Visual Studio com suas configurações preferenciais. Esse tipo de cluster é ideal para cenários de teste, onde você pode criar o cluster e, em seguida, publicar diretamente nele no Visual Studio.
* Publicar em um cluster existente na sua assinatura.  Você pode criar clusters do Service Fabric por meio do [portal do Microsoft Azure](https://portal.azure.com)usando o [PowerShel](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) ou [os scripts da CLI do Azure](./scripts/cli-create-cluster.md), ou de um modelo do [Azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

Este tutorial cria um cluster a partir do Visual Studio. Se já houver um cluster implantado, é possível copiar e colar o ponto de extremidade de conexão ou escolhê-lo na sua assinatura.
> [!NOTE]
> Muitos serviços usam o proxy inverso para se comunicar entre si. Os clusters criados no Visual Studio e os clusters party têm proxy inverso habilitado por padrão.  Se usar um cluster existente, você deverá [habilitar o proxy inverso no cluster](service-fabric-reverseproxy.md#setup-and-configuration).

### <a name="find-the-votingweb-service-endpoint"></a>Localizar o ponto de extremidade de serviço VotingWeb

Primeiro, encontre o ponto de extremidade do serviço web de front-end.  O serviço web de front-end está ouvindo em uma porta específica.  Quando o aplicativo for implantado para um cluster no Azure, o cluster e o aplicativo executam atrás de um balanceador de carga do Azure.  A porta do aplicativo deve estar aberta no balanceador de carga do Microsoft Azure para que o tráfego de entrada pode obter por meio para o serviço web.  A porta (por exemplo, 8080) foi encontrada no arquivo *VotingWeb/PackageRoot/ServiceManifest.xml* no elemento **Ponto de extremidade**:

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Na próxima etapa, especifique essa porta na guia  **Avançado** da caixa de diálogo **Criar cluster**.  Se você estiver implantando o aplicativo a um cluster existente, você pode abrir essa porta no balanceador de carga do Microsoft Azure usando um [script do PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) ou o [portal do Microsoft Azure](https://portal.azure.com).

### <a name="create-a-cluster-in-azure-through-visual-studio"></a>Criar um cluster no Microsoft Azure através do Visual Studio.

Clique com botão direito do mouse no projeto de aplicativo no Gerenciador de Soluções e escolha **Publicar**.

Entre usando sua conta do Azure para que você possa ter acesso às assinaturas. Essa etapa é opcional se você está usando um cluster party.

Selecione a lista suspensa para o **Ponto de Extremidade de Conexão** e selecione a opção **<Create New Cluster...>**.

![Caixa de diálogo Publicar](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Na caixa de diálogo **Criar cluster**, modifique as seguintes configurações:

1. Especifique o nome do cluster no campo **Nome do Cluster**, bem como a assinatura e o local que você deseja usar.
2. Opcional: você pode modificar o número de nós. Por padrão, existem três nós, o mínimo necessário para testar cenários do Service Fabric.
3. Selecione a guia **Certificado**. Nessa guia, digite uma senha a ser usada para proteger o certificado do cluster. Esse certificado ajuda a proteger o cluster. Você também pode modificar o caminho de onde deseja salvar o certificado. O Visual Studio também pode importar o certificado para você, já que essa é uma etapa necessária para publicar o aplicativo no cluster.
4. Selecione a guia **Detalhes da VM**. Especifique a senha que você deseja usar para as VMs (máquinas virtuais) que compõem o cluster. O nome de usuário e a senha podem ser usados para se conectar remotamente às VMs. Você também deve selecionar um tamanho de máquina VM e pode alterar a imagem da VM, se necessário.
5. Na guia **Avançado**, você pode modificar a lista de portas que deseja abrir no balanceador de carga que será criado juntamente com o cluster.  Adicione o ponto de extremidade de serviço VotingWeb descobertos em uma etapa anterior. Você também pode adicionar uma chave existente do Application Insights para direcionar os arquivos de log do aplicativo.
6. Quando você terminar de modificar as configurações, selecione o botão **Criar**. A criação levará alguns minutos para ser concluída; a janela de saída indica quando o cluster foi totalmente criado.

![Diálogo Criar Cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

## <a name="deploy-the-sample-application"></a>Implantar o aplicativo de exemplo

Quando o cluster que você deseja usar estiver pronto, clique com botão direito do mouse no projeto de aplicativo e escolha **Publicar**.

Quando a publicação for concluída, você poderá enviar uma solicitação ao aplicativo por meio de um navegador.

Abra o navegador de sua preferência e digite o endereço do cluster (o ponto de extremidade de conexão sem as informações de porta – por exemplo, win1kw5649s.westus.cloudapp.azure.com).

Agora você verá o mesmo resultado que viu ao executar o aplicativo localmente.

![Resposta de API do Cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um cluster no Visual Studio
> * Implantar um aplicativo em um cluster remoto usando o Visual Studio

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Habilitar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
