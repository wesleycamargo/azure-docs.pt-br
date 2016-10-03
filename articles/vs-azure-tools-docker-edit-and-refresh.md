<properties
   pageTitle="Depuração de aplicativos em um contêiner de Docker local | Microsoft Azure"
   description="Saiba como modificar um aplicativo que está sendo executado em um contêiner do Docker local, atualizar o contêiner usando Editar e Atualizar e configurar os pontos de interrupção de depuração"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/22/2016"
   ms.author="mlearned" />

# Depuração de aplicativos em um contêiner de Docker local

## Visão geral
O Visual Studio Tools para Docker oferece uma maneira consistente para desenvolver em um e validar seu aplicativo localmente em um contêiner de Docker do Linux. Você não precisa reiniciar o contêiner sempre que você alterar um código. Este artigo mostrará como usar o recurso "Editar e Atualizar" para iniciar um aplicativo Web do ASP.NET Core em um contêiner do Docker local, realizar as alterações necessárias e atualizar o navegador para visualizá-las. Ele também mostra como definir pontos de interrupção para depuração.

> [AZURE.NOTE] O suporte do Contêiner do Windows estará disponível em uma versão futura

## Pré-requisitos
As ferramentas a seguir precisam ser instaladas.

- [Visual Studio 2015 Atualização 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- Instalar o [Visual Studio 2015 Atualização 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [SDK do Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)

Para executar os contêineres de Docker localmente, você precisará de um cliente docker local. Você pode usar a [Caixa de Ferramentas de Docker](https://www.docker.com/products/overview#/docker_toolbox) liberada que requer a desabilitação do Hyper-V ou usar o [Docker para Windows Beta](https://beta.docker.com), que usa o Hyper-V e requer o Windows 10.

Se você estiver usando a Caixa de Ferramentas do Docker, precisará [configurar o cliente Docker](./vs-azure-tools-docker-setup.md)

## 1\. Criar um aplicativo Web

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 2\. Adicionar suporte ao Docker

[AZURE.INCLUDE [Adicionar suporte do docker](../includes/vs-azure-tools-docker-add-docker-support.md)]


## 3\. Editar seu código e atualizar

Para iterar rapidamente as alterações, você pode iniciar o aplicativo dentro de um contêiner e continuar a fazer alterações, exibindo-as como faria com o IIS Express.

1. Defina a Configuração da Solução para `Debug` e pressione **&lt;CTRL + F5>** para criar sua imagem do docker e executá-la localmente.

    Quando a imagem do contêiner for criada e estiver em execução em um contêiner do Docker, o Visual Studio iniciará o aplicativo Web no navegador padrão. Se você estiver usando o navegador Microsoft Edge ou tiver erros, consulte a seção [Solução de Problemas](vs-azure-tools-docker-troubleshooting-docker-errors.md).

1. Vá para a página Sobre, em que faremos nossas alterações.

1. Retorne ao Visual Studio e abra `Views\Home\About.cshtml`.

1. Adicione o conteúdo HTML a seguir ao final do arquivo e salve as alterações.

	```
	<h1>Hello from a Docker Container!</h1>
	```

1.	Exibindo a janela de saída, quando o build do .NET for concluído e você ver essas linhas, volte para seu navegador e atualize a página Sobre.

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.	As alterações foram aplicadas.

## 4\. Depurar com pontos de interrupção

Geralmente, as alterações precisarão de mais inspeção, aproveitando os recursos de depuração do Visual Studio.

1.	Retorne ao Visual Studio e abra `Controllers\HomeController.cs`

1.  Substitua o conteúdo do método About() pelo seguinte:

	```
	string message = "Your application description page from wthin a Container";
	ViewData["Message"] = message;
    ````

1.  Defina um ponto de interrupção à esquerda da linha `string message`... .

1.  Pressione **&lt;F5>** para iniciar a depuração.

1.  Navegue até a página Sobre para atingir o ponto de interrupção.

1.  Alterne para o Visual Studio para exibir o ponto de interrupção e inspecionar o valor da mensagem.

	![][2]

##Resumo

Com as [Ferramentas do Visual Studio 2015 para Docker](https://aka.ms/DockerToolsForVS), você poderá obter a produtividade de trabalhar localmente, com o realismo de produção do desenvolvimento em um contêiner do Docker.

## Solucionar problemas

[Troubleshooting Visual Studio Docker Development (Solucionar Problemas de Desenvolvimento do Docker do Visual Studio)](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## Mais informações sobre o Docker com o Visual Studio, Windows e Azure

- [Ferramentas do Docker para Visual Studio](http://aka.ms/dockertoolsforvs) – Desenvolvendo seu código do .NET Core em um contêiner
- [Ferramentas do Docker para o Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) – Criar e Implantar contêineres do docker
- [Ferramentas do Docker para Visual Studio Code](http://aka.ms/dockertoolsforvscode) – Serviços de linguagem para editar arquivos do docker, com futuros cenários e2e
- [Informações do Contêiner do Windows](http://aka.ms/containers) – Informações sobre Windows Server e Nano Server
- [Serviço de Contêiner do Azure](https://azure.microsoft.com/services/container-service/) - [Conteúdo do Serviço de Contêiner do Azure](http://aka.ms/AzureContainerService)
-    Para obter mais exemplos de como trabalhar com o Docker, consulte [Trabalhando com o Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) na demonstração do [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Para obter mais guias de início rápido na demonstração do HealthClinic.biz, consulte [Guia de Início Rápido das Ferramentas de Desenvolvedor do Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

## Várias ferramentas do Docker

[Algumas ótimas ferramentas do Docker (blog de Steve Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## Bons artigos

[Introduction to Microservices from NGINX (Introdução aos Microsserviços do NGINX)](https://www.nginx.com/blog/introduction-to-microservices/)

## Apresentações

- [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e (Steve Lasker: VS ao vivo de Las Vegas 2016 - Docker e2e)](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [Introduction to ASP.NET Core @ build 2016 - Where You At Demo (Introdução ao ASP.NET Core @ build 2016 - Onde você está na demonstração)](https://channel9.msdn.com/Events/Build/2016/B810)
- [Developing .NET apps in containers, Channel 9 (Desenvolvendo aplicativos .NET em contêineres, Channel 9)](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

<!---HONumber=AcomDC_0921_2016-->