<properties 
	pageTitle="Criar e implantar um aplicativo Web PHP-MySQL no Serviço de Aplicativo do Azure usando WebMatrix" 
	description="Um tutorial que demonstra como usar o IDE gratuito do WebMatrix para criar e implantar um aplicativo Web do PHP no Serviço de Aplicativo do Azure que armazena dados no MySQL."
	tags="azure-portal" 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="tomfitz"/>





# Criar e implantar um aplicativo Web PHP-MySQL no Serviço de Aplicativo do Azure usando WebMatrix

Este tutorial mostra como usar o WebMatrix para desenvolver e implantar um aplicativo PHP-MySQL em Aplicativos Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). O WebMatrix é uma ferramenta gratuita de desenvolvimento da Web da Microsoft que inclui tudo que você precisa para o desenvolvimento de sites. O WebMatrix oferece suporte ao PHP e inclui o IntelliSense para desenvolvimento de PHP.

Este tutorial presume que você tenha o [MySQL][install-mysql] instalado em seu computador para que possa testar um aplicativo localmente. No entanto, você pode concluir o tutorial sem ter o MySQL instalado. Em vez disso, você pode implantar seu aplicativo diretamente nos Aplicativos Web do Serviço de Aplicativo do Azure.

Após a conclusão deste guia, você terá um site PHP-MySQL em execução em aplicativos Web.
 
Você aprenderá a:

* Criar um site Web em Aplicativos Web do Serviço de Aplicativo e um banco de dados MySQL usando o [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Como o PHP está habilitado em Aplicativos Web por padrão, nada de especial é necessário para executar seu código PHP.
* Como desenvolver um aplicativo PHP usando o WebMatrix.
* Como publicar e publicar novamente o aplicativo nos Aplicativos Web usando o WebMatrix.
 
Seguindo este tutorial, você criará um simples aplicativo Lista de Tarefas em PHP. O aplicativo será hospedado nos Aplicativos Web do Serviço de Aplicativo. Uma captura de tela do aplicativo em execução está exibida a seguir:

![Site PHP do Azure][running-app]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

##Pré-requisitos

1. [Baixe][tasklist-mysql-download] os arquivos do aplicativo Lista de Tarefas. O Lista de Tarefas é um simples aplicativo PHP que permite que você adicione, marque como concluídos e exclua itens de uma lista de tarefas. Os itens da Lista de Tarefas são armazenados em um banco de dados MySQL. O aplicativo consiste destes arquivos:

	* **additem.php**: adiciona um item à lista.
	* **createtable.php**: cria a tabela MySQL para o aplicativo. Este arquivo será chamado apenas uma vez.
	* **deleteitem.php**: exclui um item.
	* **getitems.php**: obtém todos os itens no banco de dados.
	* **index.php**: exibe tarefas e fornece um formulário para adicionar um item à lista.
	* **markitemcomplete.php**: altera o status de um item para concluir.
	* **taskmodel.php**: contém funções que adicionam, obtém, atualizam e excluem itens do banco de dados.

1. Crie um banco de dados MySQL local chamado `tasklist`. Isto pode ser feito tanto a partir do espaço de trabalho do Banco de Dados no WebMatrix (depois de ser instalado abaixo no tutorial), como a partir do prompt de comando MySQL, com o seguinte comando:

		mysql> create database tasklist;

	Esta etapa será necessária somente se você quiser testar o seu aplicativo localmente.

## Criar um aplicativo Web e banco de dados MySQL

Siga estas etapas para criar um aplicativo Web e um Banco de Dados MySQL:

1. Faça logon no [Portal do Azure](https://portal.azure.com).

2. Clique no ícone **Novo** na parte inferior esquerda do portal.

	![Criar um novo site do Azure](./media/web-sites-php-mysql-use-webmatrix/new_website2.png)

3. Clique em **Web + Móvel** e, em seguida, em **Aplicativo Web + MySQL**.

	![Criação personalizada de um novo site](./media/web-sites-php-mysql-use-webmatrix/create_web_mysql.png)

4. Insira um nome válido para o grupo de recursos.

    ![Definir nome do grupo de recursos](./media/web-sites-php-mysql-use-webmatrix/set_group.png)

5. Insira valores para seu novo aplicativo Web.

    ![Criar um aplicativo Web](./media/web-sites-php-mysql-use-webmatrix/create_wa.png)

6. Insira valores para seu novo banco de dados, incluindo concordar com os termos legais.

	![Criar novo banco de dados MySQL](./media/web-sites-php-mysql-use-webmatrix/create_db.png)

	Quando o aplicativo Web tiver sido criado, você verá o novo grupo de recursos.

## Obter informações da conexão MySQL remota

Para conectar-se ao Banco de Dados MySQL que está em execução nos Aplicativos Web, você precisará das informações da conexão. Para obter informações sobre a conexão MySQL, siga estas etapas:

1. No seu grupo de recursos, verifique o banco de dados:

	![Selecionar um banco de dados](./media/web-sites-php-mysql-use-webmatrix/select_database.png)

2. No resumo do banco de dados, selecione **Propriedades**.

    ![Selecionar propriedades](./media/web-sites-php-mysql-use-webmatrix/select_properties.png)

2. Anote os valores de `Database`, `Host`, `User Id` e `Password`.

    ![Anotar propriedades](./media/web-sites-php-mysql-use-webmatrix/note-properties.png)

## Criar seu aplicativo no WebMatrix

Nas próximas etapas, você desenvolverá o aplicativo Lista de Tarefas ao adicionar os arquivos baixados anteriormente, fazendo algumas modificações. Você pode, no entanto, adicionar seus arquivos existentes ou criar novos arquivos.

1. Inicie o [Microsoft WebMatrix](http://www.microsoft.com/web/webmatrix/). Se você ainda não o tiver instalado, faça isso agora.
2. Se essa for a primeira vez em que tive usado o WebMatrix 3, você receberá uma solicitação iniciar sessão no Azure. Caso contrário, você pode clicar na **Sign In** botão e escolha **adicionar conta** Optar por **entrar** usando sua conta da Microsoft.

	![Adicionar conta](./media/web-sites-php-mysql-use-webmatrix/webmatrix-add-account.png)

3. Se você se inscrever para uma conta do Azure, você pode efetuar login usando sua conta da Microsoft:

	![Entrar no Azure](./media/web-sites-php-mysql-use-webmatrix/webmatrix-sign-in.png)

1. Na tela inicial, clique o **novo** botão e escolha **Galeria de modelos de** para criar um novo site da Galeria de modelos:

	![Novo site da Galeria de modelos](./media/web-sites-php-mysql-use-webmatrix/webmatrix-site-from-template.png)

4. A partir dos modelos disponíveis, escolha **PHP**.

	![Site a partir de modelo][site-from-template]

5. Selecione o modelo **Site Vazio**. Forneça um nome para o site e clique em **PRÓXIMO**.

	![Fornecer nome para site][site-from-template-2]

	Seu site será aberto no WebMatrix com alguns arquivos padrão estabelecidos.

	Nas próximas etapas, você desenvolverá o aplicativo Lista de Tarefas ao adicionar os arquivos baixados anteriormente, fazendo algumas modificações. Você pode, no entanto, adicionar seus arquivos existentes ou criar novos arquivos.

6. Adicione seus arquivos de aplicativo ao clicar em **Adicionar Existente**:

	![WebMatrix - adicionar arquivos existentes][edit_addexisting]

	Na caixa de diálogos resultante, navegue para os arquivos que você baixou anteriormente, selecione todos eles e clique em Abrir. Quando solicitado, escolha substituir o arquivo `index.php`.

7. Em seguida, você deverá adicionar as informações para conexão do seu banco de dados MySQL local ao arquivo `taskmodel.php`. Abra o arquivo `taskmodel.php` clicando nele duas vezes e atualize as informações de conexão do banco de dados na função `connect`. (***Observação**: vá para [Publicar seu aplicativo](#Publish) se você não desejar testar o aplicativo localmente e, em vez disso, quiser publicá-lo diretamente em Aplicativos Web do Serviço de Aplicativo do Azure.)

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Salve o arquivo `taskmodel.php`.

8. Para que o aplicativo seja executado, a tabela `items` deverá ser criada. Clique no arquivo `createtable.php` com o botão direito do mouse e selecione **Iniciar no navegador**. Isto iniciará o `createtable.php` no seu navegador e executará o código que cria a tabela `items` no banco de dados `tasklist`.

	![WebMatrix - iniciar o createtable.php no navegador][edit_run]

9. Agora você pode testar o aplicativo localmente. Clique no arquivo `index.php` com o botão direito do mouse e selecione **Iniciar no navegador**. Teste o aplicativo ao adicionar, marcar como concluídos e excluir itens.


## Publicar seu aplicativo

Antes da publicação de seu aplicativo em Aplicativos Web do Serviço de Aplicativo, as informações de conexão do banco de dados em `taskmodel.php` precisam ser atualizadas com as informações de conexão que você obteve anteriormente (na seção [Criar um aplicativo Web e Banco de Dados MySQL](#CreateWebsite)).

1. Abra o arquivo `taskmodel.php` clicando nele duas vezes e atualize as informações para conexão do banco de dados na função `connect`.

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	Salve o arquivo `taskmodel.php`.

2. Clique em **Publicar** no WebMatrix.

	![WebMatrix - publicar][edit_publish]

3. Clique em **Escolher um site existente do Microsoft Azure**.

3. Selecione o aplicativo Web do Serviço de Aplicativo criado anteriormente.

	![](./media/web-sites-php-mysql-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. Continue clicando em **Continuar** até que o WebMatrix publique o site nos Aplicativos Web do serviço de Aplicativo do Azure.

3. Navegue até http://[your nome do site].azurewebsites.net/createtable.php para criar a tabela `items`.

4. Navegue até http://[your nome do site].azurewebsites.net/index.php para usar o aplicativo.
	
##Modificar e republicar seu aplicativo

Você pode facilmente modificar seu aplicativo editando a cópia local do site que você baixou anteriormente e republicar ou pode editar diretamente em modo Remoto. Aqui, você fará uma alteração simples no cabeçalho do arquivo `index.php` e o salvará diretamente no site ativo.

1. Clique na guia Remoto do seu site no WebMatrix e selecione **Abrir Exibição Remota**. Isso abrirá o site remoto para edição direta. ![WebMatrix - abrir exibição remota][OpenRemoteView]
 
2. Abra o arquivo `index.php` clicando duas vezes nele. ![WebMatrix - abrir arquivo de índice][Remote_editIndex]

3. Altere **Minha Lista de Tarefas Pendentes** para **Minha Lista de Tarefas** nas tags **título** e **h1** e salve o arquivo.


4. Quando o salvamento for concluído, clique no botão Executar para ver as alterações no site ativo. ![WebMatrix - iniciar o site em modo remoto][Remote_run]


## Próximas etapas

* [Site do WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a alteração do portal antigo para o novo portal, consulte: [Referência para navegação no o portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)




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













 

<!---HONumber=July15_HO3-->