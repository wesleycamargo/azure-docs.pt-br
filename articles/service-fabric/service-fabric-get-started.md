---
title: Configurar o ambiente de desenvolvimento | Microsoft Docs
description: "Instale o tempo de execução, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir a instalação, você estará pronto para criar aplicativos."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 7e33e00a676f4aa7143cede3380adb58ba1d11e4
ms.openlocfilehash: 2c4a92fadaa845fc0d14e5101a87aabe8f8d2891


---
# <a name="prepare-your-development-environment"></a>Preparar seu ambiente de desenvolvimento
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 Para compilar e executar os [aplicativos do Azure Service Fabric][1] em seu computador de desenvolvimento, instale o tempo de execução, o SDK e as ferramentas. Você também precisa habilitar a execução dos scripts do Windows PowerShell incluídos no SDK.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="supported-operating-system-versions"></a>Versões de sistema operacional com suporte
Há suporte de desenvolvimento para as seguintes versões de sistema operacional:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows 10

> [!NOTE]
> O Windows 7 inclui, por padrão, apenas o Windows PowerShell 2.0. Cmdlets de PowerShell do Service Fabric exigem o PowerShell 3.0 ou superior. É possível [baixar o Windows PowerShell 5.0][powershell5-download] do Centro de Download da Microsoft.
> 
> 

## <a name="install-the-runtime-sdk-and-tools"></a>Instalar o tempo de execução, o SDK e as ferramentas
O Web Platform Installer oferece duas configurações para o desenvolvimento do Service Fabric:

* [Instalar o tempo de execução do Service Fabric, SDK e ferramentas para Visual Studio 2015 (exige o Visual Studio 2015 Atualização 2 ou mais recente)][full-bundle-vs2015]
* [Instalar somente o tempo de execução do Service Fabric e o SDK (sem ferramentas do Visual Studio)][core-sdk]

## <a name="enable-powershell-script-execution"></a>Habilitar a execução de script do PowerShell
A Malha do Serviço usa scripts do Windows PowerShell para criar um cluster de desenvolvimento local e implantar aplicativos do Visual Studio. Por padrão, o Windows bloqueia a execução desses scripts. Para habilitá-los, você deve modificar sua política de execução do PowerShell. Abra o PowerShell com um administrador e insira o seguinte comando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Próximas etapas
Agora que você terminou de configurar seu ambiente de desenvolvimento, comece a compilar e executar aplicativos.

* [Criar seu primeiro aplicativo do Service Fabric no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
* [Saiba como implantar e gerenciar aplicativos no seu cluster local](service-fabric-get-started-with-a-local-cluster.md)
* [Saiba mais sobre os modelos de programação: Reliable Services e Reliable Actors](service-fabric-choose-framework.md)
* [Confira os exemplos de código do Service Fabric no GitHub](https://aka.ms/servicefabricsamples)
* [Visualizar o cluster usando o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Siga o roteiro de aprendizagem do Service Fabric para obter uma introdução abrangente à plataforma](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Página de campanha da Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI link"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI link"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI link"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395



<!--HONumber=Nov16_HO4-->


