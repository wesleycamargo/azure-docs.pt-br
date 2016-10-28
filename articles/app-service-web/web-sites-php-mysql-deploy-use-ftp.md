<properties 
	pageTitle="Criar um aplicativo Web PHP-MySQL no Serviço de Aplicativo do Azure e implantá-lo usando FTP" 
	description="Um tutorial que demonstra como criar um aplicativo Web PHP que armazena dados no MySQL e como usar a implantação FTP no Azure." 
	services="app-service\web" 
	documentationCenter="php" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="08/11/2016" 
	ms.author="robmcm"/>


#Criar um aplicativo Web PHP-MySQL no Serviço de Aplicativo do Azure e implantá-lo usando FTP

Este tutorial mostra como criar um aplicativo Web PHP-MySQL e como implantá-lo usando FTP. Este tutorial presume que você tenha [PHP][install-php], [MySQL][install-mysql], um servidor Web e um cliente de FTP instalado no seu computador. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional, incluindo o Windows, o Mac e o Linux. Após a conclusão deste guia, você terá um aplicativo Web PHP/MySQL em execução no Azure.
 
Você aprenderá:

* Como criar um aplicativo Web e um banco de dados MySQL usando o Portal do Azure. Como o PHP está habilitado em Aplicativos Web por padrão, nada de especial é necessário para executar seu código PHP.
* Como publicar seu aplicativo no Azure usando FTP.
 
Seguindo este tutorial, você compilará um aplicativo Web de registro simples em PHP. O aplicativo será hospedado em um aplicativo Web. Abaixo, uma captura de tela do aplicativo concluído:

![Site PHP do Azure][running-app]

>[AZURE.NOTE] Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.


##Criar um aplicativo Web e configurar a publicação por FTP

Siga estas etapas para criar um aplicativo Web e um Banco de Dados MySQL:

1. Faça logon no [Portal do Azure][management-portal].
2. Clique no ícone **+ Novo** no canto superior esquerdo do Portal do Azure.

	![Criar um novo site do Azure][new-website]

3. Na pesquisa, digite **Aplicativo Web + MySQL** e clique em **Aplicativo Web + MySQL**.

	![Criação personalizada de um novo site][custom-create]

4. Clique em **Criar**. Insira um nome de serviço de aplicativo exclusivo, um nome válido para o grupo de recursos e um novo plano de serviço.

    ![Definir nome do grupo de recursos][resource-group]


6. Insira valores para seu novo banco de dados, incluindo concordar com os termos legais.

	![Criar novo banco de dados MySQL][new-mysql-db]
	
7. Quando o aplicativo Web tiver sido criado, você verá a nova folha do serviço de aplicativo.


6. Clique em **Configurações** > **Credenciais da implantação**.

	![Definir credenciais de implantação][set-deployment-credentials]

7. Para habilitar publicação de FTP, você deverá fornecer um nome de usuário e senha. Salve as credenciais e anote o nome de usuário e a senha que você criar.

	![Criar credenciais de publicação][portal-ftp-username-password]

##Compilar e testar o aplicativo localmente

O aplicativo Registro é um aplicativo simples do PHP que permite que você se registre em um evento fornecendo seu nome e endereço de email. As informações sobre inscritos anteriores são exibidas em uma tabela. As informações de registro são armazenadas em um banco de dados MySQL. O aplicativo consiste de dois arquivos:

* **index.php**: exibe um formulário de registro e uma tabela contendo informações sobre o inscrito.
* **createtable.php**: cria a tabela MySQL para o aplicativo. Este arquivo será usado apenas uma vez.

Para compilar e executar o aplicativo localmente, siga as etapas abaixo. Observe que essas etapas pressupõem que você tem PHP, MySQL e um servidor Web definido na sua máquina local, e que você tenha habilitado a [extensão PDO para MySQL][pdo-mysql].

1. Crie um banco de dados MySQL chamado `registration`. Você pode fazer isso no prompt de comando MySQL com este comando:

		mysql> create database registration;

2. No seu diretório raiz do servidor Web , crie uma pasta chamada `registration` e crie dois arquivos nela: um chamado `createtable.php` e outro chamado `index.php`.

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

	> [AZURE.NOTE] 
	Será necessário que você atualize os valores de <code>$user</code> e <code>$pwd</code> com seu nome de usuário e senha do MySQL local.

4. Abra um navegador da web e navegue para [http://localhost/registration/createtable.php][localhost-createtable]. Isso criará a tabela `registration_tbl` no banco de dados.

5. Abra o arquivo **index.php** em um editor de texto ou IDE e adicione o código básico de HTML e CSS para a página (o código PHP será adicionado em várias etapas).

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

	> [AZURE.NOTE]
	Novamente, você precisará atualizar os valores de <code>$user</code> e <code>$pwd</code> com o nome de usuário e a senha do MySQL local.

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

Agora você pode navegar para [http://localhost/registration/index.php][localhost-index] para testar o aplicativo.

##Obter informações de conexão do MySQL e FTP

Para conectar-se ao Banco de Dados MySQL que está em execução nos Aplicativos Web, você precisará das informações da conexão. Para obter informações sobre a conexão MySQL, siga estas etapas:

1. Na folha do aplicativo Web do serviço de aplicativo clique no link do grupo de recursos:

	![Escolher Grupo de Recursos][select-resourcegroup]

1. No seu grupo de recursos, verifique o banco de dados:

	![Selecionar um banco de dados][select-database]

2. No resumo do banco de dados, escolha **Configurações** > **Propriedades**.

    ![Selecionar propriedades][select-properties]
	
2. Anote os valores de `Database`, `Host`, `User Id` e `Password`.

    ![Anotar propriedades][note-properties]

3. Em seu aplicativo Web, clique no link **Baixar perfil de publicação** na parte inferior direito da página:

	![Baixar perfil de publicação][download-publish-profile]

4. Abra o arquivo `.publishsettings` em um editor XML.

3. Localize o elemento `<publishProfile >` com `publishMethod="FTP"` que parece semelhante a este:

		<publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
			...
		</publishProfile>
	
Anote os atributos de `publishUrl`, `userName` e `userPWD`.

##Publicar seu aplicativo

Depois de testar seu aplicativo localmente, você poderá publicá-lo para o aplicativo Web usando FTP. Entretanto, você precisará atualizar a conexão do banco de dados no aplicativo. Com o uso das informações de conexão do banco de dados obtido previamente (na seção **Obter informações de conexão MySQL e FTP**), atualize as seguintes informações nos **dois** arquivos `createdatabase.php` e `index.php` com os valores apropriados:

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

Após carregar `index.php` e `createtable.php`, navegue para **http://[site name].azurewebsites.net/createtable.php** para criar a tabela para o aplicativo, em seguida navegue para **http://[site name].azurewebsites.net/index.php** para começar usar o aplicativo.
 
## Próximas etapas

Para obter mais informações, consulte o [Centro de desenvolvedores do PHP](/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-ftp/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-ftp/create_web_mysql.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-ftp/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-ftp/select_webapp.png
[set-deployment-credentials]: ./media/web-sites-php-mysql-deploy-use-ftp/set_credentials.png
[portal-ftp-username-password]: ./media/web-sites-php-mysql-deploy-use-ftp/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-ftp/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-ftp/create_wa.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-ftp/select_database.png
[select-resourcegroup]: ./media/web-sites-php-mysql-deploy-use-ftp/select_resourcegroup.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/note-properties.png

[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://portal.azure.com
[download-publish-profile]: ./media/web-sites-php-mysql-deploy-use-ftp/download_publish_profile_3.png
 

<!---HONumber=AcomDC_0817_2016-->