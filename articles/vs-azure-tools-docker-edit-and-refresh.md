<properties
   pageTitle="Depuração de aplicativos em um contêiner de Docker local | Microsoft Azure"
   description="Saiba como modificar um aplicativo que está sendo executado em um contêiner do Docker local, atualizar o contêiner usando Editar e Atualizar e configurar os pontos de interrupção de depuração"
   services="visual-studio-online"
   documentationCenter="na"
   authors="AllenClark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="05/13/2016"
   ms.author="allclark" />

# Depuração de aplicativos em um contêiner de Docker local

## Visão geral
O Visual Studio Tools para Docker oferece uma maneira consistente para desenvolver em um e validar seu aplicativo localmente em um contêiner de Docker do Linux. Você não precisa reiniciar o contêiner sempre que você alterar um código. Este artigo mostrará como usar o recurso "Editar e Atualizar" para iniciar um aplicativo Web do ASP.NET Core em um contêiner do Docker local, realizar as alterações necessárias e atualizar o navegador para visualizá-las. Ele também mostra como definir pontos de interrupção para depuração.

> [AZURE.NOTE] O suporte do Contêiner do Windows estará disponível em uma versão futura

## Pré-requisitos
As ferramentas a seguir precisam ser instaladas.

- [Visual Studio 2015 Atualização 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- [Microsoft ASP .NET Core RC 2](http://go.microsoft.com/fwlink/?LinkId=798481)
- [Ferramentas do Visual Studio 2015 para Docker](https://aka.ms/DockerToolsForVS)

Para executar os contêineres de Docker localmente, você precisará de um cliente docker local. Você pode usar a [Caixa de Ferramentas de Docker](https://www.docker.com/products/overview#/docker_toolbox) liberada que requer que o Hyper-V seja desabilitado ou, como alternativa, usar [Docker para Windows Beta](https://beta.docker.com) que usa o Hyper-V e requer o Windows 10.

Se estiver usando a Caixa de Ferramentas do Docker, você precisará [Configurar o cliente Docker](./vs-azure-tools-docker-setup.md)

## Editar um aplicativo em execução em um contêiner do Docker local
As Ferramentas do Visual Studio 2015 para Docker permitem que os desenvolvedores de aplicativos Web do ASP .NET Core RC2 testem e executem seu aplicativo em um contêiner do Docker, realizem alterações no aplicativo no Visual Studio e atualizem o navegador para ver as alterações aplicadas ao aplicativo que está em execução no contêiner. Com o .NET Core e as Ferramentas do Visual Studio para Docker versão 0.20, você também pode definir pontos de interrupção para o código em execução com o Contêiner do Docker.

1. No menu do Visual Studio, selecione **Arquivo > Novo > Projeto**.

1. Na seção **Modelos** da caixa de diálogo **Novo Projeto**, selecione **Visual C# > Web**.

1. Selecione **Aplicativo Web do ASP.NET Core (.NET Core)**.

1. Dê um nome ao novo aplicativo (ou use o padrão) e toque em **OK**.

1. Em **Modelos do ASP.NET Core**, selecione **Aplicativo Web** e toque em **OK**.

1. Desmarque **Host na nuvem** já que você estará usando o Docker como sua solução de implantação.

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto e escolha **Adicionar > Suporte do Docker**.

	![][0]

1. Os seguintes arquivos são criados no nó do projeto:

	![][1]

> [AZURE.NOTE] Se estiver usando o [Docker para Windows Beta](https://beta.docker.com), abra Properties\\Docker.props, remova o valor padrão e reinicie o Visual Studio para que o valor tenha efeito. ![][2]

##Editar a Atualizar
Para iterar rapidamente as alterações, você pode iniciar o aplicativo dentro de um contêiner e continuar a fazer alterações, exibindo-as como faria com o IIS Express.

1. Defina a Configuração da Solução `Debug` e pressione **&lt;CTRL + F5>** para criar sua imagem de docker e executá-la localmente. Consulte a janela de saída, usando build ou

1. Quando a imagem do contêiner tiver sido criada e estiver em execução em um contêiner do Docker, o Visual Studio tentará iniciar o aplicativo Web no navegador padrão. Se você estiver usando o navegador Microsoft Edge ou tiver erros, veja a seção [Solução de Problemas](vs-azure-tools-docker-troubleshooting-docker-errors.md).

1. Retorne ao Visual Studio e abra `Views\Home\About.cshtml`.

1. Acrescente o conteúdo HTML a seguir no final do arquivo e salve as alterações.

	```
	<h1>Hello from a Docker Container!</h1>
	```

1.	Exibindo a janela de saída, quando o build do .NET for concluído e você ver `Application started. Press Ctrl+C to shut down`, alterne para o seu navegador e atualize a página.

1.	Você deve conseguir ver se as alterações foram aplicadas!

##Ponto de Interrupção de Depuração
Geralmente, as alterações precisarão de mais inspeção, aproveitando os recursos de depuração do Visual Studio.

1.	Retorne ao Visual Studio e abra o `Controllers\HomeController.cs`

1.  Substitua o conteúdo do método About() pelo seguinte:

	```
	string message = "Your application description page from wthin a Container";
	ViewData["Message"] = message;
    ````

1.  Defina um ponto de interrupção à esquerda da linha `string message`....

1.  Pressione **&lt;F5>** para iniciar a depuração.

1.  Navegue até a página Sobre para atingir o ponto de interrupção.

1.  Alterne para o Visual Studio para exibir o ponto de interrupção e inspecionar o valor da mensagem.

	![][3]

##Resumo
Com [Ferramentas do Visual Studio 2015 para Docker](https://aka.ms/DockerToolsForVS), você pode obter a produtividade de trabalhar localmente, com o realismo de produção de desenvolvimento em um contêiner do Docker.

## Solucionar problemas
[Troubleshooting Visual Studio Docker Development](vs-azure-tools-docker-troubleshooting-docker-errors.md) (Solucionar Problemas de Desenvolvimento do Docker do Visual Studio)

## Mais informações sobre o Docker com o Visual Studio, Windows e Azure

- [Ferramentas do Docker do Visual Studio](http://aka.ms/dockertoolsforvs) - Desenvolvendo seu código do .NET Core em um contêiner
- [Ferramentas do Docker para o Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) - Compilar e implantar contêineres do docker
- [Ferramentas do Docker para o Visual Studio Code](http://aka.ms/dockertoolsforvscode) - Serviços de linguagem para editar arquivos de docker, com mais cenários e2e vindo
- [Informações do Contêiner do Windows](http://aka.ms/containers) - Informações sobre o Windows Server e Nano Server
- [Serviço de Contêiner do Azure](https://azure.microsoft.com/services/container-service/) - [Conteúdo do Serviço de Contêiner do Azure](http://aka.ms/AzureContainerService)

## Várias ferramentas do Docker

[Algumas ótimas ferramentas do Docker (blog de Steve Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## Bons artigos

[Introduction to Microservices from NGINX](https://www.nginx.com/blog/introduction-to-microservices/) (Introdução aos Microsserviços do NGINX)

## Apresentações

- [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/) (Steve Lasker: VS ao vivo de Las Vegas 2016 - Docker e2e)
- [Introduction to ASP.NET Core @ build 2016 - Where You At Demo](https://channel9.msdn.com/Events/Build/2016/B810) (Introdução ao ASP.NET Core @ build 2016 - Onde você está na demonstração)
- [Developing .NET apps in containers, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/) (Desenvolvendo aplicativos .NET em contêineres, Channel 9)

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png
[2]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-props.png
[3]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

<!---HONumber=AcomDC_0518_2016-->