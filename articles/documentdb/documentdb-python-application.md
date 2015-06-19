<properties 
    pageTitle="Compilar um aplicativo Web com Python e Flask usando o Banco de Dados de Documentos | Azure" 
    description="Saiba como usar o Banco de Dados de Documentos para armazenar e acessar dados de um aplicativo Web Python e Flask (MVC) hospedado no Azure." 
    services="documentdb" 
    documentationCenter="python" 
    authors="crwilcox" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="03/23/2015" 
    ms.author="crwilcox"/>

# Criar um aplicativo Web com Python e Flask (MVC) usando o Banco de Dados de Documentos
------------------------------------------------------------------------

Para destacar como os clientes podem aproveitar com eficiência o Banco de Dados de Documentos do Azure para armazenar e consultar documentos JSON, este documento fornece um tutorial completo sobre como compilar um aplicativo Web de votação usando o Banco de Dados de Documentos do Azure.

Este tutorial mostra como usar o serviço do Banco de Dados de Documentos fornecido pelo Azure para armazenar e acessar dados de um aplicativo Web Python hospedado no Azure e supõe que você tenha experiência prévia com o uso do Python e os sites do Azure.

Este tutorial abrange:

1. Criar e provisionar uma conta de Banco de Dados de Documentos
2. Criar um aplicativo Python MVC
3. Conectar-se a e usar o Banco de Dados de Documento do Azure do aplicativo Web.
4. Implantar o aplicativo Web nos sites do Azure.

Seguindo este tutorial, você criará um aplicativo simples de votação que permite que você vote em uma votação.

![Screen shot of the todo list web application created by this tutorial](./media/documentdb-python-application/image1.png)


## Pré-requisitos

Antes de seguir as instruções deste artigo, verifique se você tem os seguintes itens instalados:

- [Visual Studio 2013](http://www.visualstudio.com/) ou superior, ou [Visual Studio Express], que é a versão gratuita.
- Ferramentas Python para Visual Studio [aqui][]
- SDK do Azure para o Visual Studio 2013, versão 2.4 ou superior disponível [aqui][1].
- Python 2.7 [aqui][2].
- Compilador do Microsoft Visual C++ para Python 2.7 [aqui][3].

## Etapa 1: Criar uma conta de banco de dados do Banco de Dados de Documentos

Vamos começar criando uma conta do Banco de Dados de Documentos. Se já tiver uma conta, você pode passar para a [Etapa 2: Criar um novo aplicativo Web Python Flask](#Step-2:-Create-a-new-Python-Flask-Web-Application).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

<br/>
Agora vamos abordar como criar um novo aplicativo Web Python Flask desde o início.

## Etapa 2: Criar um novo aplicativo Web Python Flask

1. Abra o Visual Studio, clique em **aArquivo** -\ > **Novo Projeto** -\ > **Python** -\ >, **Projeto Web Flask** e crie um novo projeto com o nome **tutorial**. 

	Para aqueles que não estão familiarizados com o Flask, ele é uma estrutura da Web que nos ajuda a compilar aplicativos Web no Python mais rapidamente. [Clique aqui para acessar os tutoriais do Flask][].

	![Screen shot of the New Project window in Vidual Studio with Python highlighted on the left, Flask Web Project selected in the middle, and the name tutorial in the Name box](./media/documentdb-python-application/image9.png)

2. Será perguntado se você deseja instalar pacotes externos. Clique em **Instalar em um ambiente virtual**. Certifique-se de usar o Python 2.7 como o ambiente de base, pois o PyDocumentDB não dá suporte ao Python 3.x.  Isso configurará o ambiente virtual necessário do Python para seu projeto.

	![Screen shot of the tutorial - Python Tools for Visual Studio window](./media/documentdb-python-application/image10.png)


## Etapa 3: Modificar o aplicativo Web Python Flask

### Adicione pacotes Flask ao seu projeto

Depois que seu projeto estiver configurado, você precisará adicionar alguns pacotes Flask de que precisaremos para nosso projeto, incluindo pydocumentdb, o pacote do Python para o Banco de Dados de Documentos. 

1. Abra o arquivo chamado **requirements.txt** e substitua o conteúdo pelo seguinte:

    	flask==0.9
    	flask-mail==0.7.6
    	sqlalchemy==0.7.9
    	flask-sqlalchemy==0.16
    	sqlalchemy-migrate==0.7.2
    	flask-whooshalchemy==0.55a
    	flask-wtf==0.8.4
    	pytz==2013b
    	flask-babel==0.8
    	flup
    	pydocumentdb>=0.9.4-preview

2. Clique com o botão direito em **env** e clique em **instalar de requirements.txt**.

	![Screen shot showing env (Python 2.7) selected with Install from requirements.txt highlighted in the list](./media/documentdb-python-application/image11.png)

> [AZURE.NOTE] Em casos raros, você pode ver uma falha na janela de saída. Se isso ocorrer, verifique se o erro está relacionado à limpeza. Algumas vezes, há falha na limpeza, mas a instalação ainda é bem-sucedida (role para cima na janela de saída para verificar isso). Se isso ocorrer, não há problema em continuar.

### <a name="verify-the-virtual-environment"></a>Verifique o ambiente virtual

Vamos garantir que tudo seja instalado corretamente. 

- Inicie o site clicando em F**F5**. Isso iniciará o servidor de desenvolvimento do Flask e iniciará o navegador da Web. Você deve ver a página a seguir.

	![The empty flask project displayed in a browser](./media/documentdb-python-application/image12.png)

### Criar banco de dados, coleção e definições de documentos

Agora vamos criar nosso aplicativo de votação.

- Adicione um arquivo Python clicando com o botão direto na pasta chamada **tutorial** no Gerenciador de Soluções.  Nomeie o arquivo **forms.py**.  

    	from flask.ext.wtf import Form
    	from wtforms import RadioField

    	class VoteForm(Form):
        	deploy_preference  = RadioField('Deployment Preference', choices=[
            	('Web Site', 'Web Site'),
            	('Cloud Service', 'Cloud Service'),
            	('Virtual Machine', 'Virtual Machine')], default='Web Site')

### Adicione as importações necessárias a views.py

- Adicione as seguintes instruções de importação na parte superior em **views.py**. Elas importarão pacotes PythonSDK e Flask do Banco de Dados de Documentos.

    	from forms import VoteForm
    	import config
    	import pydocumentdb.document_client as document_client
    

### Criar banco de dados, coleção e documento

- Adicione o seguinte código a **views.py**. Isso se encarrega de criar o  banco de dados usado pelo formulário. Não exclua nenhum código existente em  **views.py**. Basta acrescentá-lo ao final.

    	@app.route('/create')
    	def create():
        	"""Renders the contact page."""
        	client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
    
        	# Attempt to delete the database.  This allows this to be used to recreate as well as create
        	try:
            	db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            	client.DeleteDatabase(db['_self'])
        	except:
            	pass
    
       		# Create database
        	db = client.CreateDatabase({ 'id': config.DOCUMENTDB_DATABASE })
        	# Create collection
        	collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION })
        	# Create document
        	document = client.CreateDocument(collection['_self'],
            	{ 'id': config.DOCUMENTDB_DOCUMENT,
            	'Web Site': 0,
            	'Cloud Service': 0,
            	'Virtual Machine': 0,
            	'name': config.DOCUMENTDB_DOCUMENT })
    
        	return render_template(
            	'create.html', 
            	title='Create Page', 
            	year=datetime.now().year,
            	message='You just created a new database, collection, and document.  Your old votes have been deleted')

### Ler o Banco de Dados, a Coleção e o Documento e enviar o formulário

- Adicione o seguinte código a **views.py**. Ele cuida da configuração do formulário, da leitura do banco de dados, da coleção e do documento. Não exclua nenhum código existente em **views.py**. Basta acrescentá-lo ao final.
    
    	@app.route('/vote', methods=['GET', 'POST'])
    	def vote(): 
        	form = VoteForm()
        	replaced_document ={}
        	if form.validate_on_submit(): # is user submitted vote  
            	client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
            
            	# Read databases and take first since id should not be duplicated.
            	db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            
            	# Read collections and take first since id should not be duplicated.
            	coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.DOCUMENTDB_COLLECTION))
    
            	# Read documents and take first since id should not be duplicated.
            	doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.DOCUMENTDB_DOCUMENT))
    
            	# Take the data from the deploy_preference and increment our database
            	doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
            	replaced_document = client.ReplaceDocument(doc['_self'], doc)
    
            	# Create a model to pass to results.html
            	class VoteObject:
                	choices = dict()
                	total_votes = 0
    
            	vote_object = VoteObject()
            	vote_object.choices = {
                	"Web Site" : doc['Web Site'],
                	"Cloud Service" : doc['Cloud Service'],
                	"Virtual Machine" : doc['Virtual Machine']
            	}
            	vote_object.total_votes = sum(vote_object.choices.values())
    
            	return render_template(
                	'results.html', 
                	year=datetime.now().year, 
                	vote_object = vote_object)
    
        	else :
            	return render_template(
                	'vote.html', 
                	title = 'Vote',
                	year=datetime.now().year,
                	form = form)


### Crie os arquivos html

Na pasta de modelos, adicione os seguintes arquivos html: create.html, results.html, vote.html.

1. Adicione o código a seguir a **create.html**. Ele cuida da exibição de uma mensagem indicando que um novo banco de dados, coleção e documento foram criados.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>{{ title }}.</h2>
    	<h3>{{ message }}</h3>
    	<p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    	{% endblock %}

2. Adicione o código a seguir a **results.html**. Ele cuida da exibição dos resultados da votação.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>Results of the vote</h2>
   	 	<br />
	
    	{% for choice in vote_object.choices %}
    	<div class="row">
        	<div class="col-sm-5">{{choice}}</div>
        	<div class="col-sm-5">
            	<div class="progress">
                	<div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" 
                     aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                    	{{vote_object.choices[choice]}}
                	</div>
            	</div>
        	</div>
    	</div>
    	{% endfor %}

    	<br />
    	<a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    	{% endblock %}

3. Adicione o código a seguir a **vote.html**. Ele cuida da exibição da votação e da aceitação dos votos. Ao registrar os votos, o controle é passado a views.py, onde reconheceremos os votos realizados e atualizaremos o documento de acordo com eles.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>What is your favorite way to host an application on Azure?</h2>
    	<form action="" method="post" name="vote">
        	{{form.hidden_tag()}}
        	{{form.deploy_preference}}
        	<button class="btn btn-primary" type="submit">Vote</button>
    	</form>
    	{% endblock %}

4. Substitua o conteúdo de **index.html** pelo seguinte. Ele serve como a página de chegada de seu aplicativo.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>Python + DocumentDB Voting Application.</h2>
    	<h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    	<p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    	<p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    	{% endblock %}


### Adicione um arquivo de configuração e altere __init__.py

1. Clique com o botão direito no tutorial com o nome do projeto e adicione um arquivo, **config.py**.
Essa configuração é exigida por formulários no flask. Você também pode usá-lo para fornecer uma chave secreta. Isso não é necessário para este tutorial. 

2. Adicione o código a seguir a config.py. Altere os valores de **DOCUMENTDB_HOST** e **DOCUMENTDB_KEY**.

    	CSRF_ENABLED = True
    	SECRET_KEY = 'you-will-never-guess'
    
    	DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    	DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
    
    	DOCUMENTDB_DATABASE = 'voting database'
    	DOCUMENTDB_COLLECTION = 'voting collection'
    	DOCUMENTDB_DOCUMENT = 'voting document'

3. Da mesma forma, substitua o conteúdo de **__init__.py** pelo seguinte.

    	from flask import Flask
    	app = Flask(__name__)
    	app.config.from_object('config')
    	import tutorial.views

4. Após seguir as etapas mencionadas acima, isso é deve ser a aparência de Gerenciador de soluções.

	![Screen shot of the Visual Studio Solution Explorer window](./media/documentdb-python-application/image15.png)


## Etapa 4: Execute seu aplicativo localmente

1. Selecione F5 ou o botão de seleção no Visual Studio e você deve ver a seguinte tela.

	![Screen shot of the Python + DocumentDB Voting Application displayed in a web browser](./media/documentdb-python-application/image16.png)

2. Clique em **Criar/limpar o banco de dados de votação** para gerar o banco de dados.

	![Screen shot of the Create Page of the web application](./media/documentdb-python-application/image17.png)

3. Em seguida, clique em **Votar** e selecione sua opção

	![Screen shot of the web application with a voting question posed](./media/documentdb-python-application/image18.png)

4. Para cada voto enviado, o contador adequado será incrementado.

	![Screen shot of the Results of the vote page shown](./media/documentdb-python-application/image19.png)


## Etapa 5: Implantar o aplicativo nos Sites do Azure

Agora que você possui o aplicativo completo funcionando corretamente no Banco de Dados de Documentos, vamos implantá-lo nos sites do Azure. 

1. Clique com o botão direito no Projeto no Gerenciador de Soluções (verifique se não o está executando localmente) e selecione Publicar.  Em seguida, selecione os Sites do Microsoft Azure.

 	![Screen shot of the tutorial selected in Solution Explorer, with the Publish option highlighted](./media/documentdb-python-application/image20.png)

2. Configure seu Site do Azure fornecendo suas credenciais e clique em **Publicar**.

	![Screen shot of the Publish Web window](./media/documentdb-python-application/image21.png)

3. Em poucos segundos, o Visual Studio terminará de publicar seu aplicativo Web e iniciará um navegador onde você poderá ver seu trabalho sendo executado no Azure!

## Próximas etapas

Parabéns! Você acaba de construir seu primeiro aplicativo Python usando o Banco de Dados de Documentos do Azure e publicá-lo nos Sites do Azure.

Para adicionar funcionalidade adicional para seu aplicativo, revise as APIs disponíveis no [DocumentDB Python SDK](https://pypi.python.org/pypi/pydocumentdb).

  [Clique aqui para acessar os tutoriais do Flask]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world
  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [aqui]: http://aka.ms/ptvs
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [2]: https://www.python.org/downloads/windows/
  [3]: http://aka.ms/vcpython27 
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [Portal de Gerenciamento do Azure]: http://portal.azure.com

<!--HONumber=49--> 