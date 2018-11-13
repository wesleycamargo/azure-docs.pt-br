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
ms.date: 07/12/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: fe6df20d294a3b1802d396085c36a6587dc45730
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249074"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Tutorial: Implantar um aplicativo do Service Fabric em um cluster no Azure

Este tutorial é a parte dois de uma série. Ele mostra como implantar um aplicativo do Azure Service Fabric em um novo cluster no Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um cluster party.
> * Implantar um aplicativo em um cluster remoto usando o Visual Studio.

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Criar um aplicativo do Service Fabric .NET](service-fabric-tutorial-create-dotnet-app.md).
> * Implantar o aplicativo em um cluster remoto.
> * [Adicionar um ponto de extremidade HTTPS a um serviço de front-end do ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md).
> * [Configure CI/CD usando o Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).
> * [Configurar monitoramento e diagnóstico para o aplicativo](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Instale o Visual Studio 2017](https://www.visualstudio.com/) e instale as cargas de trabalho de **desenvolvimento do Azure** e de **desenvolvimento para a Web e ASP.NET**.
* [Instalar o SDK do Service Fabric](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Baixar o aplicativo de exemplo Votação

Se você não tiver criado o aplicativo de exemplo de votação na [parte um desta série de tutoriais](service-fabric-tutorial-create-dotnet-app.md), poderá baixá-lo. Em uma janela de comando, execute o código a seguir para clonar o repositório de aplicativos de exemplo no computador local.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

## <a name="publish-to-a-service-fabric-cluster"></a>Publicar em um cluster do Service Fabric

Agora que o aplicativo está pronto, você poderá implantá-lo no cluster diretamente por meio do Visual Studio. Um [cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere) é um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados.

Para este tutorial, você tem duas opções para a implantação do aplicativo de votação em um cluster do Service Fabric usando o Visual Studio:

* Publicar em um cluster de avaliação gratuita (party). 
* Publicar em um cluster existente na sua assinatura. Você pode criar clusters do Service Fabric por meio do [portal do Azure](https://portal.azure.com) usando os scripts de [PowerShel](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) ou da [CLI do Azure](./scripts/cli-create-cluster.md), ou de um [modelo do Azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> Muitos serviços usam o proxy inverso para se comunicar entre si. Os clusters criados no Visual Studio e os clusters party têm proxy inverso habilitado por padrão. Se você estiver usando um cluster existente, você deverá [habilitar o proxy inverso no cluster](service-fabric-reverseproxy-setup.md).


### <a name="find-the-voting-web-service-endpoint-for-your-azure-subscription"></a>Localizar o ponto de extremidade de serviço Web para sua assinatura do Azure

Para publicar o aplicativo de votação em sua própria assinatura do Azure, encontre o ponto de extremidade do serviço Web de front-end. Se você usar um cluster de terceiros, conecte-se à porta 8080 usando o exemplo de votação aberto automaticamente. Você não precisa configurá-lo no balanceador de carga do cluster de terceiros.

O serviço web de front-end está ouvindo em uma porta específica. Quando o aplicativo for implantado para um cluster no Azure, o cluster e o aplicativo executam atrás de um balanceador de carga do Azure. A porta do aplicativo deve ser aberta usando uma regra no balanceador de carga do Azure para o cluster. A porta aberta envia o tráfego de entrada para o serviço Web. A porta pode ser encontrada no arquivo **VotingWeb/PackageRoot/ServiceManifest.xml** no elemento **Ponto de extremidade**. Um exemplo é a porta 8080.

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Para sua assinatura do Azure, abra essa porta usando uma regra de balanceamento de carga no Azure por meio de um [script do PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) ou por meio do balanceador de carga para esse cluster no [portal do Azure](https://portal.azure.com).

### <a name="join-a-party-cluster"></a>Ingressar em um cluster de terceiros

> [!NOTE]
>  Para publicar o aplicativo em seu próprio cluster dentro de uma assinatura do Azure, vá direto para a seção [Publicar o aplicativo usando o Visual Studio](#publish-the-application-by-using-visual-studio). 

Clusters de entidade são clusters do Service Fabric gratuitos e com tempo limitado hospedados no Azure e executados pela equipe do Service Fabric. Qualquer pessoa pode implantar aplicativos e saber mais sobre a plataforma. O cluster usa um certificado único e autoassinado para segurança entre nós e entre cliente e nó.

Entre e [ingresse em um cluster do Windows](https://aka.ms/tryservicefabric). Baixe o certificado PFX em seu computador selecionando o link **PFX**. Selecione o link **Como se conectar a um cluster de equipe seguro?** e copie a senha do certificado. O certificado, senha do certificado e os valores de **Ponto de extremidade de conexão** são utilizados nas etapas a seguir.

![PFX e ponto de extremidade de conexão](./media/service-fabric-quickstart-dotnet/party-cluster-cert.png)

> [!Note]
> Há um número limitado de clusters de terceiros disponíveis por hora. Se você receber um erro ao tentar inscrever-se em um cluster de terceiros, aguarde e tente novamente. Ou siga estas etapas no tutorial [Implantar um aplicativo .NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application) para criar um cluster do Service Fabric em sua assinatura do Azure e implantar o aplicativo nele. Se você ainda não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
>

No computador com Windows, instale o PFX no repositório de certificados **CurrentUser\My**.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

Lembre-se da impressão digital para a próxima etapa.

> [!Note]
> Por padrão, o serviço de front-end da Web está configurado para escutar o tráfego de entrada na porta 8080. A porta 8080 está aberta no cluster de terceiros. Se você precisar alterar a porta do aplicativo, altere-a para uma das portas que estão abertas no cluster de terceiros.
>

### <a name="publish-the-application-by-using-visual-studio"></a>Publicar o aplicativo usando o Visual Studio

Agora que o aplicativo está pronto, você poderá implantá-lo no cluster diretamente por meio do Visual Studio.

1. Clique com o botão direito do mouse em **Votação** no Gerenciador de Soluções. Escolha **Publicar**. A caixa de diálogo **Publicar** é exibida.

2. Copie o **Ponto de extremidade de conexão** da página do cluster de terceiros ou sua assinatura do Azure para o campo **Ponto de extremidade de conexão**. Um exemplo é `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Selecione **Parâmetros de conexão avançados**.  Certifique-se de que os valores **FindValue** e **ServerCertThumbprint** coincidam com a impressão digital do certificado instalado em uma etapa anterior para um cluster de terceiros ou que o certificado corresponda à sua assinatura do Azure.

    ![Publicar um aplicativo do Service Fabric](./media/service-fabric-quickstart-dotnet/publish-app.png)

    Cada aplicativo no cluster deve ter um nome exclusivo. No entanto, clusters de terceiros são um ambiente público compartilhado e pode haver um conflito com um aplicativo existente. Se houver um conflito de nome, renomeie o projeto do Visual Studio e implante-o novamente.

3. Selecione **Publicar**.

4. Para ir até o seu aplicativo de votação no cluster, abra um navegador e digite o endereço do cluster seguido por **:8080**. Ou insira outra porta, se houver alguma configurada. Um exemplo é `http://zwin7fh14scd.westus.cloudapp.azure.com:8080`. Você verá o aplicativo em execução no cluster no Azure. Na página da Web de votação, tente adicionar e excluir opções de votação e vote em uma ou mais dessas opções.

    ![Exemplo de votação do Service Fabric](./media/service-fabric-quickstart-dotnet/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Próximas etapas

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Habilitar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
