<properties 
	pageTitle="Website PHP com banco de dados SQL e WebMatrix - Tutorial do Azure" 
	description="Um tutorial que demonstra como usar o WebMatrix IDE gratuito para criar e implantar um site do PHP que armazena dados no banco de dados SQL." 
	services="web-sites" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="tomfitz"/>





#Criar e implantar um Site do PHP e um Banco de Dados SQL usando o WebMatrix

Este tutorial mostra como usar o WebMatrix para desenvolver e implantar um aplicativo PHP que utilize um Banco de Dados SQL Azure para um Site do Azure. O WebMatrix é uma ferramenta gratuita de desenvolvimento da Web da Microsoft que inclui tudo que você precisa para o desenvolvimento de sites. O WebMatrix dá suporte ao PHP e inclui o IntelliSense para desenvolvimento de PHP. 

Este tutorial presume que você tenha o [SQL Server Express][install-SQLExpress] instalado em seu computador para que possa testar um aplicativo localmente. No entanto, você pode concluir o tutorial sem ter o SQL Server Express instalado. Em vez disto, você pode implantar seu aplicativo diretamente com os Sites do Azure.

Após a conclusão deste guia, você terá um Banco de Dados SQL do PHP em execução no Azure.
 
Você aprenderá:

* Como criar um Site do Azure e um banco de dados SQL usando o Portal de Gerenciamento. Como, por padrão, o PHP está habilitado no sites do Azure, nada de especial é necessário para executar seu código PHP.
* Como desenvolver um aplicativo PHP usando o WebMatrix.
* Como publicar e publicar novamente o aplicativo no Azure usando o WebMatrix.
 
Seguindo este tutorial, você criará um simples aplicativo Web Lista de Tarefas em PHP. O aplicativo será hospedado em um Site do Azure. Uma captura de tela do aplicativo em execução está exibida a seguir:

![Azure PHP Web Site][running-app]

> [AZURE.NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Você pode <a href="http://azure.microsoft.com/pt-br/pricing/member-offers/msdn-benefits-details/">ativar os benefícios de assinante do MSDN</a> ou <a href="http://azure.microsoft.com/pt-br/pricing/free-trial/">se inscrever para fazer uma avaliação gratuita</a>.
> 
> Se você quiser iniciar com Websites do Azure antes de assinar uma conta, acesse <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, em que é possível criar imediatamente um site de iniciante ASP.NET de vida curta nos Websites do Azure gratuitamente. Nenhum cartão de crédito é exigido, sem compromissos.

##Pré-requisitos

1. [Baixe][tasklist-sqlazure-download] os arquivos do aplicativo Lista de Tarefas. O Lista de Tarefas é um simples aplicativo PHP que permite que você adicione, marque como concluídos e exclua itens de uma lista de tarefas. Itens de lista de tarefas são armazenadas em um Banco de Dados SQL (SQL Server Express para locais de teste). O aplicativo consiste destes arquivos:

	* **index.php**: exibe tarefas e fornece um formulário para adicionar um item à lista.
	* **additem.php**: adiciona um item à lista.
	* **getitems.php**: obtém todos os itens no banco de dados.
	* **markitemcomplete.php**: altera o status de um item para concluir.
	* **deleteitem.php**: exclui um item.
	* **taskmodel.php**: contém funções que adicionam, obtém, atualizam e excluem itens do banco de dados.
	* **createtable.php**: Cria a tabela de banco de dados SQL para o aplicativo. Este arquivo será chamado apenas uma vez.

2. Crie um Banco de Dados SQL chamado  `tasklist`. Você pode fazer isso a partir do comando  `sqlcmd` com estes comandos:

		>sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
		1> create database tasklist
		2> GO

	Esta etapa será necessária somente se você quiser testar o seu aplicativo localmente.

## Criar um site e banco de dados SQL

1. Faça logon no [Portal de Gerenciamento][preview-portal].
2. Clique no ícone **+ Novo** na parte inferior esquerda do portal.

	![Create New Azure Web Site][NewWebSite1]

3. Clique em **SITE**, em seguida, clique em **CRIAÇÃO PERSONALIZADA**.

	![Custom Create a new Web Site][NewWebSite2]

	Insira um valor para a **URL**, selecione **Criar Novo Banco de Dados SQL** a partir da lista suspensa **BASE DE DADOS** e selecione o datacenter para seu site na lista suspensa **REGIÃO**. Clique na seta na parte inferior da caixa de diálogo.

	![Fill in web site details][NewWebSite3_SQL]

4. Insira um valor para o **nome** de banco de dados e selecione **servidor do novo banco de dados do SQL**. Digite um nome de logon do servidor e a senha (e confirme a senha). Escolha a região em que o novo servidor do Banco de Dados SQL será criado.

	![Fill in SQL Database settings][NewWebSite4_SQL]

	Quando o website tiver sido criado, você verá o texto **Criando Website '[SITENAME]' com sucesso**. Em seguida, você deverá obter as informações para conexão de banco de dados.

5. Clique em **recursos vinculados**, em seguida, o nome do banco de dados.

	![Linked Resources][NewWebSite6_SQL]

6. Clique em **Visualizar sequências de conexão**.

	![Connection string][NewWebSite7]
	
Na seção **PHP** da caixa de diálogo resultante, anote os valores de  `UID`,  `PWD`,  `Database` e`$serverName`. Você usará essas informações posteriormente.

##Instalar o WebMatrix

Você pode instalar o WebMatrix por meio do [Portal de Gerenciamento][preview-portal]. 

1. Depois de fazer logon, navegue na página Início Rápido do seu site e clique no ícone do WebMatrix na parte inferior da página:

	![Install WebMatrix][InstallWebMatrix]

	Siga os prompts para instalar o WebMatrix.

2. Depois que o WebMatrix estiver instalado, este tentará abrir o seu site como um projeto WebMatrix. Você pode escolher editar diretamente o seu site ativo ou baixar uma cópia local. Para este tutorial, selecione 'Edit local copy'. 

3. Ao ser solicitado a baixar seu site, escolha **Sim, instalar a partir da Galeria de Modelos**.

	![Download web site][download-site]

4. A partir dos modelos disponíveis, escolha **PHP**.

	![Site from template][site-from-template]

5. Selecione o modelo **Site Vazio**. Forneça um nome para o site e clique em **PRÓXIMO**.

	![Provide name for site][site-from-template-2]

Seu site será aberto no WebMatrix com alguns arquivos padrão estabelecidos.

##Implantar o seu aplicativo

Nas próximas etapas, você desenvolverá o aplicativo Lista de Tarefas ao adicionar os arquivos baixados anteriormente, fazendo algumas modificações. Você pode, no entanto, adicionar seus arquivos existentes ou criar novos arquivos.

1. Com seu site aberto no WebMatrix, adicione os arquivos do aplicativo, clicando em **Add Existing**:

	![WebMatrix - Add existing files][edit_addexisting]

	Na caixa de diálogos resultante, navegue para os arquivos que você baixou anteriormente, selecione todos eles e clique em Abrir. Quando solicitado, escolha substituir o arquivo  `index.php`. 

2. Em seguida, você deverá adicionar as informações para conexão do seu banco de dados SQL Server local ao arquivo  `taskmodel.php`. Abra o arquivo  `taskmodel.php` clicando nele duas vezes e atualize as informações para conexão do banco de dados na função  `connect`. (**Observação**: Pule para [Publicar seu aplicativo](#Publish) se você não quiser testar seu aplicativo localmente e, em vez disto, quiser publicá-lo diretamente nos Sites do Azure.)

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Salve o arquivo  `taskmodel.php`.

3. Para que o aplicativo seja executado, a tabela  `items` deverá ser criada. Clique no arquivo  `createtable.php` com o botão direito do mouse e selecione **Iniciar no navegador**. Isto iniciará o  `createtable.php` no seu navegador e executará o código que cria a tabela  `items` no banco de dados  `tasklist`.

	![WebMatrix - Launch createtable.php in browser][edit_run]

4. Agora você pode testar o aplicativo localmente. Clique no arquivo  `index.php` com o botão direito do mouse e selecione **Iniciar no navegador**. Teste o aplicativo ao adicionar, marcar como concluídos e excluir itens.   


<h2><a id="Publish"></a>Publicar seu aplicativo</h2>

Antes de publicar seu aplicativo nos Sites do Azure, as informações para conexão do banco de dados no  `taskmodel.php` precisam ser atualizadas com as informações para conexão que você obteve anteriormente (na seção [Criar Site do Azure e Banco de dados SQL](#CreateWebsite) ).

1. Abra o arquivo  `taskmodel.php` clicando nele duas vezes e atualize as informações para conexão do banco de dados na função  `connect`.

		// DB connection info
		$host = "value of $serverName";
		$user = "value of UID";
		$pwd = "the SQL password you created when creating the website";
		$db = "value of Database";
	
	Save the `taskmodel.php` file.

2. Clique em **Publicar** no WebMatrix e, em seguida, clique em **Continuar** na caixa de diálogos **Visualizar Publicação**.

	![WebMatrix - Publish][edit_publish]

3. Navegue até http://[nome do site].azurewebsites.net/createtable.php para criar a tabela  `items`.

4. Por último, navegue para http://[nome do site].azurewebsites.net/index.phppara iniciar o aplicativo.
	
##Modificar e republicar seu aplicativo

Você pode facilmente modificar seu aplicativo editando a cópia local do site que você baixou anteriormente e republicar ou pode editar diretamente em modo Remoto. Aqui você fará uma simples alteração no cabeçalho do arquivo  `index.php` e o salvará diretamente no site ativo.

1. Clique na guia Remoto do seu site no WebMatrix e selecione **Abrir Exibição Remota**. Isto abrirá seu site remoto para edição direta.
	 ![WebMatrix - Open Remote View][OpenRemoteView]
 
2. Abra o arquivo  `index.php` clicando nele duas vezes.
	![WebMatrix - Open index file][Remote_editIndex]

3. Altere **Minha Lista ToDo** para **Minha Lista de Tarefas** nas marcas **título** e **h1** e salve o arquivo.


4. Quando o arquivo estiver salvo, clique no botão Executar para visualizar as alterações no site ativo.
	![WebMatrix - Launch site in Remote][Remote_run]



## Próximas etapas

Você viu como criar e implantar um site por meio do WebMatrix para o Azure. Para saber mais sobre o WebMatrix, veja estes recursos:

* [WebMatrix para Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Site do WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[install-SQLExpress]: http://www.microsoft.com/pt-br/download/details.aspx?id=29062
[running-app]: ./media/web-sites-php-sql-database-use-webmatrix/tasklist_app_windows.png
[tasklist-sqlazure-download]: http://go.microsoft.com/fwlink/?LinkId=252504
[NewWebSite1]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite2.png
[NewWebSite3_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite3_SQL.png
[NewWebSite4_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite4_SQL.png

[NewWebSite6_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite6_SQL.png
[NewWebSite7]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite7.png

[InstallWebMatrix]: ./media/web-sites-php-sql-database-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-sql-database-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-sql-database-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-sql-database-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-sql-database-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-sql-database-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_run.png





















[preview-portal]: https://manage.windowsazure.com










<!--HONumber=42-->
