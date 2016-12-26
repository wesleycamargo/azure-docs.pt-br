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
ms.date: 12/13/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 04092b735fa77c72ffe6c492a3fc975eac2e99fd
ms.openlocfilehash: a71b77a320e9321eaa857acfcfae8822de0ac9e5


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
O Web Platform Installer oferece duas configurações para o desenvolvimento do Service Fabric.

Visual Studio 2017 (o Azure Development and Management Workload deve ser instalado):

* [Instalar o Service Fabric runtime e o SDK (sem ferramentas para Visual Studio)][core-sdk]

Visual Studio 2015 (requer a atualização do Visual Studio 2015 2 ou posterior):

* [Instalar o Service Fabric runtime, o SDK e as ferramentas][full-bundle-vs2015]
* [Instalar somente o Service Fabric runtime e o SDK (sem ferramentas para Visual Studio)][core-sdk]

> [!WARNING]
> Os clientes relataram erros durante a instalação ao usar esses links de inicialização, ou quando esses links foram usados no navegador Chrome. Esses problemas são conhecidas no Web Platform Installer e estão sendo corrigidos.  Para solucionar esse problema, tente o seguinte:
>- Inicie os links acima em navegadores Internet Explorer ou Edge, ou
>- Inicie o Web Platform Installer no menu Iniciar, procure "Service Fabric" e instale o SDK
> 
> Pedimos desculpas pelo transtorno. 

As versões atuais são:
* Service Fabric SDK 2.4.145
* Service Fabric runtime 5.4.145
* Ferramentas Visual Studio 2015 1.4.41209

Para obter uma lista das versões com suporte, consulte [suporte ao Service Fabric](service-fabric-support.md)

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
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Página da campanha do Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI link"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI link"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI link"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395



<!--HONumber=Dec16_HO2-->


