<properties title="How to create a PHP website in Azure Websites" pageTitle="Como criar um site PHP nos sites do Azure" metaKeywords="PHP Azure Web Sites" description="Saiba como criar um site da Web PHP nos sites da Web do Azure" documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="wpickett" authors="tomfitz" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/21/2014" ms.author="tomfitz" />

#Como criar um site PHP nos sites do Azure

Este artigo mostrará como criar um site do PHP nos [sites do Azure][waws] usando o [Portal de Gerenciamento do Azure], as [Ferramentas de Linha de Comando do Azure para Mac e Linux][xplat-tools] ou os cmdlets [PowerShell do Azure][powershell-cmdlets].

Em geral, criar um site do PHP não é diferente de criar *qualquer * site nos sites do Azure. Por padrão, o PHP está habilitado para todos os sites. Para obter informações sobre configurar PHP (ou fornecendo seu próprio tempo de execução do PHP personalizado), consulte [como configurar PHP em Sites do Azure][configure-php].

Cada opção descrita abaixo mostra como criar um site da web em um ambiente de hospedagem compartilhado sem custo adicional, mas com algumas limitações no uso da CPU e o uso de largura de banda. Para obter mais informações, consulte [Sites de Preço do Azure][websites-pricing]. Para obter informações sobre como atualizar e dimensionar seu site, consulte [Como dimensionar sites][scale-websites].

> [WACOM.NOTE]
> Se você deseja uma introdução aos Sites do Azure antes de criar uma conta, vá para <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, onde você pode imediatamente criar um site de introdução ao ASP.NET de curta duração nos Sites do Azure gratuitamente. Nenhum cartão de crédito é exigido, sem compromissos.

##Sumário
* [Criar um site usando o Portal de Gerenciamento do Azure](#portal)
* [Criar um site usando as ferramentas de linha de comando do Azure para Mac e Linux](#XplatTools)
* [Criar um site usando os cmdlets do PowerShell do Azure](#PowerShell)

<h2><a name="portal"></a>Criar um site do PHP usando o Portal de Gerenciamento do Azure</h2>

Quando você cria um site no Portal de Gerenciamento do Azure, você tem três opções: **Criação rápida**, **Criar com banco de dados** e **Da galeria**. As instruções a seguir abordará a **criação rápida** opção. Para obter informações sobre as duas opções, consulte [criar um site do Azure PHP MySQL e implantar usando gito][website-mysql-git] e [criar um site do WordPress da galeria no Azure][wordpress-gallery].

Para criar um Site do PHP usando o Portal de Gerenciamento do Azure, faça o seguinte:

1. Faça logon no [Portal de Gerenciamento do Azure].
1. Clique em **Novo** na parte inferior da página, selecione **Computação**, **Site** e **Criação Rápida**. Forneça uma **URL** para seu site e selecione a **Região** para seu site. Por fim, clique em **Criar Site**.

	![Selecione Criação Rápida de site](./media/web-sites-php-create-web-sites/select-quickcreate-website.png)

<h2><a name="XplatTools"></a>Criar um site do PHP usando as ferramentas de linha de comando do Azure para Mac e Linux</h2>

Criar um site do PHP usando as ferramentas de linha de comando do Azure para Mac e Linux, faça o seguinte:

1. Instale as ferramentas de linha de comando do Azure, seguindo as instruções aqui: [Como instalar as ferramentas de linha de comando do Azure para Mac e Linux](/ pt-br/develop/php/how-to-guides/command-line-tools/#Download).

1. Baixar e importar os arquivos de configurações de publicação seguindo as instruções aqui: [Como baixar e importar as configurações de publicação](/pt-br/develop/php/how-to-guides/command-line-tools/#Account).

1. Execute o seguinte comando do prompt de comando:

		azure site create MySiteName

A URL para o site criado recentemente será `http://MySiteName.azurewebsites.net`.  
 
Observe que você pode executar o comando `azure site create` com qualquer uma das seguintes opções:

* `--location [nome do local]`. Esta opção permite que você especifique o local do centro de dados em que seu site é criado (por exemplo, "Oeste dos EUA"). Se você omitir esta opção, será solicitado que escolha um local.
* `--hostname [nome de host personalizado]`. Esta opção permite que você especifique um nome de host personalizado para seu site.
* `--git`. Esta opção permite que você use o git para publicar seu site, criando repositórios git tanto no diretório do aplicativo local quanto no centro de dados do site. Observe que, se a sua pasta local já for um repositório git, o comando adicionará um novo controle remoto ao repositório existente, apontando para o repositório no datacenter do site.

Para obter informações sobre opções adicionais, consulte [Como criar e gerenciar um Site do Azure](/pt-br/develop/php/how-to-guides/command-line-tools/#WebSites).

<h2><a name="PowerShell"></a>Criar um site do PHP usando o Portal do Azure PowerShell</h2>

Para criar um Site do PHP usando o PowerShell cmdlets do Azure, faça o seguinte:

1. Instale os cmdlets do PowerShell do Azure, seguindo as instruções aqui: [Introdução PowerShell do Azure](/pt-br/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. Baixar e importar os arquivos de configurações de publicação seguindo as instruções aqui: [Como: Importar configurações de publicação](/pt-br/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. Abra um prompt de comando do PowerShell e execute o seguinte comando:

		New-AzureWebSite MySiteName

A URL para o site criado recentemente será `http://MySiteName.azurewebsites.net`.  
 
Observe que você pode executar o comando `New-AzureWebSite` com qualquer uma das seguintes opções:

* `-Location [nome do local]`. Esta opção permite que você especifique o local do centro de dados em que seu site é criado (por exemplo, "Oeste dos EUA"). Se você omitir esta opção, será solicitado que escolha um local.
* `-Hostname [nome de host personalizado]`. Esta opção permite que você especifique um nome de host personalizado para seu site.
* `-Git`. Esta opção permite que você use o git para publicar seu site, criando repositórios git tanto no diretório do aplicativo local quanto no centro de dados do site. Observe que, se a sua pasta local já for um repositório git, o comando adicionará um novo controle remoto ao repositório existente, apontando para o repositório no datacenter do site.

Para obter informações sobre opções adicionais, consulte [Como: Criar e gerenciar um Site do Azure](/pt-br/develop/php/how-to-guides/powershell-cmdlets/#WebSite).

<h2><a name="NextSteps"></a>Próximas etapas</h2>

Agora que você criou um site do PHP em Sites do Azure, você pode gerenciar, configurar, monitorar, implantar e dimensionar seu site. Para obter mais informações, consulte os links a seguir:

* [Como configurar sites](/pt-br/manage/services/web-sites/how-to-configure-websites/)
* [Como configurar o PHP nos Sites do Azure][configure-php]
* [Como gerenciar Sites](/pt-br/manage/services/web-sites/how-to-manage-websites/)
* [Como monitorar Sites](/pt-br/manage/services/web-sites/how-to-monitor-websites/)
* [Como dimensionar sites](/pt-br/manage/services/web-sites/how-to-scale-websites/)
* [Publicando com o Git](/pt-br/develop/php/common-tasks/publishing-with-git/)

Para tutoriais de ponta a ponta, visite a página [centro de desenvolvedores de PHP - Tutoriais](/pt-br/develop/php/tutorials/) .

[waws]: /pt-br/manage/services/web-sites/
[Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
[xplat-tools]: /pt-br/develop/php/how-to-guides/command-line-tools/
[powershell-cmdlets]: /pt-br/develop/php/how-to-guides/powershell-cmdlets/
[configure-php]: /pt-br/develop/php/common-tasks/configure-php-web-site/
[website-mysql-git]: /pt-br/develop/php/tutorials/website-w-mysql-and-git/
[wordpress-gallery]: /pt-br/develop/php/tutorials/website-from-gallery/
[websites-pricing]: http://www.windowsazure.com/pt-br/pricing/details/#header-1
[scale-websites]: /pt-br/manage/services/web-sites/how-to-scale-websites/

<!--HONumber=35.2-->
