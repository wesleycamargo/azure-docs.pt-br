<properties 
	pageTitle="Criar um aplicativo Web do PHP no Serviço de Aplicativo do Azure" 
	description="Saiba como criar um aplicativo Web do PHP no Serviço de Aplicativo do Azure" 
	documentationCenter="php" 
	services="app-service\web" 
	editor="mollybos" 
	manager="wpickett" 
	authors="tfitzmac"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tomfitz"/>

# Criar um aplicativo Web do PHP no Serviço de Aplicativo do Azure

## Visão geral
Este artigo mostrará como criar um aplicativo Web do PHP no [Serviço de Aplicativo do Azure] usando o [Portal do Azure], as [Ferramentas de Linha de Comando do Azure para Mac e Linux][xplat-tools] ou os [cmdlets do PowerShell do Azure][powershell-cmdlets].

Em geral, criar um aplicativo Web do PHP não é diferente de criar *any* aplicativo Web no Serviço de Aplicativo do Azure. Por padrão, o PHP está habilitado para todos os aplicativos Web. Para obter informações sobre como configurar o PHP (ou fornecer seu próprio tempo de execução do PHP personalizado), consulte [Configurar o PHP em aplicativos Web do Serviço de Aplicativo do Azure].

Cada opção descrita abaixo mostra como criar um aplicativo Web em um ambiente de hospedagem compartilhado sem custo adicional, mas com algumas limitações no uso da CPU e o uso de largura de banda. Para obter mais informações, consulte [Preços do Serviço de Aplicativos]. Para obter informações sobre como atualizar e dimensionar seu aplicativo Web no Serviço de Aplicativo, consulte [Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure].

>[AZURE.NOTE] Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

<a name="portal"></a>
## Criar um aplicativo Web do PHP usando o Portal do Azure

Ao criar um aplicativo Web do PHP no Portal do Azure, você tem três opções: 

- **Criação rápida** - consulte [Como: Criar um aplicativo Web usando o Portal do Azure](web-sites-create-deploy.md#createawebsiteportal)
- **Criar com banco de dados** - consulte [Criar um aplicativo Web do PHP-MySQL no Serviço de Aplicativo do Azure e implantar usando o Git]
- **Do Marketplace** - consulte [Criar um aplicativo Web do WordPress no Serviço de Aplicativo do Azure]

<a name="XplatTools"></a>
## Criar um aplicativo Web do PHP usando as ferramentas de linha de comando do Azure para Mac e Linux.

Para criar um aplicativo Web do PHP usando as ferramentas de linha de comando do Azure para Mac e Linux, faça o seguinte:

1. Instale as ferramentas de linha de comando do Azure, seguindo as instruções aqui: [Como instalar as ferramentas de linha de comando do Azure para Mac e Linux](xplat-cli.md#install).

1. Baixar e importar os arquivos de configurações de publicação seguindo as instruções aqui: [Como baixar e importar configurações de publicação](xplat-cli.md#configure).

1. Execute o seguinte comando do prompt de comando:

		azure site create MyWebAppName

A URL para o site recém-criado será `http://MyWebAppName.azurewebsites.net`.  
 
Observe que você pode executar o comando `azure site create` com qualquer uma das seguintes opções:

* `--location [nome do local]`. Essa opção permite que você especifique o local do data center em que seu aplicativo Web é criado (por exemplo, "Oeste dos EUA"). Se você omitir esta opção, será solicitado que escolha um local.
* `--hostname [nome de host personalizado]`. Essa opção permite que você especifique um nome de host personalizado para seu aplicativo Web.
* `--git`. Essa opção permite que você use o git para publicar seu aplicativo Web criando repositórios git tanto no diretório do aplicativo local quanto no data center do aplicativo Web. Observe se a sua pasta local já for um repositório git, o comando adicionará um novo remoto ao repositório existente, apontando para o repositório no data center do aplicativo Web.

<a name="PowerShell"></a>
## Criar um aplicativo Web do PHP usando os cmdlets do PowerShell do Azure

Para criar um aplicativo Web do PHP usando os cmdlets do PowerShell do Azure, faça o seguinte:

1. Instale os cmdlets do PowerShell do Azure, seguindo as instruções aqui: [Introdução ao PowerShell do Azure](/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. Baixar e importar os arquivos de configurações de publicação seguindo as instruções aqui: [Como: Importar configurações de publicação](/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. Abra um prompt de comando do PowerShell e execute o seguinte comando:

		New-AzureWebsite MyWebAppName

A URL para o site recém-criado será`http://MyWebAppName.azurewebsites.net`.  
 
Observe que você pode executar o comando `New-AzureWebsite` com qualquer uma das seguintes opções:

* `-Location [nome do local]`. Essa opção permite que você especifique o local do data center em que seu aplicativo Web é criado (por exemplo, "Oeste dos EUA"). Se você omitir esta opção, será solicitado que escolha um local.
* `-Hostname [nome de host personalizado]`. Essa opção permite que você especifique um nome de host personalizado para seu aplicativo Web.
* `-Git`. Essa opção permite que você use o git para publicar seu aplicativo Web criando repositórios git tanto no diretório do aplicativo local quanto no data center do aplicativo Web. Observe se a sua pasta local já for um repositório git, o comando adicionará um novo remoto ao repositório existente, apontando para o repositório no data center do aplicativo Web.

<a name="NextSteps"></a>
## Próximas etapas

Agora que criou um aplicativo Web do PHP no Serviço de Aplicativo do Azure, você pode gerenciar, configurar, monitorar, implantar e dimensionar seu aplicativo. Para obter mais informações, consulte os links a seguir:

* [Configurar aplicativos Web no Serviço de Aplicativo do Azure](web-sites-configure.md)
* [Configurar o PHP em aplicativos Web do Serviço de Aplicativo do Azure]
* [Gerenciar aplicativos Web usando o Portal do Azure](web-sites-manage.md)
* [Monitorar aplicativos Web no Serviço de Aplicativo do Azure](web-sites-monitor.md)
* [Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure]
* [Implantação contínua usando GIT no Serviço de Aplicativo do Azure](web-sites-publish-source-control.md)

Para tutoriais de ponta a ponta, visite a página [centro de desenvolvedores de PHP - Tutoriais](/develop/php/tutorials/) .

## O que mudou
* Para obter um guia para a alteração de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a alteração do portal antigo para o novo portal, consulte: [Referência à navegação pelo portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

[Serviço de Aplicativo do Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Portal do Azure]: http://go.microsoft.com/fwlink/?LinkId=529715
[xplat-tools]: xplat-cli.md
[powershell-cmdlets]: powershell-install-configure.md
[Configurar o PHP em aplicativos Web do Serviço de Aplicativo do Azure]: web-sites-php-configure.md
[Criar um aplicativo Web do PHP-MySQL no Serviço de Aplicativo do Azure e implantar usando o Git]: web-sites-php-mysql-deploy-use-git.md
[Criar um aplicativo Web do WordPress no Serviço de Aplicativo do Azure]: web-sites-php-web-site-gallery.md
[Preços do Serviço de Aplicativos]: /pricing/details/app-service/
[Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure]: web-sites-scale.md

<!--HONumber=52-->