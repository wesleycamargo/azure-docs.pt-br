#Como criar um banco de dados MySQL no Azure

Este guia mostrará como usar o [ClearDB] para criar um banco de dados MySQL na [Azure Store] e como criar um banco de dados MySQL como um recurso vinculado, quando você cria um [Site do Azure][waws]  O [ClearDB] é um provedor de banco de dados como serviço tolerante a falhas que permite que você execute e gerencie bancos de dados MySQL em datacenters do Azure e conecte-os de qualquer aplicativo.  

##Sumário
* [Como criar um banco de dados MySQL na Azure Store](#CreateFromStore)
* [Como criar um banco de dados MySQL como um recurso vinculado para o Site do Azure](#CreateForWebSite)

<div class="dev-callout"> 
<b>Nota</b> 
<p>quando você cria um banco de dados MySQL como parte do processo de criação de Sites, você só pode criar um banco de dados gratuito. Criar um banco de dados MySQL na Azure Store permite que você crie um banco de dados gratuito ou escolha uma das opções pagas.</p> 
</div>

<h2><a id="CreateFromStore"></a>Como criar um banco de dados MySQL na Azure Store</h2>

Para criar um banco de dados MySQL na [Azure Store], faça o seguinte:

1. Faça logon no [Portal de Gerenciamento do Azure][portal].
2. Clique em **+NOVO** na parte inferior da página e selecione **STORE**

	![Selecione o complemento na store](./media/create-mysql-db/select-store.png)

3. Selecione **Banco de Dados ClearDB MySQL** e, em seguida, clique na seta na parte inferior do quadro.

	![Selecione o banco de dados ClearDB MySQL](./media/create-mysql-db/select-cleardb-mysql.png)

4. Selecione um plano, digite um nome de banco de dados e selecione uma região. Em seguida, clique na seta na parte inferior do quadro.

	![Compre o banco de dados MySQL na store](./media/create-mysql-db/purchase-mysql.png)

5. Clique na marca de seleção para concluir a compra.

	![Revise e conclua a compra](./media/create-mysql-db/complete-mysql-purchase.png)

6. Depois que o banco de dados tiver sido criado, você pode gerenciá-lo na guia **COMPLEMENTOS** no portal de gerenciamento.

	![Gerencie o banco de dados MySQL no portal do Azure](./media/create-mysql-db/manage-mysql-add-on.png)

7. Você pode obter as informações de conexão do banco de dados clicando em **INFORMAÇÕES DE CONEXÃO** na parte inferior da página (mostrada acima).

	![Informações de conexão do MySql](./media/create-mysql-db/mysql-conn-info.png) 


<h2><a id="CreateForWebSite"></a>Como criar um banco de dados MySQL como um recurso vinculado para o Site do Azure</h2>

Para criar um banco de dados MySQL como um recurso vinculado ao criar um [Site do Azure][waws], faça o seguinte:

1. Faça logon no [Portal de Gerenciamento do Azure][portal].
2. Clique em **+NOVO** na parte inferior da página, selecione **COMPUTAÇÃO**, **SITE** e **CRIAR COM BANCO DE DADOS**

	![Criar site com banco de dados](./media/create-mysql-db/custom_create.png)

3. Forneça um **URL** para o seu site, selecione a **REGIÃO** para seu site e escolha **Criar um novo banco de dados MySQL** na lista suspensa **BANCO DE DADOS**. Opcionalmente, você pode substituir o nome padrão pela cadeia de conexão. Clique na seta na parte inferior da página.

	![Forneça detalhes do site](./media/create-mysql-db/provide-website-details.png) 

4. Forneça um **NOME** de banco de dados, selecione a **REGIÃO** do banco de dados (esta deve ser a mesma região do seu site), concorde com os termos legais do ClearDB e, em seguida, clique em marca de seleção na parte inferior do quadro.

	![Forneça detalhes do MySQL](./media/create-mysql-db/provide-mysql-details.png)

5. Depois de criar seu site, clique no nome do seu site para ir para o painel do site.

	![Vá para o painel do site](./media/create-mysql-db/go-to-website-dashboard.png)

6. Clique em **CONFIGURAR**.

	![Vá para a guia configurar](./media/create-mysql-db/go-to-configure-tab.png)

7. Role para baixo até a seção **cadeias de conexão** e clique em **Mostrar cadeias de conexão** 

	![Mostrar cadeia de conexão](./media/create-mysql-db/show-conn-string.png)

8. Copie a cadeia de conexão para usar em seu aplicativo.

	![Cadeia de conexão exibida](./media/create-mysql-db/shown-conn-string.png)

<div class="dev-callout"> 
<b>Nota</b> 
<p>cadeias de conexão são acessíveis ao seu aplicativo do site pelo nome da cadeia de conexão. Em aplicativos .NET, cadeias de conexão estão disponíveis no objeto <b>connectionStrings</b>. Em outras linguagens de programação, cadeias de conexão são acessíveis como variáveis de ambiente. Para obter mais informações, consulte <a href="/pt-br/manage/services/web-sites/how-to-configure-websites/">Como configurar sites</a>.</p> 
</div>

[ClearDB]: http://www.cleardb.com/
[waws]: /pt-br/manage/services/web-sites/
[Azure Store]: /pt-br/store/overview/
[portal]: http://manage.windowsazure.com

