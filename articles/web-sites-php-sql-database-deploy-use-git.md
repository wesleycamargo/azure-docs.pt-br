<properties urlDisplayName="Web w/ SQL + Git" pageTitle="Website PHP com banco de dados SQL e Git - Tutorial do Azure" metaKeywords="" description="Um tutorial que demonstra como criar um site do PHP que armazena dados no banco de dados SQL e usar a implantação do Git no Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="Create a PHP website with a SQL Database and deploy using Git" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/18/2014" ms.author="tomfitz" />

#Criar um Site do PHP com um Banco de Dados SQL e implantar usando o Git

Este tutorial mostra como criar um Site do PHP Azure com um Banco de Dados SQL Azure e como implantá-lo usando o Git. Este tutorial presume que você tem [PHP][install-php], [SQL Server Express][install-SQLExpress], os [Drivers da Microsoft para SQL Server para PHP][install-drivers], um servidor Web e [Git][install-git] instalados em seu computador. Após a conclusão deste guia, você terá um Banco de Dados SQL do PHP em execução no Azure.

> [WACOM.NOTE]
> É possível instalar e configurar PHP, SQL Server Express, os Drivers da Microsoft para SQL Server para PHP e Serviços de Informações da Internet (IIS) usando o <a href="http://www.microsoft.com/web/downloads/platform.aspx">Microsoft Web Platform Installer</a>.

Você aprenderá:

* Como criar um Site do Azure e um banco de dados SQL usando o Portal de Gerenciamento do Azure. Como, por padrão, o PHP está habilitado no sites do Azure, nada de especial é necessário para executar seu código PHP.
* Como publicar e publicar novamente o aplicativo no Azure usando o Git.
 
Seguindo este tutorial, você criará um aplicativo Web de registro simples no PHP. O aplicativo será hospedado em um Site do Azure. A seguinte é uma captura de tela do aplicativo concluído:

![Azure PHP Web Site][running-app]

> [WACOM.NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. É possível <a href="http://azure.microsoft.com/pt-br/pricing/member-offers/msdn-benefits-details/">ativar seus benefícios de assinante MSDN</a> ou <a href="http://azure.microsoft.com/pt-br/pricing/free-trial/">assinar uma avaliação gratuita</a>.
> 
> Se você quiser iniciar com Websites do Azure antes de assinar uma conta, acesse <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, em que é possível criar imediatamente um site de iniciante ASP.NET de vida curta nos Websites do Azure gratuitamente. Nenhum cartão de crédito necessário, nenhum compromisso.

##Criar um site do Azure e configurar a publicação Git

Siga estas etapas para criar um Site do Azure e um Banco de Dados SQL:

1. Faça logon no [Portal de Gerenciamento do Azure][management-portal].
2. Clique no ícone **Novo** na parte inferior esquerda do portal.
![Create New Azure Web Site][new-website]

3. Clique em **Site**, em seguida, clique em **Criação Personalizada**.

	![Custom Create a new Web Site][custom-create]

	Insira um valor para **URL**, selecione **Criar um novo banco de dados SQL** no menu suspenso **Banco de dados** e selecione **Publicar do controle do código-fonte**. Clique na seta na parte inferior da caixa de diálogo.

	![Fill in web site details][website-details-sqlazure]

4. Insira um valor para o **Nome** de seu banco de dados, selecione **NOVO servidor de banco de dados SQL**, forneça credenciais de logon e selecione uma região. Clique na seta na parte inferior da caixa de diálogo.

	![Fill in SQL Database settings][database-settings]

5. Selecione **Repositório Git local** para seu código-fonte.

	![where is your source code][where-is-code]

	Se não tiver configurado um repositório Git antes, você deverá fornecer um nome de usuário e senha.

6. Após o website ter sido criado, abra o painel do site e selecione **Visualizar implementações**.

	![Web site dashboard][go-to-dashboard]

9. Você verá instruções para enviar os arquivos do aplicativo por push ao repositório. Anote essas instruções; você precisará delas mais tarde.

	![Git instructions][git-instructions]

##Obter informações da conexão de Banco de Dados SQL

Para conectar-se à instância do Banco de Dados SQL que está em execução nos Sites do Azure, você precisará das informações da conexão. Para obter informações sobre a conexão de Banco de Dados SQL, siga estas etapas:

1. No Portal de Gerenciamento do Azure, clique em **Recursos vinculados**, em seguida, clique no nome do banco de dados.

	![Linked Resources][linked-resources]

2. Clique em **Visualizar sequências de conexão**.

	![Connection string][connection-string]
	
3. Na seção **PHP** da caixa de diálogo resultante, anote os valores de `SERVER`, `DATABASE` e `USERNAME`.

##Criar e testar o aplicativo localmente

O aplicativo Registro é um aplicativo simples do PHP que permite que você se registre em um evento fornecendo seu nome e endereço de email. As informações sobre inscritos anteriores são exibidas em uma tabela. As informações de registro são armazenadas em uma instância do Banco de Dados SQL. O aplicativo consiste em dois arquivos (copie/cole o código disponível abaixo):

* **index.php**: Exibe um formulário de registro e uma tabela que contém informações de registro.
* **createtable.php**: Cria a tabela de banco de dados SQL para o aplicativo. Este arquivo será usado apenas uma vez.

Para executar o aplicativo localmente, siga as etapas abaixo. Observe que essas etapas pressupõem que você tem PHP, SQL Server Express e um servidor Web definido em seu computador local, e que você tenha habilitado a [extensão PDO para SQL Server][pdo-sqlsrv].

1. Crie um Banco de Dados SQL Server chamado `registration`. Você pode fazer isso por meio do prompt de comando `sqlcmd` com estes comandos:

		>sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
		1> create database registration
		2> GO	


2. No seu diretório raiz do servidor Web, crie uma pasta chamada `registration` e crie dois arquivos nela - um chamado `createtable.php` e outro chamado `index.php`.

3. Abra o arquivo `createtable.php` em um editor de texto ou IDE e adicione o código abaixo. Esse código será usado para criar a tabela `registration_tbl` no banco de dados `registration`.

		<?php
		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
			id INT NOT NULL IDENTITY(1,1) 
			PRIMARY KEY(id),
			name VARCHAR(30),
			email VARCHAR(30),
			date DATE)";
			$conn->query($sql);
		}
		catch(Exception $e){
			die(print_r($e));
		}
		echo "<h3>Table created.</h3>";
		?>

	Observe que você precisará atualizar os valores para <code>$user</code> e <code>$pwd</code> com seu nome de usuário SQL Server local e senha.

4. Abra um navegador da Web e navegue para **http://localhost/registration/createtable.php**. Isso criará a tabela `registration_tbl` no banco de dados.

5. Abra o arquivo **index.php** em um editor de texto ou IDE e adicione o código básico de HTML e CSS para a página (o código PHP será adicionado em etapas posteriores).

		<html>
		<head>
		<Title>Registration Form</Title>
		<style type="text/css">
			body { background-color: #fff; border-top: solid 10px #000;
			    color: #333; font-size: .85em; margin: 20; padding: 20;
			    font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
			}
			h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
			h1 { font-size: 2em; }
			h2 { font-size: 1.75em; }
			h3 { font-size: 1.2em; }
			table { margin-top: 0.75em; }
			th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
			td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
		</style>
		</head>
		<body>
		<h1>Register here!</h1>
		<p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
		<form method="post" action="index.php" enctype="multipart/form-data" >
		      Name  <input type="text" name="name" id="name"/></br>
		      Email <input type="text" name="email" id="email"/></br>
		      <input type="submit" name="submit" value="Submit" />
		</form>
		<?php

		?>
		</body>
		</html>

6. Nas marcas de PHP, adicione o código PHP para conectar ao banco de dados.

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

    Novamente, será necessário atualizar os valores para <code>$user</code> e <code>$pwd</code> com seu nome de usuário MySQL local e senha.

7. Após o código de conexão de banco de dados, adicione código para inserir informações de registro no banco de dados.

		if(!empty($_POST)) {
		try {
			$name = $_POST['name'];
			$email = $_POST['email'];
			$date = date("Y-m-d");
			// Insert data
			$sql_insert = "INSERT INTO registration_tbl (name, email, date) 
						   VALUES (?,?,?)";
			$stmt = $conn->prepare($sql_insert);
			$stmt->bindValue(1, $name);
			$stmt->bindValue(2, $email);
			$stmt->bindValue(3, $date);
			$stmt->execute();
		}
		catch(Exception $e) {
			die(var_dump($e));
		}
		echo "<h3>Your're registered!</h3>";
		}

8. Finalmente, após o código acima, adicione código para recuperar dados do banco de dados.

		$sql_select = "SELECT * FROM registration_tbl";
		$stmt = $conn->query($sql_select);
		$registrants = $stmt->fetchAll(); 
		if(count($registrants) > 0) {
			echo "<h2>People who are registered:</h2>";
			echo "<table>";
			echo "<tr><th>Name</th>";
			echo "<th>Email</th>";
			echo "<th>Date</th></tr>";
			foreach($registrants as $registrant) {
				echo "<tr><td>".$registrant['name']."</td>";
				echo "<td>".$registrant['email']."</td>";
				echo "<td>".$registrant['date']."</td></tr>";
		    }
		 	echo "</table>";
		} else {
			echo "<h3>No one is currently registered.</h3>";
		}

Agora é possível navegar até **http://localhost/registration/index.php** para testar o aplicativo.

##Publicar seu aplicativo

Depois de testar o aplicativo localmente, você poderá publicá-lo no Site do Azure usando Git. Entretanto, você precisará atualizar a conexão de banco de dados no aplicativo. Usando as informações de conexão de banco de dados obtidas anteriormente (na seção **Obter informações da conexão do Banco de Dados SQL**), atualize as informações a seguir em **ambos** os arquivos `createdatabase.php` e `index.php` com os valores apropriados:

	// DB connection info
	$host = "tcp:<value of SERVER>";
	$user = "<value of USERNAME>@<server ID>";
	$pwd = "<your password>";
	$db = "<value of DATABASE>";

> [WACOM.NOTE]
> No <code>$host</code>, o valor de SERVER deve ser pré-anexado com <code>tcp:</code>, e o valor de <code>$user</code> é a concatenação do valor de USERNAME, '@' e seu ID do servidor. A identificação do servidor é os 10 primeiros caracteres do valor do servidor.


Agora, você está pronto para configurar a publicação gito e publicar o aplicativo.

> [WACOM.NOTE]
> Estas são as mesmas etapas observadas no fim da seção Criar um Site do Azure e configurar a publicação do Git acima.


1. Abra GitBash (ou um terminal, se o Git estiver em seu `PATH`), altere para o diretório raiz de seu aplicativo e execute os seguintes comandos:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Será solicitada a senha que você criou anteriormente.

2. Navegue até **http://[nome do site].azurewebsites.net/createtable.php** para criar a tabela MySQL para o aplicativo.
3. Navegue até **http://[nome do site].azurewebsites.net/index.php** para começar a usar o aplicativo.

Depois de ter publicado seu aplicativo, você pode começar a fazer alterações nele e usar o Git para publicá-lo. 

##Publicar alterações em seu aplicativo

Para publicar alterações no aplicativo, siga estas etapas:

1. Faça alterações em seu aplicativo localmente.
2. Abra GitBash (ou um terminal, se o Git estiver em seu `PATH`), altere para o diretório raiz de seu aplicativo e execute os seguintes comandos:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Será solicitada a senha que você criou anteriormente.

3. Navegue até **http://[nome do site].azurewebsites.net/index.php** para ver suas mudanças.

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/pt-br/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/pt-br/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv
[running-app]: ./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png
[new-website]: ./media/web-sites-php-sql-database-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-sql-database-deploy-use-git/custom_create.png
[website-details-sqlazure]: ./media/web-sites-php-sql-database-deploy-use-git/createphpgitsite.png
[database-settings]: ./media/web-sites-php-sql-database-deploy-use-git/setupdb.png
[create-server]: ./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg
[go-to-dashboard]: ./media/web-sites-php-sql-database-deploy-use-git/viewdeploy.png
[setup-git-publishing]: ./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-sql-database-deploy-use-git/gitsettings.png
[linked-resources]: ./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg
[connection-string]: ./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg
[management-portal]: https://manage.windowsazure.com/
[sql-database-editions]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee621788.aspx
[where-is-code]: ./media/web-sites-php-sql-database-deploy-use-git/setupgit.png

<!--HONumber=35.1-->
