<properties linkid="develop-php-website-with-mysql-and-git" urlDisplayName="Web com MySQL + Git" pageTitle="Site de PHP com MySQL e Git - Tutorial do Azure" metaKeywords="" description="Um tutorial que demonstra como criar um site de PHP que armazena dados em MySQL e usa a implantação do Git no Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Criar um Site do Azure de PHP-MySQL e implantar usando o Git" authors=""  solutions="" writer="waltpo" manager="" editor="mollybos"  />

#Criar um Site do Azure PHP-MySQL e implantar usando o Git

Este tutorial mostra como criar um Site do Azure PHP-MySQL e como implantá-lo usando o Git. Você usará [PHP][install-php], a Ferramenta de Linha de Comando do MySQL (parte do [MySQL][install-mysql]), um servidor web e o [Git][install-git] instalados no seu computador. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional, incluindo o Windows, o Mac e o Linux. Após a conclusão deste guia, você terá um site do PHP/MySQL em execução no Azure.
 
Você irá aprender:

* Como criar um Site do Azure e um banco de dados MySQL usando o Portal de Gerenciamento do Azure. Como, por padrão, o PHP está habilitado no Sites do Azure, nada de especial é necessário para executar seu código PHP.
* Como publicar e publicar novamente o aplicativo no Azure usando o Git.
 
Seguindo este tutorial, você irá criar um aplicativo da web de registro simples no PHP. O aplicativo será hospedado em um Site do Azure. A seguinte é uma captura de tela do aplicativo concluído:

![Site de PHP do Azure][running-app]

<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisa de uma conta do Azure com o recurso Sites do Azure habilitado. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">Avaliação gratuita do Azure</a>.</p> </div>

##Configurar o ambiente de desenvolvimento

Este tutorial pressupõe que você tem o [PHP][install-php], a Ferramenta de Linha de Comando do MySQL (parte do [MySQL][install-mysql]), um servidor web e o [Git][install-git] instalados no seu computador.

> [WACOM.NOTE]
> Se você estiver executando este tutorial no Windows, poderá configurar sua máquina para PHP e configurar automaticamente o IIS (o servidor Web interno do Windows) instalando o <a href="http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids">SDK do Azure para PHP</a>.

##<a id="create-web-site-and-set-up-git"></a>Criar um Site do Azure e configurar a publicação do Git

Siga estas etapas para criar um Site do Azure e um banco de dados MySQL:

1. Faça logon no [Portal de Gerenciamento do Azure][management-portal].
2. Clique no ícone **Novo** na parte inferior esquerda do portal.

	![Criar um novo Site do Azure][new-website]

3. Clique em **Site** e, em seguida, em **Criação Personalizada**.

	![Criação personalizada de um novo site][custom-create]
	
	Forneça um valor para a **URL**, selecione **Criar um Novo Banco de Dados MySQL** no menu suspenso **Banco de Dados** e selecione o data center para seu site no menu suspenso **Região**. Clique na seta na parte inferior da caixa de diálogo.

	![Preencha os detalhes do site][website-details]

4. Insira um valor para o **Nome** do banco de dados, selecione o data center para seu banco de dados no menu suspenso **Região** e marque a caixa que indica que você concorda com os termos legais. Clique na marca de seleção na parte inferior da caixa de diálogo.

	![Criar novo banco de dados MySQL][new-mysql-db]

	Quando o site tiver sido criado, você verá o texto **Criação do Site"[SITENAME]" concluída com êxito**. Agora, você pode habilitar a publicação do Git.

6. Clique no nome do site exibido na lista de sites para abrir o painel **QuickStart** do site.

	![Abrir o painel do site][go-to-dashboard]


7. Na parte inferior da página **QuickStart**, clique em **Configurar a publicação do Git**. 

	![Configurar a publicação do Git][setup-git-publishing]

8. Para habilitar a publicação do Git, você deve fornecer um nome de usuário e uma senha. Anote o nome do usuário e a senha que você criou. (Se você tiver configurado um repositório de Git antes, esta etapa será ignorada.)

	![Criar credenciais de publicação][credentials]

	Levará alguns segundos para configurar seu repositório.

9. Quando o repositório estiver pronto, você verá instruções para enviar os arquivos do aplicativo por push ao repositório. Tome nota destas instruções - elas serão necessárias mais tarde.

	![Instruções do Git][git-instructions]

##Obter informações da conexão MySQL remota

Para conectar-se ao banco de dados MySQL que está em execução nos Sites do Azure, você precisará das informações sobre a conexão. Para obter informações sobre a conexão MySQL, siga estas etapas:

1. No painel do site, clique no link **Exibir cadeias de conexão** no lado direito da página:

	![Obter informações da conexão do banco de dados][connection-string-info]
	
2. Anote os valores de `Database`,`Data Source`,`User Id` e `Password`.

##Criar e testar o aplicativo localmente

Agora que você criou um Site do Azure, você pode desenvolver seu aplicativo localmente e implantá-lo após o teste. 

O aplicativo Registro é um aplicativo simples do PHP que permite que você se registre em um evento fornecendo seu nome e endereço de email. As informações sobre inscritos anteriores são exibidas em uma tabela. As informações de registro são armazenadas em um banco de dados MySQL. O aplicativo consiste em um arquivo (copie/cole o código disponível abaixo):

* **index.php**: exibe um formulário de registro e uma tabela contendo informações sobre o inscrito.

Para criar e executar o aplicativo localmente, siga as etapas abaixo. Observe que essas etapas presumem que o PHP, a ferramenta de linha de comando do MySQL (parte do MySQL) e um servidor Web estão configurados no seu computador local e que você habilitou a [extensão PDO do MySQL][pdo-mysql].

1. Conecte-se ao servidor MySQL remoto usando o valor de `Data Source`,`User Id`,`Password` e `Database` que você recuperou anteriormente:

		mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. O prompt de comando do MySQL será exibido:

		mysql>

3. Cole no seguinte comando `CREATE TABLE` para criar a tabela `registration_tbl` em seu banco de dados:

		mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. No diretório raiz do servidor web, crie uma pasta chamada `Registration` e crie um arquivo chamado `index.php`.

5. Abra o arquivo **index.php** em um editor de texto ou IDE, adicione o seguinte código e conclua as alterações necessárias marcadas com os comentários `//TODO:`.


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
			tabela { margin-top: 0.75em; }
			th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
			td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
		</style>
		</head>
		<body>
		<h1>Registre-se aqui.</h1>
		<p>Preencha seu nome e endereço de email e clique em <strong>Enviar</strong> para registrar-se.</p>
		<form method="post" action="index.php" enctype="multipart/form-data" >
		      Nome  <input type="text" name="name" id="name"/></br>
		      Email <input type="text" name="email" id="email"/></br>
		      <input type="submit" name="submit" value="Submit" />
		</form>
		<?php
			// Informações sobre a conexão do BD
			//TODO: Atualize os valores de $host, $user, $pwd e $db
			//usando os valores que você recuperou anteriormente no portal.
			$host = "valor da fonte de dados";
			$user = "valor do Id de usuário";
			$pwd = "valor da senha";
			$db = "valor do banco de dados";
			// Conecte-se ao banco de dados.
			try {
				$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
				$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			}
			catch(Exception $e){
				die(var_dump($e));
			}
			// Insira informações sobre o registro
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
			echo "<h3>Você está inscrito.</h3>";
			}
			// Retrieve data
			$sql_select = "SELECT * FROM registration_tbl";
			$stmt = $conn->query($sql_select);
			$registrants = $stmt->fetchAll(); 
			if(count($registrants) > 0) {
				echo "<h2>Pessoas que estão inscritas:</h2>";
				echo "<table>";
				echo "<tr><th>Nome</th>";
				echo "<th>Email</th>";
				echo "<th>Data</th></tr>";
				foreach($registrants as $registrant) {
					echo "<tr><td>".$registrant['name']."</td>";
					echo "<td>".$registrant['email']."</td>";
					echo "<td>".$registrant['date']."</td></tr>";
		    	}
		 		echo "</table>";
			} else {
				echo "<h3>Ninguém está inscrito no momento.</h3>";
			}
		?>
		</body>
		</html>

Agora você pode navegar até **http://localhost/registration/index.php** para testar o aplicativo.


##Publicar o aplicativo

Depois de testar o aplicativo localmente, você poderá publicá-lo no Site do Azure usando Git. Você inicializara seu repositório local do Git e publicará o aplicativo.

> [WACOM.NOTE]
> Estas são as mesmas etapas mostradas no portal no final da seção Criar um Site do Azure e Configurar a publicação do Git acima.

1. (Opcional) Se você tiver esquecido a URL de seu repositório remoto do Git, navegue até a guia Implantação no portal.
	
	![Obter URL do Git][git-instructions]

1. Abra GitBash (ou um terminal, se o Git estiver em seu `PATH`), altere para o diretório raiz de seu aplicativo e execute os seguintes comandos:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Será solicitada a senha que você criou anteriormente.

	![Envio por push inicial ao Azure via Git][git-initial-push]

2. Navegue até **http://[nome do site].azurewebsites.net/index.php** para começar a usar o aplicativo (essas informações serão armazenadas no painel de sua conta):

	![Site de PHP do Azure][running-app]

Depois de ter publicado seu aplicativo, você pode começar a fazer alterações nele e usar o Git para publicá-lo. 

##Publicar alterações em seu aplicativo

Para publicar alterações no aplicativo, siga estas etapas:

1. Faça alterações em seu aplicativo localmente.
2. Abra GitBash (ou um terminal, se o Git estiver em seu `PATH`), altere para o diretório raiz de seu aplicativo e execute os seguintes comandos:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Será solicitada a senha que você criou anteriormente.

	![Enviando alterações por push ao Site do Azure via Git][git-change-push]

3. Navegue até **http://[nome do site].azurewebsites.net/index.php** para ver seu aplicativo e todas as alterações feitas:

	![Site de PHP do Azure][running-app]

4. Você também pode ver a nova implantação na guia 'Implantações' no Portal de Gerenciamento do Azure:

	![Lista das implantações de site][deployments-list]

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/pt-br/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/pt-br/download/details.aspx?id=20098
[install-git]: http://git-scm.com/

[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/custom_create.png
[website-details]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-mysql-deploy-use-git/go_to_dashboard.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
[deployments-list]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
[connection-string-info]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
[management-portal]: https://manage.windowsazure.com
[sql-database-editions]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee621788.aspx

