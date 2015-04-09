<properties 
	pageTitle="Sites Python com Django - Tutorial do Azure" 
	description="Um tutorial que apresenta a execução de um site Python no Azure." 
	services="app-service\web" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huvalo"/>




# Criando aplicativos Web com Django

Este tutorial descreve como começar a execução de Python em aplicativos Web do Azure.  Os aplicativos Web do Azure fornecem hospedagem gratuita limitada e implantação rápida, além permitirem a você usar o Python!  Conforme o aplicativo cresce, você pode alternar para hospedagem paga e também integrar-se com todos os outros serviços do Azure.

Você criará um aplicativo usando o framework Web Django (consulte versões alternativas deste tutorial para [Flask](web-sites-python-create-deploy-flask-app.md) e [Bottle](web-sites-python-create-deploy-bottle-app.md)).  Você vai criar o site na Galeria do Azure, configurar a implantação do Git e clonar o repositório localmente.  Em seguida, você vai executar o aplicativo localmente, fazer alterações, confirmá-las e enviá-las por push para o Azure.  O tutorial mostra como fazer isso por meio do Windows ou Mac/Linux.

> [AZURE.NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Você pode <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">ativar os benefícios de assinante do MSDN</a> ou <a href="http://azure.microsoft.com/pricing/free-trial/">se inscrever para fazer uma avaliação gratuita</a>.
> 
> Se você quiser iniciar com Websites do Azure antes de assinar uma conta, acesse <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a>, em que é possível criar imediatamente um site inicial ASP.NET de curta duração nos Websites do Azure, gratuitamente. Nenhum cartão de crédito é exigido, sem compromissos.


## Pré-requisitos

- Windows, Mac ou Linux
- Python 2.7 ou 3.4
- setuptools, pip, virtualenv (somente Python 2.7)
- Git
- [Python Tools 2.1 para Visual Studio][] (opcional)

**Observação**: A publicação do TFS atualmente não tem suporte em projetos do Python.

### Windows

Se você ainda não tiver o Python 2.7 ou 3.4 instalado (32 bits), recomendamos instalar o [SDK do Azure para Python 2.7][] ou [SDK do Azure para Python 3.4][] usando o Web Platform Installer.  Isso instala a versão de 32 bits do Python, setuptools, pip, virtualenv, etc (Python de 32 bits é o que está instalado nos computadores host do Azure).  Como alternativa, você pode obter o Python por meio de [python.org][].

Para Git, recomendamos [Git para Windows][] ou [GitHub para Windows][].  Se você usar o Visual Studio, você pode usar o suporte integrado a Git.

Também recomendamos a instalação do [Python Tools 2.1 para Visual Studio][].  Isso é opcional, mas se você tiver [Visual Studio][], incluindo o Visual Studio Community 2013 ou o Visual Studio Express 2013 para Web gratuitos, então isso lhe dará um excelente IDE (ambiente de desenvolvimento integrado) do Python.

### Mac/Linux

Você deve ter o Python e Git já instalados, mas certifique-se de ter uma das versões 2.7 ou 3.4 do Python.


## Criação de aplicativos Web no Portal

A primeira etapa na criação de seu aplicativo é criar o aplicativo Web por meio do Portal de Gerenciamento do Azure.  Para fazer isso, você precisará fazer logon no portal e clicar no botão **NOVO**, no canto inferior esquerdo. Uma janela será exibida. Clique em **COMPUTAR**, em **SITE** e por fim em **DA GALERIA**.

![](./media/web-sites-python-create-deploy-django-app/portal-create-site.png)

Uma janela será exibida, listando os aplicativos disponíveis na galeria. Clique na categoria **ESTRUTURAS DE APLICATIVO** à esquerda e selecione **Django**.

![](./media/web-sites-python-create-deploy-django-app/portal-gallery-django.png)

Na próxima página, insira um nome e uma região para seu site e clique no botão Concluir.

O site será rapidamente configurado. Você pode clicar no botão **PROCURAR** da barra de ferramentas inferior e então verá o novo aplicativo Django em execução no Azure.

![](./media/web-sites-python-create-deploy-django-app/portal-website-django.png)
 
Em seguida, você adicionará suporte para publicação via Git.  Isso pode ser feito escolhendo **Configurar a implantação do controle do código-fonte**.

![](./media/web-sites-python-create-deploy-django-app/portal-site-created.png)

Na caixa de diálogo **Configurar implantação**, role para baixo e selecione a opção **Repositório Git Local**. Clique na seta direita para continuar.

![](./media/web-sites-python-create-deploy-django-app/portal-setup-deployment.png)

Depois de configurar a publicação do Git, você verá momentaneamente uma página informando que o repositório está sendo criado. Quando ele estiver pronto, você verá instruções sobre como se conectar.  

![](./media/web-sites-python-create-deploy-django-app/portal-repo-created.png)

Vamos seguir essas instruções nas próximas seções.


## Visão geral do aplicativo

### Conteúdos do repositório Git

Eis aqui uma visão geral dos arquivos que você encontrará no repositório Git inicial, o qual iremos clonar na próxima seção.

    \app\__init__.py
    \app\forms.py
    \app\models.py
    \app\tests.py
    \app\views.py
    \app\static\content\
    \app\static\fonts\
    \app\static\scripts\
    \app\templates\about.html
    \app\templates\contact.html
    \app\templates\index.html
    \app\templates\layout.html
    \app\templates\login.html
    \app\templates\loginpartial.html
    \DjangoWebProject\__init__.py
    \DjangoWebProject\settings.py
    \DjangoWebProject\urls.py
    \DjangoWebProject\wsgi.py

Fontes principais para o aplicativo.  Consiste em 3 páginas (índice, sobre, contato) com um layout mestre.  Scripts e conteúdo estático incluem bootstrap, jquery, modernizr e respond.

    \manage.py

Gerenciamento local e suporte ao servidor de desenvolvimento. Use isto para executar o aplicativo localmente, sincronizar o banco de dados, etc.

    \db.sqlite3

Banco de dados padrão. Inclui as tabelas necessárias para que o aplicativo seja executado, mas não contém nenhum usuário (sincronize o banco de dados para criar um usuário).

    \DjangoWebProject.pyproj
    \DjangoWebProject.sln

Arquivos de projeto para uso com [Python Tools para Visual Studio](http://pytools.codeplex.com).

    \ptvs_virtualenv_proxy.py

Proxy de IIS para ambientes virtuais e suporte à depuração remota de PTVS.

    \requirements.txt

Pacotes externos requeridos por este aplicativo. O script de implantação fará a instalação por pip dos pacotes listados nesse arquivo.
 
    \web.2.7.config
    \web.3.4.config

Arquivos de configuração do IIS.  O script de implantação usará o web.x.y.config apropriado e o copiará como web.config.

### Arquivos opcionais - personalizando a implantação

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../includes/web-sites-python-customizing-deployment.md)]

### Arquivos opcionais - tempo de execução do Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

### Arquivos adicionais no servidor

Alguns arquivos existem no servidor, mas não são adicionados ao repositório git.  Eles são criados pelo script de implantação.

    \web.config

Arquivo de configuração do IIS.  Criado por meio de web.x.y.config em toda implantação.

    \env\

Ambiente virtual do Python.  Criado durante a implantação, se ainda não existir um ambiente virtual compatível no site.  Pacotes listados em requirements.txt são instalados por pip, mas o pip ignorará a instalação se os pacotes já estiverem instalados.

As próximas 3 seções descrevem como prosseguir com o desenvolvimento de site em 3 ambientes diferentes:

- Windows, com Python Tools para Visual Studio
- Windows, com linha de comando
- Mac/Linux, com linha de comando


## Desenvolvimento de site - Windows - Python Tools para Visual Studio

### Clonar o repositório

Primeiro, clone o repositório usando a URL fornecida no portal do Azure.

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-clone.png)

Abra o arquivo da solução (.sln) que está incluído na raiz do repositório.

![](./media/web-sites-python-create-deploy-django-app/ptvs-solution-django.png)

### Criar um ambiente virtual

Agora vamos criar um ambiente virtual para desenvolvimento local.  Clique com o botão direito do mouse em **Ambientes Python** e selecione **Adicionar ambiente virtual...**.

- Certifique-se de que o nome do ambiente seja `env`.

- Selecione o interpretador de base.  Certifique-se de usar a mesma versão do Python que é selecionada para seu site (em runtime.txt ou a página de configuração do site).

- Verifique se a opção para baixar e instalar pacotes está marcada.

![](./media/web-sites-python-create-deploy-django-app/ptvs-add-virtual-env-27.png)

Clique em **Criar**.  Isso criará o ambiente virtual e instalar dependências listadas em requirements.txt.

### Criar um superusuário

O banco de dados que acompanha o aplicativo não tem nenhum superusuário definido.  Para usar a funcionalidade de entrar no aplicativo, ou então a interface de administrador do Django (se você decidir habilitá-la), você precisará criar um superusuário.

Execute isso na linha de comando da sua pasta de projeto:

    env\scripts\python manage.py createsuperuser

Siga os prompts para definir o nome de usuário, senha, etc.

### Executar usando o servidor de desenvolvimento

Pressione F5 para iniciar a depuração e o navegador da Web abrirá automaticamente na página sendo executada localmente.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

Você pode definir pontos de interrupção nas fontes, usar as janelas de observação, etc. Consulte a [documentação PTVS][] para obter mais informações sobre os vários recursos.

### Fazer alterações

Agora você pode experimentar, fazendo alterações às fontes e/ou modelos de aplicativos.

Após ter testado as alterações, confirme-as no repositório do Git:

![](./media/web-sites-python-create-deploy-django-app/ptvs-commit-django.png)

### Instalar mais pacotes

Seu aplicativo pode ter dependências além de Python e Django.

Você pode instalar pacotes adicionais usando pip.  Para instalar um pacote, clique no ambiente virtual e selecione **Instalar pacote Python**.

Por exemplo, para instalar o SDK do Azure para Python, que fornece acesso ao armazenamento do Azure, ao barramento de serviço e a outros serviços do Azure, digite `azure`:

![](./media/web-sites-python-create-deploy-django-app/ptvs-install-package-dialog.png)

Clique com o botão direito do mouse em no ambiente virtual e selecione **Gerar requirements.txt** para atualizar requirements.txt.

Em seguida, confirme as alterações a requirements.txt no repositório Git.

### Implantar no Azure

Para disparar uma implantação, clique em **Sincronização** ou **Push**.  A opção Sincronização faz um envio por push e a extração.

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-push.png)

A primeira implantação levará algum tempo, pois isso criará um ambiente virtual, pacotes de instalação, etc.

O Visual Studio não mostra o progresso da implantação.  Se você quiser revisar a saída, consulte a seção sobre [Solução de problemas - Implantação](#troubleshooting-deployment).

Navegue até a URL do Azure para exibir suas alterações.


## Desenvolvimento de site - Windows - Linha de comando

### Clonar o repositório

Primeiro, clone o repositório usando a URL fornecida no portal do Azure e adicione o repositório do Azure como um remoto.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Criar um ambiente virtual

Criaremos um novo ambiente virtual para fins de desenvolvimento (não o adicione ao repositório).  Ambientes virtuais em Python não são relocáveis, portanto, todo desenvolvedor trabalhando no aplicativo criará seu próprio ambiente virtual localmente.

Certifique-se de usar a mesma versão do Python que é selecionada para seu site (em runtime.txt ou a página de configuração do site)

Para o Python 2.7:

    c:\python27\python.exe -m virtualenv env

Para o Python 3.4:

    c:\python34\python.exe -m venv env

Instale quaisquer pacotes externos exigidos pelo seu aplicativo. Você pode usar o arquivo requirements.txt na raiz do repositório para instalar os pacotes no seu ambiente virtual:

    env\scripts\pip install -r requirements.txt

### Criar um superusuário

O banco de dados que acompanha o aplicativo não tem nenhum superusuário definido.  Para usar a funcionalidade de entrar no aplicativo, ou então a interface de administrador do Django (se você decidir habilitá-la), você precisará criar um superusuário.

Execute isso na linha de comando da sua pasta de projeto:

    env\scripts\python manage.py createsuperuser

Siga os prompts para definir o nome de usuário, senha, etc.

### Executar usando o servidor de desenvolvimento

Você pode iniciar o aplicativo em um servidor de desenvolvimento com o seguinte comando:

    env\scripts\python manage.py runserver

O console exibirá a URL e a porta a que o servidor escuta:

![](./media/web-sites-python-create-deploy-django-app/windows-run-local-django.png)

Em seguida, abra o navegador da Web para essa URL.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

### Fazer alterações

Agora você pode experimentar, fazendo alterações às fontes e/ou modelos de aplicativos.

Após ter testado as alterações, confirme-as no repositório do Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalar mais pacotes

Seu aplicativo pode ter dependências além de Python e Django.

Você pode instalar pacotes adicionais usando pip.  Por exemplo, para instalar o SDK do Azure para Python, que fornece acesso ao armazenamento do Azure, ao barramento de serviço e a outros serviços do Azure, digite:

    env\scripts\pip install azure

Certifique-se de atualizar requirements.txt:

    env\scripts\pip freeze > requirements.txt

Confirme as alterações:

    git add requirements.txt
    git commit -m "Added azure package"

### Implantar no Azure

Para disparar uma implantação, envie as alterações por push para o Azure:

    git push azure master

Você verá a saída do script de implantação, incluindo a criação do ambiente virtual, instalação de pacotes, criação do web.config.

Navegue até a URL do Azure para exibir suas alterações.


## Desenvolvimento de site - Mac/Linux - Linha de comando

### Clonar o repositório

Primeiro, clone o repositório usando a URL fornecida no portal do Azure e adicione o repositório do Azure como um remoto.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Criar um ambiente virtual

Criaremos um novo ambiente virtual para fins de desenvolvimento (não o adicione ao repositório).  Ambientes virtuais em Python não são relocáveis, portanto, todo desenvolvedor trabalhando no aplicativo criará seu próprio ambiente virtual localmente.

Certifique-se de usar a mesma versão do Python que é selecionada para seu site (em runtime.txt ou a página de configuração do site).

Para o Python 2.7:

    python -m virtualenv env

Para o Python 3.4:

    python -m venv env

Instale quaisquer pacotes externos exigidos pelo seu aplicativo. Você pode usar o arquivo requirements.txt na raiz do repositório para instalar os pacotes no seu ambiente virtual:

    env/bin/pip install -r requirements.txt

### Criar um superusuário

O banco de dados que acompanha o aplicativo não tem nenhum superusuário definido.  Para usar a funcionalidade de entrar no aplicativo, ou então a interface de administrador do Django (se você decidir habilitá-la), você precisará criar um superusuário.

Execute isso na linha de comando da sua pasta de projeto:

    env/bin/python manage.py createsuperuser

Siga os prompts para definir o nome de usuário, senha, etc.

### Executar usando o servidor de desenvolvimento

Você pode iniciar o aplicativo em um servidor de desenvolvimento com o seguinte comando:

    env/bin/python manage.py runserver

O console exibirá a URL e a porta a que o servidor escuta:

![](./media/web-sites-python-create-deploy-django-app/mac-run-local-django.png)

Em seguida, abra o navegador da Web para essa URL.

![](./media/web-sites-python-create-deploy-django-app/mac-browser-django.png)

### Fazer alterações

Agora você pode experimentar, fazendo alterações às fontes e/ou modelos de aplicativos.

Após ter testado as alterações, confirme-as no repositório do Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalar mais pacotes

Seu aplicativo pode ter dependências além de Python e Django.

Você pode instalar pacotes adicionais usando pip.  Por exemplo, para instalar o SDK do Azure para Python, que fornece acesso ao armazenamento do Azure, ao barramento de serviço e a outros serviços do Azure, digite:

    env/bin/pip install azure

Certifique-se de atualizar requirements.txt:

    env/bin/pip freeze > requirements.txt

Confirme as alterações:

    git add requirements.txt
    git commit -m "Added azure package"

### Implantar no Azure

Para disparar uma implantação, envie as alterações por push para o Azure:

    git push azure master

Você verá a saída do script de implantação, incluindo a criação do ambiente virtual, instalação de pacotes, criação do web.config.

Navegue até a URL do Azure para exibir suas alterações.


## Solução de problemas - Implantação

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


## Solução de problemas - Instalação de pacotes

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


## Solução de problemas - Ambiente virtual

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## Solução de problemas - Arquivos estáticos

O Django tem o conceito de coletar arquivos estáticos.  Isso retira todos os arquivos estáticos de seu local original e copia-os para uma única pasta.  Para este aplicativo, eles são copiados para `/static`.

Isso é feito porque arquivos estáticos podem vir de 'apps' do Django diferentes.  Por exemplo, os arquivos estáticos das interfaces de admin do Django estão localizados em uma subpasta de biblioteca do Django no ambiente virtual.  Arquivos estáticos definidos por esse aplicativo estão localizados em `/app/static`.  Já que você usa mais 'apps' Django, você terá arquivos estáticos localizados em múltiplos locais.

Ao executar o aplicativo no modo de depuração, o aplicativo serve os arquivos estáticos desde seu local original.

Ao executar o aplicativo no modo de liberação, o aplicativo **não** servirá os arquivos estáticos.  Servir os arquivos é responsabilidade do servidor Web.  Para este aplicativo, o IIS servirá os arquivos estáticos por meio de `/static`.

A coleta de arquivos estáticos é feita automaticamente como parte do script de implantação, limpando arquivos coletados anteriormente.  Isso significa que a coleta ocorre em cada implantação, desacelerando um pouco o processo de implantação; todavia, garante que os arquivos obsoletos não estarão disponíveis, evitando um problema de segurança em potencial. 

Se você quiser ignorar a coleção de arquivos estáticos para seu aplicativo Django:

    \.skipDjango 

Em seguida, você precisará fazer a coleta manualmente no computador local:

    env\scripts\python manage.py collectstatic

Em seguida, remova a pasta `\static` de `.gitignore` e adicione-a ao repositório Git.


## Solução de problemas - Configurações

Várias configurações para o aplicativo podem ser alteradas em `DjangoWebProject/settings.py`.

Para conveniência do desenvolvedor, o modo de depuração está habilitado.  Um bom efeito colateral disso é que você poderá ver as imagens e outros conteúdos estáticos quando em execução localmente, sem precisar coletar arquivos estáticos.

Para desabilitar o modo de depuração:

    DEBUG = False

Quando a depuração é desabilitada, o valor de `ALLOWED_HOSTS` precisa ser atualizado para incluir o nome de host do Azure.  Por exemplo:

    ALLOWED_HOSTS = (
        'pythonapp.azurewebsites.net',
    )

ou para habilitar qualquer um:

    ALLOWED_HOSTS = (
        '*',
    )
 
Na prática, talvez você queira fazer algo mais complexo para lidar com a alternância entre os modos de depuração e liberação, e obter o nome do host.

Você pode definir variáveis de ambiente por meio da página **CONFIGURAR** do portal do Azure, na seção **configurações do aplicativo**.  Isso pode ser útil para definir valores que você não deseja que apareçam nas fontes (cadeias de conexão, senhas etc.), ou que você deseja definir de maneira diferente entre o Azure e o computador local.  Em `settings.py`, você pode consultar as variáveis de ambiente usando `os.getenv`.


## Usando um banco de dados

O banco de dados incluído com o aplicativo é um banco de dados sqlite.  Isso é um banco de dados padrão útil e conveniente no uso para o desenvolvimento, pois ele não exige praticamente nenhuma configuração.  O banco de dados é armazenado no arquivo db.sqlite3, na pasta do projeto.

O Azure fornece serviços de banco de dados que são fáceis de usar em um aplicativo Django.  Tutoriais para uso de [Banco de dados SQL][] e [MySQL][] por meio de um aplicativo Django mostram as etapas necessárias para criar o serviço de banco de dados e alterar as configurações de banco de dados em `DjangoWebProject/settings.py`, além das bibliotecas necessárias para a instalação.

É claro que, se você preferir gerenciar seus próprios servidores de banco de dados, você pode fazer isso usando máquinas virtuais em Windows ou Linux em execução no Azure.


## Interface de administração do Django

Depois que você começar a criar seus modelos, você desejará preencher o banco de dados com alguns dados.  Uma maneira fácil de adicionar e editar o conteúdo de modo interativo é usar a interface de administração do Django.

O código para a interface do administrador é comentado nas fontes de aplicativo, mas é claramente marcado para que você possa habilitá-lo com facilidade (procure por 'admin').

Depois que ele estiver habilitado, sincronize o banco de dados, execute o aplicativo e navegue até `/admin`.


## Próximas etapas

Siga esses links para saber mais sobre o Django e Python Tools para o Visual Studio: 
 
- [Documentação do Django][]
- [Documentação do Python Tools para Visual Studio][]

Para obter informações sobre como usar o MySQL e banco de dados SQL:

- [Django e banco de dados SQL no Azure com Python Tools 2.1 para Visual Studio][]
- [Django e MySQL no Azure com Python Tools 2.1 para Visual Studio][]


<!--Link references-->
[Django e MySQL no Azure com Python Tools 2.1 para Visual Studio]: web-sites-python-ptvs-django-mysql.md
[Django e banco de dados SQL no Azure com Python Tools 2.1 para Visual Studio]: web-sites-python-ptvs-django-sql.md
[Banco de Dados SQL]: web-sites-python-ptvs-django-sql.md
[MySQL]: web-sites-python-ptvs-django-mysql.md


<!--External Link references-->
[SDK do Azure para Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[SDK do Azure para Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git para Windows]: http://msysgit.github.io/
[GitHub para Windows]: https://windows.github.com/
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Visual Studio]: http://www.visualstudio.com/
[Documentação do PTVS]: http://pytools.codeplex.com/documentation
[Documentação do Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation 
[Documentação do Django]: https://www.djangoproject.com/


<!--HONumber=49-->