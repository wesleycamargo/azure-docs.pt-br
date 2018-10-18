---
title: Depurando aplicativos em um contêiner de Docker local | Microsoft Docs
description: Saiba como modificar um aplicativo que está sendo executado em um contêiner do Docker local, atualizar o contêiner usando Editar e Atualizar e configurar os pontos de interrupção de depuração
services: container-service
author: ghogen
manager: douge
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.topic: article
ms.workload: multiple
ms.date: 09/11/2018
ms.author: ghogen
ms.openlocfilehash: 0f3f323cb4486c06f6f18de4c695efaf8dce4d99
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44715713"
---
# <a name="debugging-apps-in-a-local-docker-container"></a>Depuração de aplicativos em um contêiner de Docker local
## <a name="overview"></a>Visão geral
O Visual Studio 2017 fornece uma maneira consistente de desenvolver em um contêiner do Docker e validar o aplicativo localmente.
Você não precisa reiniciar o contêiner sempre que você alterar um código.
Este artigo ilustra como usar o recurso "Editar e Atualizar" para iniciar um aplicativo Web do ASP.NET Core em um contêiner do Docker local, realizar as alterações necessárias e atualizar o navegador para visualizá-las.
Ele também mostra como definir pontos de interrupção para depuração.

## <a name="prerequisites"></a>Pré-requisitos
As ferramentas a seguir devem ser instaladas.

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) com a carga de trabalho de desenvolvimento da Web instalada.

Para executar os contêineres de Docker localmente, você precisará de um cliente docker local.
Você pode usar a [Caixa de Ferramentas do Docker](https://www.docker.com/products/docker-toolbox), que requer a desabilitação do Hyper-V ou usar o [Docker para Windows](https://www.docker.com/get-docker), que usa o Hyper-V e requer o Windows 10.

Se você estiver usando a Caixa de Ferramentas do Docker, será preciso [configurar o cliente Docker](vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. Criar um aplicativo Web
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-edit-your-code-and-refresh"></a>2. Editar seu código e atualizar
Para iterar rapidamente as alterações, você pode iniciar o aplicativo dentro de um contêiner e continuar a fazer alterações, exibindo-as como faria com o IIS Express.

1. Defina a Configuração da Solução para `Debug` e pressione **&lt;CTRL + F5>** para criar sua imagem do docker e executá-la localmente.

    Depois que a imagem de contêiner for criada e estiver em execução em um contêiner do Docker, o Visual Studio iniciará o aplicativo Web no navegador padrão.
    Se você estiver usando o navegador Microsoft Edge ou tiver erros, consulte a seção [Solução de Problemas](vs-azure-tools-docker-troubleshooting-docker-errors.md) .
2. Vá para a página Sobre, em que faremos nossas alterações.
3. Retorne ao Visual Studio e abra `Views\Home\About.cshtml`.
4. Adicione o conteúdo HTML a seguir ao final do arquivo e salve as alterações.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. Exibindo a janela de saída, quando o build do .NET for concluído e você ver essas linhas, volte para seu navegador e atualize a página Sobre.

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```

6. As alterações foram aplicadas.

## <a name="3-debug-with-breakpoints"></a>3. Depurar com pontos de interrupção
Geralmente, as alterações precisarão de mais inspeção, aproveitando os recursos de depuração do Visual Studio.

1. Retorne ao Visual Studio e abra `About.cshtml.cs`
2. Substitua o conteúdo do método OnGet() com o seguinte:

   ```cs
       Message = "Your application description page from within a Container";
   ```

3. Defina um ponto de interrupção à esquerda da linha de código.
4. Pressione **&lt;F5>** para iniciar a depuração.
5. Navegue até a página Sobre para atingir o ponto de interrupção.
6. Alterne para o Visual Studio para exibir o ponto de interrupção e inspecionar o valor da mensagem.

   ![][2]

## <a name="summary"></a>Resumo
Com o suporte do Docker no Visual Studio 2017, é possível obter a produtividade de trabalhar localmente, com o realismo de produção de desenvolver dentro de um contêiner do Docker.

## <a name="troubleshooting"></a>solução de problemas
[Troubleshooting Visual Studio Docker Development (Solucionar Problemas de Desenvolvimento do Docker do Visual Studio)](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Mais informações sobre o Docker com o Visual Studio, Windows e Azure
* [Desenvolvimento de contêiner com o Visual Studio](/visualstudio/containers) -uma página de hub de desenvolvimento de contêiner
* [Integração com o docker para os Pipelines do Azure](http://aka.ms/dockertoolsforvsts) – criar e implantar contêineres do docker
* [Ferramentas do Docker para Visual Studio Code](http://aka.ms/dockertoolsforvscode) – Serviços de linguagem para editar arquivos do docker, com futuros cenários e2e
* [Informações do Contêiner do Windows](http://aka.ms/containers)– Informações sobre Windows Server e Nano Server
* [O serviço Kubernetes do Azure](https://azure.microsoft.com/services/kubernetes-service/) - [documentação do Serviço de Kubernetes do Azure](/azure/aks)

## <a name="various-docker-tools"></a>Várias ferramentas do Docker
[Algumas ótimas ferramentas do Docker (blog de Steve Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Bons artigos
[Introduction to Microservices from NGINX (Introdução aos Microsserviços do NGINX)](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Apresentações
* [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e (Steve Lasker: VS ao vivo de Las Vegas 2016 - Docker e2e)](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Introduction to ASP.NET Core @ build 2016 - Where You At Demo](https://channel9.msdn.com/Events/Build/2016/B810) (Introdução ao ASP.NET Core no build 2016 – Demonstração Onde Você Está)
* [Developing .NET apps in containers, Channel 9 (Desenvolvendo aplicativos .NET em contêineres, Channel 9)](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
