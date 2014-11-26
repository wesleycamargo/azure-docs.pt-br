<properties title="Build a web application with Python and Flask (MVC) using DocumentDB" pageTitle="Build a web app with Python and Flask using DocumentDB | Azure" description="Learn how to use DocumentDB to store and access data from an Python and Flask (MVC) web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="hawong" />

# <a name="_Toc395888515"></a><a name="_Toc395809324">Criar um aplicativo Web com Python e Flask (MVC) usando o Banco de Dados de Documentos</a>

<a name="_Toc395809324">

# <a name="_Toc395888516"></a><a name="_Toc395809325"></a><a name="_Toc389865467"></a><a name="_Toc389828008">Visão geral</a>

## <a name="_Toc395888517"></a><a name="_Toc395809326">Cenário</a>

Para destacar como os clientes podem aproveitar com eficiência o Banco de Dados de Documentos do Azure para
armazenar e consultar documentos JSON, este documento fornece um passo a passo
completo sobre como criar um aplicativo Web de votação usando o Banco de Dados de Documentos do Azure
.

Este passo a passo mostra como usar o serviço do Banco de Dados de Documentos fornecido pelo
Azure para armazenar e acessar dados de um aplicativo Web Python hospedado no
Azure e supõe que você tenha experiência prévia com o uso do
Python e os Websites do Azure.

Você aprenderá a:

1. Criar e provisionar uma Conta de Banco de Dados de Documentos

2. Criar um aplicativo Python MVC

3. Conectar-se a e usar o Banco de Dados de Documento do Azure a partir do aplicativo Web

4. Implantar o aplicativo Web nos sites do Azure

Seguindo esse passo a passo, você criará um aplicativo simples de votação
que permite que você vote em uma enquete.

![Texto Alt][Texto Alt]

<a name="_Toc395888520"></a><a name="_Toc395809329">Pré-requisitos</a>

Antes de seguir as instruções deste artigo, verifique se
você possui os seguintes itens instalados:

Visual Studio 2013 (ou [Visual Studio Express][Visual Studio Express], que é a
versão gratuita)

Ferramentas Python para Visual Studio [aqui][aqui]

SDK do Azure para o Visual Studio 2013, versão 2.4 ou superior disponível
[aqui][1]

Ferramentas da linha de comando entre plataformas do Azure, disponíveis por meio do [Microsoft
Web Platform Installer][Microsoft
Web Platform Installer]

# <a name="_Toc395888519"></a><a name="_Toc395809328">Criar uma conta de banco de dados do Banco de Dados de Documentos</a>

Para provisionar uma conta de Banco de Dados de Documentos no Azure, abra o Portal de Gerenciamento do Azure
e clique no bloco da Galeria do Azure na
homepage ou clique em “+” no canto inferior esquerdo da tela.

![Texto Alt][2]

Isso abrirá a Galeria do Azure, onde você pode selecionar por meio de muitos serviços
disponíveis do Azure. Na Galeria, selecione “Dados, armazenamento e
backup” na lista de categorias.

![Texto Alt][3]

A partir daqui, selecione a opção para Banco de Dados de Documentos do Azure

![Texto Alt][4]

Depois, selecione “Criar” na parte inferior da tela

![Texto Alt][5]

Isso abrirá a blade “Novo Banco de Dados de Documentos”, onde você pode especificar o
nome, a região, a escala, o grupo de recursos e outras configurações para sua nova
conta.

![Texto Alt][6]

Depois de concluir a inserção de valores na conta, clique em “Criar”,
e o processo de provisionamento começará a criar sua conta de banco de dados.
Quando o provisionamento estiver concluído, você verá uma notificação
exibida na área de notificações do portal, e o bloco na sua
tela inicial (se você tiver selecionado para criar uma tela) mudará para mostrar a
ação concluída.

![Texto Alt][7]

Depois de concluir o provisionamento, clique no bloco do Banco de Dados de Documentos na
tela inicial para exibir o blade principal dessa conta de Banco de Dados de Documentos
recém-criada.

![Texto Alt][8]
![Texto Alt][9]

Usando o botão “Chaves”, acesse a URL do ponto de extremidade e a Chave Primária,
copie-as para sua área de transferência e mantenha-as por perto, pois usaremos esses
valores no aplicativo Web que criaremos em seguida.

</a>

# <a name="_Toc395888520"></a><a name="_Toc395809329">Criar um novo aplicativo Web Python Flask</a>

Abra o Visual Studio, Arquivo -\> Novo Projeto -\> Python -\> Projeto da
Web Flask com o nome **tutorial**. Será perguntado se você deseja
instalar pacotes externos. Clique em Instalar em um ambiente virtual
e clique em Criar. Isso configurará o ambiente virtual necessário do Python
para seu projeto.

Para aqueles que não estão familiarizados com o Flask, ele é uma estrutura da Web que nos ajuda a construir aplicativos Web
no Python mais rapidamente. [Clique aqui para acessar os tutoriais do Flask][Clique aqui para acessar os tutoriais do Flask].

![Texto Alt][10]

# <a name="_Toc395888520"></a><a name="_Toc395809329">Adicione pacotes Flask ao seu projeto</a>

Após seu projeto estar configurado, é preciso adicionar determinados pacotes Flask que
precisaremos para nosso projeto, por exemplo, formulários. Clique com o botão direito do mouse em **env**
e em **Instalar os seguintes Pacotes Python (seguir essa ordem é
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

![Texto Alt][11]

**Observação:** Em casos raros, você pode ver uma falha na janela de saída. Se
isso ocorrer, verifique se o erro está relacionado à limpeza. Algumas vezes, há falha na
limpeza, mas a instalação ainda é bem-sucedida (role para cima
na janela de saída para verificar isso).
<a name="verify-the-virtual-environment"></a> Se isso ocorrer, não tem problema de
continuar.

</h1>
# <a name="_Toc395888522"></a><a name="_Toc395809331">Verifique o ambiente virtual</a>

Verifique se tudo está instalado adequadamente. Inicie o
site clicando em **F5**. Isso lançará o servidor de desenvolvimento do Flask
e iniciará o navegador da Web. Você deve ver a seguinte página:

![Texto Alt][12]

# <a name="_Toc395888523"></a><a name="_Toc395809332">Adicionando o Banco de Dados de Documentos ao seu projeto</a>

O SDK do Python do Banco de Dados de Documentos é hospedado no PyPi e pode ser instalado com o
pip.

Expanda o nó de Ambientes do Python no Gerenciador de Soluções, clique com o botão direito do mouse no
seu ambiente e selecione “Instalar Pacote do Phyton…”

![Texto Alt][13]

Digite “--pre pydocumentdb”, que é o nome do pacote PyPi. Como alternativa,
você pode fornecer uma versão conhecida se quiser controlar qual versão
deseja instalar; deixar a versão de fora assegura que a versão
estável mais recente seja instalada. Observe que você terá que inserir o
nome inteiro "--pre pydocumentdb".

![Texto Alt][14]

Isso fará o download e instalará o pacote pydocumentdb em seu
ambiente; após a conclusão você deve ver pydocumentdb listado como um
módulo em seu ambiente.

</h1>
# <a name="_Toc395888524"></a><a name="_Toc395809333">Criar banco de ados, coleção e definição do documento</a>

Adicione o seguinte arquivo python, **forms.py**, à pasta chamada
tutorial no Gerenciador de Soluções. Adicione o seguinte código a
forms.py. Estamos criando nosso aplicativo de votação. Isso é feito ao
criar três campos booleanos que podem ser alternados com base na entrada do usuário.


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

# <a name="_Toc395888520"></a>

### <a name="_Toc395809338">Criar banco de dados, coleção e documento</a>

Nota nº: É considerado mais seguro manter suas credenciais de autenticação
em um arquivo de configuração ao invés de no aplicativo. Para fins de simplicidade, estamos
colocando-o em código de origem. Crie uma nova função em views.py e denomine-a como
create. Anexe-a a **views.py**. Não exclua nenhum
código existente.


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

# <a name="_Toc395888529"></a><a name="_Toc395809338">Aceite a votação dos usuários e atualize o documento</a>

### <a name="_Toc395809338">Adicione as importações necessárias

# <a name="_Toc395888529"></a>

Adicione as seguintes declarações de importação na parte superior em **views.py**. Elas
importação os pacotes do PythonSDK e do Flask no Banco de Dados de Documentos.

    from wtforms import Form, BooleanField, TextField, PasswordField, validators
    from forms import LoginForm
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors

### <a name="_Toc395809338">Leitura do banco de dados, coleção e documento</a>

Adicione o seguinte código a **views.py**. Isso cuida da configuração do
formulário, da leitura do banco de dados, da coleção e do documento. Não exclua
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

### <a name="_Toc395809338">Registrando o voto e modificando o documento</a>

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

Na página de modelos, adicione os seguintes arquivos html a ela.
create.html, results.html, vote.html

Adicione o código a seguir a **create.html**. Ele cuida da exibição
da mensagem de que um novo banco de dados, coleção e documento foram criados.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Click here to Vote &raquo;</a></p>
    {% endblock %}

Adicione o código a seguir a **results.html**. Ele cuida da exibição
dos resultados da votação.

    {% extends "layout.html" %}
    {% block content %}
    <h3>Results of the vote</h3>
    <pre>{{ title }}</pre>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote Again &raquo;</a></p>
    {% endblock %}

Adicione o código a seguir a **vote.html**. Ele cuida da exibição da
votação e da aceitação dos votos. Ao registrar os votos, o controle é
transmitido a views.py, em que reconheceremos o voto enviado e
anexamos o documento de acordo.

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

# <a name="_Toc395888529"></a>

Exclua o código em **layout.html** e substitua-o pelo seguinte. Isso
configura a barra de navegação e as respectivas URLs de roteamento

Exclua o código em index.html e substitua-o pelo seguinte. Isso
serve como a página de chegada de seu aplicativo

</h1>
## <a name="_Toc395888532"></a><a name="_Toc395809341">Adicione um arquivo de configuração e altere o \_\_init\_\_.py</a>.

Clique com o botão direito do mouse no tutorial com o nome do programa e adicione um arquivo, **config.py**.
Essa configuração é exigida pelos formulários no Flask. Você também pode usá-lo para fornecer uma
chave secreta. Adicione o código a seguir a config.py

    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    OPENID_PROVIDERS = [
       ]

Da mesma forma, anexe o arquivo **\_\_init\_\_.py**. Localize-o na
pasta chamada tutorial. Substitua o código original pelo seguinte. Isso
cuida da conexão entre as exibições e o arquivo config.py.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

Após seguir os passos mencionados acima, essa é a aparência do gerenciador
de soluções

![Texto Alt][15]

# <a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Execute seu aplicativo localmente</a>

Selecione F5 ou o botão de seleção no Visual Studio e você deve ver a
seguinte tela.

![Texto Alt][16]

Clique e Votar e selecione sua opção

![Texto Alt][17]

Para cada voto enviado, o contador adequado será incrementado. Ao
votar da próxima vez, você poderá ver os resultados.

![Texto Alt][18]

</h1>
# <a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Implante o aplicativo nos sites do Azure</a>

Agora que o aplicativo completo está funcionando corretamente no
Banco de Dados de Documentos, vamos implantar isso nos Websites do Azure. Clique com o botão direito do mouse
no Projeto no Gerenciador de Soluções (verifique se ele não está sendo executado
localmente) e selecione Publicar.

![Texto Alt][19]

Configure seu Website do Azure fornecendo suas credenciais e criando um
novo Website/reutilizando um Website antigo.

![Texto Alt][20]

Em poucos segundos, o Visual Studio terminará de publicar seu aplicativo
Web e iniciará um navegador onde você poderá ver seu trabalho
sendo executado no Azure!

</h1>
# <a name="_Toc395809338">Conclusão</a>

Parabéns! Só é preciso construir seu primeiro aplicativo Python usando o
Banco de Dados de Documentos do Azure e publicá-lo nos Websites do Azure.

Se quiser concluir a solução, [clique aqui][clique aqui]. (Nota: Você ainda
terá que adicionar o ambiente virtual, instalar as ferramentas e
pacotes do Python, conforme mencionado acima)

</h1>

  [Texto Alt]: ./media/documentdb-python-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/pt-br/products/visual-studio-express-vs.aspx
  [aqui]: https://pytools.codeplex.com/releases/view/123624
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [2]: ./media/documentdb-python-application/image2.png
  [3]: ./media/documentdb-python-application/image3.png
  [4]: ./media/documentdb-python-application/image4.png
  [5]: ./media/documentdb-python-application/image5.png
  [6]: ./media/documentdb-python-application/image6.png
  [7]: ./media/documentdb-python-application/image7.png
  [8]: ./media/documentdb-python-application/image8.png
  [9]: ./media/documentdb-python-application/image9.png
  [Clique aqui para acessar os tutoriais do Flask]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world
  [10]: ./media/documentdb-python-application/image10.png
  [11]: ./media/documentdb-python-application/image11.png
  [12]: ./media/documentdb-python-application/image12.png
  [13]: ./media/documentdb-python-application/image13.png
  [14]: ./media/documentdb-python-application/image14.png
  [15]: ./media/documentdb-python-application/image15.png
  [16]: ./media/documentdb-python-application/image16.png
  [17]: ./media/documentdb-python-application/image17.png
  [18]: ./media/documentdb-python-application/image18.png
  [19]: ./media/documentdb-python-application/image19.png
  [20]: ./media/documentdb-python-application/image20.png
  [clique aqui]: http://go.microsoft.com/fwlink/?LinkID=509840&clcid=0x409
