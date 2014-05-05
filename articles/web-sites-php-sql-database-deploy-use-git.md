<properties linkid="develop-php-website-with-sql-database-and-git" urlDisplayName="Web c/ SQL + Git" pageTitle="Site do PHP com o Banco de Dados SQL e Git - Tutorial do Azure" metaKeywords="" description="Um tutorial que demonstra como criar um site do PHP que armazene dados no Banco de Dados SQL e use a implantação Git para o Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="Criar um site do PHP com um Banco de Dados SQL Database e implantar usando o Git" authors="waltpo" solutions="" manager="" editor="mollybos" />



#Criar um Site do PHP com um Banco de Dados SQL e implantar usando o Git

Este tutorial mostra como criar um Site do PHP Azure com um Banco de Dados SQL Azure e como implantá-lo usando o Git. Este tutorial presume que você tenha [PHP][install-php], [SQL Server Express][install-SQLExpress], o [Drivers da Microsoft para SQL Server para PHP][install-drivers], um servidor web, e [gito][install-git] instalado no seu computador. Após a conclusão deste guia, você terá um Banco de Dados SQL do PHP em execução no Azure.

> [WACOM.NOTE]
> Você pode instalar e configurar o PHP, SQL Server Express, os Drivers Microsoft para SQL Server para PHP e Internet Information Services (IIS) usando o <a href="http://www.microsoft.com/web/downloads/platform.aspx">Microsoft Web Platform Installer</a>

Você irá aprender:

* Como criar um Site do Azure e um Banco de Dados usando o Portal de Gerenciamento do Azure. Como, por padrão, o PHP está habilitado no sites do Azure, nada de especial é necessário para executar seu código PHP.
* Como publicar e publicar novamente o aplicativo no Azure usando o Git.
 
Seguindo este tutorial, você irá criar um aplicativo da web de registro simples no PHP. O aplicativo será hospedado em um Site do Azure. A seguinte é uma captura de tela do aplicativo concluído:

![Site PHP do Azure][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]


##Criar um Site do Azure e configurar a publicação Git

Siga estas etapas para criar um Site do Azure e um Banco de Dados SQL:

1. Faça logon no [Portal de Gerenciamento do Azure][management-portal].
2. Clique no ícone **Novo** na parte inferior esquerda do portal.
![Criar um novo site do Azure][new-website]

3. Clique em **Site**, e, em seguida, em **Criação Personalizada**.

	![Criação personalizada de um novo site][custom-create]

	Forneça um valor para a **URL**, selecione **Criar um Novo Banco de Dados SQL** no menu suspenso **Banco de dados** e selecione o banco de dados para seu site no menu suspenso **Região**. Clique na seta na parte inferior da caixa de diálogo.

	![Preencha os detalhes do site][website-details-sqlazure]

4. Insira um valor para o **Nome** do seu banco de dados, selecione a **Edição** [(WEB ou BUSINESS)][sql-database-editions], selecione o **Tamanho máximo** de seu banco de dados, escolha o **Agrupamento** e selecione **NOVO Servidor de Banco de Dados SQL**. Clique na seta na parte inferior da caixa de diálogo.

	![Preencher as configurações do Banco de Dados SQL][database-settings]

5. Digite um nome de administrador e uma senha (e confirme a senha), escolha a região na qual seu novo servidor de Banco de Dados SQL será criado e marque a caixa `Permitir que os serviços do Azure acessem o servidor`.

	![Criar um novo servidor de Banco de Dados SQL][create-server]

	Quando o site tiver sido criado, você verá o texto **Criação do Site"[SITENAME]" concluída com êxito**. Agora, você pode habilitar a publicação do Git.

6. Clique no nome do site exibido na lista de sites para abrir o painel de Início rápido do site.

	![Abrir o painel do site][go-to-dashboard]


7. Na parte inferior direita da página Início rápido, clique em **Configurar a implantação a partir do controle de origem**. 

	![Configurar a publicação do Git][setup-git-publishing]

6. Quando receber a pergunta "Onde está seu código-fonte?" selecione **Repositório Git Loca** e, em seguida, clique na seta.

	![onde está o código-fonte][where-is-code]

8. Para habilitar a publicação do Git, você deve fornecer um nome de usuário e uma senha. Anote o nome do usuário e a senha que você criou. (Se você tiver configurado um repositório de Git antes, esta etapa será ignorada.)

	![Criar credenciais de publicação][credentials]

	Levará alguns segundos para configurar seu repositório.

9. Quando o repositório estiver pronto, você verá instruções para enviar os arquivos do aplicativo por push ao repositório. Tome nota destas instruções - elas serão necessárias mais tarde.

	![Instruções do Git][git-instructions]

##Obter informações da conexão do Banco de Dados SQL

Para conectar-se à instância do Banco de Dados SQL que está em execução nos Sites do Azure, você precisará das informações da conexão. Para obter informações sobre a conexão do Banco de Dados SQL, siga estas etapas:

1. No Portal de Gerenciamento do Azure, clique em **Recursos Vinculado** e, em seguida, clique no nome do banco de dados.

	![Recursos Vinculados][linked-resources]

2. Clique em **Exibir Cadeias de Conexão**.

	![Cadeia de conexão][connection-string]
	
3. Na seção **PHP** da caixa de diálogo resultante, anote os valores de `SERVIDOR`, `BANCO DE DADOS` e `NOME DE USUÁRIO`.

##Criar e testar o aplicativo localmente

O aplicativo Registro é um aplicativo simples do PHP que permite que você se registre em um evento fornecendo seu nome e endereço de email. As informações sobre inscritos anteriores são exibidas em uma tabela. As informações de registro são armazenadas em uma instância do Banco de Dados SQL. O aplicativo consiste em dois arquivos (copie/cole o código disponível abaixo):

* **index.php**: exibe um formulário de registro e uma tabela contendo informações sobre o inscrito.
* **createtable.php**: cria a tabela de Banco de Dados SQL para o aplicativo. Este arquivo será usado apenas uma vez.

Para executar o aplicativo localmente, siga as etapas abaixo. Observe que essas etapas pressupõem que você tem PHP, SQL Server Express e um servidor Web definido na sua máquina local, e que você tenha habilitado a [extensão PDO para SQL Server][pdo-sqlsrv].

1. Crie um Banco de Dados SQL chamado `registro`. Você pode fazer isso a partir do comando `sqlcmd` com estes comandos:

		>sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
		1> create database registration
		2> GO	


2. No seu diretório raiz do servidor Web, crie uma pasta chamada `registro` e crie dois arquivos nela - um chamado `createtable.php` e outro chamado `index.php`.

3. Abra o arquivo `createtable.php` em um editor de texto ou IDE e adicione o código abaixo. Esse código será usado para criar a tabela `registration_tbl` no banco de dados `registro`.

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

	Observe que você precisará atualizar os valores de <code>$user</code> e <code>$pwd</code> com seu nome de usuário e senha SQL Server local.

4. Abra um navegador da web e navegue para **http://localhost/registration/createtable.php** Isso criará a tabela `registration_tbl` no banco de dados.

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

    Será necessário que você atualize os valores de <code>$user</code> e <code>$pwd</code> com seu nome de usuário e senha do MySQL local.

7. Após o código de conexão do banco de dados, adicione código para inserir informações de registro no banco de dados.

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

Agora você pode navegar para **http://localhost/registration/index.php** para testar o aplicativo.

##Publicar seu aplicativo

Depois de testar o aplicativo localmente, você poderá publicá-lo no Site do Azure usando Git. Entretanto, você precisará atualizar a conexão do banco de dados no aplicativo. Com o uso das informações de conexão do banco de dados obtido previamente (na seção **Obter informações de conexão do Banco de Dados SQL**), atualize as seguintes informações nos **dois** arquivos `createdatabase.php` e `index.php` com os valores apropriados:

	// DB connection info
	$host = "tcp:<value of SERVER>";
	$user = "<value of USERNAME>@<server ID>";
	$pwd = "<your password>";
	$db = "<value of DATABASE>";

> [WACOM.NOTE]
> No <code>$host</code>, o valor do servidor deve ser precedido de <code>tcp:</code>e o valor de <code>$user</code> é a concatenação do valor do nome de usuário, ' @' e a identificação do servidor. A identificação do servidor é os 10 primeiros caracteres do valor do servidor.


Agora, você está pronto para configurar a publicação gito e publicar o aplicativo.

> [WACOM.NOTE]
> Estas são as mesmas etapas observadas no final da seção Criar um Site do Azure e Configurar a publicação do Git acima.


1. Abra GitBash (ou um terminal, se o Git estiver em seu `CAMINHO`), altere os diretórios para o diretório raiz de seu aplicativo e execute os seguintes comandos:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Será solicitada a senha que você criou anteriormente.

2. Vá para **http://[nome do site].azurewebsites.net/createtable.php** para criar a tabela do MySQL para o aplicativo.
3. Vá para **http://[nome do site].azurewebsites.net/index.php** para começar a usar o aplicativo.

Depois de ter publicado seu aplicativo, você pode começar a fazer alterações nele e usar o Git para publicá-lo. 

##Publicar alterações em seu aplicativo

Para publicar alterações no aplicativo, siga estas etapas:

1. Faça alterações em seu aplicativo localmente.
2. Abra GitBash (ou um terminal, se o Git estiver em seu `CAMINHO`), altere os diretórios para o diretório raiz de seu aplicativo e execute os seguintes comandos:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Será solicitada a senha que você criou anteriormente.

3. Vá para **http://[nome do site].azurewebsites.net/index.php** para ver suas alterações.

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/pt-br/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/pt-br/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv
[running-app]: ./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png
[new-website]: ./media/web-sites-php-sql-database-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-sql-database-deploy-use-git/custom_create.png
[website-details-sqlazure]: ./media/web-sites-php-sql-database-deploy-use-git/website_details_sqlazure.jpg
[database-settings]: ./media/web-sites-php-sql-database-deploy-use-git/database_settings.jpg
[create-server]: ./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg
[go-to-dashboard]: ./media/web-sites-php-sql-database-deploy-use-git/go_to_dashboard.png
[setup-git-publishing]: ./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-sql-database-deploy-use-git/git-instructions.png
[linked-resources]: ./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg
[connection-string]: ./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg
[management-portal]: https://manage.windowsazure.com/
[sql-database-editions]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee621788.aspx
[where-is-code]: ./media/web-sites-php-sql-database-deploy-use-git/where_is_code.png

