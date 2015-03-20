<properties 
    pageTitle="Compilar um aplicativo Web com Python e Flask usando o Banco de Dados de Documentos | Azure" 
    description="Saiba como usar o Banco de Dados de Documentos para armazenar e acessar dados de um aplicativo Web Python e Flask (MVC) hospedado no Azure." 
    services="documentdb" 
    documentationCenter="" 
    authors="crwilcox" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2015" 
    ms.author="crwilcox"/>

# Criar um aplicativo Web com Python e Flask (MVC) usando o Banco de Dados de Documentos
------------------------------------------------------------------------

Para destacar como os clientes podem aproveitar com eficiência o Banco de Dados de Documentos do Azure para armazenar e consultar documentos JSON, este documento fornece um passo a passo completo sobre como criar um aplicativo Web de votação usando o Banco de Dados de Documentos do Azure.

Este passo a passo mostra como usar o serviço do Banco de Dados de Documentos fornecido pelo Azure para armazenar e acessar dados de um aplicativo Web Python hospedado no Azure e supõe que você tenha experiência prévia com o uso do Python e os sites do Azure.

Este passo a passo aborda:

1. Criar e provisionar uma Conta de Banco de Dados de Documentos

2. Criar um aplicativo Python MVC

3. Conectar-se a e usar o Banco de Dados de Documento do Azure a partir do aplicativo Web

4. Implantar o aplicativo Web nos sites do Azure

Seguindo este passo a passo, você compilará um simples aplicativo
de votação que permite votar em uma pesquisa.

![Alt text](./media/documentdb-python-application/image1.png)


## Pré-requisitos

Antes de seguir as instruções deste artigo, verifique se você tem os seguintes itens instalados:

- Visual Studio 2013 (ou [Visual Studio Express][], que é uma versão gratuita)

- Ferramentas Python para Visual Studio [aqui][]

- SDK do Azure para Visual Studio 2013, versão 2.4 ou superior disponível [aqui][1]

- Python 2.7 [aqui][2]

- Compilador do Microsoft Visual C++ para Python 2.7 [aqui][3]

## Etapa 1: Criar uma conta de banco de dados do Banco de Dados de Documentos

Para provisionar uma conta de Banco de Dados de Documentos no Azure, abra o [Portal de Gerenciamento do Azure][] e clique no bloco da Galeria do Azure na home page ou clique em "+" no canto inferior esquerdo da tela.

![Alt text](./media/documentdb-python-application/image2.png)


Isso abrirá o Azure Marketplace, onde você pode selecionar entre os muitos serviços disponíveis do Azure. No Marketplace, selecione "Dados + análise" na lista de categorias.

![Alt text](./media/documentdb-python-application/image3.png)

Aqui, pesquise e selecione a opção para o Banco de Dados de Documentos

![Alt text](./media/documentdb-python-application/image4.png)

Depois selecione "Criar" na parte inferior da tela

![Alt text](./media/documentdb-python-application/image5.png)

Isso abrirá a folha "Novo Banco de Dados de Documentos", onde você pode especificar o nome, a região, a escala, o grupo de recursos e outras configurações para sua nova conta.

![Alt text](./media/documentdb-python-application/image6.png)

Depois de concluir a inserção de valores na conta, clique em Criar e o processo de provisionamento começará a criar sua conta de banco de dados.
Quando o provisionamento estiver concluído, você verá uma notificação exibida na área de notificações do portal, e o bloco na sua tela inicial (se você tiver selecionado para criar uma tela) mudará para mostrar a ação concluída.

![Alt text](./media/documentdb-python-application/image7.png)

Depois de concluir o provisionamento, clicar no bloco Banco de Dados de Documentos na tela inicial exibirá a folha principal dessa conta de Banco de Dados de Documentos recém-criada.

![Alt text](./media/documentdb-python-application/image8.png)

Usando o botão "Chaves", acesse a URL do ponto de extremidade e a Chave Primária, copie-as para sua área de transferência e mantenha-as por perto, pois usaremos esses valores no aplicativo Web que criaremos em seguida.


## Etapa 2: Criar um novo aplicativo Web Python Flask

Abra o Visual Studio, Arquivo -> Novo Projeto -> Python -> Projeto Web Flask com o nome **tutorial**. 

Para aqueles que não estão familiarizados com o Flask, ele é uma estrutura da Web que nos ajuda a compilar aplicativos Web no Python mais rapidamente. [Clique aqui para acessar os tutoriais do Flask][].

![Alt text](./media/documentdb-python-application/image9.png)

Será perguntado se você deseja instalar pacotes externos. Clique em **Instalar em um ambiente virtual**. Certifique-se de usar o Python 2.7 como o ambiente de base, pois o PyDocumentDB não dá suporte ao Python 3.x.  Isso configurará o ambiente virtual necessário do Python para seu projeto.

![Alt text](./media/documentdb-python-application/image10.png)


## Etapa 3: Modificar o aplicativo Web Python Flask


### Adicione pacotes Flask ao seu projeto

Depois que seu projeto estiver configurado, você precisará adicionar alguns pacotes Flask de que precisaremos para nosso projeto, incluindo pydocumentdb, o pacote do Python para o Banco de Dados de Documentos. Abra o arquivo chamado **requirements.txt** e substitua o conteúdo pelo seguinte:

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

Clique com o botão direito em **env** e clique em **instalar de requirements.txt**

![Alt text](./media/documentdb-python-application/image11.png)

**Observação:** em casos raros, você poderá ver uma falha na janela de saída. Se isso acontecer, verifique se o erro está relacionado à limpeza. Às vezes, haverá falhas na limpeza mas a instalação ainda será bem-sucedida (role para cima na janela de saída para verificar).
<a name="verify-the-virtual-environment"></a> Se isso ocorrer, pode continuar.


### Verifique o ambiente virtual

Vamos garantir que tudo seja instalado corretamente. Inicie o site clicando em F**F5**. Isso iniciará o servidor de desenvolvimento do Flask e iniciará o navegador da Web. Você deve ver a seguinte página:

![Alt text](./media/documentdb-python-application/image12.png)

### Criar banco de dados, coleção e definição do documento

Adicione um arquivo Python clicando com o botão direto na pasta chamada **tutorial** no Gerenciador de Soluções.  Nomeie o arquivo **forms.py**.  Estamos criando nosso aplicativo de sondagem.

    from flask.ext.wtf import Form
    from wtforms import RadioField

    class VoteForm(Form):
        deploy_preference  = RadioField('Deployment Preference', choices=[
            ('Web Site', 'Web Site'),
            ('Cloud Service', 'Cloud Service'),
            ('Virtual Machine', 'Virtual Machine')], default='Web Site')

### Adicione as importações necessárias a views.py

Adicione as seguintes instruções de importação na parte superior em **views.py**. Elas importarão pacotes PythonSDK e Flask do Banco de Dados de Documentos.


    from forms import VoteForm
    import config
    import pydocumentdb.document_client as document_client
    

### Criar banco de dados, coleção e documento

Adicione o seguinte código a **views.py**. Ele se encarrega de criar o banco de dados usado pelo formulário. Não exclua nenhum código existente em **views.py**. Basta acrescentá-lo ao final.

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


### Leia o banco de dados, a coleção e o documento e envie o formulário

Adicione o seguinte código a **views.py**. Ele cuida da configuração do formulário, da leitura do banco de dados, da coleção e do documento. Não exclua nenhum código existente em **views.py**. Basta acrescentá-lo ao final.
    
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

Na pasta de modelos, adicione os seguintes arquivos html: create.html, results.html, vote.html

Adicione o código a seguir a **create.html**. Ele cuida da exibição da mensagem de que um novo banco de dados, coleção e documento foram criados.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}

Adicione o código a seguir a **results.html**. Ele cuida da exibição dos resultados da votação.

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

Adicione o código a seguir a **vote.html**. Ele cuida da exibição da votação e da aceitação dos votos. Ao registrar os votos, o controle é passado a views.py, onde reconheceremos os votos realizados e atualizaremos o documento de acordo com eles.

    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
        {{form.deploy_preference}}
        <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}

Substitua o conteúdo de **index.html** pelo seguinte. Ele serve como a página de chegada de seu aplicativo.

    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + DocumentDB Voting Application.</h2>
    <h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}


### Adicione um arquivo de configuração e altere \_\_init\_\_.py

Clique com o botão direito no tutorial com o nome do projeto e adicione um arquivo - **config.py**.
Essa configuração é exigida por formulários no flask. Você também pode usá-lo para fornecer uma chave secreta. Isso não é necessário para este tutorial. Adicione o código a seguir a config.py.   Altere os valores de **DOCUMENTDB\_HOST** e **DOCUMENTDB\_KEY**.

    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    
    DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
    
    DOCUMENTDB_DATABASE = 'voting database'
    DOCUMENTDB_COLLECTION = 'voting collection'
    DOCUMENTDB_DOCUMENT = 'voting document'


Da mesma forma, substitua o conteúdo de **\_\_init\_\_.py** pelo seguinte.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

Depois de seguir as etapas mencionadas acima, o gerenciador de soluções
deverá ter a seguinte aparência

![Alt text](./media/documentdb-python-application/image15.png)


## Etapa 4: Execute seu aplicativo localmente

Selecione F5 ou o botão de seleção no Visual Studio e você deve ver a seguinte tela.

![Alt text](./media/documentdb-python-application/image16.png)

Clique em "Criar/limpar o banco de dados de votação" para gerar o banco de dados.

![Alt text](./media/documentdb-python-application/image17.png)

Em seguida, clique em "Votar" e selecione sua opção

![Alt text](./media/documentdb-python-application/image18.png)

Para cada voto enviado, o contador adequado será incrementado.

![Alt text](./media/documentdb-python-application/image19.png)


## Etapa 5: Implante o aplicativo nos sites do Azure

Agora que você possui o aplicativo completo funcionando corretamente no Banco de Dados de Documentos, vamos implantá-lo nos sites do Azure. Clique com o botão direito no Projeto no Gerenciador de Soluções (verifique se não o está executando localmente) e selecione Publicar.  Em seguida, selecione os Sites do Microsoft Azure.

![Alt text](./media/documentdb-python-application/image20.png)


Configure seu Site do Azure fornecendo suas credenciais e clique em Publicar.

![Alt text](./media/documentdb-python-application/image21.png)

Em poucos segundos, o Visual Studio terminará de publicar seu aplicativo Web e iniciará um navegador onde você poderá ver seu trabalho sendo executado no Azure!


## Próximas etapas

Parabéns! Você acaba de construir seu primeiro aplicativo Python usando o Banco de Dados de Documentos do Azure e publicá-lo nos Sites do Azure.


  [Clique aqui para acessar os tutoriais do Flask]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [aqui]: http://aka.ms/ptvs
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [2]: https://www.python.org/downloads/windows/
  [3]: http://aka.ms/vcpython27 
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [Portal de Gerenciamento do Azure]: http://portal.azure.com
<!--HONumber=47-->
