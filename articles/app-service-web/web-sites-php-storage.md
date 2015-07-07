<properties 
	pageTitle="Criar um aplicativo Web do PHP no Serviço de Aplicativo do Azure usando o Armazenamento do Azure" 
	description="Este tutorial mostra como criar um site do PHP no Serviço de Aplicativo do Azure e usar o serviço de armazenamento de tabelas do Azure no back-end." 
	services="app-service\web, storage" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="tomfitz"/>

# Criar um aplicativo Web do PHP no Serviço de Aplicativo do Azure usando o Armazenamento do Azure

Este tutorial mostra como criar um site do PHP no [serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) e usar o serviço de armazenamento de tabelas do Azure no back-end. Este tutorial assume que você tem [PHP][install-php] e um servidor web instalados no seu computador. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional, incluindo o Windows, o Mac e o Linux. Ao concluir este guia, você terá um aplicativo Web PHP sendo executado no Azure e acessando o serviço de armazenamento de tabela.
 
Você aprenderá:

* Como instalar as bibliotecas de cliente Azure e incluí-las em seu aplicativo.
* Como usar as bibliotecas de cliente para criar tabelas e para criar, consultar e excluir entidades de tabela.
* Como criar uma conta de armazenamento do Azure e configurar seu aplicativo para usá-lo.
* Como criar um aplicativo Web do Azure e implantar nele usando o Git
 
Você criará um aplicativo Web de lista de tarefas simples no PHP. Abaixo, uma captura de tela do aplicativo concluído:

![Aplicativo Web do PHP do Azure][ws-storage-app]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

##Instalar as bibliotecas de cliente do Azure

Para instalar as Bibliotecas de Clientes do PHP através do computador, siga estas etapas:

1. [Instalação do Git][install-git]

	> [AZURE.NOTE]No Windows, também será necessário adicionar o Git executável à variável de ambiente PATH.

2. Crie um arquivo chamado **composer.json** na raiz do seu projeto e adicione o seguinte código a ele:

		{
			"require": {
				"microsoft/windowsazure": "*"
			},			
			"repositories": [
				{
					"type": "pear",
					"url": "http://pear.php.net"
				}
			],
			"minimum-stability": "dev"
		}

3. Baixe o **[composer.phar][composer-phar]** na raiz do seu projeto.

4. Abra um prompt de comando e execute esta função na raiz do projeto

		php composer.phar install

##Introdução à biblioteca de clientes

Há quatro etapas básicas que devem ser executadas antes de fazer uma chamada para um API do Azure usando as bibliotecas. Você criará um script de inicialização que irá executar essas etapas.

* Crie um arquivo chamado **init.php**.

* Primeiro, inclua o script do carregador automático:

		require_once 'vendor\autoload.php'; 
	
* Inclua os namespaces que você usará.

	Para criar qualquer cliente de serviço do Azure é necessário usar a classe **ServicesBuilder**:

		use WindowsAzure\Common\ServicesBuilder;

	Para capturar exceções produzidas por qualquer chamada de API, é necessário a classe **ServiceException**:

		use WindowsAzure\Common\ServiceException;
	
* Para criar uma instância de cliente de serviço será necessário uma sequência de conexão válida. O formato para as cadeias de conexão do serviço Tabela é:

	Para acessar um serviço ao vivo:
	
		DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
	
	Para acessar o armazenamento do emulador:
	
		UseDevelopmentStorage=true

* Use o método de fábrica `ServicesBuilder::createTableService` para instanciar um invólucro em torno das chamadas de serviço da tabela.

		$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	`$tableRestProxy` contém um método para cada chamada REST disponível nas tabelas do Azure.


## Criando uma tabela

Antes de armazenar dados, você primeiro precisa criar um recipiente para ele, a tabela.

* Crie um arquivo chamado **createtable.php**.

* Primeiro, inclua o script de inicialização que você acabou de criar. Você estará incluindo o script em cada arquivo acessando o Azure:

		<?php
		require_once "init.php";

* Em seguida, faça uma chamada para *createTable* passando o nome da tabela. Da mesma forma para outros armazenamentos de tabela NoSQL, nenhum esquema é necessário para tabelas do Azure.
	
		try	{
			$tableRestProxy->createTable('tasks');
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		?>

	Códigos de erro e verificação de mensagens ser encontradas aqui: [http://msdn.microsoft.com/library/windowsazure/dd179438.aspx][msdn-errors]


##Consultando uma tabela

A home page do aplicativo Tasklist deve listar todas as tarefas existentes e permite a inserção de novos produtos.

* Crie um arquivo chamado **index.php** e insira o seguinte código HTML e PHP que formarão o cabeçalho da página:
	
		<html>
		<head>
			<title>Index</title>
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
		<h1>My ToDo List <font color="grey" size="5">(powered by PHP and Azure Tables) </font></h1>
		<?php		
		require_once "init.php";

* Para consultar tabelas do Azure para **todas as entidades** armazenadas na tabela *tarefas*, você chamará o método *queryEntities*passando apenas o nome da tabela. Na seção **Atualizar uma entidade** abaixo, você também verá como passar um filtro de consulta para uma entidade específica.

		try {
		    $result = $tableRestProxy->queryEntities('tasks');
		}
		catch(ServiceException $e){
		    $code = $e->getCode();
		    $error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		
* Para iterar em entidades no conjunto de resultados:

		$entities = $result->getEntities();
			
		for ($i = 0; $i < count($entities); $i++) {

* Depois que você obtiver uma `Entity`, o modelo para leitura de dados será `Entity->getPropertyValue('[name]')`:

			if ($i == 0) {
				echo "<table border='1'>
				<tr>
					<td>Name</td>
					<td>Category</td>
					<td>Date</td>
					<td>Mark Complete?</td>
					<td>Delete?</td>
				</tr>";
			}
			echo "
				<tr>
					<td>".$entities[$i]->getPropertyValue('name')."</td>
					<td>".$entities[$i]->getPropertyValue('category')."</td>
					<td>".$entities[$i]->getPropertyValue('date')."</td>";
					if ($entities[$i]->getPropertyValue('complete') == false)
						echo "<td><a href='markitem.php?complete=true&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Mark Complete</a></td>";
					else
						echo "<td><a href='markitem.php?complete=false&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Unmark Complete</a></td>";
					echo "
					<td><a href='deleteitem.php?pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Delete</a></td>
				</tr>";
		}
	
		if ($i > 0)
			echo "</table>";
		else
			echo "<h3>No items on list.</h3>";
		?>

* Por fim, você deve inserir o formulário que fornece dados para o script de inserção de tarefa e concluir o HTML:

			<hr/>
			<form action="additem.php" method="post">
				<table border="1">
					<tr>
						<td>Item Name: </td>
						<td><input name="itemname" type="textbox"/></td>
					</tr>
					<tr>
						<td>Category: </td>
						<td><input name="category" type="textbox"/></td>
					</tr>
					<tr>
						<td>Date: </td>
						<td><input name="date" type="textbox"/></td>
					</tr>
				</table>
				<input type="submit" value="Add item"/>
			</form>
		</body>
		</html>

## Inserir entidades em uma tabela

Seu aplicativo pode ler todos os itens armazenados na tabela. Desde que não haja qualquer punho, vamos adicionar uma função que grava os dados no banco de dados.

* Crie um arquivo chamado **additem.php**.

* Adicione o seguinte ao arquivo:

		<?php		
		require_once "init.php";		
		use WindowsAzure\Table\Models\Entity;
		use WindowsAzure\Table\Models\EdmType;		

* A primeira etapa para inserir uma entidade é instanciar um objeto `Entity` e configurar as propriedades nele:
		
		$entity = new Entity();
		$entity->setPartitionKey('p1');
		$entity->setRowKey((string) microtime(true));
		$entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
		$entity->addProperty('category', EdmType::STRING, $_POST['category']);
		$entity->addProperty('date', EdmType::STRING, $_POST['date']);
		$entity->addProperty('complete', EdmType::BOOLEAN, false);

* Em seguida, você pode passar a `$entity` que acabou de criar ao método `insertEntity`:

		try{
			$tableRestProxy->insertEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* Por último, para fazer a página retornar à home page depois de inserir a entidade:

		header('Location: index.php');		
		?>
	
## Atualizando uma entidade

O aplicativo de lista de tarefa tem a capacidade de marcar um item como concluído, bem como desmarcá-lo. A home page passa o *RowKey* e *PartitionKey* de uma entidade e o estado de destino (marcado==1, desmarcado==0).

* Crie um arquivo chamado **markitem.php** e adicione a parte de inicialização:

		<?php		
		require_once "init.php";
		

* O primeiro passo para atualização de uma entidade é buscá-la da tabela:
		
		$result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq ''.$_GET['pk'].'' and RowKey eq ''.$_GET['rk'].''');		
		$entities = $result->getEntities();		
		$entity = $entities[0];

	Como você pode ver no filtro da consulta aprovado, o formato é `Key eq 'Value'`. Uma descrição completa da sintaxe de consulta está disponível [aqui][msdn-table-query-syntax].

* Em seguida, você pode alterar quaisquer propriedades:

		$entity->setPropertyValue('complete', ($_GET['complete'] == 'true') ? true : false);

* E o método `updateEntity` executa a atualização:

		try{
			$result = $tableRestProxy->updateEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* Para fazer a página retornar à home page depois de inserir a entidade:

		header('Location: index.php');		
		?>


## Excluindo uma entidade

A exclusão de um item é realizada por uma única chamada para `deleteItem`. Os valores passados são **PartitionKey** e **RowKey**, que juntos formam a chave primária da entidade. Crie um arquivo chamado **deleteitem.php** e insira o seguinte código:

		<?php
		
		require_once "init.php";		
		$tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);		
		header('Location: index.php');
		
		?>


## Criar uma conta de Armazenamento do Azure

Para fazer seu aplicativo armazenar dados na nuvem, você precisa primeiro criar uma conta de armazenamento no Azure e, em seguida, passar as informações de autenticação adequadas para a classe *Configuração*.

1. Faça logon no [Portal do Azure][management-portal].

2. Clique no ícone **Novo** na parte inferior esquerda do portal e clique em **Dados + Armazenamento** > **Armazenamento**. Dê um nome exclusivo à conta de armazenamento e crie um novo [grupo de recursos](../resource-group-overview.md) para ela.

	![Criar uma nova conta de armazenamento][storage-quick-create]
	
	Quando a conta de armazenamento tiver sido criada, o botão **Notificações** piscará **ÊXITO** em verde e a folha da conta de armazenamento será aberta para mostrar que ela pertence ao novo grupo de recursos que você criou.

5. Clique na parte **Configurações** na folha da conta de armazenamento. Anote o nome da conta e a chave primária.

	![Selecione Gerenciar chaves][storage-access-keys]

7. Abra **init.php** e substitua `[YOUR_STORAGE_ACCOUNT_NAME]` e `[YOUR_STORAGE_ACCOUNT_KEY]` com o nome da conta e a chave que você anotou na última etapa. Salve o arquivo.

## Criar um aplicativo Web do Azure e configurar a publicação Git

Siga estas etapas para criar um aplicativo Web do Azure:

1. Faça logon no [Portal do Azure][management-portal].

2. Crie um aplicativo web vazio com as instruções em [Como: Criar um aplicativo web usando o Portal do Azure](../web-sites-create-deploy.md#createawebsiteportal). Certifique-se de criar um novo [plano de serviço do aplicativo](azure-web-sites-web-hosting-plans-in-depth-overview) e selecione o grupo de recursos que você criou anteriormente para a conta de armazenamento.

	Quando o aplicativo Web tiver sido criado, o botão **Notificações** piscará **ÊXITO** em verde e a folha do aplicativo web será aberta para mostrar que ele pertence ao novo grupo de recursos que você criou.

6. Na folha do aplicativo web, clique em **Configurar implantação contínua** e escolha **Repositório do Git local**. Clique em **OK**.

	![Configurar a publicação do Git][setup-git-publishing]

7. Para poder implantar seu repositório do Git local no Azure, você também deve configurar credenciais de implantação. Na folha do aplicativo web, clique em **Todas as configurações** > **Credenciais de implantação** para configurar as credenciais. Clique em **Salvar** quando terminar.

	![Criar credenciais de publicação][credentials]

	Levará alguns segundos para configurar seu repositório.

8. Quando o repositório Git estiver pronto, você enviará suas alterações para ele. Você pode encontrar a URL do repositório clicando na mesma parte de implantação na folha do aplicativo Web.

	![Instruções da implantação do Git retornadas após a criação de um repositório para o site.][git-instructions]

	Observe as instruções, pois elas serão usadas na próxima seção para publicar o aplicativo.

##Publicar seu aplicativo

Para publicar o aplicativo com Git, siga as etapas abaixo.

1. Abra a pasta **vendor/microsoft/windowsazure** sob a raiz do aplicativo e exclua os seguintes arquivos e pastas:
	* .git
	* .gitattributes
	* .gitignore
			
	Quando o Gerenciador de pacotes do Composer baixa as bibliotecas de cliente do Azure e suas dependências, isso é feito por meio da clonagem do repositório GitHub no qual eles residem. Na próxima etapa, o aplicativo será implantado por meio de Git, criando um repositório fora da pasta raiz do aplicativo. O Git ignorará o repositório sub onde as bibliotecas de cliente residem, a menos que os arquivos específicos do repositório são removidos.

2. Abra GitBash (ou um terminal, se o Git estiver em seu `PATH`), altere para o diretório raiz de seu aplicativo e execute os seguintes comandos:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Será solicitada a senha que você criou anteriormente.

3. Vá para **http://[yourweb app domain]/createtable.php** para criar a tabela para o aplicativo.
4. Vá para **http://[yourweb app domain]/index.php** para começar a usar o aplicativo.

Depois de ter publicado seu aplicativo, você pode começar a fazer alterações nele e usar o Git para publicá-lo.

##Publicar alterações em seu aplicativo

Para publicar alterações no aplicativo, siga estas etapas:

1. Faça alterações em seu aplicativo localmente.
2. Abra GitBash (ou um terminal, se o Git estiver em seu `PATH`), altere os diretórios para o diretório raiz de seu aplicativo e execute os seguintes comandos:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Será solicitada a senha que você criou anteriormente.

3. Vá para **http://[yourweb app domain]/index.php** para ver suas alterações.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, consulte: [Referência para navegar no portal](http://go.microsoft.com/fwlink/?LinkId=529715)




[install-php]: http://www.php.net/manual/en/install.php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer-phar]: http://getcomposer.org/composer.phar
[msdn-errors]: http://msdn.microsoft.com/library/windowsazure/dd179438.aspx

[msdn-table-query-syntax]: http://msdn.microsoft.com/library/windowsazure/dd894031.aspx
[ws-storage-app]: ./media/web-sites-php-storage/ws-storage-app.png
[management-portal]: https://portal.azure.com

[storage-quick-create]: ./media/web-sites-php-storage/createstorage.png
[storage-access-keys]: ./media/web-sites-php-storage/keydetails.png

[setup-git-publishing]: ./media/web-sites-php-storage/setup_git_publishing.png
[credentials]: ./media/web-sites-php-storage/git-deployment-credentials.png

[git-instructions]: ./media/web-sites-php-storage/git-instructions.png
 

<!---HONumber=62-->