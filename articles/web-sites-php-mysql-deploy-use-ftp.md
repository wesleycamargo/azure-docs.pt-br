<properties urlDisplayName="Web w/ MySQL + FTP" pageTitle="Website PHP com MySQL e FTP - Tutorial do Azure" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use FTP deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure Website and Deploy Using FTP" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/14/2014" ms.author="tomfitz" />


#Criar um Site do Azure PHP-MySQL e implantar usando FTP

Este tutorial mostra como criar um site PHP-MySQL do Microsoft Azure e como implantá-lo usando FTP.  Este tutorial presume que você tenha [PHP, ][install-php][MySQL][install-mysql], um servidor Web e um cliente de FTP instalado no seu computador.As instruções deste tutorial podem ser seguidas em qualquer sistema operacional, incluindo o Windows, o Mac e o Linux.Após a conclusão deste guia, você terá um site do PHP/MySQL em execução no Azure.
 
Você aprenderá:

* Como criar um Site do Azure e um banco de dados MySQL usando o Portal de Gerenciamento do Azure. Como, por padrão, o PHP está habilitado no sites do Azure, nada de especial é necessário para executar seu código PHP.
* Como publicar seu aplicativo no Azure usando FTP.
 
Seguindo este tutorial, você criará um aplicativo Web de registro simples no PHP. O aplicativo será hospedado em um Site do Azure. A seguinte é uma captura de tela do aplicativo concluído:

![Azure PHP Web Site][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Criar um site do Azure e configurar a publicação FTP

Siga estas etapas para criar um Site do Azure e um Banco de Dados MySQL:

1. Faça logon no [Portal de Gerenciamento do Azure][management-portal].
2. Clique no ícone **+ New** na parte inferior esquerda do portal.

	![Create New Azure Web Site][new-website]

3. Clique em **SITE**, em seguida, clique em **CRIAÇÃO PERSONALIZADA**.

	![Custom Create a new Web Site][custom-create]
	
	Insira um valor para a **URL**, selecione **Criar novo banco de dados MySQL** na lista suspensa **BANCO DE DADOS** e selecione o data center para seu site na lista suspensa **REGIÃO**.Clique na seta na parte inferior da caixa de diálogo.

	![Fill in Web Site details][website-details]

4. Insira um valor para o **NOME** do banco de dados, selecione o data center para seu banco de dados na lista suspensa **REGIÃO** e marque a caixa que indica que você concorda com os termos legais.Clique na marca de seleção na parte inferior da caixa de diálogo.

	![Create new MySQL database][new-mysql-db]

	Quando o website tiver sido criado, você verá o texto **Criação do Website '[SITENAME]' concluído com sucesso**.Agora, é possível habilitar uma publicação de FTP.

5. Clique no nome do site exibido na lista de sites para abrir o painel **QUICKSTART**.

	![Open web site dashboard][go-to-dashboard]


6. Na parte inferior da página **QUICKSTART**, clique em **Redefinir credenciais de implantação**. 

	![Reset deployment credentials][reset-deployment-credentials]

7. Para habilitar publicação de FTP, você deverá fornecer um nome de usuário e senha. Anote o nome de usuário e a senha que você criou.

	![Create publishing credentials][portal-git-username-password]

##Criar e testar o aplicativo localmente

O aplicativo Registro é um aplicativo simples do PHP que permite que você se registre em um evento fornecendo seu nome e endereço de email. As informações sobre inscritos anteriores são exibidas em uma tabela. As informações de registro são armazenadas em um banco de dados MySQL. O aplicativo consiste de dois arquivos:

* **index.php**:Exibe um formulário de registro e uma tabela que contém informações de registro.
* **createtable.php**: cria a tabela MySQL para o aplicativo.Este arquivo será usado apenas uma vez.

Para criar e executar o aplicativo localmente, siga as etapas abaixo. Observe que essas etapas pressupõem que você tem PHP, MySQL e um servidor Web definido em seu computador local, e que você tenha habilitado a [extensão PDO para MySQL][pdo-mysql].

1. Crie um banco de dados MySQL chamado "registro". Você pode fazer isso no prompt de comando MySQL com este comando:

		mysql> create database registration;

2. No seu diretório raiz do servidor Web, crie uma pasta chamada `registration` e crie dois arquivos nela - um chamado `createtable.php` e outro chamado `index.php`.

3. Abra o arquivo `createtable.php` em um editor de texto ou IDE e adicione o código abaixo. Esse código será usado para criar a tabela `registration_tbl` no banco de dados `registration`.

		<?php
		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
						id INT NOT NULL AUTO_INCREMENT, 
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

	> [WACOM.NOTE] 
	> Será necessário que você atualize os valores de <code>$user</code> e <code>$pwd</code> com seu nome de usuário e senha do MySQL local.

4. Abra um navegador da Web e navegue para [http://localhost/registration/createtable.php][localhost-createtable].Isso criará a tabela `registration_tbl` no banco de dados.

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

6. Dentro de marcas PHP, add PHP code for connecting to the database.

		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

	> [WACOM.NOTE]
	> Novamente, será necessário atualizar os valores para <code>$user</code> e <code>$pwd</code> com seu nome de usuário e senha do MySQL local.

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

Agora é possível navegar até [http://localhost/registration/index.php][localhost-index] para testar o aplicativo.

##Obter informações de conexão do MySQL e FTP

Para conectar-se ao do Banco de Dados SQL que está em execução nos Sites do Azure, você precisará das informações da conexão. Para obter informações sobre a conexão MySQL, siga estas etapas:

1. No painel do site, clique no link **Exibir cadeias de conexão** no lado direito da página:

	![Get database connection information][connection-string-info]
	
2. Anote os valores de "Banco de Dados", "Fonte de Dados", "ID do Usuário" e "Senha".

3. No painel do site, clique no link **Baixar perfil de publicação** na parte inferior direito da página:

	![Download publish profile][download-publish-profile]

4. Abra o arquivo `.publishsettings` em um editor XML. 

3. Encontre o elemento `<publishProfile >` com `publishMethod="FTP"` que é semelhante a isto:

		<publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
			...
		</publishProfile>
	
Tome nota dos atributos `publishUrl`, `userName` e `userPWD`.

##Publicar seu aplicativo

Depois de testar o aplicativo localmente, você poderá publicá-lo no Site do Azure usando FTP. Entretanto, você precisará atualizar a conexão de banco de dados no aplicativo. Com o uso das informações de conexão de banco de dados obtido previamente (na seção **Obter informações de conexão MySQL e FTP**), atualize as seguintes informações nos **dois** arquivos `createdatabase.php` e `index.php` com os valores apropriados:

	// DB connection info
	$host = "value of Data Source";
	$user = "value of User Id";
	$pwd = "value of Password";
	$db = "value of Database";

Agora você está pronto para publicar seu aplicativo usando FTP.

1. Abra o cliente de FTP de sua escolha.

2. Insira a *porção do nome de host* do atributo `publishUrl` que foi anotado acima no seu cliente de FTP.

3. Insira os atributos `userName` e `userPWD` que foram anotados acima inalterados no seu cliente de FTP.

4. Estabeleça uma conexão.

Após você ter se conectado, será capaz de carregar e descarregar arquivos de downloads, conforme necessário. Tenha certeza de que você está carregando arquivos para o diretório raiz, que é `/site/wwwroot`.

Depois de carregar `index.php` e `createtable.php`, navegue para **http://[nome do site].azurewebsites.net/createtable.php** para criar a tabela MySQL para o aplicativo, então navegue para **http://[nome do site].azurewebsites.net/index.php** para começar a usar o aplicativo.
 

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_website.jpg
[custom-create]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/custom_create.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/go_to_dashboard.png
[reset-deployment-credentials]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/reset-deployment-credentials.png
[portal-git-username-password]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/git-deployment-credentials.png


[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://manage.windowsazure.com
[download-publish-profile]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/download_publish_profile_2.png

<!--HONumber=35.1-->
