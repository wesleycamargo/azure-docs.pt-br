<properties linkid="dev-nodejs-basic-web-app-with-storage" urlDisplayName="Aplicativo Web com Armazenamento" pageTitle="Aplicativo Web com armazenamento de tabela (Node.js) | Microsoft Azure" metaKeywords="tutorial de hello world com Node.js do Azure, hello world com Node.js do Azure, tutorial de introdução ao Node.js do Azure, tutorial do Node.js do Azure, tutorial do Node.js Express do Azure" description="Um tutorial que tem como base o tutorial Aplicativo Web com o Express adicionando os serviços de armazenamento do Azure e o módulo Azure." metaCanonical="" services="cloud-services,storage" documentationCenter="Node.js" title="Aplicativo Web do Node.js usando Armazenamento" authors="" solutions="" manager="" editor="" />





# Aplicativo Web do Node.js usando Armazenamento

Neste tutorial, você ampliará o aplicativo criado no tutorial [Aplicativo Web do Node.js usando o Express] usando as bibliotecas de clientes do Azure para o Node.js para trabalhar com serviços de gerenciamento de dados. Você ampliará seu aplicativo para criar um aplicativo de lista de tarefas com base na Web que pode ser implantado no Azure. A lista de tarefas permite que um usuário recupere tarefas, adicione novas tarefas e marque tarefas como concluídas.

Os itens da tarefa são armazenados no Armazenamento do Azure. O Armazenamento do Azure fornece um armazenamento de dados não estruturado altamente disponível e tolerante a falhas. O Armazenamento do Azure inclui diversas estruturas de dados, onde você pode armazenar e acessar dados, além de poder utilizar os serviços de armazenamento das APIs incluídos no SDK do Azure para Node.js ou por meio de APIs REST. Para obter mais informações, consulte [Armazenando e acessando dados no Azure (a página pode estar em inglês)].

Este tutorial pressupõe que você tenha concluído os tutoriais [Aplicativo Web do Node.js] e [Node.js com Express][Node.js Web Application using Express].

Você aprenderá:

-   Como trabalhar com o mecanismo de modelo Jade
-   Como trabalhar com os serviços de gerenciamento de dados do Azure

A seguinte é uma captura de tela do aplicativo concluído:

![A página da Web completa no Internet Explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## Definindo credenciais de armazenamento em Web.Config

Para acessar o Armazenamento do Azure, você precisa transmitir as credenciais de armazenamento. Para isso, você deve utilizar as configurações do aplicativo web.config.
Essas configurações serão transmitidas como variáveis de ambiente para Node que, em seguida, serão lidas pelo SDK do Azure.

<div class="dev-callout">
<strong>Observação</strong>
<p>As credenciais de armazenamento são usadas somente quando o aplicativo é implantado no Azure. Quando executado no emulador, o aplicativo usará o emulador de armazenamento.</p>
</div>

Execute as etapas a seguir para recuperar as credenciais da conta de armazenamento e adicioná-las às configurações de web.config:

1.  Se ainda não estiver aberto, inicie o Azure PowerShell no menu **Iniciar** expandindo **Todos os Programas, Azure** e clicando com o botão direito do mouse em **Azure PowerShell** e, em seguida, selecione **Executar como Administrador**.

2.  Altere os diretórios para a pasta que contém o aplicativo. Por exemplo, C:\\node\\tasklist\\WebRole1.

3.  Na janela do Azure Powershell, insira o seguinte cmdlet para recuperar as informações da conta de armazenamento:

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

	Isso recupera a lista de contas de armazenamento e as chaves da conta associada ao seu serviço hospedado.

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Como o SDK do Azure cria uma conta de armazenamento quando você implanta um serviço, uma conta de armazenamento já deve existir da implantação de seu aplicativo nos guias anteriores.</p>
	</div>

4.  Abra o arquivo web.cloud.config que contém as configurações de ambiente usadas quando o aplicativo é implantado no Azure:

        PS C:\node\tasklist\WebRole1> notepad web.cloud.config

5.  Insira o seguinte bloco sob o elemento **configuration**, substituindo {STORAGE ACCOUNT} e {STORAGE ACCESS KEY} pelo nome da conta e a chave primária da conta de armazenamento que deseja usar para a implantação:

        <appSettings>
          <add key="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}"/>
          <add key="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}"/>
        </appSettings>

	![O conteúdo do arquivo web.cloud.config](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Salve o arquivo e feche o Bloco de Notas.

## Instalar módulos

Para usar os serviços de gerenciamento de dados do Azure, você deve instalar o módulo do Azure para node. Também deve instalar o módulo node-uuid, pois ele será usado para gerar UUIDs (Universally Unique Identifier - Identificadores Universais Exclusivos). Para instalar esses módulos, digite o comando a seguir:

    PS C:\node\tasklist\WebRole1> npm install node-uuid azure

Quando o comando for concluído, os módulos terão sido adicionados à
pasta **node\_modules**. Execute as etapas a seguir para usar esses módulos em seu aplicativo:

1.  Abra o arquivo server.js:

        PS C:\node\tasklist\WebRole1> notepad server.js

2.  Adicione o código abaixo após a linha que termina com express.createServer() para incluir os módulos node-uuid, home e azure. O módulo home ainda não existe, mas você irá criá-lo em breve.

	![O código de server.js com a linha app = modules.exports realçada](./media/storage-nodejs-use-table-storage-cloud-service-app/node38.png)

        var uuid = require('node-uuid');
        var Home = require('./home');
        var azure = require('azure');

3.  Adicione o código para criar um cliente de tabela de armazenamento que transmite informações da conta de armazenamento e da chave de acesso.

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Quando for executado no emulador, o SDK usará o emulador automaticamente, mesmo que as informações da conta de armazenamento tenham sido fornecidas via web.config.</p>
	</div>

        var client = azure.createTableService();

4.  Em seguida, crie uma tabela no Armazenamento do Azure chamada tasks. A lógica a seguir cria uma nova tabela, se ela não existir, e a preenche com alguns dados padrão.

        //table creation
        client.createTableIfNotExists('tasks', function(error){
            if(error){
                throw error;
            }

            var item = {
                name: 'Add readonly task list',
                category: 'Site work',
                date: '12/01/2011',
                RowKey: uuid(),
                PartitionKey: 'partition1',
                completed: false
            };

            client.insertEntity('tasks', item, function(){});

        });

5.  Substitua o código existente na seção route pelo código a seguir, que cria uma instância de controlador home e roteia todas as solicitações para **/** ou **/home** para ela.

	![O arquivo server.js com a seção routes realçada.](./media/storage-nodejs-use-table-storage-cloud-service-app/node39.png)

        var home = new Home(client);
        app.get('/', home.showItems.bind(home));
        app.get('/home', home.showItems.bind(home));

	Observe que, em vez de manipular a solicitação embutida, você agora está delegando o comando para um objeto Home. O comando **bind** é necessário para garantir que essas referências sejam corretamente resolvidas localmente no controlador home.

## Criando o controlador Home

Agora, você deve criar um controlador home que manipule todas as solicitações para o site da lista de tarefas. Execute as seguintes etapas para criar o controlador:

1.  Crie um novo arquivo home.js no Bloco de Notas. Esse arquivo irá conter o código do controlador que manipula a lógica da lista de tarefas.

        PS C:\node\tasklist\WebRole1> notepad home.js

2.  Substitua o conteúdo pelo código a seguir e salve o arquivo. O código a seguir usa o padrão de módulo de javascript. Ele exporta uma função Home. O protótipo Home contém as funções para manipular as solicitações reais.

        var azure=require('azure');
        module.exports = Home;

        function Home (client) {
            this.client = client;
        };

        Home.prototype = {
            showItems: function (req, res) {
                var self = this;
                this.getItems(false, function (resp, tasklist) {
                    if (!tasklist) {
                        tasklist = [];
                    }			
                    self.showResults(res, tasklist);
                });
            },

            getItems: function (allItems, callback) {
                var query = azure.TableQuery
                    .select()
                    .from('tasks');
        	
                if (!allItems) {
                    query = query.where('completed eq ?', 'false');
                }
                this.client.queryEntities(query, callback);
             },

             showResults: function (res, tasklist) {
                res.render('home', { 
                    title: 'Todo list', 
                    layout: false, 
                    tasklist: tasklist });
             },
        };

	Agora, o seu controlador home inclui três funções:

	-   *showItems* manipula a solicitação.
	-   *getItems* usa o cliente de tabela para recuperar itens de tarefas abertas de sua tabela de tarefas. Observe que a consulta pode ter filtros adicionais aplicados; por exemplo, a consulta acima filtra apenas a exibição de tarefas quando completed é igual a false.
	-   *showResults* chama a função de renderização Express para renderizar a página usando a exibição home que você criará na próxima seção.

### Modificando a exibição Home

O mecanismo de modelo Jade usa uma sintaxe de marcação que é menos detalhada do que o HTML e é o mecanismo padrão para trabalhar com o Express. Execute as etapas a seguir para criar uma exibição que oferece suporte à visualização de itens de lista de tarefas:

1.  Na janela de comando do Windows PowerShell, edite o arquivo home.jade usando o seguinte comando:

        PS C:\node\tasklist\WebRole1\views> notepad home.jade

2.  Substitua o conteúdo do arquivo home.jade pelo código a seguir e salve o arquivo. O formulário abaixo contém funcionalidades de leitura e atualização de itens de tarefa. Observe que, atualmente, o controlador home oferece suporte apenas à leitura; você irá alterar isso posteriormente. O formulário contém detalhes para cada item na lista de tarefas.

        html
        head
            title Index
        body
            h1 My ToDo List

            form
                table(border="1")
                    tr
                        td Name
                        td Category
                        td Date
                        td Complete

                        each item in tasklist
                            tr
                                td #{item.name}
                                td #{item.category} 
                                td #{item.date} 
                                td 
                                    input(type="checkbox", name="completed", value="#{item.RowKey}") 

## Executando o aplicativo no emulador de computação

1.  Na janela do Windows PowerShell, insira o seguinte cmdlet para iniciar o seu serviço no emulador de computação e exibir uma página da Web que chama o seu serviço.

        PS C:\node\tasklist\WebRole1> Start-AzureEmulator -launch

	O navegador exibirá a página a seguir, mostrando o item de tarefa que foi recuperado do Armazenamento do Azure:

	![Internet Explorer exibindo uma página My Tasklist com um item em uma tabela.](./media/storage-nodejs-use-table-storage-cloud-service-app/node40.png)

## Adicionando novas funcionalidades de tarefa

Nesta seção, você atualizará o aplicativo para oferecer suporte à adição de novos itens de tarefa.

### Adicionando uma nova rota para Server.js

No arquivo server.js, adicione a seguinte linha após a última entrada de rota para **/home** e, em seguida, salve o arquivo.

![O arquivo server.js com a linha que contém a rota para home realçada.](./media/storage-nodejs-use-table-storage-cloud-service-app/node41.png)

        app.post('/home/newitem', home.newItem.bind(home));

	A seção de rotas agora deve ser semelhante ao seguinte:

       // Routes

       var home = new Home(client);
       app.get('/', home.showItems.bind(home));
       app.get('/home', home.showItems.bind(home));
       app.post('/home/newitem', home.newItem.bind(home));

### Adicionando o módulo Node-UUID

Para usar o módulo node-uuid para criar um identificador exclusivo, adicione a seguinte linha na parte superior do arquivo home.js depois da primeira linha em que o módulo é exportado.

![O arquivo home.js com a linha module.exports = Home realçada.](./media/storage-nodejs-use-table-storage-cloud-service-app/node42.png)

       var uuid = require('node-uuid');

### Adicionando a função de item novo ao controlador home

Para implementar a funcionalidade de item novo, crie uma função **newItem**.
No arquivo home.js, cole o seguinte código após a última função e, em seguida, salve o arquivo.

![A função showresults está realçada](./media/storage-nodejs-use-table-storage-cloud-service-app/node43.png)

       newItem: function (req, res) {
           var self = this;
           var createItem = function (resp, tasklist) {
               if (!tasklist) {
                   tasklist = [];
               }

               var count = tasklist.length;

               var item = req.body.item;
               item.RowKey = uuid();
               item.PartitionKey = 'partition1';
               item.completed = false;

               self.client.insertEntity('tasks', item, function (error) {
                   if(error){  
                       throw error;
                   }
                   self.showItems(req, res);
               });
           };

           this.getItems(true, createItem);
       },

A função **newItem** executa as seguintes tarefas:

-   Extrai o item publicado do corpo.
-   Define os valores de **RowKey** e **PartitionKey** para o novo item.
    Esses valores são necessários para inserir o item na tabela do Azure. Um UUID é gerado para o valor de **RowKey**.
-   Insere o item na tabela de tarefas chamando a função **insertEntity**.
-   Renderiza a página chamando a função **getItems**.

### Adicionando o formulário de item novo à exibição home

Agora, atualize o modo de exibição adicionando um novo formulário para permitir que o usuário adicione um item. No arquivo home.jade, cole o seguinte código no final do arquivo e salve-o.

<div class="dev-callout">
<strong>Observação</strong>
<p>No Jade, os espaços em branco são significativos; por isso, não remova nenhum dos espaços abaixo.</p>
</div>

        hr
        form(action="/home/newitem", method="post")
            table(border="1")    
                tr
                    td Item Name: 
                    td 
                        input(name="item[name]", type="textbox")
                tr
                    td Item Category: 
                    td 
                        input(name="item[category]", type="textbox")
                tr
                    td Item Date: 
                    td 
                        input(name="item[date]", type="textbox")
            input(type="submit", value="Add item")

### Executando o aplicativo no emulador

1.  Como o emulador do Azure já está em execução, você pode procurar pelo aplicativo atualizado:

        PS C:\node\tasklist\WebRole1> start http://localhost:81/home

	O navegador é aberto e exibe a página a seguir:

	![Uma página da Web chamada My Task List com uma tabela que contém tarefas e campos para adicionar uma nova tarefa.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

2.  Para **Item Name:**, insira "New task functionality", para **Item Category:**, insira "Site work"? e para **Item Date:**, insira "12/02/2011". Em seguida, clique em **Add item**.

	O item será adicionado à sua tabela de tarefas no Armazenamento do Azure e exibido conforme mostrado na captura de tela a seguir.

	![Uma página da Web chamada My Task List com uma tabela que contém tarefas, depois de você ter adicionado uma tarefa à lista.](./media/storage-nodejs-use-table-storage-cloud-service-app/node45.png)

## Republicando o aplicativo no Azure

Agora que o aplicativo foi concluído, publique-o no Azure atualizando a implantação para o serviço hospedado existente.

1.  Na janela do Windows PowerShell, chame o seguinte cmdlet para reimplantar o serviço hospedado no Azure. As configurações e o local de armazenamento foram salvas anteriormente e não precisam ser inseridas novamente.

        PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

	Após a conclusão da implantação, você deverá ver uma resposta semelhante ao seguinte:

	![As mensagens de status exibidas durante a implantação.](./media/storage-nodejs-use-table-storage-cloud-service-app/node35.png)

	Da mesma maneira que antes, como você especificou a opção **-launch**, o navegador será aberto e exibirá o aplicativo em execução no Azure quando a publicação for concluída.

	![Uma janela do navegador exibindo a página My Task List. A URL indica que a página agora está sendo hospedada no Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/node47.png)

## Parando e excluindo o aplicativo

Depois de implantar o aplicativo, você talvez queira desativá-lo, de forma que seja possível evitar os custos ou compilar e implantar outros aplicativos no período de avaliação gratuita.

O Azure cobra as instâncias de função web por hora de acordo com o tempo consumido do servidor.
O tempo do servidor é consumido assim que o aplicativo é implantado, mesmo se as instâncias não estiverem sendo executadas e estiverem no estado parado.

As etapas a seguir mostram como parar e excluir o aplicativo.

1.  Na janela do Windows PowerShell, pare a implantação do serviço criada na seção anterior com o seguinte cmdlet:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

	Interromper o serviço pode levar alguns minutos. Quando o serviço for interrompido, você recebe uma mensagem indicando que foi interrompido.

	![Mensagens de status indicando que o serviço foi parado.](./media/storage-nodejs-use-table-storage-cloud-service-app/node48.png)

3.  Para excluir o serviço, chame o seguinte cmdlet:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

	Quando solicitado, insira **Y** para excluir o serviço.

	Excluir o serviço pode levar alguns minutos. Após o serviço ter sido excluído, você recebe uma mensagem indicando que o serviço foi excluído.

	![Mensagens de status indicando que o serviço foi excluído.](./media/storage-nodejs-use-table-storage-cloud-service-app/node49.png)

  [Aplicativo Web do Node.js usando o Express]: http://www.windowsazure.com/pt-br/develop/nodejs/tutorials/web-app-with-express/
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Aplicativo Web do Node.js]: http://www.windowsazure.com/pt-br/develop/nodejs/tutorials/getting-started/
 

