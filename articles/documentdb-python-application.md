<properties title="Build a web application with Python and Flask (MVC) using DocumentDB" pageTitle="Compilar um aplicativo Web com Python e Flask usando o Banco de Dados de Documentos | Azure" description="Learn how to use DocumentDB to store and access data from an Python and Flask (MVC) web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />


<tags ms.service="documentdb" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="hawong" />

<a name="_Toc395888515"></a><a name="_Toc395809324">Criar um aplicativo Web com Python e Flask (MVC) usando o Banco de Dados de Documentos</a>
===========================================================================================================================================

<a name="_Toc395809324">

<a name="_Toc395888516"></a><a name="_Toc395809325"></a><a name="_Toc389865467"></a><a name="_Toc389828008">Visão geral</a>
========================================================================================================================

<a name="_Toc395888517"></a><a name="_Toc395809326">Cenário</a>
----------------------------------------------------------------

Para destacar como os clientes podem aproveitar com eficiência o Banco de Dados de Documentos do Azure para
armazenar e consultar documentos JSON, este documento fornece um passo a passo
completo da criação de um aplicativo Web de votação usando o Banco de Dados de Documentos do
Azure.

Este passo a passo mostra como usar o serviço de Banco de Dados de Documentos fornecido pelo
Azure para armazenar e acessar dados de um aplicativo Web Python hospedado no
Azure e presume que você tenha alguma experiência anterior com o Python e
os sites do Azure.

Você aprenderá a:

1\. Criar e provisionar uma Conta de Banco de Dados de Documentos

2\. Criar um aplicativo Python MVC

3\. Conectar-se a e usar o Banco de Dados de Documento do Azure a partir do aplicativo Web

4\. Implantar o aplicativo Web nos sites do Azure

Seguindo este passo a passo, você compilará um simples aplicativo
de votação que permite votar em uma pesquisa.

![Alt text](./media/documentdb-python-application/image1.png)

<a name="_Toc395888520"></a><a name="_Toc395809329">Pré-requisitos</a>


Antes de seguir as instruções neste artigo, você deve garantir
que você tem o seguinte instalado:

Visual Studio 2013 (ou [Visual Studio Express][] que é a versão
gratuita)

Ferramentas Python para Visual Studio [aqui][]

SDK do Azure para o Visual Studio 2013, versão 2.4 ou superior disponível
[aqui][1]

Ferramentas da linha de comando da plataforma cruzada do Azure, disponíveis através da [Microsoft
Web Platform Installer][]

<a name="_Toc395888519"></a><a name="_Toc395809328">Criar uma conta de banco de dados do Banco de Dados de Documentos</a>
============================================================================================

Para provisionar uma conta de banco de dados do Banco de Dados de Documentos no Azure, abra o Portal
de Gerenciamento do Azure e um clique no bloco de galeria do Azure na
página inicial ou clique em "+" no canto inferior esquerdo da tela.

![Alt text](./media/documentdb-python-application/image2.png)


Isso abrirá a galeria do Azure, onde você pode selecionar dentre muitos
serviços do Azure disponíveis. Na galeria, selecione "Dados, armazenamento e
backup" na lista de categorias.

![Alt text](./media/documentdb-python-application/image3.png)

A partir daqui, selecione a opção para Banco de Dados de Documentos do Azure

![Alt text](./media/documentdb-python-application/image4.png)

Depois selecione "Criar" na parte inferior da tela

![Alt text](./media/documentdb-python-application/image5.png)

Isso abrirá a folha de "Novo Banco de Dados de Documentos", onde você pode especificar o
nome, a região, a escala, o grupo de recursos e outras configurações para sua nova
conta.

![Alt text](./media/documentdb-python-application/image6.png)

Quando você terminar de fornecer os valores para a sua conta, clique em "Criar"
e o processo de provisionamento começará a criar a sua conta de banco de dados.
Quando o processo de provisionamento for concluído, você verá uma notificação
aparecer na área de notificações do portal e o bloco na sua
tela inicial (se você optou por criar um) será alterado para mostrar a
ação concluída.

![Alt text](./media/documentdb-python-application/image7.png)

Quando o provisionamento for concluído, clicar no bloco Banco de Dados de Documentos na
tela inicial abrirá a folha principal dessa conta recém-criada do
Banco de Dados de Documentos.

![Alt text](./media/documentdb-python-application/image8.png)
![Alt text](./media/documentdb-python-application/image9.png)



Usando o botão "Chaves", acesse a URL do ponto de extremidade e a chave primária,
copie-os para a área de transferência e mantenha-os próximos, pois usaremos esses
valores no aplicativo Web que criaremos em seguida.

</a>

<a name="_Toc395888520"></a><a name="_Toc395809329">Criar um novo aplicativo Web Python Flask</a>
=================================================================================================

Abra o Visual Studio, Arquivo -\> Novo Projeto -\> Python -\>, Projeto do Flask Web
com o nome **tutorial**. Ele perguntará se você deseja
Instalar pacotes externos. Clique em Instalar em um ambiente virtual
e, em seguida, clique em Criar. Isso vai configurar o ambiente virtual necessário do Python
de seu projeto.

Para os iniciantes no Flask, é uma estrutura da Web que nos ajuda a compilar aplicativos
Web no Python mais rapidamente. [Clique aqui para acessar os tutoriais do Flask][].

![Alt text](./media/documentdb-python-application/image10.png)

<a name="_Toc395888520"></a><a name="_Toc395809329">Adicione pacotes Flask ao seu projeto</a>
==================================

Quando seu projeto é configurado, que você precisa adicionar determinados pacotes de flask que
serão necessários para o nosso projeto, por exemplo, formulários. Clique com o botão direito do mouse **env**
e **instale os seguintes pacotes do Python (seguir essa ordem é
importante)**:

    flask==0.9
    flask-login
    flask-openid
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup

![Alt text](./media/documentdb-python-application/image11.png)

**Observação:** em casos raros, você pode ver uma falha na janela de saída. Se
isso ocorrer, verifique se o erro está relacionado à limpeza. Às vezes, ocorrerá
falha na limpeza, mas instalação ainda será bem-sucedida (role para cima
na janela de saída para verificar isso).
<a name="verify-the-virtual-environment"></a> Se isso ocorrer, não há problema em continuar.

</h1>
<a name="_Toc395888522"></a><a name="_Toc395809331">Verifique o ambiente virtual</a>
======================================================================================


Vamos garantir que tudo seja instalado corretamente. Inicie o site
clicando em **F5** Isso iniciará o servidor de desenvolvimento do flask
e o navegador da Web. Você deve ver a seguinte página:

![Alt text](./media/documentdb-python-application/image12.png)

<a name="_Toc395888523"></a><a name="_Toc395809332">Adicionando o Banco de Dados de Documentos ao seu projeto</a>
=========================================================================================

O SDK do Python do Banco de Dados de Documentos é hospedado no PyPi e pode ser instalado com o
pip.

Expanda o nó de ambientes Python no Gerenciador de Soluções, clique com o botão direito do mouse no
ambiente e selecione "Instalar Pacote Python..."

![Alt text](./media/documentdb-python-application/image13.png)

Digite "--pre pydocumentdb", que é o nome do pacote PyPi. Como opção, você pode
fornecer uma versão conhecida se quiser controlar qual versão
você deseja instalar, omitindo a versão garantirá que a versão estável
mais recente seja instalada. Observe que você precisará inserir o
nome todo"- pre pydocumentdb".

![Alt text](./media/documentdb-python-application/image14.png)

Isso baixará e instalará o pacote pydocumentdb no seu
ambiente. Uma vez concluído, você verá pydocumentdb listado como um
módulo em seu ambiente.

</h1>
<a name="_Toc395888524"></a><a name="_Toc395809333">Criar banco de dados, coleção e definição do documento</a>
============================================================================================================

Adicione o seguinte arquivo python - **forms.py** e adicione-o à pasta
chamada tutorial no Gerenciador de Soluções. Adicione o seguinte códigos ao
forms.py. Estamos criando nosso aplicativo de sondagem. Isso é feito com a
criação de três campos boolianos que serão alternados com base na entrada do usuário.

</h1>

    #forms.py
    from flask.ext.wtf import Form
    from wtforms import TextField, BooleanField
    from wtforms.validators import Required
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors
    class LoginForm(Form):
        openid = TextField('openid')
        remember_me = BooleanField('remember_me', default = False)
        remember_me1 = BooleanField('remember_me1', default = False)
        remember_me2 = BooleanField('remember_me2', default = False)

<a name="_Toc395888520"></a>
============================

### <a name="_Toc395809338">Criar banco de dados, coleção e documento</a>

\#Observação: é considerado mais seguro manter as credenciais de autenticação
em um arquivo de configuração em vez de em seu aplicativo. Para fins de simplicidade, vamos
colocá-la no código-fonte. Crie uma nova função em views.py e chame-a
de criação. Anexe o seguinte a **views.py**. Não exclua nenhum
código existente.

</h1>

    @app.route('/create')
    def create():
        """Renders the contact page."""
        host = 'https://meetdemo.documents.azure.com:443/'
        masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
        client = document_client.DocumentClient(host, {'masterKey': masterKey})
        databases = client.ReadDatabases().ToArray()
        #delete any existing databases for simplicity
        for database in databases:
            client.DeleteDatabase(database['_self'])
        #create database
        db = client.CreateDatabase({ 'id': 'sample database' })
        # create collection
        collection = client.CreateCollection(db['_self'],{ 'id': 'sample collection' })
        # create document
        document = client.CreateDocument(collection['_self'],
        { 'id': 'sample document',
        'Web Site': '0 votes',
        'Cloud Service': '0 votes',
        'Virtual Machine': '0 votes',
        'name': 'sample document' })
        return render_template('create.html',title='Create Page',year=datetime.now().year,
        message='You just created a new database - sample database a collection - sample collection and a document - sample document that has your votes. Your old votes have been deleted')

<a name="_Toc395888529"></a><a name="_Toc395809338">Aceite a votação dos usuários e atualize o documento</a>
=================================================================================================

### <a name="_Toc395809338">Adicionar as importações solicitadas

<a name="_Toc395888529"></a>
============================

Adicione as seguintes instruções de importação na parte superior em **views.py**. Elas
importarão os pacotes do PythonSDK e do Flask no Banco de Dados de Documentos.


    from wtforms import Form, BooleanField, TextField, PasswordField, validators
    from forms import LoginForm
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors

### <a name="_Toc395809338">Leitura do banco de dados, coleção e documento</a>

Adicione o seguinte código a **views.py**. Isso cuida da configuração
do formulário, da leitura do banco de dados, da coleção e do documento. Não exclua
nenhum código existente em views.py. Simplesmente anexe isso ao final.

    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = LoginForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            host = 'https://meetdemo.documents.azure.com:443/'
            masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
            client = document_client.DocumentClient(host, {'masterKey': masterKey})
            databases = client.ReadDatabases().ToArray()   #Read database
            db =databases[0] #For simplicity we are assuming there is only one database
            collections = client.ReadCollections(db['_self']).ToArray() #Read collection
            coll =collections[0] #For simplicity we are assuming there is only one collection
            documents = client.ReadDocuments(coll['_self']).ToArray() #Read document
            doc = documents[0]   #For simplicity we are assuming there is only document
    
### <a name="_Toc395809338">Registering the vote and modifying the document</a>

            replaced_document = doc
            if form.remember_me.data:
                print 'choice 1'             
                setvar = doc['Web Site']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Web Site'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
                print replaced_document
            elif form.remember_me1.data:
                print 'choice 2'             
                setvar = doc['Cloud Service']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Cloud Service'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
            else:
                print 'choice 2'             
                setvar = doc['Virtual Machine']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Virtual Machine'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)

### <a name="_Toc395809338">Exibindo os resultados</a>

    return render_template('results.html', 
            title = 'Website = ' + replaced_document['Web Site'] + '\n' +'Cloud Service = ' + replaced_document['Cloud Service'] + '\n' + 'Virtual Machine = ' + replaced_document['Virtual Machine'])
        else :        
            return render_template('vote.html', 
            title = 'Vote',
            form = form,
            providers = app.config['OPENID_PROVIDERS'])

    @app.route('/results')
    def results():
        """Renders the results page."""
        return render_template(
            'results.html',
            title='Results',
            year=datetime.now().year,
            message='Your application description page.')


### <a name="_Toc395809338">Crie os arquivos html</a>

Na pasta de modelos, adicione os seguintes arquivos html a ela.
create.html, results.html, vote.html

Adicione o código a seguir a **create.html**. Ele se encarrega de exibir
a mensagem de que criamos um novo banco de dados, a coleção e o documento.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Click here to Vote &raquo;</a></p>
    {% endblock %}

Adicione o código a seguir a **results.html**. Ele se encarrega de exibir
os resultados da pesquisa.
    
    {% extends "layout.html" %}
    {% block content %}
    <h3>Results of the vote</h3>
    <pre>{{ title }}</pre>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote Again &raquo;</a></p>
    {% endblock %}

Adicione o seguinte código a **vote.html**. Ele se encarrega de exibir a
pesquisa e aceitar os votos. Ao registrar os votos, o controle é
passado para views.py onde iremos reconhecer o voto enviado e
anexar o documento de forma adequada.

    {% extends "layout.html" %}
    {% block content %}
    <h1>What is your favorite way to host an application on Azure?</h1>
    <form action="" method="post" name="login">
    {{form.hidden_tag()}}
    <p> </p>
        {{form.remember_me}} Website
        {{form.remember_me1}} Cloud
        {{form.remember_me2}} Virtual Machine
        <p><input type="submit" value="Cast your vote"></p>
    </form>
    {% endblock %}
    
<a name="_Toc395888529"></a>
============================

Exclua o código em **layout.html** e substitua-o pelo seguinte. Ele
configura a barra de navegação e as respectivas URLs de roteamento

Exclua o código em index.html e substitua-o pelo seguinte. Isso
serve como a página de chegada de seu aplicativo

</h1>
<a name="_Toc395888532"></a><a name="_Toc395809341">Adicione um arquivo de configuração e altere \_\_init\_\_.py</a>.
----------------------------------------------------------------------------------------------------------------

Clique com o botão direito do mouse no tutorial do nome do projeto e adicione um arquivo - **config.py**.
Essa configuração é exigida por formulários no flask. Você também pode usá-la para fornecer uma
chave secreta. Adicione o seguinte código a config.py
    
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    OPENID_PROVIDERS = [
       ]

Da mesma forma, anexe o arquivo **\_\_init\_\_.py**. Localize-o na
pasta chamada tutorial. Substitua o código original pelo seguinte. Ele
cuida da conexão entre as exibições e o arquivo config.py.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

Depois de seguir as etapas mencionadas acima, o gerenciador de soluções
deverá ter a seguinte aparência

![Alt text](./media/documentdb-python-application/image15.png)

<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Execute seu aplicativo localmente</a>
============================

Selecione F5 ou o botão de execução no Visual Studio e você verá o
seguinte na tela.

![Alt text](./media/documentdb-python-application/image16.png)

Clique e Votar e selecione sua opção

![Alt text](./media/documentdb-python-application/image17.png)

Para cada voto enviado, o contador adequado será incrementado. Ao
votar da próxima vez, você poderá ver os resultados.

![Alt text](./media/documentdb-python-application/image18.png)

</h1>
<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Implante o aplicativo nos sites do Azure</a>
========================================================================================================================

Agora que você tem o aplicativo concluído funcionando corretamente no
Banco de Dados de Documentos, vamos implantá-lo nos sites do Azure. Clique com o botão direito do mouse no
Projeto no Gerenciador de Soluções (verifique se ele ainda não está em execução
localmente) e selecione Publicar.

![Alt text](./media/documentdb-python-application/image19.png)


Configure seu site do Azure fornecendo suas credenciais e criando
um novo site/reutilizando um site antigo.

![Alt text](./media/documentdb-python-application/image20.png)


Em poucos segundos, o Visual Studio terminará a publicação do seu aplicativo
Web e iniciará um navegador onde você pode ver seu trabalho útil
em execução no Azure!

</h1>
<a name="_Toc395809338">Conclusão</a>
==========

Parabéns! Você acaba de criar seu primeiro aplicativo Python usando
o Banco de Dados de Documentos do Azure e o publicou nos sites do Azure.

Se quiser a solução completa, [clique aqui][]. (Observação: você
ainda precisa adicionar o ambiente virtual, instalar as ferramentas e pacotes python
conforme mencionado acima)

</h1>

  [clique aqui]: http://go.microsoft.com/fwlink/?LinkID=509840&clcid=0x409
  [Clique aqui para acessar os tutoriais do Flask]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [aqui]: https://pytools.codeplex.com/releases/view/123624
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
