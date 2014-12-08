#Como criar um banco de dados MySQL no Azure

Este guia vai mostrar-lhe como usar o [ClearDB]  para criar um banco de dados MySQL nda [Azure Store] e como criar um banco de dados MySQL como um recurso vinculado, quando você cria um [Azure Web Site][waws]. O [ClearDB] é um provedor de banco de dados como serviço tolerante a falhas que permite que você execute e gerencie bancos de dados MySQL em datacenters do Azure e conecte-os de qualquer aplicativo.  

##Sumário
* [Como: criar um banco de dados MySQL na Azure Store](#CreateFromStore)
* [Como: criar um banco de dados MySQL como um recurso vinculado para o Site do Azure](#CreateForWebSite)

<div class="dev-callout"> 
<b>Observação</b> 
<p>Quando você cria um banco de dados MySQL como parte do processo de criação de Sites, você pode criar somente um banco de dados gratuito. Criar um banco de dados MySQL na Azure Store permite que você crie um banco de dados gratuito ou escolha uma das opções pagas.</p> 
</div>

<h2><a id="CreateFromStore"></a>Como: criar um banco de dados MySQL na Azure Store</h2>

Para criar um banco de dados MySQL na [Azure Store], faça o seguinte:

1. Faça logon no [Portal de Gerenciamento do Azure][].
2. Clique em **+NOVO** na parte inferior da página e selecione **STORE**.

	![Select add-on from store](./media/create-mysql-db/select-store.png)

3. Selecione **Banco de Dados ClearDB MySQL** e, em seguida, clique na seta na parte inferior do quadro.

	![Select ClearDB MySQL Database](./media/create-mysql-db/select-cleardb-mysql.png)

4. Selecione um plano, digite um nome de banco de dados e selecione uma região. Em seguida, clique na seta na parte inferior do quadro.

	![Purchase MySQL database from store](./media/create-mysql-db/purchase-mysql.png)

5. Clique na marca de seleção para concluir a compra.

	![Review and complete your purchase](./media/create-mysql-db/complete-mysql-purchase.png)

6. Depois que o banco de dados tiver sido criado, você pode gerenciá-lo na guia **COMPLEMENTOS** no portal de gerenciamento.

	![Manage MySQL database in Azure portal](./media/create-mysql-db/manage-mysql-add-on.png)

7. Você pode obter as informações de conexão de banco de dados clicando em **INFORMAÇÕES DE CONEXÃO** na parte inferior da página (mostrada acima).

	![MySql connection information](./media/create-mysql-db/mysql-conn-info.png) 


<h2><a id="CreateForWebSite"></a>Como: criar um banco de dados MySQL como um recurso vinculado para o Site do Azure</h2>

Para criar um banco de dados MySQL como um recurso vinculado ao criar um [Site do Azure][waws], faça o seguinte:

1. Faça logon no [Portal de Gerenciamento do Azure][].
2. Clique em **+NOVO** na parte inferior da página, em seguida, selecione **CALCULAR**, **SITE** e **CRIAR COM BANCO DE DADOS**.

	![Create website with database](./media/create-mysql-db/custom_create.png)

3. Forneça uma **URL** para o seu site, selecione a **REGIÃO** para o seu site, e escolha **Criar um novo banco de dados MySQL** no menu suspenso **BANCO DE DADOS**. Opcionalmente, você pode substituir o nome padrão pela cadeia de conexão. Clique na seta na parte inferior da página.

	![Provide website details](./media/create-mysql-db/provide-website-details.png) 

4. Forneça um **NOME** de banco de dados, selecione a **REGIÃO** do banco de dados (esta deve ser a mesma região do seu site), concorde com os termos legais do ClearDB e, em seguida, clique em marca de seleção na parte inferior do quadro.

	![Provide MySQL details](./media/create-mysql-db/provide-mysql-details.png)

5. Depois de criar seu site, clique no nome do seu site para ir para o painel do site.

	![Go to web site dashboard](./media/create-mysql-db/go-to-website-dashboard.png)

6. Clique em **CONFIGURAR**.

	![Go to configure tab](./media/create-mysql-db/go-to-configure-tab.png)

7. Role para baixo até a seção **cadeias de caracteres de conexão** e clique em **Mostrar cadeias de caracteres de conexão**. 

	![Show connection string](./media/create-mysql-db/show-conn-string.png)

8. Copie a cadeia de conexão para usar em seu aplicativo.

	![Shown connection string](./media/create-mysql-db/shown-conn-string.png)

> [WACOM.NOTE] As cadeias de conexão são acessíveis para o aplicativo do seu site pelo nome da cadeia de conexão. Em aplicativos .NET, cadeias de conexão estão disponíveis no objeto **connectionStrings**. Em outras linguagens de programação, cadeias de conexão são acessíveis como variáveis de ambiente. Para obter mais informações, consulte [Como configurar sites][configurar].

[ClearDB]: http://www.cleardb.com/
[waws]: /pt-br/documentation/services/web-sites/
[Azure Store]: /pt-br/gallery/store/
[portal]: http://manage.windowsazure.com
[configurar]: ../web-sites-configure/
