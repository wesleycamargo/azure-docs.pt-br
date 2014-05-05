<properties linkid="develop-php-website-with-storage" urlDisplayName="Web com armazenamento" pageTitle="Site PHP com armazenamento de tabela - tutorial do Azure" metaKeywords="Armazenamento de tabelas PHP do Azure, site do Azure PHP, site do Azure PHP, tutorial PHP do Azure, exemplo PHP do Azure" description="Este tutorial mostra como criar um site em PHP e usar o serviço de armazenamento de tabelas do Azure no back-end." metaCanonical="" services="web-sites,storage" documentationCenter="PHP" title="Criar um Site da Web PHP usando o armazenamento do Azure" authors="" solutions="" manager="" editor="" />

#Criar um site PHP usando armazenamento do Azure

Este tutorial mostra como criar um site PHP e usar o serviço de armazenamento de tabelas do Azure no back-end. Este tutorial assume que você tem [PHP][install-php] e um servidor web instalados no seu computador. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional, incluindo o Windows, o Mac e o Linux. Ao completar este guia, você terá um site PHP em execução no Azure e o acesso ao serviço de armazenamento de tabela.
 
Você aprenderá:

* Como instalar as bibliotecas de cliente Azure e incluí-las em seu aplicativo.
* Como usar as bibliotecas de cliente para criar tabelas e para criar, consultar e excluir entidades de tabela.
* Como criar uma conta de armazenamento do Azure e configurar seu aplicativo para usá-lo.
* Como criar um Site do Azure e implantar a ele usando Git
 
Você criará um aplicativo simples da web Tasklist no PHP. A seguinte é uma captura de tela do aplicativo concluído:

![Site PHP do Azure][ws-storage-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Instalar as bibliotecas de cliente do Azure

Para instalar as Bibliotecas de Clientes do PHP através do computador, siga estas etapas:

1. [Instalar o Git.][install-git]

	> [WACOM.NOTE]
	> No Windows, você também precisará adicionar o executável do Git à variável de ambiente PATH.

2. Crie um arquivo chamado **composer.json** na raiz do seu projeto e adicione o seguinte código nele:

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

##Guia de Introdução com as bibliotecas de cliente

Há quatro etapas básicas que devem ser executadas antes de fazer uma chamada para um API do Azure usando as bibliotecas. Você criará um script de inicialização que irá executar essas etapas.

* Crie um arquivo chamado **init.php**.

* Primeiro, inclua o script do carregador automático:

		require_once 'vendor\autoload.php'; 
	
* Inclua os namespaces que você usará.

	Para criar qualquer cliente de serviço do Azure é necessário usar a classe **ServicesBuilder**:

		use WindowsAzure\Common\ServicesBuilder;

	Para capturar exceções produzidas por qualquer chamada de API, é necessário a classe **ServiceException**:

		use o WindowsAzure\Common\ServiceException;
	
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

	Os códigos de erro e a verificação de mensagens são encontrados aqui: [http://msdn.microsoft.com/pt-br/library/windowsazure/dd179438.aspx][msdn-errors]


##Consultando uma tabela

A home page do aplicativo Tasklist deve listar todas as tarefas existentes e permite a inserção de novos produtos.

* Crie um arquivo chamado **index.php** e insira o seguinte código HTML e PHP que formarão o cabeçalho da página:
	
		<html>
		<head>
			<title>Índice</title>
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
		<h1>Minha lista de tarefas pendentes <font color="grey" size="5">(alimentada por tabelas do Azure e PHP) </font></h1>
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

* Depois que você obtiver uma entidade, o modelo para leitura de dados é `Entity->getPropertyValue('[name]')`:

			if ($i == 0) {
				echo "<table border='1'>
				<tr>
					<td>Nome</td>
					<td>Categoria</td>
					<td>Data</td>
					<td>Marcar como concluída?</td>
					<td>Excluir?</td>
				</tr>";
			}
			echo "
				<tr>
					<td>".$entities[$i]->getPropertyValue('name')."</td>
					<td>".$entities[$i]->getPropertyValue('category')."</td>
					<td>".$entities[$i]->getPropertyValue('date')."</td>";
					if ($entities[$i]->getPropertyValue('complete') == false)
						echo "<td><a href='markitem.php?complete=true&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Marcar como concluída</a></td>";
					else
						echo "<td><a href='markitem.php?complete=false&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Desmarcar como concluída</a></td>";
					echo "
					<td><a href='deleteitem.php?pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Excluir</a></td>
				</tr>";
		}
	
		if ($i > 0)
			echo "</table>";
		else
			echo "<h3>Não há itens na lista.</h3>";
		?>

* Por fim, você deve inserir o formulário que fornece dados para o script de inserção de tarefa e concluir o HTML:

			<hr/>
			<form action="additem.php" method="post">
				<table border="1">
					<tr>
						<td>Nome do item: </td>
						<td><input name="itemname" type="textbox"/></td>
					</tr>
					<tr>
						<td>Categoria: </td>
						<td><input name="category" type="textbox"/></td>
					</tr>
					<tr>
						<td>Data: </td>
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

* A primeira etapa da inserção de uma entidade é instanciar um objeto 'Entity' e configurar as propriedades nela:
		
		$entity = new Entity();
		$entity->setPartitionKey('p1');
		$entity->setRowKey((string) microtime(true));
		$entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
		$entity->addProperty('category', EdmType::STRING, $_POST['category']);
		$entity->addProperty('date', EdmType::STRING, $_POST['date']);
		$entity->addProperty('complete', EdmType::BOOLEAN, false);

* Depois você pode passar a `$entity` que você acabou de criar para o método `insertEntity`:

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
	
## Atualizar uma entidade

O aplicativo de lista de tarefa tem a capacidade de marcar um item como concluído, bem como desmarcá-lo. A home page passa o *RowKey* e *PartitionKey* de uma entidade e o estado de destino (marcado==1, desmarcado==0).

* Crie um arquivo chamado **markitem.php** e adicione a parte de inicialização:

		<?php		
		require_once "init.php";
		

* O primeiro passo para atualização de uma entidade é buscá-la da tabela:
		
		$result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq \''.$_GET['pk'].'\' and RowKey eq \''.$_GET['rk'].'\'');		
		$entities = $result->getEntities();		
		$entity = $entities[0];

	Como você pode ver no filtro da consulta aprovado, o formato é `key eq 'Value'`. Uma descrição completa da sintaxe de consulta está disponível [aqui][msdn-table-query-syntax].

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


## Excluir uma entidade

A exclusão de um item é realizada por uma única chamada para `deleteItem`. Os valores passados são **PartitionKey** e **RowKey**, que juntos formam a chave primária da entidade. Crie um arquivo chamado **deleteitem.php** e insira o seguinte código:

		<?php
		
		require_once "init.php";		
		$tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);		
		header('Location: index.php');
		
		?>


## Criar uma conta de armazenamento do Azure

Para fazer seu aplicativo armazenar dados na nuvem, você precisa primeiro criar uma conta de armazenamento no Azure e, em seguida, passar as informações de autenticação adequadas para a classe *Configuração*.

1. Faça logon no [Portal de Gerenciamento do Azure][management-portal].

2. Clique no ícone **+ Novo** na parte inferior esquerda do portal.

	![Criar um novo site do Azure][new-website]

3. Clique em **Serviços de Dados**, **Armazenamento**, depois em **Criação Rápida**.

	![Criação personalizada de um novo site][storage-quick-create]
	
	Insira um valor para **URL** e selecione o centro de dados para seu site no menu suspenso **REGIÃO**. Clique no botão **Criar conta de armazenamento** na parte inferior da caixa de diálogo.

	![Preencha os detalhes do site][storage-quick-create-details]

	Quando a conta de armazenamento tiver sido criada, você verá o texto **Criação da conta de armazenamento '[NOME]' criada com êxito**.

4. Verifique se a guia **Armazenamento** está selecionada e, em seguida, selecione a conta de armazenamento que você recém criou na lista.

5. Clique em **Gerenciar Chaves** na barra de aplicativos na parte inferior.

	![Selecione Gerenciar chaves][storage-manage-keys]

6. Anote o nome da conta de armazenamento que você criou e da chave primária.

	![Selecione Gerenciar chaves][storage-access-keys]

7. Abra **init.php** e substitua `[NOME DA SUA CONTA DE ARMAZENAMENTO]` e `[NOME DA SUA CHAVE DE ARMAZENAMENTO]` com o nome da conta e a chave você tirou nota na última etapa. Salve o arquivo.


## Crie um Site do Azure e configure a publicação Git

Siga estas etapas para criar um site do Azure:

1. Faça logon no [Portal de Gerenciamento do Azure][management-portal].
2. Clique no ícone **+ Novo** na parte inferior esquerda do portal.

	![Criar um novo site do Azure][new-website]

3. Clique em **Computar**, **Site da web** e depois em **Criação rápida**.

	![Criação personalizada de um novo site][website-quick-create]
	
	Insira um valor para **URL** e selecione o centro de dados para seu site no menu suspenso **REGIÃO**. Clique no botão **Criar novo site da web** na parte inferior da caixa de diálogo.

	![Preencha os detalhes do site][website-quick-create-details]

	Quando o site tiver sido criado, você verá o texto **Criação do Site '[SITENAME]' concluída com êxito**. Agora, você pode habilitar a publicação do Git.

5. Clique no nome do site exibido na lista de sites para abrir o painel **QUICKSTART** do site.

	![Abrir o painel do site][go-to-dashboard]


6. Na parte inferior direita da página Quickstart, selecione **Configurar uma implantação a partir do controle de origem**.

	![Configurar a publicação do Git][setup-git-publishing]

6. Quando receber a pergunta "Onde está seu código-fonte?" selecione **Repositório Git Loca** e, em seguida, clique na seta.

	![onde está o código-fonte][where-is-code]

7. Para habilitar a publicação do Git, você deve fornecer um nome de usuário e uma senha. Anote o nome do usuário e a senha que você criou. (Se você tiver configurado um repositório de Git antes, esta etapa será ignorada.)

	![Criar credenciais de publicação][credentials]

	Levará alguns segundos para configurar seu repositório.

8. Quando o repositório Git estiver pronto, você receberá instruções sobre os comandos Git a serem usados para configurar um repositório local e enviar os arquivos ao Azure.

	![Instruções da implantação do Git retornadas após a criação de um repositório para o site.][git-instructions]

	Observe as instruções, pois elas serão usadas na próxima seção para publicar o aplicativo.

##Publicar seu aplicativo

Para publicar o aplicativo com Git, siga as etapas abaixo.

1. Abra a pasta **vendor/microsoft/windowsazure** sob a raiz do aplicativo e exclua os seguintes arquivos e pastas:
	* .git
	* .gitattributes
	* .gitignore
			
	Quando o Gerenciador de pacotes do Composer baixa as bibliotecas de cliente Azure e suas dependências, ele faz isso por meio da clonagem de repositório GitHub em que reside. Na próxima etapa, o aplicativo será implantado por meio do Git através da criação de um repositório fora da pasta raiz do aplicativo. O Git irá ignorar o repositório sub onde as bibliotecas de cliente residem, a menos que os arquivos específicos do repositório são removidos.

2. Abra GitBash (ou um terminal, se o Git está no seu `PATH`), altere os diretórios para o diretório raiz do seu aplicativo e execute os seguintes comandos (**Observação:** estas são as mesmas etapas indicadas no final da seção **Criar um Site do Azure e configurar a publicação Git**):

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL do repositório remoto]
		git push azure master

	Será solicitada a senha que você criou anteriormente.

3. Vá para **http://[domínio do seu site]/createtable.php** para criar a tabela para o aplicativo.
4. Vá para **http://[domínio do seu site]/index.php** para começar a usar o aplicativo.

Depois de ter publicado seu aplicativo, você pode começar a fazer alterações nele e usar o Git para publicá-lo. 

##Publicar alterações em seu aplicativo

Para publicar alterações no aplicativo, siga estas etapas:

1. Faça alterações em seu aplicativo localmente.
2. Abra GitBash (ou um terminal, se o Git estiver em seu `CAMINHO`), altere os diretórios para o diretório raiz de seu aplicativo e execute os seguintes comandos:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Será solicitada a senha que você criou anteriormente.

3. Vá para **http://[domínio do seu site]/index.php** para ver suas alterações. 

[install-php]: http://www.php.net/manual/en/install.php


[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer-phar]: http://getcomposer.org/composer.phar

[msdn-errors]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd179438.aspx



[msdn-table-query-syntax]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd894031.aspx
[ws-storage-app]: ./media/web-sites-php-storage/ws-storage-app.png
[portal de gerenciamento]: https://manage.windowsazure.com
[novo site]: ./media/web-sites-php-storage/new_website.jpg

[site de criação rápida]: ./media/web-sites-php-storage/website-quick-create.png
[detalhes da criação rápida do site]: ./media/web-sites-php-storage/website-quick-create-details.png
[criação rápida de armazenamento]: ./media/web-sites-php-storage/storage-quick-create.png
[detalhes da criação rápida de armazenamento]: ./media/web-sites-php-storage/storage-quick-create-details.png
[chaves para gerenciar o armazenamento]: ./media/web-sites-php-storage/storage-manage-keys.png
[chaves de acesso do armazenamento]: ./media/web-sites-php-storage/storage-access-keys.png

[vá para o painel de controle]: ./media/web-sites-php-storage/go_to_dashboard.png
[instalação do git de publicação]: ./media/web-sites-php-storage/setup_git_publishing.png
[credenciais]: ./media/web-sites-php-storage/git-deployment-credentials.png


[instruções do git]: ./media/web-sites-php-storage/git-instructions.png
[onde está o código]: ./media/web-sites-php-storage/where_is_code.png

