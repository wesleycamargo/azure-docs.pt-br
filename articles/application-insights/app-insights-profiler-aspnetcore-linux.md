---
title: Criar o perfil de Aplicativos Web ASP.NET Core Azure Linux com o Application Insights Profiler | Microsoft Docs
description: Visão geral do conceito e um tutorial passo a passo sobre como habilitá-lo
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 2d7405baee84b53311f01e748ca7975147c107d8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Criar o perfil de Aplicativos Web ASP.NET Core Azure Linux com o Application Insights Profiler

Esse recurso está atualmente em versão prévia

Descubra quanto tempo é gasto em cada método do seu aplicativo Web em tempo real ao usar o [Application Insights](app-insights-overview.md). Criador de perfil agora está disponível para aplicativos de web do ASP.NET core hospedados no Linux em Serviços de Aplicativos. Este guia fornece instruções passo a passo sobre como os rastreamentos do criador de perfil podem ser coletados para aplicativos web do ASP.NET core Linux.

Depois de concluir este passo a passo, seu aplicativo irá coletar rastreamentos de criador de perfil semelhantes à captura de tela abaixo. Neste exemplo, o rastreamento do criador de perfil indica que uma solicitação da web específica está lenta porque a maior parte do tempo é gasto na espera. O afunilamento no código que retarda o aplicativo é precedido por um ícone de chamas. Este exemplo mostra `About` método em `HomeController` lento do aplicativo web porque ela estava chamando `Thread.Sleep`.

![Rastreamentos do Criador de Perfil](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="pre-requisites"></a>Pré-requisitos
Instruções abaixo aplicadas a todos os ambientes de desenvolvimento do Windows, Linux e Mac:

* Instale o [SDK 2.1.2 do .NET Core ou posterior](https://www.microsoft.com/net/download/windows/build)
* Instale o Git seguindo as instruções em [Introdução - Instalação do Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

## <a name="setup-project-locally"></a>Instale o projeto localmente

1. Abra um prompt de comando no seu computador. As instruções abaixo funcionam para todos os ambientes de desenvolvimento do Windows, Linux e Mac.

2. Crie um aplicativo Web MVC ASP.NET core
    ```
    dotnet new mvc -n LinuxProfilerTest
    ```
3. Altere o diretório no prompt de comando para a pasta raiz do projeto

4. Adicione o pacote do Nuget para coletar rastreamentos do criador de perfil.
    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```
5. Adicione uma linha de código para atrasar aleatoriamente alguns segundos no HomeController.cs

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```
6. Salve e confirme suas alterações no repositório local

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-azure-app-service-for-hosting-your-project"></a>Crie Serviço de Aplicativo do Azure para hospedar seu projeto
1. Crie um ambiente de Serviços de Aplicativos do Linux

    ![Crie Serviços de Aplicativos do Linux](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. Crie credencial de implantação. Anote sua senha, você precisará dela posteriormente quando implantar seu aplicativo.

    ![Crie credenciais de implantação](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Escolha a opção de implantação. Configure um repositório Git local no aplicativo web seguindo as instruções no portal do Azure. Um repositório Git será criado automaticamente para você.

    ![Configure o repositório Git](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

Mais opções de implantação estão disponíveis [aqui](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type)

## <a name="deploy-your-project"></a>Implante o seu projeto

1. No seu prompt de comando, navegue até a pasta raiz do projeto. Adicione repositório remoto de Git para apontar para um no Serviços de Aplicativos:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```
    * Use 'username' da etapa de "criar credenciais de implantação".
    * Use o 'nome do aplicativo' da etapa de "criar o serviço de aplicativo".

2. Implante o projeto efetuando push nas alterações para o Azure

    ```
    git push azure master
    ```
Você verá saídas semelhantes às seguintes:

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Adicionar o Application Insights para monitorar seus aplicativos web
1. [Crie um recurso Application Insights](./app-insights-create-new-resource.md)
2. Copie o iKey do recurso do Application Insights e defina as seguintes configurações nos serviços de aplicativo

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![Defina as configurações do aplicativo](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    Alterar as configurações de aplicativo reiniciará automaticamente o site. Depois que as novas configurações são aplicadas, o criador de perfil começará a ser executado por 2 minutos imediatamente. em seguida, ele será executado por 2 minutos a cada hora.

3. Gere algum tráfego para seu site. Você pode atualizar a página ```About``` do site algumas vezes.

4. Aguarde 2 a 5 minutos para que os eventos possam ser agregados ao Application Insights.

5. Navegue até o painel de desempenho do Application Insights no portal do Azure. Você verá os rastreamentos do criador de perfil no canto inferior direito.

    ![Exibir rastreamentos](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="next-steps"></a>Próximas etapas
Se você estiver usando contêineres personalizados hospedados pelos Serviços de Aplicativos, siga as instruções em [ Habilitar o Criador de Perfil de Serviço para aplicativo em contêineres do ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) para habilitar o Criador de Perfil do Aplicativo Insights

Se você tiver problemas ou sugestões, informe ao nosso repositório do Github: [ApplicationInsights-profiler-AspNetCore: problemas](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues)
