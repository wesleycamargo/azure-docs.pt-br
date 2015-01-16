<properties title="How to create a PHP website in Azure Websites" pageTitle="Como criar um site do PHP nos sites do Azure" metaKeywords="Sites do Azure PHP" description="Saiba como criar um site do PHP nos sites do Azure" documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="wpickett" authors="tomfitz" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/21/2014" ms.author="tomfitz" />

#Como criar um site PHP nos sites do Azure

Este artigo mostra como criar um site do PHP em [Sites do Azure][waws] usando o [Portal de Gerenciamento do Azure], as [Ferramentas de linha de comando do Azure para Mac e Linux][xplat-tools] ou os [cmdlets do PowerShell do Azure][powershell-cmdlets].

Em geral, criar um site do PHP não é diferente de criar *qualquer* outro site no Sites do Azure. Por padrão, o PHP está habilitado para todos os sites. Para obter informações sobre como configurar PHP (ou fornecer seu próprio tempo de execução do PHP personalizado), consulte [como configurar PHP em sites do Azure][configure-php].

Cada opção descrita abaixo mostra como criar um site em um ambiente de hospedagem compartilhado sem custo, mas com algumas limitações no uso da CPU e uso de largura de banda. Para obter mais informações, consulte [Sites de Preço do Azure][websites-pricing]. Para obter informações adicionais sobre como um atualizar e dimensionar um site, consulte [Como dimensionar sites][scale-websites].

##Sumário
* [Criar um site usando o Portal de Gerenciamento do Azure](#portal)
* [Criar um site usando as ferramentas de linha de comando do Azure para Mac e Linux.](#XplatTools)
* [Criar um site usando o PowerShell cmdlets do Azure](#PowerShell)

<h2><a name="portal"></a>Criar um site do PHP usando o Portal de Gerenciamento do Azure</h2>

Quando você cria um site no Portal de Gerenciamento do Azure, você tem três opções: **Criação rápida**, **Criação com banco de dados** e **Da galeria**. As instruções a seguir abordará a **criação rápida** opção. Para obter informações sobre as duas opções, consulte [criar um site do Azure PHP-MySQL e implantar usando Git][website-mysql-git] e [Criar um site do WordPress da galeria no Azure][wordpress-gallery].

Para criar um Site do PHP usando o Portal de Gerenciamento do Azure, faça o seguinte:

1. Faça logon no [Portal de Gerenciamento do Azure].
1. Clique em **Novo** na parte inferior da página, selecione **Computação**, **Site** e **Criação Rápida**. Fornecer um **URL** para seu site e selecione o **região** para seu site da web. Por fim, clique em **Criar site**.

	![Select Quick Create web site](./media/web-sites-php-create-web-sites/select-quickcreate-website.png)

<h2><a name="XplatTools"></a>Criar um site do PHP usando as ferramentas de linha de comando do Azure para Mac e Linux</h2>

Criar um site do PHP usando as ferramentas de linha de comando do Azure para Mac e Linux, faça o seguinte:

1. Instale as ferramentas de linha de comando do Azure, seguindo as instruções aqui: [Como instalar as ferramentas de linha de comando do Azure para Mac e Linux](/pt-br/develop/php/how-to-guides/command-line-tools/#Download).

1. Baixe e importe os arquivos de configurações de publicação seguindo as instruções aqui: [Como baixar e importar configurações de publicação](/pt-br/develop/php/how-to-guides/command-line-tools/#Account).

1. Execute o seguinte comando do prompt de comando:

		azure site create MySiteName

A URL para o site recém-criado será `http://MySiteName.azurewebsites.net`.  
 
Observe que você pode executar o comando `azure site create` com qualquer uma das seguintes opções:

* `--location [location name]`. Esta opção permite que você especifique o local do datacenter em que o site será criado (por exemplo, "Oeste dos EUA"). Se você omitir esta opção, será solicitado que escolha um local.
* `--hostname [custom host name]`. Esta opção permite que você especifique um nome de host personalizado para o site.
* `--git`. Esta opção permite que você use o git para publicar o site, criando repositórios git tanto no diretório do aplicativo local quanto no datacenter do site. Observe que, se a sua pasta local já for um repositório git, o comando adicionará um novo controle remoto ao repositório existente, apontando para o repositório no datacenter do site.

Para obter informações sobre opções adicionais, consulte [Como criar e gerenciar um site do Azure](/pt-br/develop/php/how-to-guides/command-line-tools/#WebSites).

<h2><a name="PowerShell"></a>Criar um site do PHP usando o cmdlet do PowerShell do Azure</h2>

Para criar um Site do PHP usando o PowerShell cmdlets do Azure, faça o seguinte:

1. Instale os cmdlets do PowerShell do Azure seguindo as instruções aqui: [Introdução ao PowerShell do Azure](/pt-br/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. Baixe e importe os arquivos de configurações de publicação seguindo as instruções aqui: [Como: Importar configurações de publicação](/pt-br/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. Abra um prompt de comando do PowerShell e execute o seguinte comando:

		New-AzureWebSite MySiteName

A URL para o site recém-criado será `http://MySiteName.azurewebsites.net`.  
 
Observe que você pode executar o comando `New-AzureWebSite` com qualquer uma das seguintes opções:

* `-Location [location name]`. Esta opção permite que você especifique o local do datacenter em que o site será criado (por exemplo, "Oeste dos EUA"). Se você omitir esta opção, será solicitado que escolha um local.
* `-Hostname [custom host name]`. Esta opção permite que você especifique um nome de host personalizado para o site.
* `-Git`. Esta opção permite que você use o git para publicar o site, criando repositórios git tanto no diretório do aplicativo local quanto no datacenter do site. Observe que, se a sua pasta local já for um repositório git, o comando adicionará um novo controle remoto ao repositório existente, apontando para o repositório no datacenter do site.

Para obter informações sobre opções adicionais, consulte [Como: Criar e gerenciar um site do Azure](/pt-br/develop/php/how-to-guides/powershell-cmdlets/#WebSite).

<h2><a name="NextSteps"></a>Próximas etapas</h2>

Agora que você criou um site do PHP em Sites do Azure, você pode gerenciar, configurar, monitorar, implantar e dimensionar seu site. Para obter mais informações, consulte os links a seguir:

* [Como configurar sites](/pt-br/manage/services/web-sites/how-to-configure-websites/)
* [Como configurar o PHP nos sites do Azure][configure-php]
* [Como gerenciar Sites](/pt-br/manage/services/web-sites/how-to-manage-websites/)
* [Como monitorar Sites](/pt-br/manage/services/web-sites/how-to-monitor-websites/)
* [Como dimensionar sites](/pt-br/manage/services/web-sites/how-to-scale-websites/)
* [Publicando com o Git](/pt-br/develop/php/common-tasks/publishing-with-git/)

Para tutoriais de ponta a ponta, acesse a página [Central de desenvolvedores de PHP - Tutoriais](/pt-br/develop/php/tutorials/).

[waws]: /pt-br/manage/services/web-sites/
[Azure Management Portal]: http://manage.windowsazure.com/
[xplat-tools]: /pt-br/develop/php/how-to-guides/command-line-tools/
[powershell-cmdlets]: /pt-br/develop/php/how-to-guides/powershell-cmdlets/
[configure-php]: /pt-br/develop/php/common-tasks/configure-php-web-site/
[website-mysql-git]: /pt-br/develop/php/tutorials/website-w-mysql-and-git/
[wordpress-gallery]: /pt-br/develop/php/tutorials/website-from-gallery/
[websites-pricing]: http://www.windowsazure.com/pt-br/pricing/details/#header-1
[scale-websites]: /pt-br/manage/services/web-sites/how-to-scale-websites/
