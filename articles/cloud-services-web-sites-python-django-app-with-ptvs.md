<properties linkid="develop-python-tutorials-django-with-python-tools-for-visual-studio" urlDisplayName="Django with Python Tools for Visual Studio 2.0" pageTitle="Creating Django applications with Python Tools for Visual Studio 2.0" metaKeywords="" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a SQL Database or MySQL database instance and can be deployed to a website or cloud service." metaCanonical="" services="web-sites,cloud-services" documentationCenter="Python" title="Creating Django applications with Python Tools 2.0 for Visual Studio" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Criando aplicativos Django com as Python Tools 2.0 para Visual Studio

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Neste tutorial, usaremos as Python Tools 2.0 para Visual Studio para criar um aplicativo Django simples. O aplicativo permite que os usu&aacute;rios votem em pesquisas. Primeiro, usaremos um banco de dados local sqlite3 e, em seguida, mudaremos para um banco de dados SQL Server ou MySQL no Azure. Mostraremos como habilitar a interface de administra&ccedil;&atilde;o do Django e us&aacute;-la para adicionar pesquisas ao nosso banco de dados. Tamb&eacute;m usaremos o shell do Django integrado no Visual Studio.  Finalmente, implantamos nosso aplicativo em um site do Azure e em um servi&ccedil;o de nuvem do Azure.</p>
<p>Se voc&ecirc; preferir assistir a um v&iacute;deo, o clipe &agrave; direita segue as mesmas etapas deste tutorial.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/python/videos/django-tutorial-180x120.png') !important;" href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="dev-onpage-video"><span class="icon">Executar o v&iacute;deo</span></a> <span class="time">33:08</span></div>

</div>

Este tutorial concentra-se nas Python Tools para Visual Studio e Azure. Para obter mais detalhes sobre o Django e o aplicativo de pesquisa criado neste tutorial, consulte [][]<https://www.djangoproject.com/></a>.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Requisitos

Para concluir o tutorial, você precisará de

-   [Python Tools 2.0 para Visual Studio][Python Tools 2.0 para Visual Studio]
-   [Python 2.7 (32 bits)][Python 2.7 (32 bits)]
-   Visual Studio e SDK do Azure:
-   VS 2010 Pro ou superior com o SDK do Azure 2.1
-   VS 2012 Pro ou superior com o SDK do Azure 2.1, 2.2 ou superior
-   VS 2013 Pro ou superior com o SDK do Azure 2.2 ou superior
-   Shell Integrado do VS 2013 gratuito. **O SDK do Azure não oferece suporte ao Shell integrado**. Você pode desenvolver, depurar e executar aplicativos Django localmente usando o Shell Integrado gratuito, mas não poderá publicar nos Sites ou nos Serviços de Nuvem do Azure usando o Visual Studio.

Use o Web Platform Installer para instalar o SDK do Azure. Isso instalará o SDK, o emulador e as Ferramentas do Azure para Visual Studio. No Web Platform Installer, procure por **SDK do Azure para .NET** e selecione uma das versões com suporte do SDK para sua versão do Visual Studio.

**Observação:** para executar o aplicativo com êxito usando um Site ou um Serviço de Nuvem do Azure, você precisará usar a distribuição oficial do CPython 2.7 em [python.org][Python 2.7 (32 bits)]. Outros distros podem funcionar, mas não são suportados oficialmente.

## Baixar um projeto existente

Se você desejar avançar no tutorial, poderá [baixar o código-fonte deste projeto (a página pode estar em inglês)][baixar o código-fonte deste projeto (a página pode estar em inglês)].

O banco de dados sqlite3 já foi criado, com as seguintes credenciais para o superusuário:

    Username: tutorial
    Password: azure

O download NÃO inclui um ambiente virtual. Você deverá criar um seguindo as etapas na seção [Criar um ambiente virtual][Criar um ambiente virtual]. Depois disso, seu projeto estará pronto para a seção [Depurando][Depurando].

## Criar o projeto

As Python Tools para Visual Studio oferecem suporte a ambientes virtuais Python. Criaremos um projeto django e usaremos um Ambiente Virtual para instalar nossas dependências. Essa é a maneira recomendada de configurar projetos que são publicados nos Sites ou nos Serviços de Nuvem do Azure.

1.  Abra o Visual Studio, Arquivo/Novo Projeto, aplicativo Django, com o nome **tutorial**.

    ![Novo Projeto][Novo Projeto]

**Observação:** no Gerenciador de Soluções, sob Referências, você verá um nó para o Django 1.4. Esse nó é usado para a implantação do Serviço de Nuvem do Azure, para instalar o Python e o Django no computador de destino. Não exclua a referência ao Django 1.4 da nota de referências. Como estamos usando um ambiente virtual e instalando o nosso próprio pacote Django nele, será usado o pacote Django instalado em nosso ambiente virtual.

## <a name="creating-a-virtual-environment"></a>Criando um ambiente virtual

Vamos usar um ambiente virtual para instalar nossas dependências. Esta é uma boa prática para qualquer aplicativo Python e é necessária ao publicar no Azure.

1.  Criar um novo ambiente virtual. No Gerenciador de Soluções, clique com o botão direito do mouse em **Ambientes Python** e selecione **Adicionar Ambiente Virtual**.

    ![Adicionar Ambiente Virtual][Adicionar Ambiente Virtual]

2.  Selecione Python 2.7 como o interpretador de Python base e aceite o nome padrão **env**. O PTVS instalará pip e/ou virtualenv, se você não os tiver instalado.

3.  Clique com o botão direito do mouse em **env** e em **Instalar Pacote Python**: **django**

    ![Instalar Django][Instalar Django]

4.  O Django tem um grande número de arquivos, por isso levará algum tempo para ser instalado. Você pode exibir o andamento na janela de saída.

    ![Saída da instalação do Django][Saída da instalação do Django]

    **Observação:** em casos raros, você pode ver uma falha na janela de saída. Se isso ocorrer, verifique se o erro está relacionado à limpeza. Algumas vezes, há falha na limpeza, mas a instalação ainda é bem-sucedida (role para cima na janela de saída para verificar isso). Isso ocorre devido ao PTVS obter um bloqueio nos arquivos/pastas temporárias recém-criadas, o que impede que a etapa de limpeza pip os exclua.

5.  Clique com o botão direito do mouse em **env** e em **Instalar Pacote Python**: **pytz** (opcional, mas recomendável, usado pelo django para suporte a fuso horário)

## Verifique o ambiente virtual

1.  Vamos garantir que tudo seja instalado corretamente. Inicie o site com **F5** ou **CTRL+F5**. Isso irá iniciar o servidor de desenvolvimento do django e iniciar seu navegador da web. Você deve ver a seguinte página:

    ![Navegador da Web do Django][Navegador da Web do Django]

## Criar o aplicativo de pesquisa

Nesta seção, adicionaremos um aplicativo para manusear a votação em pesquisas.

Um projeto Django pode ter vários aplicativos. Neste tutorial, o nome do projeto é 'tutorial' e corresponde ao projeto do Visual Studio. O nome do aplicativo que estamos adicionando é **pesquisas** e será uma pasta sob nosso nó do projeto.

1.  Selecione o **nó do projeto**, **Adicionar**-\>**Aplicativo Django**, com o nome **pesquisas**. Isso criará uma pasta para o aplicativo, com o código clichê para arquivos de aplicativos comumente usados.

    ![Adicionar aplicativo Django][Adicionar aplicativo Django]

2.  No **tutorial/settings.py**, adicione o seguinte a **INSTALLED\_APPS**:

        'polls',

3.  e remova os comentários de **INSTALLED\_APPS**:

        'django.contrib.admin',

4.  Substitua o **tutorial/urls.py** com o seguinte código:

        from django.conf.urls import patterns, include, url

        from django.contrib import admin
        admin.autodiscover()

        urlpatterns = patterns('',
            url(r'^', include('polls.urls', namespace="polls")),
            url(r'^admin/', include(admin.site.urls)),
        )

5.  Substitua o **polls/models.py** com o seguinte código:

        from django.db import models

        class Poll(models.Model):
            question = models.CharField(max_length=200)
            pub_date = models.DateTimeField('date published')

            def __unicode__(self):
                return self.question

        class Choice(models.Model):
            poll = models.ForeignKey(Poll)
            choice_text = models.CharField(max_length=200)
            votes = models.IntegerField(default=0)

            def __unicode__(self):
                return self.choice_text

6.  Substitua o **polls/views.py** com o seguinte código:

        from django.shortcuts import get_object_or_404, render
        from django.http import HttpResponseRedirect
        from django.core.urlresolvers import reverse
        from polls.models import Choice, Poll

        def vote(request, poll_id):
            p = get_object_or_404(Poll, pk=poll_id)
            try:
                selected_choice = p.choice_set.get(pk=request.POST['choice'])
            except (KeyError, Choice.DoesNotExist):
                # Redisplay the poll voting form.
                return render(request, 'polls/detail.html', {
                    'poll': p,
                    'error_message': "You didn't select a choice.",
                })
            else:
                selected_choice.votes += 1
                selected_choice.save()
                # Always return an HttpResponseRedirect after successfully dealing
                # with POST data. This prevents data from being posted twice if a
                # user hits the Back button.
                return HttpResponseRedirect(reverse('polls:results', args=(p.id,)))

7.  Adicione um novo arquivo Python **polls/urls.py** com o seguinte código:

        from django.conf.urls import patterns, url
        from django.views.generic import DetailView, ListView
        from polls.models import Poll

        urlpatterns = patterns('',
            url(r'^$',
                ListView.as_view(
                    queryset=Poll.objects.order_by('-pub_date')[:5],
                    context_object_name='latest_poll_list',
                    template_name='polls/index.html'),
                name='index'),
            url(r'^(?P<pk>\d+)/$',
                DetailView.as_view(
                    model=Poll,
                    template_name='polls/detail.html'),
                name='detail'),
            url(r'^(?P<pk>\d+)/results/$',
                DetailView.as_view(
                    model=Poll,
                    template_name='polls/results.html'),
                name='results'),
            url(r'^(?P<poll_id>\d+)/vote/$', 'polls.views.vote', name='vote'),
        )

8.  Crie um novo item **polls/admin.py** com o seguinte código:

        from django.contrib import admin
        from polls.models import Choice, Poll

        class ChoiceInline(admin.TabularInline):
            model = Choice
            extra = 3

        class PollAdmin(admin.ModelAdmin):
            fieldsets = [
                (None,               {'fields': ['question']}),
                ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
            ]
            inlines = [ChoiceInline]
            list_display = ('question', 'pub_date')
            list_filter = ['pub_date']
            search_fields = ['question']
            date_hierarchy = 'pub_date'

        admin.site.register(Poll, PollAdmin)

9.  Na pasta **polls/templates**, crie uma nova pasta chamada **polls**.

10. Mova o arquivo **polls/templates/index.html** para a pasta **polls/templates/polls** usando arrastar e soltar ou recortar/colar.

11. Substituir **polls/templates/polls/index.html** com a seguinte marcação:

        <html>
        <head></head>
        <body>
        {% if latest_poll_list %}
            <ul>
            {% for poll in latest_poll_list %}
                <li><a href="{% url 'polls:detail' poll.id %}">{{ poll.question }}</a></li>
            {% endfor %}
            </ul>
        {% else %}
            <p>No polls are available.</p>
        {% endif %}
        </body>
        </html>

12. Crie um novo modelo HTML Django **polls/templates/polls/detail.html** com o seguinte:

        <html>
        <head></head>
        <body>
        <h1>{{ poll.question }}</h1>
        {% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
        <form action="{% url 'polls:vote' poll.id %}" method="post">
        {% csrf_token %}
        {% for choice in poll.choice_set.all %}
            <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
            <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br />
        {% endfor %}
        <input type="submit" value="Vote" />
        </form>
        </body>
        </html>

13. Crie um novo modelo HTML Django **polls/templates/polls/results.html** com o seguinte:

        <html>
        <head></head>
        <body>
        <h1>{{ poll.question }}</h1>
        <ul>
        {% for choice in poll.choice_set.all %}
            <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
        {% endfor %}
        </ul>
        <a href="{% url 'polls:detail' poll.id %}">Vote again?</a>
        </body>
        </html>

14. Agora você deve ter os seguintes arquivos em seu projeto:

    ![Gerenciador de Soluções][Gerenciador de Soluções]

## Criar um banco de dados sqlite3 localmente

Nosso aplicativo web está quase pronto para uso, mas primeiro é necessário configurar um banco de dados. Para testar nosso site localmente, vamos criar um banco de dados sqlite3. Esse é um banco de dados muito simples que não exige nenhuma instalação adicional. O arquivo de banco de dados será criado na pasta do projeto.

1.  Em **tutorial/settings.py**, adicione a seguinte importação na parte superior do arquivo:

        from os import path

2.  Adicione a seguinte definição próximo à parte superior do arquivo depois da importação:

        PROJECT_ROOT = path.dirname(path.abspath(path.dirname(__file__)))

3.  Altere a seção BANCOS DE DADOS para o código a seguir:

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.sqlite3',
                'NAME': path.join(PROJECT_ROOT, 'db.sqlite3'),
                'USER': '',
                'PASSWORD': '',
                'HOST': '',
                'PORT': '',
            }
        }

4.  Clique com o botão direito do mouse no nó do projeto e selecione **Django**-\>**BD de sincronização do Django**. Uma janela interativa de gerenciamento do Django será exibida. Como o banco de dados ainda não existe, ele solicitará que você crie credenciais de administrador. Digite um nome de usuário e uma senha. O email é opcional.

    ![BD de sincronização do Django][BD de sincronização do Django]

5.  Inicie o site com F5 ou CTRL-F5. Isso irá iniciar o servidor de desenvolvimento do django e iniciar seu navegador da web. A url raiz do site exibe o índice da pesquisa, mas ainda não existe nenhum no banco de dados.

    ![Navegador da Web][Navegador da Web]

6.  Navegue até **<http://localhost>:{port}/admin**. Você pode obter o número da porta na janela do console do servidor de desenvolvimento. Faça logon usando as credenciais criadas na etapa anterior.

    ![Adicionar pesquisa][Adicionar pesquisa]

7.  Use a interface do administrador para adicionar uma ou duas pesquisas. Não perca muito tempo adicionando pesquisas ao banco de dados local. Vamos mudar para um banco de dados em nuvem mais tarde e popular o banco de dados novamente.

    ![Índice da pesquisa][Índice da pesquisa]

8.  Navegue até **<http://localhost>:{port}/**. Você verá um índice das pesquisas adicionadas.

    ![][0]

9.  Clique em uma das pesquisas para ir para a página de votação.

    ![Detalhes da pesquisa][Detalhes da pesquisa]

10. Envie seu voto e você será direcionado para a página de resultados onde você verá a contagem de votos incrementada.

    ![Resultados da pesquisa][Resultados da pesquisa]

## Usar folhas de estilos e outros arquivos estáticos

Nesta seção, atualizaremos a aparência de nosso site para usar uma folha de estilos. Arquivos estáticos, como folhas de estilos, são tratados diferentemente, portanto, é importante armazená-los no local certo.

1.  Em **tutorial/settings.py**, localize a atribuição de **STATIC\_ROOT** e altere-a para:

        STATIC_ROOT = path.join(PROJECT_ROOT, 'static').replace('\','/')

2.  Na pasta **polls**, crie uma nova pasta chamada **static**.

3.  Na pasta **polls/static**, crie uma nova pasta chamada **polls**.

4.  Na pasta **polls/static/polls**, crie uma nova pasta chamada **images**.

5.  Adicione um novo arquivo de folha de estilos **polls/static/polls/style.css** com o seguinte:

        body {
            color: darkblue;
            background: white url("images/background.jpg");
        }

6.  Adicione uma imagem existente à pasta **polls/static/polls/images**, e nomeie-a **background.jpg**.

7.  Agora você deve ter os seguintes arquivos em seu projeto:

    ![Gerenciador de Soluções][1]

8.  Edite o cabeçalho de todos os modelos para fazer referência à folha de estilos com a marcação a seguir:

        <head>
        {% load staticfiles %}
        <link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
        </head>

9.  Execute o site novamente. As páginas de resultados, pesquisa e índice usarão a folha de estilos que criamos, com texto em azul-escuro e uma imagem de plano de fundo.

    ![Detalhes da pesquisa][2]

## <a name="debugging"></a>Depurando

As Python Tools para Visual Studio têm suporte especial para modelos de depuração do Django.

1.  Abra **polls/templates/polls/index.html** e coloque um ponto de interrupção usando **F9** nesta linha:

        {% if latest_poll_list %}

2.  Comece a depurar usando **F5**. O Visual Studio interromperá no arquivo de modelo.

3.  Abra a **Janela de Locais** em **Depurar -\> Janelas -\> Locais** onde a variável **latest\_poll\_list** e seu valor são exibidos.

4.  Você pode pressionar **F10** para entrar, exatamente como em código Python normal. Dentro do loop for, você pode inspecionar o valor de **pesquisa**.

    ![Depurando][3]

## Criar um banco de dados no Windows Azure

Agora que constatamos que nosso aplicativo de pesquisa funciona localmente, vamos alternar para usar um banco de dados hospedado no Azure.

Nas duas seções a seguir, mostraremos como usar um banco de dados SQL e um banco de dados MySQL. Os dois são serviços hospedados.

Outra opção seria criar uma Máquina Virtual e instalar um servidor de banco de dados. Consulte as instruções [aqui][aqui] para configurar o MySQL em uma VM Linux do Azure.

**Observação:** é possível usar um banco de dados sqlite3 no Azure (para fins de desenvolvimento apenas, não é recomendável usá-lo em produção). Você precisará adicionar o arquivo **db.sqlite3** ao seu projeto para implantar o banco de dados com o aplicativo django.

### Banco de dados SQL

Nesta seção, vamos criar um banco de dados SQL no Azure, adicionar os pacotes necessários para nosso ambiente virtual e alterar as configurações para usar o novo banco de dados.

1.  No Portal de Gerenciamento do WindowsAzure, selecione **BANCOS DE DADOS SQL**.

2.  Primeiro, crie um servidor para hospedar o banco de dados. Selecione **SERVIDORES** e **ADICIONAR**.

3.  Na guia **CONFIGURAR** do servidor recém-criado, você verá seu endereço IP de cliente atual exibido. Ao lado dele, clique em **ADICIONAR AOS ENDEREÇOS IP PERMITIDOS**.

    **Observação:** algumas vezes, o Azure não detecta corretamente o endereço IP do cliente. Se receber um erro ao sincronizar o banco de dados, você deverá copiar/colar o endereço IP da mensagem de erro e adicioná-lo aos endereços IP permitidos.

4.  Em seguida, vamos criar o banco de dados. Na guia **BANCOS DE DADOS**, clique em **ADICIONAR** na barra inferior.

5.  No Visual Studio, instalaremos os pacotes necessários para acessar bancos de dados SQL Server no Django em nosso ambiente virtual.

6.  Clique com o botão direito do mouse em **env** e em **Instalar Pacote Python**: **pyodbc** usando **easy\_install**.

7.  Clique com o botão direito do mouse em **env** e em **Instalar Pacote Python**: **django-pyodbc-azure** usando **pip**.

8.  Edite **tutorial/settings.py** e altere a definição **DATABASES** para o seguinte, substituindo **NAME**, **USER**, **PASSWORD** e **HOST** pelos valores listados no painel de controle ClearDB:

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<database name>',
                'USER': '<user name>@<server name>',
                'PASSWORD': '<user password>',
                'HOST': '<server name>.database.windows.net',
                'PORT': '',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': True,
                },
            }
        }

    Você precisa ter certeza de usar um driver instalado em seu computador. Abra **Ferramentas Administrativas** no menu/tela inicial, **Fontes de Dados ODBC (32 bits)**. Os drivers são listados na guia **Drivers**.

    Quando em execução em um Site do Azure, o **SQL Server Native Client 10.0** e o **SQL Server Native Client 11.0** funcionarão.

    Quando em execução em um Serviço de Nuvem do Azure, apenas **SQL Server Native Client 11.0** funcionará.

9.  Sincronize o banco de dados e crie credenciais de administrador, como foi feito para o banco de dados sqlite3 local.

### Banco de Dados MySQL

Nesta seção, vamos criar um banco de dados MySQL no Azure, adicionar os pacotes necessários para nosso ambiente virtual e alterar as configurações para usar o novo banco de dados.

Na Azure Store, você pode adicionar vários serviços à sua conta, incluindo um banco de dados MySQL. Podemos criar uma conta de avaliação gratuitamente e pequenos bancos de dados são gratuitos até um determinado tamanho.

1.  No Portal de Gerenciamento do Azure, selecione **NOVO**-\>**REPOSITÓRIO**-\>**SERVIÇOS DE APLICATIVO**-\>**Banco de Dados MySQL ClearDB**. Crie um banco de dados com o plano gratuito.

2.  Em seguida, instalaremos o pacote necessário para acessar bancos de dados MySQL no Django em nosso ambiente virtual. Clique com o botão direito do mouse em **env** e em **Instalar Pacote Python**: **mysql-python** usando **easy\_install**.

3.  Edite **tutorial/settings.py** e altere a definição **DATABASES** para o seguinte, substituindo **NAME**, **USER**, **PASSWORD** e **HOST** pelos valores listados no painel de controle ClearDB:

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<database name>',
                'USER': '<user name>',
                'PASSWORD': '<user password>',
                'HOST': '<host url>',
                'PORT': '',
            }
        }

4.  Sincronize o banco de dados e crie credenciais de administrador, como foi feito para o banco de dados sqlite3 local.

## Usando o Shell do Django

1.  Clique com o botão direito do mouse no nó do projeto e selecione **Django**-\> **Abrir Shell do Django**.

2.  Nessa janela interativa, podemos acessar nosso banco de dados usando nossos modelos. Insira o código a seguir para adicionar uma pesquisa ao banco de dados:

        from polls.models import Poll, Choice
        from django.utils import timezone

        p = Poll(question="Favorite way to host Django on Azure?", pub_date=timezone.now())
        p.save()

        p.choice_set.create(choice_text='Web Site', votes=0)
        p.choice_set.create(choice_text='Cloud Service', votes=0)
        p.choice_set.create(choice_text='Virtual Machine', votes=0)

    ![Shell Add Poll do Django][Shell Add Poll do Django]

3.  A análise estática dos modelos fornece uma visão limitada da API completa. Na janela interativa, você obterá o IntelliSense contra objetos ativos, portanto, essa é uma ótima maneira de explorar a API. Aqui estão algumas opções para tentar na janela interativa:

        # all poll objects
        Poll.objects.all()

        # primary key for poll
        p.id

        # all choice objects for the poll
        p.choice_set.all()

        # get object by primary key
        Poll.objects.get(pk=1)

    ![Shell Query Poll do Django][Shell Query Poll do Django]

4.  Iniciar o site. Você deve ver a pesquisa que adicionamos usando o shell do Django.

## Publicar no Azure

Agora que nosso banco de dados está no Azure, a próxima etapa é hospedar o próprio site no Azure.

O Azure tem algumas opções para hospedar aplicativos Django:

-   [Site][Site]
-   [Serviço de Nuvem][Serviço de Nuvem]
-   [Máquina Virtual][Máquina Virtual]

As Python Tools para Visual Studio têm recursos de publicação para Sites e Serviços de Nuvem do Azure. As próximas duas seções descrevem esses recursos e você pode optar por percorrer uma ou as duas.

Nos dois casos, o PTVS cuida da configuração do IIS para você e gerará um arquivo web.config para você, se ainda não existir um no projeto. Os arquivos estáticos serão coletados automaticamente (manage.py collectstatic) desde que você tenha definido STATIC\_ROOT em seu settings.py.

A hospedagem do Django em uma Máquina Virtual está fora do escopo deste tutorial. A hospedagem envolve a criação de uma VM com o sistema operacional desejado (Windows ou Linux), a instalação do Python e a implantação manual do aplicativo Django.

### Site do Azure

1.  Primeiro, é necessário criar um Site. Usando o Portal de Gerenciamento do Azure, clique em **NOVO**-\>**SITE DE**-\>**COMPUTAÇÃO**-\>**CRIAÇÃO RÁPIDA**. Escolha qualquer nome que esteja disponível.

2.  Depois de criado, baixe o perfil de publicação para o site.

    ![Perfil de download do site][Perfil de download do site]

3.  No Visual Studio, clique com o botão direito do mouse no nó do projeto e selecione **Publicar**.

    ![Publicação do site][Publicação do site]

4.  Importe o perfil de publicação de site baixado anteriormente.

5.  Aceite os padrões e clique em **Publicar** para iniciar a publicação.

6.  Quando a publicação for concluída, um navegador da web será aberto no site publicado.

    ![Navegador do site][Navegador do site]

### Serviço de Nuvem do Azure

1.  Clique com o botão direito do mouse no nó do projeto e selecione **Adicionar a Projeto de Serviço de Nuvem do Azure** ou **Converter -\> Converter em Projeto de Serviço de Nuvem do Azure** (você verá um ou outro, dependendo da versão do Visual Studio). Isso adicionará um projeto novo à solução, com o sufixo .Azure. Esse novo projeto é marcado como projeto de inicialização na solução.

    **Observação:** se você não vir o comando para criar o Projeto de Serviço de Nuvem do Azure no menu de contexto do projeto, será necessário instalar as Ferramentas do Azure para Visual Studio. Essas ferramentas são instaladas como parte do SDK do Azure para .NET. Consulte a seção de requisitos no início deste tutorial.

    ![Gerenciador de Soluções][4]

#### Executar no emulador do Azure

1.  Você precisará **reiniciar o Visual Studio como administrador** para poder executar no emulador de computação.

2.  Inicie a depuração com **F5**, e o aplicativo será executado e implantado no emulador de computação. Verifique se a interface do administrador funciona e se você pode votar nas pesquisas.

    ![Emulador de computação][Emulador de computação]

3.  Agora você poderá reiniciar o Visual Studio se não desejar continuar executando como administrador.

#### Publicar no Serviço de Nuvem do Azure

1.  Em seguida, você publicará o Serviço de Nuvem no Azure. Clique com o botão direito do mouse no projeto de Serviço de Nuvem **tutorial.Azure** e em **Publicar**.

    **Observação:** certifique-se de selecionar **Publicar** no projeto de Serviço de Nuvem. Isso iniciará a caixa de diálogo **Publicar Aplicativo do Azure** para publicar em um Serviço de Nuvem do Azure. Se você selecionar **Publicar** no projeto Django, ele iniciará a caixa de diálogo **Publicar Web** usada para publicar em um Site do Azure.

    ![Publicação de Serviço de Nuvem][Publicação de Serviço de Nuvem]

2.  Você precisará importar seu arquivo de assinatura do Azure. Clique no link [Entrar para baixar credenciais (a página pode estar em inglês)][Entrar para baixar credenciais (a página pode estar em inglês)] para baixá-las no portal do Azure.

3.  Na página Configurações, selecione **Criar Novo** na caixa de combinação Serviço de Nuvem para criar um novo Serviço de Nuvem. Você pode usar qualquer nome que esteja disponível.

    ![Configurações de Serviço de Nuvem][Configurações de Serviço de Nuvem]

4.  Aceite os padrões e clique em **Publicar**. Isso levará mais tempo do que publicar em um site, uma vez que é necessário provisionar uma máquina virtual para o Serviço de Nuvem. Você pode exibir o andamento na janela Log de Atividades do Azure:

    ![Publicação de Serviço de Nuvem][5]

5.  Quando a operação for concluída, clique na URL do site na janela Log de Atividades do Azure para abrir um navegador da web.

    ![Navegador de Serviço de Nuvem][Navegador de Serviço de Nuvem]

## Conclusão

Neste tutorial, desenvolvemos um aplicativo Django usando as [Python Tools for Visual Studio][Python Tools 2.0 para Visual Studio]. Usamos 3 bancos de dados diferentes: sqlite3, SQL Server e bancos de dados do MySQL. Finalmente, publicamos o aplicativo nos Sites e nos Serviços de Nuvem do Azure.

  [0]: https://www.djangoproject.com/
  [Python Tools 2.0 para Visual Studio]: http://pytools.codeplex.com
  [Python 2.7 (32 bits)]: http://www.python.org/download/
  [baixar o código-fonte deste projeto (a página pode estar em inglês)]: http://download-codeplex.sec.s-msft.com/Download?ProjectName=pytools&DownloadId=783376
  [Criar um ambiente virtual]: #creating-a-virtual-environment
  [Depurando]: #debugging
  [Novo Projeto]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-001-new-project.png
  [Adicionar Ambiente Virtual]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-002-add-virtual-env.png
  [Instalar Django]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-003-install-django.png
  [Saída da instalação do Django]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-004-install-django-output.png
  [Navegador da Web do Django]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-004b-itworked.png
  [Adicionar aplicativo Django]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-005-add-django-app.png
  [Gerenciador de Soluções]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-006-solution-explorer.png
  [BD de sincronização do Django]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-007-sqlite3.png
  [Navegador da Web]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-008-dev-server.png
  [Adicionar pesquisa]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-login.png
  [Índice da pesquisa]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-add-poll.png
  [0]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-010-index.png
  [Detalhes da pesquisa]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-011-detail.png
  [Resultados da pesquisa]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-012-results.png
  [1]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-013-solution-explorer.png
  [2]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-014-detail-styled.png
  [3]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-015-debugging.png
  [aqui]: http://www.windowsazure.com/pt-BR/manage/linux/common-tasks/mysql-on-a-linux-vm/
  [Shell Add Poll do Django]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-018-shell-add-poll.png
  [Shell Query Poll do Django]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-019-shell-query.png
  [Site]: http://www.windowsazure.com/pt-BR/services/web-sites/
  [Serviço de Nuvem]: http://www.windowsazure.com/pt-BR/services/cloud-services/
  [Máquina Virtual]: http://www.windowsazure.com/pt-BR/services/virtual-machines/
  [Perfil de download do site]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-download-profile.png
  [Publicação do site]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-publish.png
  [Navegador do site]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website.png
  [4]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-solution-explorer.png
  [Emulador de computação]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-emulator.png
  [Publicação de Serviço de Nuvem]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish.png
  [Entrar para baixar credenciais (a página pode estar em inglês)]: https://manage.windowsazure.com/publishsettings/index?client=vs&schemaversion=2.0
  [Configurações de Serviço de Nuvem]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-settings.png
  [5]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish-progress.png
  [Navegador de Serviço de Nuvem]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice.png
