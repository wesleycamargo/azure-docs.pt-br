---
title: "Criar um aplicativo Web .NET Core e implantá-lo no Serviço de Aplicativo no Linux | Microsoft Docs"
description: "Implante seu primeiro aplicativo .NET Core Olá, Mundo no Serviço de Aplicativo no Linux em alguns minutos."
keywords: "serviço de aplicativo do azure, aplicativo web, dotnet, core, linux, oss"
services: app-service
documentationCenter: 
author: cephalin
manager: syntaxc4
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: quickstart
ms.date: 08/30/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 2a8000cadd6f6d7204e1790df62443a7ac7598c9
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="create-a-net-core-web-app-in-app-service-on-linux"></a>Criar um aplicativo Web .NET Core no Serviço de Aplicativo no Linux

O [Serviço de Aplicativo no Linux](app-service-linux-intro.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches usando o sistema operacional Linux. Este guia de início rápido mostra como criar um aplicativo [.NET Core](https://docs.microsoft.com/aspnet/core/) no Serviço de Aplicativo no Linux. Crie o aplicativo Web usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) e use o Git para implantar o código .NET Core no aplicativo Web.

![Aplicativo de exemplo em execução no Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Você pode seguir as etapas abaixo usando um computador Mac, Windows ou Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* [Instalar o Git](https://git-scm.com/)
* [Instalar o SDK do .NET Core](https://www.microsoft.com/net/download/core)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-app-locally"></a>Criar o aplicativo localmente

Em uma janela de terminal no computador, crie um diretório chamado `hellodotnetcore` e altere o diretório atual para ele.

```bash
md hellodotnetcore
cd hellodotnetcore
```

Crie um novo aplicativo Web .NET Core.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Executar o aplicativo localmente

Restaure os pacotes NuGet e execute o aplicativo.

```bash
dotnet restore
dotnet run
```

Abra um navegador da Web e navegue até o aplicativo em `http://localhost:5000`.

Você vê a mensagem **Olá, Mundo** no aplicativo de exemplo exibido na página.

![Testar com um navegador](media/quickstart-dotnetcore/dotnet-browse-local.png)

Na janela do terminal, pressione **Ctrl+C** para sair do servidor Web. Inicialize um repositório Git para o projeto .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-with-built-in-image"></a>Criar um aplicativo Web com imagem interna

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-no-h.md)]

Navegue até o aplicativo Web recém-criado. Substitua _&lt;nome do aplicativo>_ por um nome exclusivo do aplicativo.

```bash
http://<app name>.azurewebsites.net
```

![Página de aplicativo Web vazia](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 22, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (18/18), done.
Writing objects: 100% (22/22), 51.21 KiB | 3.94 MiB/s, done.
Total 22 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '741f16d1db'.
remote: Generating deployment script.
remote: Project file path: ./hellodotnetcore.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: ...............................................................................................
remote:   Restoring packages for /home/site/repository/hellodotnetcore.csproj...
remote: ....................................
remote:   Installing System.Xml.XPath 4.0.1.
remote:   Installing System.Diagnostics.Tracing 4.1.0.
remote:   Installing System.Threading.Tasks.Extensions 4.0.0.
remote:   Installing System.Reflection.Emit.ILGeneration 4.0.1.
remote:   ...
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://cephalin-dotnetcore.scm.azurewebsites.net/cephalin-dotnetcore.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Navegar até o aplicativo

Navegue até o aplicativo implantado usando o navegador da Web.

```bash
http://<app_name>.azurewebsites.net
```

O código de exemplo do Node.js está em execução em um aplicativo Web com imagem interna.

![Aplicativo de exemplo em execução no Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Parabéns!** Você implantou seu primeiro aplicativo Node.js no Serviço de Aplicativo no Linux.

## <a name="update-and-redeploy-the-code"></a>Atualizar e reimplantar o código

No diretório local, abra o arquivo _Startup.cs_. Faça uma pequena alteração no texto na chamada do método `context.Response.WriteAsync`:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Confirme suas alterações no Git e, em seguida, envie as alterações de código por push para o Azure.

```bash
git commit -am "updated output"
git push azure master
```

Depois que a implantação for concluída, mude de volta para a janela do navegador aberta na etapa **Navegar até o aplicativo** e clique em Atualizar.

![Aplicativo de exemplo atualizado em execução no Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-web-app"></a>Gerenciar seu novo aplicativo Web do Azure

Vá para o <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerenciar o aplicativo Web que você criou.

No menu à esquerda, clique em **Serviços de Aplicativos** e então clique no nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/quickstart-dotnetcore/portal-app-service-list.png)

A página Visão Geral do seu aplicativo Web é exibida. Aqui você pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. 

![Página Serviço de Aplicativo no portal do Azure](media/quickstart-dotnetcore/portal-app-overview.png)

O menu à esquerda fornece páginas diferentes para configurar seu aplicativo. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web .NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure no Linux](tutorial-dotnetcore-sqldb-app.md)
