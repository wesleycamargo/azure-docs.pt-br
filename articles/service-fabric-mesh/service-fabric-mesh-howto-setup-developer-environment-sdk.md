---
title: Configurar um ambiente de desenvolvimento do Windows para os aplicativos de malha do Service Fabric | Microsoft Docs
description: Configure seu ambiente de desenvolvimento do Windows para que você possa criar um aplicativo de malha do Service Fabric e implante-o na malha do Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 96549696013a2dd94741090a0a017b57a3b1e19e
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125154"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-applications"></a>Configurar seu ambiente de desenvolvimento do Windows para compilar aplicativos do Service Fabric

Para compilar e executar os aplicativos do Azure Service Fabric em seu computador de desenvolvimento do Windows, instale o tempo de execução do Service Fabric, o SDK e as ferramentas.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Versões de sistema operacional com suporte

Há suporte de desenvolvimento para as seguintes versões de sistema operacional:

* Windows 10 (Enterprise, Professional ou Education)
* Windows Server 2016

## <a name="enable-hyper-v"></a>Habilitar Hyper-V

O Hyper-V deve ser habilitado para a criação de aplicativos do Service Fabric. 

### <a name="windows-10"></a>Windows 10

Abra o PowerShell como um administrador e execute o seguinte comando:

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

Reinicie seu computador. Para obter mais informações sobre como habilitar o Hyper-V, consulte [Instalar o Hyper-V no Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

### <a name="windows-server-2016"></a>Windows Server 2016

Abra o PowerShell como um administrador e execute o seguinte comando:

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Reinicie seu computador. Para obter mais informações sobre como habilitar o Hyper-V, consulte [Instalar a função de Hyper-V no Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="visual-studio"></a>Visual Studio

O Visual Studio 2017 é necessário para implantar aplicativos do Service Fabric. [Instale a versão 15.6.0][download-visual-studio] ou uma versão posterior e habilite as cargas de trabalho a seguir:

- Desenvolvimento Web e ASP.NET
- Desenvolvimento do Azure

## <a name="docker"></a>Docker

Instale o Docker para dar suporte a aplicativos do Service Fabric em contêineres usados pela malha do Service Fabric.

### <a name="windows-10"></a>Windows 10

Baixe e instale a versão mais recente do [Docker Community Edition para Windows][download-docker]. 

Durante a instalação, selecione **contêineres do Windows de uso em vez de contêineres do Linux** quando for solicitado. Você precisará, em seguida, sair e entrar novamente. Depois de entrar novamente, se você não ativou anteriormente o Hyper-V, você será solicitado a habilitar o Hyper-V. Você precisa habilitar o Hyper-V e, em seguida, reiniciar o computador.

Depois que o computador for reiniciado, o Docker solicitará que você habilite o recurso **Contêineres**, habilite-o e reinicie o computador.

### <a name="windows-server-2016"></a>Windows Server 2016

Use os seguintes comandos do PowerShell para instalar o Docker. Para saber mais, confira a [Edição Docker Enterprise para Windows Server][download-docker-server].

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Reinicie seu computador.

## <a name="sdk-and-tools"></a>SDK e ferramentas

Instale o tempo de execução da malha do Service Fabric, o SDK e as ferramentas em uma ordem de dependência.

1. Instale o [SDK da malha do Service Fabric][download-sdkmesh] usando o Web Platform Installer. Isso instalará também o tempo de execução e o SDK do Microsoft Azure Service Fabric.
2. Instale a [extensão de Ferramentas do Service Fabric do Visual Studio (versão prévia)][download-tools] do Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Criar um cluster

Para melhor desempenho de depuração ao criar e executar aplicativos do Service Fabric, é recomendável criar um cluster de nó único de desenvolvimento local. Esse cluster deve ser executado sempre que você implanta ou depura um projeto de malha do Service Fabric.

O Docker **deve** estar em execução antes de um cluster ser compilado. Teste se o Docker está em execução abrindo uma janela de terminal e executando `docker ps` para ver se ocorre um erro. Se a resposta não indicar um erro, o Docker está em execução e você está pronto para compilar um cluster.

Depois de instalar o tempo de execução, os SDKs e as ferramentas do Visual Studio, crie um cluster de desenvolvimento.

1. Feche a janela do PowerShell.
2. Abra uma janela nova, com privilégios elevados do PowerShell como administrador. Essa etapa é necessária carregar os módulos do Service Fabric que você instalou.
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
