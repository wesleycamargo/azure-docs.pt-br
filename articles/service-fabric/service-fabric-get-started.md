<properties
   pageTitle="Configurar o ambiente de desenvolvimento | Microsoft Azure"
   description="Instale o tempo de execução, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir a instalação, você estará pronto para criar aplicativos."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/16/2016"
   ms.author="ryanwi"/>

# Preparar seu ambiente de desenvolvimento
 Para compilar e executar os [aplicativos do Azure Service Fabric][1] em seu computador de desenvolvimento, instale o tempo de execução, o SDK e as ferramentas. Você também precisa habilitar a execução dos scripts do Windows PowerShell incluídos no SDK.

## Pré-requisitos
### Versões de sistema operacional com suporte
Há suporte de desenvolvimento para as seguintes versões de sistema operacional:

- Windows 7
- Windows 8/Windows 8.1
- Windows Server 2012 R2
- Windows 10

>[AZURE.NOTE] O Windows 7 inclui, por padrão, apenas o Windows PowerShell 2.0. Cmdlets de PowerShell do Service Fabric exigem o PowerShell 3.0 ou superior. É possível [baixar o Windows PowerShell 5.0][powershell5-download] do Centro de Download da Microsoft.

## Instalar o tempo de execução, o SDK e as ferramentas

O Web Platform Installer oferece três configurações para o desenvolvimento do Service Fabric:

- [Instalar a execução do Service Fabric, SDK e ferramentas para Visual Studio 2015 Atualização 2 ou mais recente][full-bundle-vs2015]
- [Instalar o Service Fabric Runtime, o SDK e as ferramentas para a Visualização do Visual Studio “15”][full-bundle-dev15]
- [Instalar somente o Service Fabric Runtime e o SDK (sem ferramentas para Visual Studio)][core-sdk]

## Habilitar a execução de script do PowerShell

A Malha do Serviço usa scripts do Windows PowerShell para criar um cluster de desenvolvimento local e implantar aplicativos do Visual Studio. Por padrão, o Windows bloqueia a execução desses scripts. Para habilitá-los, você deve modificar sua política de execução do PowerShell. Abra o PowerShell com um administrador e insira o seguinte comando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Próximas etapas
Agora que você terminou de configurar seu ambiente de desenvolvimento, comece a compilar e executar aplicativos.

- [Criar seu primeiro aplicativo do Service Fabric no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Saiba como implantar e gerenciar aplicativos no seu cluster local](service-fabric-get-started-with-a-local-cluster.md)
- [Saiba mais sobre os modelos de programação: Reliable Services e Reliable Actors](service-fabric-choose-framework.md)
- [Confira os exemplos de código do Service Fabric no GitHub](https://aka.ms/servicefabricsamples)
- [Visualizar o cluster usando o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
- [Siga o roteiro de aprendizagem do Service Fabric para obter uma introdução abrangente à plataforma](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Página de campanha da Malha do Serviço"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015-2_1 "Link do WebPI do VS 2015"
[full-bundle-dev15]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15-2_1 "Link do WebPI do Dev15"
[core-sdk]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=ServiceFabricSDK_2_1 "Link do WebPI do SDK principal"
[powershell5-download]: https://www.microsoft.com/pt-BR/download/details.aspx?id=50395

<!---HONumber=AcomDC_0824_2016-->