<properties 
	pageTitle="Criar e implantar um aplicativo Web PHP-SQL no Serviço de Aplicativo do Azure usando WebMatrix" 
	description="Um tutorial que demonstra como usar o WebMatrix IDE gratuito para criar e implantar um aplicativo Web do PHP no Serviço de Aplicativo do Azure que armazena dados no Banco de Dados SQL." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>





# Criar e implantar um aplicativo Web PHP-SQL no Serviço de Aplicativo do Azure usando WebMatrix

Este tutorial mostra como usar o WebMatrix para desenvolver um aplicativo PHP que usa um banco de dados do SQL Azure e implantá-lo em aplicativos Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). O WebMatrix é uma ferramenta gratuita de desenvolvimento da Web da Microsoft que inclui tudo que você precisa para o desenvolvimento de aplicativos Web. O WebMatrix oferece suporte ao PHP e inclui o IntelliSense para desenvolvimento de PHP.

Este tutorial presume que você tenha o [SQL Server Express][install-SQLExpress] instalado em seu computador para que possa testar um aplicativo localmente. No entanto, você pode concluir o tutorial sem ter o SQL Server Express instalado. Em vez disso, você pode implantar seu aplicativo diretamente nos Aplicativos Web do Serviço de Aplicativo do Azure.

Após a conclusão deste guia, você terá um aplicativo Web do Banco de Dados PHP-SQL em execução no Azure.
 
Você aprenderá a:

* Criar um aplicativo Web em Aplicativos Web do Serviço de Aplicativo e um Banco de Dados SQL usando o [portal de visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Como o PHP está habilitado em Aplicativos Web por padrão, nada de especial é necessário para executar seu código PHP.
* Como desenvolver um aplicativo PHP usando o WebMatrix.
* Como publicar e publicar novamente o aplicativo no Azure usando o WebMatrix.
 
Seguindo este tutorial, você criará um simples aplicativo Lista de Tarefas em PHP. O aplicativo será hospedado nos Aplicativos Web do Serviço de Aplicativo. Uma captura de tela do aplicativo em execução está exibida a seguir:

![Site PHP do Azure][running-app]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

##Pré-requisitos

1. [Baixe][tasklist-sqlazure-download] os arquivos do aplicativo Lista de Tarefas. O Lista de Tarefas é um simples aplicativo PHP que permite que você adicione, marque como concluídos e exclua itens de uma lista de tarefas. Itens de lista de tarefas são armazenadas em um Banco de Dados SQL (SQL Server Express para locais de teste). O aplicativo consiste destes arquivos:

	* **index.php**: exibe tarefas e fornece um formulário para adicionar um item à lista.
	* **additem.php**: adiciona um item à lista.
	* **getitems.php**: obtém todos os itens no banco de dados.
	* **markitemcomplete.php**: altera o status de um item para concluir.
	* **deleteitem.php**: exclui um item.
	* **taskmodel.php**: contém funções que adicionam, obtém, atualizam e excluem itens do banco de dados.
	* **createtable.php**: cria a tabela de Banco de Dados SQL para o aplicativo. Este arquivo será chamado apenas uma vez.

2. Crie um Banco de Dados SQL chamado `tasklist`. Você pode fazer isso a partir do comando `sqlcmd` com estes comandos:

		>sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
		1> create database tasklist
		2> GO

	Esta etapa será necessária somente se você quiser testar o seu aplicativo localmente.

## Criar um aplicativo Web e Banco de Dados MySQL

Siga estas etapas para criar um aplicativo Web do Azure e um banco de dados SQL:

1. Faça logon no [Portal de Visualização do Azure](https://portal.azure.com).

2. Abra o Azure Marketplace clicando no ícone **Marketplace** ou então clicando no ícone **Novo** no canto inferior esquerdo do painel, selecionando **Web + Móvel** e **Azure Marketplace** na parte inferior.
	
3. No Marketplace, selecione **Aplicativos Web**.

4. Clique no ícone **Aplicativo Web + SQL**.

5. Depois de ler a descrição do aplicativo Web + aplicativo SQL, selecione **Criar**.

6. Clique em cada parte (**Grupo de Recursos**, **Aplicativo Web**, **Banco de Dados** e **Assinatura**) e insira ou selecione valores para os campos obrigatórios:
	
	- Insira um nome de URL de sua escolha	
	- Configurar credenciais de servidor de banco de dados
	- Selecione a região mais próxima de você

	![configurar o aplicativo](./media/web-sites-php-sql-database-use-webmatrix/configure-db-settings.png)

7. Quando terminar de definir o aplicativo Web, clique em **Criar**.

	Quando o aplicativo Web tiver sido criado, o botão **Notificações** piscará **SUCESSO** em verde e abrirá a folha do grupo de recursos para exibir o aplicativo Web e o banco de dados SQL no grupo.

4. Clique no ícone do aplicativo Web na folha do grupo de recursos para abrir a folha do aplicativo Web.

	![grupo de recursos do aplicativo Web](./media/web-sites-php-sql-database-use-webmatrix/resource-group-blade.png)

##Obter informações da conexão do Banco de Dados SQL

Para se conectar à instância do banco de dados SQL que está vinculada a seu aplicativo Web, você precisa das informações de conexão, que especificou ao criar o banco de dados. Para obter informações sobre a conexão do Banco de Dados SQL, siga estas etapas:

1. Na folha do grupo de recursos, clique no ícone do banco de dados SQL.

2. Na folha do banco de dados SQL, clique em **Propriedades** e em **Mostrar cadeias de conexão do banco de dados**.

	![Exibir propriedades do banco de dados](./media/web-sites-php-sql-database-use-webmatrix/view-database-properties.png)
	
3. Na seção **PHP** da caixa de diálogo resultante, anote os valores de `Server`, `SQL Database` e `User Name`. Você usará posteriormente esses valores ao publicar seu aplicativo Web do PHP para o Serviço de Aplicativo do Azure.

## Criar seu aplicativo no WebMatrix

Nas próximas etapas, você desenvolverá o aplicativo Lista de Tarefas ao adicionar os arquivos baixados anteriormente, fazendo algumas modificações. Você pode, no entanto, adicionar seus arquivos existentes ou criar novos arquivos.

1. Inicie o [Microsoft WebMatrix](http://www.microsoft.com/web/webmatrix/). Se você ainda não o tiver instalado, faça isso agora.
2. Se essa for a primeira vez em que tive usado o WebMatrix 3, você receberá uma solicitação iniciar sessão no Azure. Caso contrário, você pode clicar na **Sign In** botão e escolha **adicionar conta** Optar por **entrar** usando sua conta da Microsoft.

	![Adicionar conta](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-add-account.png)

3. Se você se inscrever para uma conta do Azure, você pode efetuar login usando sua conta da Microsoft:

	![Entrar no Azure](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-sign-in.png)

1. Na tela inicial, clique o **novo** botão e escolha **Galeria de modelos de** para criar um novo site da Galeria de modelos:

	![Novo site da Galeria de modelos](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-site-from-template.png)

4. A partir dos modelos disponíveis, escolha **PHP**.

	![Site a partir de modelo][site-from-template]

5. Selecione o modelo **Site Vazio**. Forneça um nome para o site e clique em **PRÓXIMO**.

	![Fornecer nome para site][site-from-template-2]

3. Se você tiver entrado no Azure, agora terá a opção de criar uma instância dos Aplicativos Web do Serviço de Aplicativo do Azure para seu site local. Escolha **Ignorar** por enquanto.

	![Criar um site no Azure](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-site-from-template-azure.png)

1. Após o WebMatrix terminar de criar o site local, o WebMatrix IDE é exibido. Adicione seus arquivos de aplicativo ao clicar em **Adicionar Existente**:

	![WebMatrix - adicionar arquivos existentes][edit_addexisting]

	Na caixa de diálogos resultante, navegue para os arquivos que você baixou anteriormente, selecione todos eles e clique em Abrir. Quando solicitado, escolha substituir o arquivo `index.php`.

2. Em seguida, você deverá adicionar as informações para conexão do seu banco de dados SQL Server local ao arquivo `taskmodel.php`. Abra o arquivo `taskmodel.php` clicando nele duas vezes e atualize as informações de conexão do banco de dados na função `connect`. (***Observação**: vá para [Publicar seu aplicativo](#Publish) se você não desejar testar o aplicativo localmente e, em vez disso, quiser publicá-lo diretamente em Aplicativos Web do Serviço de Aplicativo do Azure.)

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Salve o arquivo `taskmodel.php`.

3. Para que o aplicativo seja executado, a tabela `items` deverá ser criada. Clique no arquivo `createtable.php` com o botão direito do mouse e selecione **Iniciar no navegador**. Isto iniciará o `createtable.php` no seu navegador e executará o código que cria a tabela `items` no banco de dados `tasklist`.

	![WebMatrix - iniciar o createtable.php no navegador][edit_run]

4. Agora você pode testar o aplicativo localmente. Clique no arquivo `index.php` com o botão direito do mouse e selecione **Iniciar no navegador**. Teste o aplicativo ao adicionar, marcar como concluídos e excluir itens.

<a id="Publish"></a>
## Publicar seu aplicativo

Antes da publicação de seu aplicativo em Aplicativos Web do Serviço de Aplicativo, as informações de conexão do banco de dados em `taskmodel.php` precisam ser atualizadas com as informações de conexão que você obteve anteriormente (na seção [Criar um aplicativo Web e Banco de Dados SQL](#CreateWebsite)).

1. Abra o arquivo `taskmodel.php` clicando nele duas vezes e atualize as informações para conexão do banco de dados na função `connect`.

		// DB connection info
		$host = "value of $serverName";
		$user = "value of UID";
		$pwd = "the SQL password you created when creating the web app";
		$db = "value of Database";
	
	Salve o arquivo `taskmodel.php`.

2. Clique em **Publicar** no WebMatrix.

	![WebMatrix - publicar][edit_publish]

3. Clique em **Escolher um site existente do Microsoft Azure**.

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site.png)

3. Selecione o aplicativo Web do Serviço de Aplicativo criado anteriormente.

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. Continue clicando em **Continuar** até que o WebMatrix publique o site nos Aplicativos Web do serviço de Aplicativo do Azure.

3. Navegue para http://[your nome do site].azurewebsites.net/createtable.php para criar a tabela `items`.

4. Por fim, navegue para http://[your nome do site].azurewebsites.net/index.php para iniciar o aplicativo.
	
##Modificar e republicar seu aplicativo

Você pode facilmente modificar o aplicativo editando a cópia local do site e republicar ou pode editar diretamente no modo **Remoto**. Aqui você fará uma simples alteração no cabeçalho do arquivo `index.php` e o salvará diretamente no aplicativo Web do Serviço de Aplicativo.

1. Clique na guia **Remoto** do seu site no WebMatrix e selecione **Abrir Exibição Remota**. Isso abrirá os arquivos remotos (hospedados em aplicativos Web) para edição direta. ![WebMatrix - abrir exibição remota][OpenRemoteView]
 
2. Abra o arquivo `index.php` clicando nele duas vezes. ![WebMatrix - abrir arquivo de índice][Remote_editIndex]

3. Altere **Minha Lista de Tarefas Pendentes** para **Minha Lista de Tarefas** nas tags **título** e **h1** e salve o arquivo.


4. Quando o arquivo estiver salvo, clique no botão Executar para visualizar as alterações no aplicativo Web do Serviço de Aplicativo. ![WebMatrix - iniciar o site em modo remoto][Remote_run]



## Próximas etapas

Você viu como criar e implantar um aplicativo Web do WebMatrix para Aplicativos Web do Serviço de Aplicativo do Azure. Para saber mais sobre o WebMatrix, consulte [Site do WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398).

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, confira: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal para o portal de visualização, consulte: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)




[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
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








 

<!---HONumber=62-->