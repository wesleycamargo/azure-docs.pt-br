<properties 
	pageTitle="Website PHP com MySQL e WebMatrix - Tutorial do Azure" 
	description="Um tutorial que demonstra como usar o WebMatrix IDE gratuito para criar e implantar um site do PHP que armazena dados no MySQL." 
	services="web-sites" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/14/2014" 
	ms.author="tomfitz"/>





#Criar e implantar um Site do Azure PHP-MySQL usando o WebMatrix

Este tutorial mostra como usar o WebMatrix para desenvolver e implantar um aplicativo PHP-MySQL para um Site do Azure. O WebMatrix é uma ferramenta gratuita de desenvolvimento da Web da Microsoft que inclui tudo que você precisa para o desenvolvimento de sites. O WebMatrix dá suporte ao PHP e inclui o IntelliSense para desenvolvimento de PHP.

Este tutorial presume que você tenha o [MySQL][install-mysql] instalado em seu computador para que possa testar um aplicativo localmente. No entanto, você pode concluir o tutorial sem ter o MySQL instalado. Em vez disto, você pode implantar seu aplicativo diretamente com os Sites do Azure.

Após a conclusão deste guia, você terá um site do PHP-MySQL em execução no Azure.
 
Você aprenderá:

* Como criar um Site do Azure e um banco de dados MySQL usando o Portal de Gerenciamento. Como, por padrão, o PHP está habilitado no sites do Azure, nada de especial é necessário para executar seu código PHP.
* Como desenvolver um aplicativo PHP usando o WebMatrix.
* Como publicar e publicar novamente o aplicativo no Azure usando o WebMatrix.
 
Seguindo este tutorial, você criará um simples aplicativo Web Lista de Tarefas em PHP. O aplicativo será hospedado em um Site do Azure. Uma captura de tela do aplicativo em execução está exibida a seguir:

![Azure PHP Web Site][running-app]

> [AZURE.NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Você pode <a href="http://azure.microsoft.com/pt-br/pricing/member-offers/msdn-benefits-details/">ativar os benefícios de assinante do MSDN</a> ou <a href="http://azure.microsoft.com/pt-br/pricing/free-trial/">se inscrever para fazer uma avaliação gratuita</a>.
> 
> Se você quiser iniciar com Websites do Azure antes de assinar uma conta, acesse <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, em que é possível criar imediatamente um site de iniciante ASP.NET de vida curta nos Websites do Azure gratuitamente. Nenhum cartão de crédito é exigido, sem compromissos.

##Pré-requisitos

1. [Baixe][tasklist-mysql-download] os arquivos do aplicativo Lista de Tarefas. O Lista de Tarefas é um simples aplicativo PHP que permite que você adicione, marque como concluídos e exclua itens de uma lista de tarefas. Os itens da Lista de Tarefas são armazenados em um banco de dados MySQL. O aplicativo consiste destes arquivos:

	* **additem.php**: adiciona um item à lista.
	* **createtable.php**: cria a tabela MySQL para o aplicativo. Este arquivo será chamado apenas uma vez.
	* **deleteitem.php**: exclui um item.
	* **getitems.php**: obtém todos os itens no banco de dados.
	* **index.php**: exibe tarefas e fornece um formulário para adicionar um item à lista.
	* **markitemcomplete.php**: altera o status de um item para concluir.
	* **taskmodel.php**: contém funções que adicionam, obtém, atualizam e excluem itens do banco de dados.

1. Crie um banco de dados MySQL local chamado  `tasklist`. Isto pode ser feito tanto por meio do espaço de trabalho do Banco de Dados no WebMatrix (depois de ser instalado abaixo no tutorial), como por meio do prompt de comando MySQL, com o seguinte comando:

		mysql> create database tasklist;

	Esta etapa será necessária somente se você quiser testar o seu aplicativo localmente.

<h2><a id="CreateWebsite"></a>Criar um site do Azure e banco de dados do MySQL</h2>

1. Faça logon no [Portal de Gerenciamento][preview-portal].
1. Clique no ícone **+ Novo** na parte inferior esquerda do portal.

	![Create New Azure Web Site][NewWebSite1]

1. Clique em **SITE**, em seguida, clique em **CRIAÇÃO PERSONALIZADA**.

	![Custom Create a new Web Site][NewWebSite2]

	> [AZURE.NOTE]
	> Não é possível criar um Banco de Dados MySQL para um site depois de criar o website. Você deve criar um site e um banco de dados MySQL conforme descrito nas etapas abaixo.

1. Insira um valor para a **URL**, selecione **Criar Novo Banco de Dados MySQL** a partir da lista suspensa **BASE DE DADOS** e selecione o datacenter para seu site na lista suspensa **REGIÃO**. Clique na seta na parte inferior da caixa de diálogo.

	![Fill in web site details][NewWebSite3]

5. Insira um valor para o **NOME** do seu banco de dados, selecione o data center para seu banco de dados na lista suspensa **REGIÃO** e marque a caixa indicando que você concorda com os termos legais. Clique na marca de seleção na parte inferior da caixa de diálogo.

	![Create new MySQL database][NewWebSite4]

	Quando o website tiver sido criado, você verá o texto **Criando Website '[SITENAME]' com sucesso**.

	Em seguida, você deverá obter as informações para conexão do MySQL.


6. Clique no nome do site exibido na lista de sites para abrir a página Início Rápido.

	![Open web site dashboard][NewWebSite5]

7. Clique na guia **CONFIGURAR**:

	![Configure tab][NewWebSite6]

8. Role para baixo até a seção **cadeias de conexão**. Os valores para  `Database`,  `Data Source`,  `User Id` e  `Password` são (respectivamente) o nome do banco de dados, nome do servidor e senha do usuário. Anote as informações para conexão de banco de dados, pois estas serão necessárias posteriormente.

	![Connection string][ConnectionString]

##Instale o WebMatrix e desenvolva seu aplicativo

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

	Nas próximas etapas, você desenvolverá o aplicativo Lista de Tarefas ao adicionar os arquivos baixados anteriormente, fazendo algumas modificações. Você pode, no entanto, adicionar seus arquivos existentes ou criar novos arquivos.

6. Adicione seus arquivos de aplicativo ao clicar em **Adicionar Existente**:

	![WebMatrix - Add existing files][edit_addexisting]

	Na caixa de diálogos resultante, navegue para os arquivos que você baixou anteriormente, selecione todos eles e clique em Abrir. Quando solicitado, escolha substituir o arquivo  `index.php`. 

7. Em seguida, você deverá adicionar as informações para conexão do seu banco de dados MySQL local ao arquivo  `taskmodel.php`. Abra o arquivo  `taskmodel.php` clicando nele duas vezes e atualize as informações para conexão do banco de dados na função  `connect`. (**Observação**: Pule para [Publicar seu aplicativo](#Publish) se você não quiser testar seu aplicativo localmente e, em vez disto, quiser publicá-lo diretamente nos Sites do Azure.)

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Salve o arquivo  `taskmodel.php`.

8. Para que o aplicativo seja executado, a tabela  `items` deverá ser criada. Clique no arquivo  `createtable.php` com o botão direito do mouse e selecione **Iniciar no navegador**. Isto iniciará o  `createtable.php` no seu navegador e executará o código que cria a tabela  `items` no banco de dados  `tasklist`.

	![WebMatrix - Launch createtable.php in browser][edit_run]

9. Agora você pode testar o aplicativo localmente. Clique no arquivo  `index.php` com o botão direito do mouse e selecione **Iniciar no navegador**. Teste o aplicativo ao adicionar, marcar como concluídos e excluir itens.  


<h2><a id="Publish"></a>Publicar seu aplicativo</h2>

Antes de publicar seu aplicativo nos Sites do Azure, as informações para conexão do banco de dados no  `taskmodel.php` precisam ser atualizadas com as informações para conexão que você obteve anteriormente (na seção [Criar Site do Azure e Banco de dados MySQL](#CreateWebsite) ).

1. Abra o arquivo  `taskmodel.php` clicando nele duas vezes e atualize as informações para conexão do banco de dados na função  `connect`.

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	Save the `taskmodel.php` file.

2. Clique em **Publicar** no WebMatrix e, em seguida, clique em **Continuar** na caixa de diálogos **Visualizar Publicação**.

	![WebMatrix - Publish][edit_publish]

3. Navegue até http://[nome do site].azurewebsites.net/createtable.php para criar a tabela  `items`.

4. Por fim, navegue para http://[nome do seu site].azurewebsites.net/index.php  para usar o aplicativo.
	
##Modificar e republicar seu aplicativo

Você pode facilmente modificar seu aplicativo editando a cópia local do site que você baixou anteriormente e republicar ou pode editar diretamente em modo Remoto. Aqui você fará uma simples alteração no cabeçalho do arquivo  `index.php` e o salvará diretamente no site ativo.

1. Clique na guia Remoto do seu site no WebMatrix e selecione **Abrir Exibição Remota**. Isto abrirá seu site remoto para edição direta.
	 ![WebMatrix - Open Remote View][OpenRemoteView]
 
2. Abra o arquivo  `index.php` clicando nele duas vezes.
	![WebMatrix - Open index file][Remote_editIndex]

3. Altere **Minha Lista ToDo** para **Minha Lista de Tarefas** nas marcas **título** e **h1** e salve o arquivo.


4. Quando o arquivo estiver salvo, clique no botão Executar para visualizar as alterações no site ativo.
	![WebMatrix - Launch site in Remote][Remote_run]


# Próximas etapas

Você viu como criar e implantar um site por meio do WebMatrix para o Azure. Para saber mais sobre o WebMatrix, veja estes recursos:

* [WebMatrix para Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Site do WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[running-app]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
[tasklist-mysql-download]: http://go.microsoft.com/fwlink/?LinkId=252506
[NewWebSite1]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
[NewWebSite3]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
[NewWebSite4]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
[NewWebSite5]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
[NewWebSite6]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
[ConnectionString]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
[InstallWebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png













[preview-portal]: https://manage.windowsazure.com















<!--HONumber=42-->
