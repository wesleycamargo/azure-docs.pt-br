<properties urlDisplayName="Web App with Blob Storage" pageTitle="Aplicativo Web Python com armazenamento de tabela | Microsoft Azure" metaKeywords="Azure table storage Python, Azure Python application, Azure Python tutorial, Azure Python example" description="Um tutorial que ensina a criar um aplicativo Web Python usando as bibliotecas de cliente do Azure. O Django &eacute; usado como a estrutura da Web." metaCanonical="" services="storage" documentationCenter="Python" title="Aplicativo Web Python com armazenamento de tabela" authors="huvalo" solutions="" videoId="" scriptId="" manager="wpickett" editor="mollybos" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Aplicativo Web Python com armazenamento de tabela

Neste tutorial, você aprenderá como criar um aplicativo que usa o armazenamento de tabela com as bibliotecas de cliente do Azure para Python. Se este for o seu primeiro aplicativo Python do Azure, talvez você queira dar uma olhada em [Aplicativo Web Django Hello World][Aplicativo Web Django Hello World] primeiro.

Neste guia, você criará um aplicativo de lista de tarefas com base na Web que pode ser implantado no Azure. A lista de tarefas permite que um usuário recupere tarefas, adicione novas tarefas e marque tarefas como concluídas. Nós usaremos o Django como a estrutura da Web.

Os itens da tarefa são armazenados no Armazenamento do Azure. O Armazenamento do Azure fornece um armazenamento de dados não estruturado altamente disponível e tolerante a falhas. O Armazenamento do Azure inclui diversas estruturas de dados, onde você pode armazenar e acessar dados, além de poder utilizar os serviços de armazenamento das APIs incluídos no SDK do Azure para Python ou
por meio de APIs REST. Para obter mais informações, consulte [Armazenando e acessando dados no Azure (a página pode estar em inglês)][Armazenando e acessando dados no Azure (a página pode estar em inglês)].

Você aprenderá a:

-   Como trabalhar com o serviço de armazenamento de tabela do Azure

Uma captura de tela do aplicativo concluído será semelhante ao mostrado a seguir (os itens de tarefas adicionadas serão diferentes):

![][0]

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <span id="setup"></span> </a>Configurar o ambiente de desenvolvimento

**Observação:** se você precisar instalar o Python ou as bibliotecas de cliente, consulte o [Guia de instalação do Python (a página pode estar em inglês)][Guia de instalação do Python (a página pode estar em inglês)].

*Observação para Windows*: se usou o instalador WebPI do Windows, você já tem o Django e as bibliotecas de cliente instalados.

## Criar uma conta de armazenamento no Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## Criar um projeto do Django

Estas são as etapas para criar o aplicativo:

-   Crie um projeto Django padrão denominado 'TableserviceSample'
-   Na linha de comando, utilize cd em um diretório em que você deseja armazenar o código e, em seguida, execute o comando a seguir:

        django-admin.py startproject TableserviceSample

-   Adicione um novo arquivo do Python **views.py** ao projeto
-   Adicione o código a seguir a **views.py** para importar o suporte necessário ao Django:

        from django.http import HttpResponse
        from django.template.loader import render_to_string
        from django.template import Context

-   Crie uma nova pasta chamada **modelos** na pasta **TableserviceSample/TableserviceSample**.
-   Edite as configurações do aplicativo, de forma que os modelos possam ser localizados. Abra **settings.py** e adicione a seguinte entrada em INSTALLED\_APPS:

        'TableserviceSample',

-   Adicione um novo arquivo de modelo do Django **mytasks.html** na pasta **modelos** e adicione o seguinte código a ele:

<!-- -->

        <html>
        <head><title></title></head>
        <body>
        <h2>My Tasks</h2> <br>
        <table border="1"> 
        <tr>
        <td>Name</td><td>Category</td><td>Date</td><td>Complete</td><td>Action</td></tr>
        {% for entity in entities %}
        <form action="update_task" method="GET">
        <tr><td>{{entity.name}} <input type="hidden" name='name' value="{{entity.name}}"></td>
        <td>{{entity.category}} <input type="hidden" name='category' value="{{entity.category}}"></td>
        <td>{{entity.date}} <input type="hidden" name='date' value="{{entity.date}}"></td>
        <td>{{entity.complete}} <input type="hidden" name='complete' value="{{entity.complete}}"></td>

        <td><input type="submit" value="Complete"></td>
        </tr>
        </form>
        {% endfor %}
        </table>
        <br>
        <hr>
        <table border="1">
        <form action="add_task" method="GET">
        <tr><td>Name:</td><td><input type="text" name="name"></input></td></tr>
        <tr><td>Category:</td><td><input type="text" name="category"></input></td></tr>
        <tr><td>Item Date:</td><td><input type="text" name="date"></input></td></tr>
        <tr><td><input type="submit" value="add task"></input></td></tr>
        </form>
        </table>
        </body>
        </html>    

## Importar o módulo de armazenamento do windowsazure

Adicione o seguinte código na parte superior do **views.py** após as importações do Django

    from azure.storage import TableService

## Obtenha nome da conta de armazenamento e a chave da conta

Adicione o código a seguir a **views.py** logo após a importação de windowsazure e substitua 'youraccount' e 'yourkey' pelo nome e pela chave da conta real. Você pode obter um nome e uma chave de conta no Portal de Gerenciamento do Azure.

    account_name = 'youraccount'
    account_key = 'yourkey'

## Criar TableService

Adicione o seguinte código após **account\_name**:

    table_service = TableService(account_name=account_name, account_key=account_key)
    table_service.create_table('mytasks')

## Listar tarefas

Adicione a função list\_tasks a **views.py**:

    def list_tasks(request): 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

## Adicionar tarefa

Adicione a função add\_task a **views.py**:

    def add_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        table_service.insert_entity('mytasks', {'PartitionKey':name+category, 'RowKey':date, 'name':name, 'category':category, 'date':date, 'complete':'No'}) 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

## Atualizar o status da tarefa

Adicione a função update\_task a **views.py**:

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

## Mapeando URLs

Agora você deve mapear as URLs no aplicativo Django. Abra **urls.py** e adicione os seguintes mapeamentos a urlpatterns:

    url(r'^$', 'TableserviceSample.views.list_tasks'),
    url(r'^list_tasks$', 'TableserviceSample.views.list_tasks'),
    url(r'^add_task$', 'TableserviceSample.views.add_task'),
    url(r'^update_task$', 'TableserviceSample.views.update_task'),

## Executar o aplicativo

-   Alterne para o diretório **TableserviceSample** se ainda não tiver feito isso e execute o comando:

    python manage.py runserver

-   Aponte seu navegador para: `http://127.0.0.1:8000/`. Substitua 8000 pelo número real da porta.

Agora, você pode clicar em **Adicionar Tarefa** para criar uma tarefa e, em seguida, no botão **Concluir** para atualizar a tarefa e definir seu status como Sim.

## Executando o aplicativo no emulador de computação, publicando e parando/excluindo o aplicativo

Agora que executou o aplicativo com êxito no servidor Django interno, você pode testá-lo ainda mais implantando-o no emulador do Azure (somente no Windows) e, em seguida, publicá-lo no Azure. Para obter instruções gerais sobre como fazer isso, consulte o artigo [Aplicativo Web Django Hello World][Aplicativo Web Django Hello World], que aborda essas etapas em detalhes.

## <span id="NextSteps"></span></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do serviço de armazenamento Tabela do Azure, siga estes links para saber como fazer tarefas de armazenamento mais complexas.

-   Consulte a referência de MSDN: [Armazenando e acessando dados no Azure][Armazenando e acessando dados no Azure (a página pode estar em inglês)]
-   Visite o Blog da Equipe de Armazenamento do Azure: <http://blogs.msdn.com/b/windowsazurestorage/>

  [Aplicativo Web Django Hello World]: http://windowsazure.com/pt-br/documentation/articles/virtual-machines-python-django-web-app-windows-server
  [Armazenando e acessando dados no Azure (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [0]: ./media/storage-python-use-table-storage-web-app/web-app-with-storage-Finaloutput-mac.png
  [Guia de instalação do Python (a página pode estar em inglês)]: http://windowsazure.com/pt-br/documentation/articles/python-how-to-install
