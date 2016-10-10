<properties
	pageTitle="Criar e conectar-se a um banco de dados MySQL no Azure"
	description="Saiba como usar o Portal do Azure para criar um banco de dados MySQL e, em seguida, conectar-se a ele de um aplicativo Web PHP no Azure."
	documentationCenter="php"
	services="app-service\web"
	authors="cephalin"
	manager="wpickett"
	editor=""
	tags="mysql"/>

<tags
	ms.service="multiple"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="robmcm;cephalin"/>

# Criar e conectar-se a um banco de dados MySQL no Azure

Este tutorial mostrará como criar um banco de dados MySQL no [Portal do Azure](https://portal.azure.com) (o provedor é [ClearDB](http://www.cleardb.com/)) e como conectar a ele de um aplicativo Web PHP em execução no [Serviço de Aplicativo do Azure](./app-service/app-service-value-prop-what-is.md).

> [AZURE.NOTE] Você também pode criar um banco de dados MySQL como parte de um [modelo de aplicativo do Marketplace](./app-service-web/app-service-web-create-web-app-from-marketplace.md).

## Criar um banco de dados MySQL no Portal do Azure

Para criar um banco de dados MySQL no Portal do Azure, faça o seguinte:

1. Faça logon no [Portal do Azure](https://portal.azure.com).

2. No menu à esquerda, clique em **Novo** > **Dados + Armazenamento** > **Banco de dados MySQL**.

	![Criar um banco de dados MySQL no Azure - iniciar](./media/store-php-create-mysql-database/create-db-1-start.png)

2. Na [folha](azure-portal-overview.md) Novo Banco de Dados MySQL, configure seu novo banco de dados MySQL da seguinte maneira (*folha*: uma página do portal abre horizontalmente):

	- **Nome do Banco de Dados**: digite um nome que possa ser identificado de forma exclusiva
	- **Assinatura**: escolha a assinatura a ser usada
	- **Tipo de Banco de Dados**: escolha **Compartilhado** para os tipos de baixo custo ou gratuito, ou **Dedicado** para obter recursos dedicados.
	- **Grupo de recursos**: adicione o banco de dados MySQL a um [grupo de recursos](../resource-group-overview.md) existente ou coloque-o em um novo. Os recursos no mesmo grupo podem ser gerenciados juntos com facilidade.
	- **Local**: escolha um local perto de você. Ao adicionar a um grupo de recursos existente, você fica bloqueado no local do grupo de recursos.
	- **Tipo de Preço**: clique em **Tipo de Preço** e escolha uma opção de preço (o tipo **Mercury** é gratuito) e clique em **Selecionar**.
	- **Termos Legais**: clique em **Termos Legais**, examine os detalhes da compra e clique em **Comprar**.
	- **Fixar no painel**: selecione se você quiser acessá-lo diretamente do painel. Isso é especialmente útil se você ainda não estiver familiarizado com a navegação do portal.
	
    A captura de tela a seguir é apenas um exemplo de como você pode configurar o banco de dados MySQL. ![Criar um banco de dados MySQL no Azure - configurar](./media/store-php-create-mysql-database/create-db-2-configure.png)

3. Quando tiver concluído a configuração, clique em **Criar**.

	![Criar um banco de dados MySQL no Azure - criar](./media/store-php-create-mysql-database/create-db-3-create.png)

	Você verá uma notificação pop-up informando que a implantação foi iniciada.

	![Criar um banco de dados MySQL no Azure - em andamento](./media/store-php-create-mysql-database/create-db-4-started-status.png)

	Você receberá outro pop-up depois que a implantação for bem-sucedida. O portal também abrirá automaticamente a folha do banco de dados MySQL.

<a name="connect"></a>
## Conectar-se ao seu banco de dados MySQL

Para ver as informações de conexão do novo banco de dados MySQL, basta clicar em **Propriedades** na folha de seu aplicativo Web.
	
![Criar um banco de dados MySQL no Azure - folha Banco de Dados MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Agora você pode usar essas informações de conexão em qualquer aplicativo Web. Um exemplo que mostra como usar as informações de conexão de um aplicativo PHP simples está disponível [aqui](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql).

## Conectar um aplicativo Web Laravel (a partir do tutorial de Introdução de PHP)

Vamos suporte que você concluiu o tutorial [Criar, configurar e implantar um aplicativo Web PHP no Azure](./app-service-web/app-service-web-php-get-started.md) e te um aplicativo Web [Laravel](https://www.laravel.com/) em execução no Azure. Você pode adicionar facilmente recursos de banco de dados ao seu aplicativo Laravel. Basta executar as etapas abaixo:

>[AZURE.NOTE] As etapas a seguir presumem que você concluiu o tutorial [Criar, configurar e implantar um aplicativo Web PHP no Azure](./app-service-web/app-service-web-php-get-started.md).

1. Configure o aplicativo Laravel em seu ambiente de desenvolvimento local para apontar para o banco de dados MySQL. Para fazer isso, abra `.env` no diretório raiz de seu aplicativo Laravel e configure as opções de banco de dados MySQL.

		DB_CONNECTION=mysql
		DB_HOST=<HOSTNAME_from_properties_blade>
		DB_PORT=<PORT_from_properties_blade>
		DB_DATABASE=<see_note_below>
		DB_USERNAME=<USERNAME_from_properties_blade>
		DB_PASSWORD=<PASSWORD_from_properties_blade>

	>[AZURE.NOTE] Na folha **Propriedades**, o nome do banco de dados MySQL pode ou não ser aquele mostrado no campo **NOME DO BANCO DE DADOS**. É melhor verificar o parâmetro Banco de dados no campo **CADEIA DE CONEXÃO** campo.
	>
	>![Criar um banco de dados MySQL no Azure - em andamento](./media/store-php-create-mysql-database/connect-db-1-database-name.png)

2. A maneira mais rápida de verificar se você tem acesso ao MySQL é usar [scaffolding de autenticação padrão do Laravel](https://laravel.com/docs/5.2/authentication#authentication-quickstart). No terminal de linha de comando, execute os seguintes comandos do diretório raiz de seu aplicativo Laravel:

		 php artisan migrate
		 php artisan make:auth

	O primeiro comando cria as tabelas no Azure com base em migrações predefinidas no diretório `database/migrations`, e o segundo comando aplica scaffold nas exibições básicas e rotas para autenticação e registro do usuário.

3. Execute o servidor de desenvolvimento:

		php artisan serve

4. No navegador, navegue até http://localhost:8000 e registre um novo usuário, conforme exibido:

	![Conectar-se ao banco de dados MySQL no Azure - registrar usuário](./media/store-php-create-mysql-database/connect-db-2-development-server.png)

	Siga os prompts da interface do usuário e complete o registro. Após a conclusão do registro, você será conectado.
	
	![Conectar-se ao banco de dados MySQL no Azure - registrar usuário](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)

	Agora você está desenvolvendo seu aplicativo no banco de dados MySQL no Azure.

5. Basta apenas replicar suas configurações do `.env` em seu aplicativo Web do Azure. Execute os seguintes comandos da CLI do Azure:

		azure site appsetting add DB_CONNECTION=mysql
		azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
		azure site appsetting add DB_PORT=<PORT_from_properties_blade>
		azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
		azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
		azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>

	Descubra como isso funciona em [Configurar o aplicativo Web do Azure](./app-service-web/app-service-web-php-get-started.md#configure).

6. Em seguida, confirme e envie ao Azure as alterações locais feitas anteriormente durante a execução de `php artisan make:auth`.

		git add .
		git commit -m "scaffold auth views and routes"
		git push azure master

7. Navegue até o aplicativo Web do Azure.

		azure site browse

8. Faça logon usando as credenciais de usuário que você criou anteriormente.

	![Conectar-se ao banco de dados MySQL no Azure - procurar o aplicativo Web do Azure](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)

	Depois de fazer logon, você verá a tela amigável pós-logon.
	
	![Conectar-se ao banco de dados MySQL no Azure - conectado](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)

	Parabéns, seu aplicativo Web PHP no Azure está acessando os dados de seu banco de dados MySQL.

## Próximas etapas

Para obter mais informações, consulte o [Centro de Desenvolvimento PHP](/develop/php/).

<!---HONumber=AcomDC_0928_2016-->