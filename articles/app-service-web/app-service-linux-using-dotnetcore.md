---
title: Usar o .NET Core em aplicativo Web no Linux | Microsoft Docs
description: Usar o .NET Core em aplicativo Web no Linux.
keywords: "serviço de aplicativo do azure, aplicativo web, dotnet, core, linux, oss"
services: app-service
documentationCenter: 
authors: michimune, rachelappel
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: aelnably;wesmc;mikono;rachelap
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: cbcc43e04e58894b6505fdf6c48c7792343049b9
ms.contentlocale: pt-br
ms.lasthandoff: 07/26/2017

---

# <a name="use-net-core-in-an-azure-web-app-on-linux"></a>Usar o .NET Core em um aplicativo Web do Azure no Linux #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

[Aplicativo Web](https://docs.microsoft.com/azure/app-service-web/app-service-linux-intro) no Linux fornece um serviço de hospedagem na Web com aplicação de patch automática e altamente escalonável usando o sistema operacional Linux. Este tutorial contém instruções passo a passo mostrando como criar um aplicativo [.NET Core](https://docs.microsoft.com/aspnet/core/) no aplicativo Web do Azure no Linux. 

![Aplicativo Web no Linux][10]

Você pode seguir as etapas abaixo usando um computador Mac, Windows ou Linux.

## <a name="prerequisites"></a>Pré-requisitos ##

Para concluir este tutorial: 

* Instale o [SDK do .NET Core](https://www.microsoft.com/net/download/core).
* Instale o [Git](https://git-scm.com/downloads).

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-local-net-core-application"></a>Criar um aplicativo .NET Core local ##

Inicie uma nova sessão de terminal. Crie um diretório chamado `hellodotnetcore` e altere o diretório atual para ele. Em seguida, digite o seguinte: 

```
dotnet new web
``` 

  Esse comando cria três arquivos (*hellodotnetcore.csproj*, *Program.cs* e *Startup.cs*) e uma pasta vazia (*wwwroot /*) no diretório atual. O conteúdo do arquivo `.csproj` deverá ter a seguinte aparência: 

```xml
  <!-- Empty lines are omitted. -->

  <Project Sdk="Microsoft.NET.Sdk.Web">
        <PropertyGroup>
        <TargetFramework>netcoreapp1.1</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
        <Folder Include="wwwroot\" />
        </ItemGroup>
        <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore" Version="1.1.2" />
        </ItemGroup>
  </Project>
```

Como esse aplicativo é um aplicativo Web, uma referência a um pacote do ASP.NET Core foi adicionada automaticamente ao arquivo *hellodotnetcore.csproj*. O número da versão do pacote é definido de acordo com o framework escolhida. Este exemplo faz referência à versão 1.1.2 do ASP.NET Core porque o .NET Core 1.1 é usado.

## <a name="build-and-test-the-application-locally"></a>Compilar e testar o aplicativo localmente ##

Você pode compilar e executar seu aplicativo .NET Core com o comando `dotnet restore` seguido do comando `dotnet run`, conforme mostrado aqui:

```
dotnet restore
dotnet run
```


Quando o aplicativo é iniciado, ele exibe uma mensagem indicando que está escutando as solicitações de entrada em uma porta. 

```bash
Hosting environment: Production
Content root path: C:\hellodotnetcore
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Teste-o navegando até `http://localhost:5000/` com seu navegador. Se tudo estiver funcionando bem, você verá "Olá, Mundo!" como texto de resultado.

![Testar com um navegador][7]

## <a name="create-a-net-core-app-in-the-azure-portal"></a>Criar um aplicativo .NET Core no Portal do Azure ##

Primeiro você precisa criar um aplicativo Web vazio. Faça logon no [Portal do Azure](https://portal.azure.com/) e crie um novo [aplicativo Web no Linux](https://portal.azure.com/#create/Microsoft.AppSvcLinux).

![Criar um aplicativo Web][1]

Quando a página **Criar** abrir, forneça detalhes sobre seu aplicativo Web:

![Escolher uma pilha de tempo de execução do .NET Core][2]

Use a tabela a seguir como guia para preencher a página **Criar** e, em seguida, selecione **OK** e **Criar** para criar o aplicativo.

| Configuração      | Valor sugerido  | Descrição                                        |
| ------------ | ---------------- | -------------------------------------------------- |
| Nome do aplicativo | hellodotnetcore  | O nome do seu aplicativo. Esse nome deve ser exclusivo. |
| Assinatura | Escolher uma assinatura existente | A assinatura do Azure. |
| Grupo de recursos | myResourceGroup |  O nome do grupo de recursos do Azure para conter o aplicativo Web. |
| Plano do Serviço de Aplicativo | Nome do plano do Serviço de Aplicativo existente |  O plano do Serviço de Aplicativo.  |
| Configurar o contêiner | .NET Core 1.1 | O tipo de contêiner desse aplicativo Web: registro interno, Docker ou privado. |
| Origem da imagem  | Interno  |  A origem da imagem. |
| Pilha de tempo de execução  | .NET Core 1.1  | A pilha de tempo de execução e a versão.  |

## <a name="deploy-your-application-via-git"></a>Implantar seu aplicativo por meio do Git ##

Use o Git para implantar o aplicativo .NET Core ao aplicativo Web do Serviço de Aplicativo do Azure no Linux.

O novo aplicativo Web do Azure já tem uma implantação do Git configurada. Você encontrará a URL de implantação do Git navegando até a URL a seguir depois de inserir o nome do aplicativo Web:

```https://{your web app name}.scm.azurewebsites.net/api/scm/info```

A URL do Git tem o seguinte formato, com base no nome do seu aplicativo Web:

```https://{your web app name}.scm.azurewebsites.net/{your web app name}.git```

Execute os seguintes comandos para implantar o aplicativo local em seu aplicativo Web do Azure: 
 
```bash
git init
git remote add azure <Git deployment URL from above>
git add *.csproj *.cs
git commit -m "Initial deployment commit"
git push azure master
```

Você não precisa enviar nenhum arquivo por push no diretório *bin/* ou *obj/* porque seu aplicativo é criado na nuvem quando os arquivos de origem do aplicativo são enviados por push para o Azure. Depois que o processo de compilação for concluído, os arquivos binários são copiados para o diretório do aplicativo no *inicial/site/wwwroot/*.

Confirme que as operações de implantação remota relatam êxito. As operações de envio por push podem demorar, pois a resolução de pacotes e o processo de compilação são executados na nuvem. Você verá várias mensagens de status, inclusive algumas informando que os arquivos foram copiados. O resultado deve ser semelhante ao seguinte:

```bash
/* some output has been removed for brevity */
remote: Copying file: 'System.Net.Websockets.dll' 
remote: Copying file: 'System.Runtime.CompilerServices.Unsafe.dll' 
remote: Copying file: 'System.Runtime.Serialization.Primitives.dll' 
remote: Copying file: 'System.Text.Encodings.Web.dll' 
remote: Copying file: 'hellodotnetcore.deps.json' 
remote: Copying file: 'hellodotnetcore.dll' 
remote: Omitting next output lines...
remote: Finished successfully.
remote: Running post deployment commands...
remote: Deployment successful.
To https://hellodotnetcore.scm.azurewebsites.net/
 * [new branch]           master -> master

```

Depois que a implantação for concluída, reinicie o aplicativo Web para a implantação entrar em vigor. Para isso, vá ao Portal do Azure e navegue até a página **Visão Geral** do seu aplicativo Web. Selecione o botão **Reiniciar** na página. Quando uma janela pop-up aparecer, selecione **Sim** para confirmar. Em seguida, você pode procurar o aplicativo Web, conforme mostrado aqui:

![Procurar aplicativo .NET Core implantado para o Serviço de Aplicativo do Azure no Linux][10]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas
* [Perguntas frequentes sobre o Aplicativo Web do Serviço de Aplicativo do Azure no Linux](./app-service-linux-faq.md)

[1]: ./media/app-service-linux-using-dotnetcore/top-level-create.png
[2]: ./media/app-service-linux-using-dotnetcore/dotnet-new-webapp.png
[7]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-local.png
[10]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-azure.png

