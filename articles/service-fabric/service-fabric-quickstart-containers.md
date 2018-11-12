---
title: Criar um aplicativo de contêiner Windows no Service Fabric no Azure | Microsoft Docs
description: Neste início rápido, você cria seu primeiro aplicativo de contêiner do Windows no Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/30/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: be6c13b998664cda65b1002c23726cbe89359a59
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261189"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Início Rápido: Implantar contêineres do Windows no Service Fabric

O Azure Service Fabric é uma plataforma de sistemas distribuídos para implantação e gerenciamento de contêineres e microsserviços escalonáveis e confiáveis.

Executar um aplicativo existente em um contêiner do Windows em um cluster do Service Fabric não requer alterações no seu aplicativo. Este guia de início rápido mostra como implantar uma imagem de contêiner do Docker predefinida em um aplicativo do Service Fabric. Quando você terminar, terá um contêiner do Windows Server 2016 Nano Server e o IIS em execução. Este guia de início rápido descreve a implantação de um contêiner do Windows; leia [este guia de início rápido](service-fabric-quickstart-containers-linux.md) para implantar um contêiner do Linux.

![Página de Web do IIS padrão][iis-default]

Neste guia de início rápido, você aprende a:

* Empacotar um contêiner de imagem do Docker
* Configurar a comunicação
* Compilar e empacotar o aplicativo do Service Fabric
* Implantar o aplicativo de contêiner no Azure

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure (você pode criar um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* Um computador de desenvolvimento executando:
  * Visual Studio 2015 ou Visual Studio 2017.
  * [Ferramentas e SDK do Service Fabric](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Empacotar um recipiente de imagem do Docker com o Visual Studio

As ferramentas e o SDK do Service Fabric oferecem um modelo de serviço para ajudar você a implantar um contêiner em um cluster do Service Fabric.

Inicie o Visual Studio como “Administrador”.  Selecione **Arquivo** > **Novo** > **Projeto**.

Selecione **Aplicativo do Service Fabric**, nomeie-o como "MyFirstContainer" e clique em **OK**.

Selecione **Contêiner** nos modelos **Aplicativos e Contêineres Hospedados**.

Em **Nome da Imagem**, insira "microsoft/iis:nanoserver", a [imagem de base do Windows Server Nano Server e IIS](https://hub.docker.com/r/microsoft/iis/).

Configure o mapeamento de portas porta a host do contêiner para que as solicitações de entrada para o serviço na porta 80 sejam mapeadas para a porta 80 no contêiner.  Defina a **Porta do Contêiner** como "80" e defina a **Porta do Host** como "80".  

Chame o serviço de "MyContainerService" e clique em **OK**.

![Caixa de diálogo Novo serviço][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>Especifique o build do sistema operacional para a imagem de contêiner
Contêineres criados com uma versão específica do Windows Server podem não ser executados em um host executando uma versão diferente do Windows Server. Por exemplo, os contêineres do Windows Server criados usando o Windows Server 1709 não funcionam em hosts que estão executando o Windows Server 2016. Para obter mais informações, consulte [Compatibilidade do sistema operacional contêiner e do sistema operacional do host do Windows Server](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). 

Com a versão 6.1 do tempo de execução do Service Fabric e mais recente, você pode especificar várias imagens do sistema operacional por contêiner e marcar cada um com a versão do build do sistema operacional na que ele deve ser implantado. Isso ajuda a garantir que seu aplicativo será executado em hosts que executam diferentes versões do sistema operacional Windows. Para saber mais, consulte [Especifique a compilação do sistema operacional das imagens de contêiner específicas](service-fabric-get-started-containers.md#specify-os-build-specific-container-images). 

A Microsoft publica imagens diferentes para versões do IIS criadas em diferentes versões do Windows Server. Para certificar-se de que o Service Fabric implanta um contêiner compatível com a versão do Windows Server em execução em nós de cluster onde ele implanta o aplicativo, adicione as seguintes linhas ao arquivo *ApplicationManifest.xml*. A versão de compilação para o Windows Server 2016 é 14393 e a versão de compilação para a versão 1709 do Windows Server é 16299. 

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="microsoft/iis:nanoserverDefault" /> 
          <Image Name= "microsoft/iis:nanoserver" Os="14393" /> 
          <Image Name="microsoft/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

O manifesto do serviço continua a especificar apenas uma imagem para o Nano Server, `microsoft/iis:nanoserver`. 

## <a name="create-a-cluster"></a>Criar um cluster

Para implantar o aplicativo em um cluster no Azure, você pode ingressar em um cluster de terceiros. Os clusters Party são clusters gratuitos de duração limitada do Service Fabric, hospedados no Azure e executados pela equipe do Service Fabric, nos quais qualquer pessoa pode implantar aplicativos e aprender mais sobre a plataforma.  O cluster usa um único certificado autoassinado para nó-a-nó, bem como segurança de cliente para nó. Clusters de equipe dão suporte a contêineres. Se optar por configurar e usar seu próprio cluster, ele deve estar sendo executado em um SKU que ofereça suporte a contêineres (como Windows Server 2016 Datacenter com Contêineres).

Entre e [ingresse em um cluster do Windows](https://aka.ms/tryservicefabric). Baixe o certificado PFX em seu computador clicando no link **PFX**. Clique no link **Como se conectar a um cluster de equipe seguro?** e copie a senha do certificado. O certificado, senha do certificado e os valores de **Ponto de extremidade de conexão** são utilizados nas etapas a seguir.

![PFX e ponto de extremidade de conexão](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

> [!Note]
> Há um número limitado de clusters de equipe disponíveis por hora. Se você receber um erro ao tentar se inscrever para um cluster de equipe, você poderá aguardar um período e tentar novamente, ou você pode seguir estas etapas no tutorial [Implantar um aplicativo .NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application) para criar um cluster do Service Fabric em sua assinatura do Azure e implantar o aplicativo nele. O cluster criado pelo Visual Studio oferece suporte a contêineres. Depois de implantar e verificar o aplicativo em seu cluster, é possível pular para a seção [Completar aplicativo de exemplo e manifestos de serviço do Service Fabric](#complete-example-service-fabric-application-and-service-manifests) neste de início rápido.
>

Em um computador com Windows, instale o PFX no repositório de certificados *CurrentUser\My*.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

Lembre-se da impressão digital para uma etapa posterior.

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Implantar o aplicativo no Azure usando o Visual Studio

Agora que o aplicativo está pronto, você poderá implantá-lo no cluster diretamente por meio do Visual Studio.

Clique com botão direito do mouse em **MyFirstContainer** no Gerenciador de Soluções e escolha **Publicar**. A caixa de diálogo Publicar será exibida.

Copie o **Ponto de Extremidade de Conexão** da página do cluster Party no campo **Ponto de Extremidade de Conexão**. Por exemplo, `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Clique em **Parâmetros de Conexão Avançados** e verifique as informações de parâmetro de conexão.  Os valores de *FindValue* e *ServerCertThumbprint* devem coincidir com a impressão digital do certificado instalado na etapa anterior.

![Caixa de diálogo Publicar](./media/service-fabric-quickstart-containers/publish-app.png)

Clique em **Publicar**.

Cada aplicativo no cluster deve ter um nome exclusivo.  No entanto, Clusters Party são um ambiente público compartilhado e pode haver um conflito com um aplicativo existente.  Se houver um conflito de nome, renomeie o projeto do Visual Studio e implante novamente.

Abra um navegador e navegue até o **ponto de extremidade de conexão** especificado na página de cluster de terceiros. Como opção, preceda o identificador do esquema, `http://`, e anexe a porta, `:80`, para a URL. Por exemplo, http://zwin7fh14scd.westus.cloudapp.azure.com:80. Você deve ver a página da Web do IIS padrão: ![Página da Web do IIS padrão][iis-default]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

* Empacotar um contêiner de imagem do Docker
* Configurar a comunicação
* Compilar e empacotar o aplicativo do Service Fabric
* Implantar o aplicativo de contêiner no Azure

Para saber mais sobre como trabalhar com os contêineres do Windows no Service Fabric, prossiga com o tutorial sobre aplicativos de contêiner do Windows.

> [!div class="nextstepaction"]
> [Criar um aplicativo de contêiner do Windows](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
