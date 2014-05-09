<properties linkid="develop-python-web-app-with-blob-storage" urlDisplayName="Aplicativo Web com armazenamento de blobs" pageTitle="Aplicativo Web Python com armazenamento de tabela | Microsoft Azure" metaKeywords="Armazenamento de tabela do Azure com Python, aplicativo Python para Azure, tutorial do Python para Azure, exemplo de Python no Azure" description="Um tutorial que ensina a criar um aplicativo Web Python usando as bibliotecas de cliente do Azure. O Django é usado como a estrutura da Web." metaCanonical="" services="storage" documentationCenter="Python" title="Aplicativo Web Python com armazenamento de tabela" authors="" solutions="" videoId="" scriptId="" manager="" editor="mollybos" />





# Aplicativo Web Python com armazenamento de tabela

Neste tutorial, você aprenderá como criar um aplicativo que usa o armazenamento de tabela com as bibliotecas de cliente do Azure para Python. Se este for o seu primeiro aplicativo Python do Azure, talvez você queira dar uma olhada em [Aplicativo Web Django Hello World][] primeiro.

Neste guia, você criará um aplicativo de lista de tarefas com base na Web que pode ser implantado no Azure. A lista de tarefas permite que um usuário recupere tarefas, adicione novas tarefas e marque tarefas como concluídas.  Nós usaremos o Django como a estrutura da Web.

Os itens da tarefa são armazenados no Armazenamento do Azure. O Armazenamento do Azure fornece um armazenamento de dados não estruturado altamente disponível e tolerante a falhas. O Armazenamento do Azure inclui diversas estruturas de dados, onde você pode armazenar e acessar dados, além de poder utilizar os serviços de armazenamento das APIs incluídos no SDK do Azure para Python ou por meio de APIs REST. Para obter mais informações, consulte [Armazenando e acessando dados no Azure].

Você aprenderá:

-   Como trabalhar com o serviço Armazenamento de tabela do Azure

Uma captura de tela do aplicativo concluído será semelhante ao mostrado a seguir (os itens de tarefas adicionadas serão diferentes):

![](./media/storage-python-use-table-storage-web-app/web-app-with-storage-Finaloutput-mac.png)

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <a id="setup"> </a>Configurar o ambiente de desenvolvimento

**Observação:** se você precisar instalar o Python ou as bibliotecas de cliente, consulte o [Guia de instalação do Python (a página pode estar em inglês)](http://windowsazure.com/pt-br/documentation/articles/python-how-to-install).



*Observação para Windows*: se usou o instalador WebPI do Windows, você já tem o Django e as bibliotecas de cliente instalados.

## Criar uma conta de armazenamento no Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## Criar um projeto do Django

Estas são as etapas para criar o aplicativo:

-   Crie um projeto do Django padrão chamado 'TableserviceSample'. 
- 	Na linha de comando, utilize cd em um diretório em que você deseja armazenar o código e, em seguida, execute o comando a seguir: 

		django-admin.py startproject TableserviceSample

-   Adicione um novo arquivo do Python **views.py** ao projeto.
-   Adicionar o código a seguir a **views.py** para importar o suporte ao Django necessário:
           
        from django.http import HttpResponse
        from django.template.loader import render_to_string
        from django.template import Context

-   Crie uma nova pasta chamada **templates** sob a pasta **TableserviceSample/TableserviceSample**.
-   Edite as configurações do aplicativo, de forma que os modelos possam ser localizados. Abra **settings.py** e adicione a seguinte entrada em INSTALLED_APPS:

        'TableserviceSample',

-   Adicione um novo arquivo de modelo do Django **mytasks.html** na pasta **templates** e adicione o seguinte código a ele:
 
<pre>
	&lt;html&gt;
	&lt;head&gt;&lt;title&gt;&lt;/title&gt;&lt;/head&gt;
	&lt;body&gt;
	&lt;h2&gt;My Tasks&lt;/h2&gt; &lt;br&gt;
	&lt;table border="1"&gt; 
	&lt;tr&gt;
	&lt;td&gt;Name&lt;/td&gt;&lt;td&gt;Category&lt;/td&gt;&lt;td&gt;Date&lt;/td&gt;&lt;td&gt;Complete&lt;/td&gt;&lt;td&gt;Action&lt;/td&gt;&lt;/tr&gt;
	{% for entity in entities %}
	&lt;form action=&quot;update_task&quot; method=&quot;GET&quot;&gt;
	&lt;tr&gt;&lt;td&gt;{{entity.name}} &lt;input type=&quot;hidden&quot; name='name' value=&quot;{{entity.name}}&quot;&gt;&lt;/td&gt;
	&lt;td&gt;{{entity.category}} &lt;input type=&quot;hidden&quot; name='category' value=&quot;{{entity.category}}&quot;&gt;&lt;/td&gt;
	&lt;td&gt;{{entity.date}} &lt;input type=&quot;hidden&quot; name='date' value=&quot;{{entity.date}}&quot;&gt;&lt;/td&gt;
	&lt;td&gt;{{entity.complete}} &lt;input type=&quot;hidden&quot; name='complete' value=&quot;{{entity.complete}}&quot;&gt;&lt;/td&gt;

	&lt;td&gt;&lt;input type=&quot;submit&quot; value=&quot;Complete&quot;&gt;&lt;/td&gt;
	&lt;/tr&gt;
	&lt;/form&gt;
	{% endfor %}
	&lt;/table&gt;
	&lt;br&gt;
	&lt;hr&gt;
	&lt;table border=&quot;1&quot;&gt;
	&lt;form action=&quot;add_task&quot; method=&quot;GET&quot;&gt;
	&lt;tr&gt;&lt;td&gt;Name:&lt;/td&gt;&lt;td&gt;&lt;input type=&quot;text&quot; name=&quot;name&quot;&gt;&lt;/input&gt;&lt;/td&gt;&lt;/tr&gt;
	&lt;tr&gt;&lt;td&gt;Category:&lt;/td&gt;&lt;td&gt;&lt;input type=&quot;text&quot; name=&quot;category&quot;&gt;&lt;/input&gt;&lt;/td&gt;&lt;/tr&gt;
	&lt;tr&gt;&lt;td&gt;Item Date:&lt;/td&gt;&lt;td&gt;&lt;input type=&quot;text&quot; name=&quot;date&quot;&gt;&lt;/input&gt;&lt;/td&gt;&lt;/tr&gt;
	&lt;tr&gt;&lt;td&gt;&lt;input type=&quot;submit&quot; value=&quot;add task&quot;&gt;&lt;/input&gt;&lt;/td&gt;&lt;/tr&gt;
	&lt;/form&gt;
	&lt;/table&gt;
	&lt;/body&gt;
	&lt;/html&gt;    

</pre> 

    
## Importar o módulo de armazenamento de windowsazure
Adicione o código a seguir na parte superior de **views.py** logo após as importações do Django:

	from azure.storage import TableService

## Obter o nome da conta de armazenamento e da chave da conta
Adicione o código a seguir a **views.py** logo após a importação de windowsazure e substitua 'youraccount' e 'yourkey' pelo nome e pela chave da conta real. Você pode obter um nome e uma chave de conta no Portal de Gerenciamento do Azure. 

	account_name = 'youraccount'
	account_key = 'yourkey'

## Criar TableService
Adicione o código a seguir depois de **account_name**:

	table_service = TableService(account_name=account_name, account_key=account_key)
	table_service.create_table('mytasks')

## Listar tarefas Adicione a função list_tasks a **views.py**:

	def list_tasks(request): 
		entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
		html = render_to_string('mytasks.html', Context({'entities':entities}))
		return HttpResponse(html)

##  Adicionar tarefa
Adicione a função add_task a **views.py**:

	def add_task(request):
		name = request.GET['name']
		category = request.GET['category']
		date = request.GET['date']
		table_service.insert_entity('mytasks', {'PartitionKey':name+category, 'RowKey':date, 'name':name, 'category':category, 'date':date, 'complete':'No'}) 
		entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')
		html = render_to_string('mytasks.html', Context({'entities':entities}))
		return HttpResponse(html)

## Atualizar status da tarefa
Adicione a função update_task a **views.py**:

	def update_task(request):
		name = request.GET['name']
		category = request.GET['category']
		date = request.GET['date']
		partition_key = name + category
		row_key = date
		table_service.update_entity('mytasks', partition_key, row_key, {'PartitionKey':partition_key, 'RowKey':row_key, 'name': name, 'category':category, 'date':date, 'complete':'Yes'})
		entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
		html = render_to_string('mytasks.html', Context({'entities':entities}))
		return HttpResponse(html)


## Mapear URLs
Agora, você deve mapear as URLs no aplicativo Django. Abra **urls.py** e adicione os seguintes mapeamentos a urlpatterns:

	url(r'^$', 'TableserviceSample.views.list_tasks'),
	url(r'^list_tasks$', 'TableserviceSample.views.list_tasks'),
	url(r'^add_task$', 'TableserviceSample.views.add_task'),
	url(r'^update_task$', 'TableserviceSample.views.update_task'),

## Executar o aplicativo


-  Alterne para o diretório **TableserviceSample** se ainda não tiver feito isso e execute o comando:

	python manage.py runserver

-   Aponte o navegador para : `http://127.0.0.1:8000/`. Substitua 8000 pelo número real da porta.

Agora, você pode clicar em **Adicionar Tarefa** para criar uma tarefa e, em seguida, no botão **Concluir** para atualizar a tarefa e definir seu status como Sim.



## Executar o aplicativo no emulador de computação, publicar e parar/excluir o aplicativo

Agora que executou o aplicativo com êxito no servidor Django interno, você pode testá-lo ainda mais implantando-o no emulador do Azure (somente no Windows) e, em seguida, publicá-lo no Azure.  Para obter instruções gerais sobre como fazer isso, consulte o artigo [Aplicativo Web Django Hello World], que aborda essas etapas em detalhes.


<h2><a id="NextSteps"></a>Próximas etapas</h2>

Agora que você aprendeu os conceitos básicos do serviço de armazenamento Tabela do Azure, siga estes links para saber como fazer tarefas de armazenamento mais complexas.

- Consulte a referência do MSDN: [Armazenando e acessando dados no Azure] []
- Acesse o Blog da Equipe de Armazenamento do Azure: <http://blogs.msdn.com/b/windowsazurestorage/>


[Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx

[Guia de Instalação]: ../python-how-to-install 

[Aplicativo Web Django Hello World]: http://windowsazure.com/pt-br/documentation/articles/virtual-machines-python-django-web-app-windows-server

