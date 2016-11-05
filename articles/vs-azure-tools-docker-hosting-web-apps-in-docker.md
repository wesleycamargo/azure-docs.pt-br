---
title: Implantar um contêiner do Docker do Linux do ASP.NET Core em um host do Docker remoto | Microsoft Docs
description: Saiba como usar as Ferramentas do Visual Studio para Docker para implantar um aplicativo Web do ASP.NET Core em um contêiner do Docker em execução em uma VM do Linux do Host do Docker do Azure
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: ''

ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2016
ms.author: mlearned

---
# Implantar um contêiner ASP.NET em um host remoto do Docker
## Visão geral
O Docker é um mecanismo de contêiner leve, semelhante em alguns pontos a uma máquina virtual, que você pode usar para hospedar aplicativos e serviços. Este tutorial explica como usar a extensão [Ferramentas do Visual Studio 2015 para Docker](http://aka.ms/DockerToolsForVS) para implantar um aplicativo ASP.NET Core em um host do Docker no Azure usando o PowerShell.

## Pré-requisitos
Para concluir este tutorial, você precisará:

* Criar uma VM de Host do Docker no Azure, conforme descrito em [Usar o computador Docker com o driver do Azure](virtual-machines/virtual-machines-linux-docker-machine.md)
* Instalar o [Visual Studio 2015 Atualização 3](https://go.microsoft.com/fwlink/?LinkId=691129)
* [SDK do Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)
* Instalar as [Ferramentas do Visual Studio 2015 para Docker - Prévia](http://aka.ms/DockerToolsForVS)

## 1\. Criar um aplicativo Web ASP.NET Core
As etapas a seguir guiarão você na criação de um aplicativo básico ASP.NET Core que será usado neste tutorial.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 2\. Adicionar suporte ao Docker
[!INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## 3\. Use o Script do PowerShell DockerTask.ps1
1. Abra um prompt do PowerShell para o diretório-raiz do seu projeto.
   
   ```
   PS C:\Src\WebApplication1>
   ```
2. Valide o host remoto em execução. Você deve ver o estado = Em execução
   
   ```
   docker-machine ls
   NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
   MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
   ```
   
   > [!NOTE]
   > Se estiver usando a versão Beta do Docker, o host não será listado aqui.
   > 
   > 
3. Compilar o aplicativo usando o parâmetro -Build
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
   ```  
   
   > [!NOTE]
   > Se você estiver usando a versão Beta do Docker, omita o argumento -Machine
   > 
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
   > ```  
   > 
   > 
4. Executar o aplicativo usando o parâmetro -Run
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
   ```
   
   > [!NOTE]
   > Se você estiver usando a versão Beta do Docker, omita o argumento -Machine
   > 
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
   > ```
   > 
   > 
   
   Quando o docker for concluído, você deverá ver resultados semelhantes ao seguinte:
   
   ![Exibir aplicativo][3]

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png

<!---HONumber=AcomDC_0921_2016-->