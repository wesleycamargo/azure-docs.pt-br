<properties
   pageTitle="Depurar aplicativos em um contêiner do Docker local com Editar e Atualizar | Microsoft Azure"
   description="Saiba como modificar um aplicativo que está sendo executado em um contêiner do Docker local e atualizar o contêiner usando Editar e Atualizar"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="03/25/2016"
   ms.author="tarcher" />

# Depurar aplicativos em um contêiner do Docker local com Editar e Atualizar

## Visão geral
As Ferramentas do Visual Studio para Docker oferecem uma maneira conveniente de desenvolver e testar seu aplicativo localmente em um contêiner do Docker sem precisar reiniciar o contêiner sempre que um código for alterado. Este artigo mostrará como usar o recurso "Editar e Atualizar" para iniciar um aplicativo Web do ASP.NET 5 em um contêiner do Docker local, realizar as alterações necessárias e atualizar o navegador para visualizá-las.

## Pré-requisitos
As ferramentas a seguir precisam ser instaladas.

- [Visual Studio 2015 Atualização 1](https://go.microsoft.com/fwlink/?LinkId=691979)
- [Microsoft ASP .NET e Web Tools 2015 RC](https://go.microsoft.com/fwlink/?LinkId=627627)
- [Caixa de Ferramentas do Docker](https://www.docker.com/products/overview#/docker_toolbox)
- [Ferramentas do Visual Studio 2015 para Docker](https://aka.ms/DockerToolsForVS)
- [Configurar o cliente Docker](./vs-azure-tools-docker-setup.md)

> [AZURE.NOTE] Se você tiver uma versão anterior das Ferramentas do Visual Studio 2015 para Docker instalada, será preciso desinstalá-la no Painel de Controle antes de instalar a versão mais recente.

## Editar um aplicativo em execução em um contêiner do Docker local
As Ferramentas do Visual Studio 2015 para Docker permitem que os desenvolvedores de aplicativos Web do ASP .NET 5 testem e executem seu aplicativo em um contêiner do Docker, realizem alterações no aplicativo no Visual Studio e atualizem o navegador para ver as alterações aplicadas ao aplicativo que está em execução no contêiner.

1. No menu do Visual Studio, selecione **Arquivo > Novo > Projeto**. 

1. Na seção **Modelos** da caixa de diálogo **Novo Projeto**, selecione **Visual C# > Web**.

1. Selecione **Aplicativo Web do ASP.NET**.

1. Dê um nome ao novo aplicativo (ou use o nome padrão).

1. Toque em **OK**.

1. Em **Modelos do ASP.NET 5**, escolha **Aplicativo Web ASP.NET**.

1. Toque em **OK**.

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto e escolha **Adicionar > Suporte do Docker**.

	![][0]
 
1. Os seguintes arquivos são criados no nó do projeto:

	![][1]

1. Defina a Configuração da Solução como `Debug` e pressione **<F5>** para começar a testar o aplicativo localmente.

1. Como a imagem do contêiner já foi criada e está em execução em um contêiner do Docker, um console do PowerShell tentará iniciar o aplicativo Web no navegador padrão. Se você estiver usando o navegador Microsoft Edge, veja a seção [Solução de problemas](#troubleshooting).

1. Retorne ao Visual Studio e abra `Views\Home\Index.cshtml`.

1. Acrescente o conteúdo HTML a seguir no final do arquivo e salve as alterações

		<div>
			<h1>Hello from Docker Container!</h1>
		</div>

1.	Volte para o navegador e atualize-o.

1.	Role até o final da home page e verá as alterações aplicadas! Observe que pode levar alguns segundos para o site recompilar, portanto, se você não estiver vendo as alterações refletidas imediatamente, basta atualizar o navegador novamente.

##Solucionar problemas 

- **A execução do aplicativo faz com que o PowerShell abra, exiba um erro e feche. A página do navegador não abre.**

	Isso pode ser um erro durante a `docker-compose-up`. Para exibir o erro, execute as seguintes etapas:

	1. Abra o arquivo `Properties\launchSettings.json`.
	
	1. Localize a entrada do Docker.
	
	1. Localize a linha que começa assim:

			"commandLineArgs": "-ExecutionPolicy RemoteSigned …”
	
	1. Adicione o parâmetro `-noexit` para que a linha agora se pareça com o seguinte. Isso manterá o PowerShell aberto para que você possa exibir o erro.

			"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”

- **Compilação: falha ao criar a imagem, erro ao verificar a conexão TLS: o host não está em execução**

	Verifique se o host do Docker padrão está em execução. Consulte o artigo [Configurar o cliente Docker](./vs-azure-tools-docker-setup.md).

- **Não é possível localizar o mapeamento de volume**

	Por padrão, a VirtualBox compartilha `C:\Users` como `c:/Users`. Se o projeto não está em `c:\Users`, adicione-o manualmente às [Pastas compartilhadas](https://www.virtualbox.org/manual/ch04.html#sharedfolders) da VirtualBox.

- **Usar o Microsoft Edge como o navegador padrão**

	Se você estiver usando o navegador Microsoft Edge, o site pode não abrir se o Edge considerar que o endereço IP não é seguro. Para corrigir isso, execute as seguintes etapas:
	1. Na caixa Executar do Windows, digite `Internet Options`.
	2. Toque em **Opções da Internet** quando elas forem exibidas. 
	2. Toque na guia **Segurança**.
	3. Selecione a zona da **Intranet Local**.
	4. Toque em **Sites**. 
	5. Adicione o IP da máquina virtual (nesse caso, o Host do Docker) à lista. 
	6. Atualize a página no Edge e verá o site em funcionamento. 
	7. Para obter mais informações sobre esse problema, visite a postagem do blog de Scott Hanselman, [Microsoft Edge can't see or open VirtualBox-hosted local web sites (O Microsoft Edge não pode ver ou abrir sites da Web locais hospedados na VirtualBox)](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx).

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png

<!-----------HONumber=AcomDC_0330_2016-->