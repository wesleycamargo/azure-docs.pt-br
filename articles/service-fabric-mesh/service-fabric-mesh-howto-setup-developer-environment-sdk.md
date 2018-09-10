---
title: Configurar um ambiente de desenvolvimento do Windows para compilar aplicativos de Malha do Service Fabric | Microsoft Docs
description: Configure seu ambiente de desenvolvimento do Windows para que você possa criar um aplicativo de malha do Service Fabric e implante-o na malha do Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 08/08/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: e95c0ad90503410601cc0764fdc559579a1dd13d
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2018
ms.locfileid: "41918864"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Configurar seu ambiente de desenvolvimento do Windows para compilar aplicativos de Malha do Service Fabric

Para compilar e executar os aplicativos de Malha do Azure Service Fabric em seu computador de desenvolvimento do Windows, instale o tempo de execução da Malha do Service Fabric, o SDK e as ferramentas.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Versões de sistema operacional com suporte

Há suporte de desenvolvimento para as seguintes versões de sistema operacional:

* Windows 10 (Enterprise, Professional ou Education)
* Windows Server 2016

## <a name="visual-studio"></a>Visual Studio

O Visual Studio 2017 é necessário para implantar aplicativos de Malha do Service Fabric. [Instale a versão 15.6.0][download-visual-studio] ou uma versão posterior e habilite as cargas de trabalho a seguir:

* Desenvolvimento Web e ASP.NET
* Desenvolvimento do Azure

## <a name="install-docker"></a>Instalar o Docker

#### <a name="windows-10"></a>Windows 10

Baixe e instale a versão mais recente do [Docker Community Edition para Windows][download-docker] para dar suporte a aplicativos do Service Fabric em contêineres usados pela Malha do Service Fabric.

Durante a instalação, selecione **Usar contêineres do Windows em vez de contêineres do Linux** quando for solicitado.

Se o Hyper-V não estiver habilitado em seu computador, o instalador do Docker se oferecerá para habilitá-lo. Clique em **OK** para fazê-lo se solicitado.

#### <a name="windows-server-2016"></a>Windows Server 2016

Se a função do Hyper-V não estiver habilitada, abra o PowerShell como administrador e execute o seguinte comando para instalar o Hyper-V e reinicie o computador. Para saber mais, confira a [Edição Docker Enterprise para Windows Server][download-docker-server].

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Reinicie seu computador.

Abra o PowerShell como um administrador e execute os seguintes comandos para instalar o Docker:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK e ferramentas

Instale o tempo de execução da Malha do Service Fabric, o SDK e as ferramentas na ordem a seguir.

1. Instale o [SDK da malha do Service Fabric][download-sdkmesh] usando o Web Platform Installer. Isso instalará também o tempo de execução e o SDK do Microsoft Azure Service Fabric.
2. Instale a [extensão de Ferramentas do Service Fabric do Visual Studio (versão prévia)][download-tools] do Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Criar um cluster

Se você estiver usando o Visual Studio, você pode ignorar esta seção porque o Visual Studio criará um cluster local para você se você não tiver um.

Para melhor desempenho de depuração ao criar e executar aplicativos do Service Fabric, é recomendável criar um cluster de nó único de desenvolvimento local. Esse cluster deve ser executado sempre que você implanta ou depura um projeto de malha do Service Fabric.

O Docker **deve** estar em execução antes de um cluster ser compilado. Teste se o Docker está em execução abrindo uma janela de terminal e executando `docker ps` para ver se ocorre um erro. Se a resposta não indicar um erro, o Docker está em execução e você está pronto para compilar um cluster.

Depois de instalar o tempo de execução, os SDKs e as ferramentas do Visual Studio, crie um cluster de desenvolvimento.

1. Feche a janela do PowerShell.
2. Abra uma janela nova, com privilégios elevados do PowerShell como administrador. Essa etapa é necessária para carregar os módulos do Service Fabric que foram instalados.
3. Digite o seguinte comando do PowerShell para criar um cluster de desenvolvimento:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster -UseMachineName
    ```

4. Para iniciar a ferramenta de gerenciamento do cluster, execute o comando do PowerShell a seguir:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```

Agora você está pronto para criar os aplicativos de malha do Service Fabric!

## <a name="next-steps"></a>Próximas etapas

Leia o tutorial [Criar um aplicativo do Azure Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.ServiceFabricMesh
[download-visual-studio]: https://www.visualstudio.com/downloads/