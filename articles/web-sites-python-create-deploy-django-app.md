<properties 
	pageTitle="Websites Python com Django - Tutorial do Azure" 
	description="Um tutorial que apresenta a execução de um site Python no Azure." 
	services="web-sites" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="12/17/2014" 
	ms.author="huvalo"/>




# Criando Sites com o Django

Este tutorial descreve como começar a execução do Python em Sites do Azure.  Os Websites do Azure fornecem hospedagem gratuita limitada e implantação rápida, e você pode usar o Python!  Conforme o aplicativo cresce, você pode alternar para hospedagem paga e também integrar-se com todos os outros serviços do Azure.

Você criará um aplicativo usando a estrutura na web do Django (consulte versões alternativas deste tutorial para [Bulbo](../web-sites-python-create-deploy-flask-app) e [Garrafa](../web-sites-python-create-deploy-bottle-app)).  Você criará o site a partir da Galeria do Azure, configurará a implantação do Git e clonará o repositório localmente.  Em seguida, você executará o aplicativo localmente, fará alterações, as confirmará e as enviará para o Azure.  O tutorial mostra como fazer isso no Windows ou Mac/Linux.

> [AZURE.NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Você pode <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">ativar os benefícios de assinante do MSDN</a> ou <a href="http://azure.microsoft.com/pricing/free-trial/">se inscrever para fazer uma avaliação gratuita</a>.
> 
> Se você quiser iniciar com Websites do Azure antes de assinar uma conta, acesse <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a>, em que é possível criar imediatamente um site de iniciante ASP.NET de vida curta nos Websites do Azure gratuitamente. Nenhum cartão de crédito é exigido, sem compromissos.


+ [Pré-requisitos](#prerequisites)
+ [Criação de sites no Portal](#website-creation-on-portal)
+ [Visão Geral do Aplicativo](#application-overview)
+ Desenvolvimento de site
  + [Windows - ferramentas Python para Visual Studio](#website-development-windows-ptvs)
  + [Windows - linha de comando](#website-development-windows-command-line)
  + [Mac/Linux - linha de comando](#website-development-mac-linux-command-line)
+ [Solução de problemas - implantação](#troubleshooting-deployment)
+ [Solução de problemas - instalação do pacote](#troubleshooting-package-installation)
+ [Solução de problemas - ambiente Virtual](#troubleshooting-virtual-environment)
+ [Solução de problemas - arquivos estáticos](#troubleshooting-static-files)
+ [Solução de problemas - configurações](#troubleshooting-settings)
+ [Usando um banco de dados](#using-a-database)
+ [Interface de administração do Django](#django-admin-interface)
+ [Próximas etapas](#next-steps)


<h2><a name="prerequisites"></a>Pré-requisitos</h2>

- Windows, Mac ou Linux
- Python 2.7 ou 3.4
- setuptools, pip, virtualenv (apenas Python 2.7)
- Git
- Python Tools para Visual Studio (opcional)

**Observação**: A publicação do TFS atualmente não tem suporte em projetos do Python.

### Windows

Se você ainda não tiver o Python 2.7 ou 3.4 instalado (32 bits), recomendamos instalar o [SDK do Azure para Python 2.7](http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409) ou [SDK do Azure para Python 3.4](http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409) usando o Web Platform Installer.  Isso instala a versão de 32 bits do Python, setuptools, pip, virtualenv, etc. (o Python de 32 bits é o que está instalado nos computadores host do Azure).  Como alternativa, você pode obter o Python a partir de [python.org](http://www.python.org/).

Para Git, recomendamos [Git para Windows](http://msysgit.github.io/). Este tutorial usa o Git Shell a partir do Git para Windows.  Se você usar o Visual Studio, você pode usar o suporte Git integrado.

Também recomendamos a instalação do [Python Tools para Visual Studio](http://pytools.codeplex.com).  Isso é opcional, mas se você tiver o [Visual Studio](http://www.visualstudio.com/), incluindo o Visual Studio Express 2013 para Web gratuito, e isso lhe dará um excelente IDE do Python.

### Mac/Linux

Você deve ter o Python e o Git já instalados, mas certifique-se de ter o Python 2.7 ou 3.4.


<h2><a name="website-creation-on-portal"></a>Criação de sites no Portal</h2>

A primeira etapa na criação de seu aplicativo é criar o site através do Portal de Gerenciamento do Azure.  Para fazer isso, você precisará fazer logon no portal e clicar no botão **NOVO** no canto inferior esquerdo. Uma janela será exibida. Clique em **COMPUTAR**, **WEBSITE** e, em seguida, **A PARTIR DA GALERIA**.

![](./media/web-sites-python-create-deploy-django-app/portal-create-site.png)

Uma janela será exibida, listando os aplicativos disponíveis na Galeria. Clique na categoria **ESTRUTURAS DO APLICATIVO** à esquerda e selecione **Django**.

![](./media/web-sites-python-create-deploy-django-app/portal-gallery-django.png)

Na próxima página, insira um nome e uma região do seu site e clique no botão Concluir.

O site será rapidamente configurado. Você pode clicar no botão **PROCURAR** a partir da barra de ferramentas inferior e você verá o novo aplicativo Django em execução no Azure.

![](./media/web-sites-python-create-deploy-django-app/portal-website-django.png)
 
Em seguida, você adicionará suporte para publicação via Git.  Isso pode ser feito escolhendo **Configurar a implantação do controle do código-fonte**.

![](./media/web-sites-python-create-deploy-django-app/portal-site-created.png)

Da caixa de diálogo **Configurar implantação**, role para baixo e selecione a opção **Repositório Git Local** Clique na seta direita para continuar.

![](./media/web-sites-python-create-deploy-django-app/portal-setup-deployment.png)

Depois de configurar a publicação Git, você verá momentaneamente uma página informando que o repositório está sendo criado. Quando estiver pronto, você verá instruções sobre como se conectar.  

![](./media/web-sites-python-create-deploy-django-app/portal-repo-created.png)

Vamos acompanhar essas instruções nas próximas seções.


<h2><a name="application-overview"></a>Visão Geral do Aplicativo</h2>

### Conteúdos do repositório Git

Aqui está uma visão geral dos arquivos que você encontrará no repositório Git inicial, que clonaremos na próxima seção.

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

Fontes principais para o aplicativo.  Consiste em 3 páginas (índice, sobre, contato) com um layout mestre.  Conteúdo estátivo e scripts incluem bootstrap, jquery, o modernizr e responder.

    \manage.py

Gerenciamento local e suporte do servidor de desenvolvimento. Use isto para executar o aplicativo localmente, sincronizar o banco de dados, etc.

    \db.sqlite3

Banco de dados padrão. Inclui as tabelas necessárias para o aplicativo ser executado, mas não contém quaisquer usuários (sincronize o banco de dados para criar um usuário).

    \DjangoWebProject.pyproj
    \DjangoWebProject.sln

Projete arquivos para uso com o [Python Tools para Visual Studio](http://pytools.codeplex.com).

    \ptvs_virtualenv_proxy.py

Proxy de IIS para ambientes virtuais e suporte para depuração do PTVS remoto.

    \requirements.txt

Pacotes externos necessitados para este aplicativo. O script de implantação instalará o pip dos pacotes listados nesse arquivo.
 
    \web.2.7.config
    \web.3.4.config

Arquivos de configuração do IIS.  O script de implantação usará o web.x.y.config apropriado e o copiará como web.config.

### Arquivos opcionais - personalizando implantação

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../includes/web-sites-python-customizing-deployment.md)]

### Arquivos opcionais - tempo de execução do Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

### Arquivos adicionais no servidor

Alguns arquivos existem no servidor, mas não são adicionados ao repositório Git.  Eles são criados pelo script de implantação.

    \web.config

Arquivos de configuração do IIS.  Criado a partir de web.x.y.config em cada implantação.

    \env\

Ambiente virtual do Python.  Criado durante a implantação, se ainda não existir um ambiente virtual compatível no site.  Pacotes listados no pip do requirements.txt são instalados, mas o pip ignorará a instalação se os pacotes já estiverem instalados.

As próximas 3 seções descrevem como prosseguir com o desenvolvimento de site em 3 ambientes diferentes:

- Windows, com Python Tools para Visual Studio
- Windows, com a linha de comando
- Mac/Linux, com a linha de comando


<h2><a name="website-development-windows-ptvs"></a>Desenvolvimento do Site da Web - Windows - Python Tools para Visual Studio</h2>

### Clonar o repositório

Primeiro, clone o repositório usando a URL fornecida no portal do Azure.

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-clone.png)

Abra o arquivo de solução (.sln) que está incluído na raiz do repositório.

![](./media/web-sites-python-create-deploy-django-app/ptvs-solution-django.png)

### Criar ambiente virtual

Agora criaremos um ambiente virtual para desenvolvimento local.  Clique com o botão direito do mouse em **ambientes Python**, selecione **Adicionar ambiente Virtual...**.

- Verifique se o nome do ambiente é `env`.

- Selecione o interpretador de base.  Certifique-se de usar a mesma versão do Python que é selecionada para seu site (em runtime.txt ou na página de configuração do site).

- Verifique se a opção para baixar e instalar pacotes está marcada.

![](./media/web-sites-python-create-deploy-django-app/ptvs-add-virtual-env-27.png)

Clique em **Criar**.  Isso criará o ambiente virtual e instalará as dependências listadas em requirements.txt.

### Criar um superusuário

O banco de dados incluído no aplicativo não tem qualquer superusuário definido.  Para usar a funcionalidade de entrada no aplicativo ou na interface do administrador do Django (se você decidir habilitá-lo), você precisará criar um superusuário.

Execute este na linha de comando da sua pasta de projeto:

    env\scripts\python manage.py createsuperuser

Siga os prompts para definir o nome de usuário, senha, etc.

### Execute usando o servidor de desenvolvimento

Pressione F5 para iniciar a depuração e o navegador da web abrirá automaticamente para a página ser executada localmente.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

Você pode definir pontos de interrupção nas origens, usar as janelas de observação, etc. Consulte a [documentação PTVS](http://pytools.codeplex.com/documentation) para obter mais informações sobre os vários recursos.

### Fazer alterações

Agora você pode experimentar, fazendo alterações de origens do aplicativos e/ou modelos.

Depois que você testou as alterações, confirme-as no repositório do Git:

![](./media/web-sites-python-create-deploy-django-app/ptvs-commit-django.png)

### Instalar mais pacotes

Seu aplicativo pode ter dependências além de Python e Django.

Você pode instalar pacotes adicionais usando o pip.  Para instalar um pacote, clique com o botão direito do mouse no ambiente virtual e selecione **instalar pacote Python**.

Por exemplo, para instalar o SDK do Azure para Python, que fornece acesso ao armazenamento do Azure, barramento de serviço e outros serviços do Azure, digite `azure`:

![](./media/web-sites-python-create-deploy-django-app/ptvs-install-package-dialog.png)

Clique com o botão direito do mouse no ambiente virtual e selecione **gerar requirements.txt** para atualizar o requirements.txt.

Em seguida, confirme as alterações do requirements.txt ao repositório Git.

### Implantar no Azure

Para disparar uma implantação, clique em **Sincronização** ou **Enviar por push**.  A sincronização faz um envio por push e efetua um pull.

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-push.png)

A primeira implantação levará algum tempo, pois criará um ambiente virtual, pacotes de instalação, etc.

O Visual Studio não mostra o progresso da implantação.  Se você quiser revisar a saída, consulte a seção sobre [Solução de problemas - implantação](#troubleshooting-deployment).

Navegue até a URL do Azure para ver suas alterações.


<h2><a name="website-development-windows-command-line"></a>Implantação do site - Windows - linha de comando</h2>

### Clonar o repositório

Primeiro, clone o repositório usando a URL fornecida no portal do Azure e adicione o repositório do Azure como um controle remoto.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Criar ambiente virtual

Criaremos um novo ambiente virtual para fins de desenvolvimento (não o adicione ao repositório).  Os ambientes virtuais no Python não são relocáveis, então todo desenvolvedor que trabalha no aplicativo criará o seu próprio localmente.

Certifique-se de usar a mesma versão do Python que é selecionada para seu site (em runtime.txt ou na página de configuração do site).

Para o Python 2.7:

    c:\python27\python.exe -m virtualenv env

Para o Python 3.4:

    c:\python34\python.exe -m venv env

Instale quaisquer pacotes externos exigidos pelo seu aplicativo. Você pode usar o arquivo requirements.txt na raiz do repositório para instalar os pacotes no seu ambiente virtual:

    env\scripts\pip install -r requirements.txt

### Criar um superusuário

O banco de dados incluído no aplicativo não tem qualquer superusuário definido.  Para usar a funcionalidade de entrada no aplicativo ou na interface do administrador do Django (se você decidir habilitá-lo), você precisará criar um superusuário.

Execute este na linha de comando da sua pasta de projeto:

    env\scripts\python manage.py createsuperuser

Siga os prompts para definir o nome de usuário, senha, etc.

### Execute usando o servidor de desenvolvimento

Você pode iniciar o aplicativo em um servidor de desenvolvimento com o seguinte comando:

    env\scripts\python manage.py runserver

O console exibirá a URL e a porta do servidor escuta para:

![](./media/web-sites-python-create-deploy-django-app/windows-run-local-django.png)

Em seguida, abra o navegador da web para essa URL.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

### Fazer alterações

Agora você pode experimentar, fazendo alterações de origens do aplicativos e/ou modelos.

Depois que você testou as alterações, confirme-as no repositório do Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalar mais pacotes

Seu aplicativo pode ter dependências além de Python e Django.

Você pode instalar pacotes adicionais usando o pip.  Por exemplo, para instalar o SDK do Azure para Python, que fornece acesso ao armazenamento do Azure, barramento de serviço e outros serviços do Azure, digite:

    env\scripts\pip install azure

Certifique-se de atualizar requirements.txt:

    env\scripts\pip freeze > requirements.txt

Confirme as alterações:

    git add requirements.txt
    git commit -m "Added azure package"

### Implantar no Azure

Para disparar uma implantação, envie as alterações para o Azure:

    git push azure master

Você verá a saída do script de implantação, incluindo a criação do ambiente virtual, instalação de pacotes, criação do web.config.

Navegue até a URL do Azure para ver suas alterações.


<h2><a name="website-development-mac-linux-command-line"></a>Desenvolvimento de site - Mac/Linux - Linha de comando</h2>

### Clonar o repositório

Primeiro, clone o repositório usando a URL fornecida no portal do Azure e adicione o repositório do Azure como um controle remoto.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Criar ambiente virtual

Criaremos um novo ambiente virtual para fins de desenvolvimento (não o adicione ao repositório).  Os ambientes virtuais no Python não são relocáveis, então todo desenvolvedor que trabalha no aplicativo criará o seu próprio localmente.

Certifique-se de usar a mesma versão do Python que é selecionada para seu site (em runtime.txt ou na página de configuração do site).

Para o Python 2.7:

    python -m virtualenv env

Para o Python 3.4:

    python -m venv env

Instale quaisquer pacotes externos exigidos pelo seu aplicativo. Você pode usar o arquivo requirements.txt na raiz do repositório para instalar os pacotes no seu ambiente virtual:

    env/bin/pip install -r requirements.txt

### Criar um superusuário

O banco de dados incluído no aplicativo não tem qualquer superusuário definido.  Para usar a funcionalidade de entrada no aplicativo ou na interface do administrador do Django (se você decidir habilitá-lo), você precisará criar um superusuário.

Execute este na linha de comando da sua pasta de projeto:

    env/bin/python manage.py createsuperuser

Siga os prompts para definir o nome de usuário, senha, etc.

### Execute usando o servidor de desenvolvimento

Você pode iniciar o aplicativo em um servidor de desenvolvimento com o seguinte comando:

    env/bin/python manage.py runserver

O console exibirá a URL e a porta do servidor escuta para:

![](./media/web-sites-python-create-deploy-django-app/mac-run-local-django.png)

Em seguida, abra o navegador da web para essa URL.

![](./media/web-sites-python-create-deploy-django-app/mac-browser-django.png)

### Fazer alterações

Agora você pode experimentar, fazendo alterações de origens do aplicativos e/ou modelos.

Depois que você testou as alterações, confirme-as no repositório do Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalar mais pacotes

Seu aplicativo pode ter dependências além de Python e Django.

Você pode instalar pacotes adicionais usando o pip.  Por exemplo, para instalar o SDK do Azure para Python, que fornece acesso ao armazenamento do Azure, barramento de serviço e outros serviços do Azure, digite:

    env/bin/pip install azure

Certifique-se de atualizar requirements.txt:

    env/bin/pip freeze > requirements.txt

Confirme as alterações:

    git add requirements.txt
    git commit -m "Added azure package"

### Implantar no Azure

Para disparar uma implantação, envie as alterações para o Azure:

    git push azure master

Você verá a saída do script de implantação, incluindo a criação do ambiente virtual, instalação de pacotes, criação do web.config.

Navegue até a URL do Azure para ver suas alterações.


<h2><a name="troubleshooting-deployment"></a>Solução de problemas - implantação</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


<h2><a name="troubleshooting-package-installation"></a>Solução de problemas - instalação do pacote</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


<h2><a name="troubleshooting-virtual-environment"></a>Solução de problemas - ambiente Virtual</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]


<h2><a name="troubleshooting-static-files"></a>Solução de problemas - arquivos estáticos</h2>

Django tem o conceito de coleta de arquivos estáticos.  Isso coloca todos os arquivos estáticos de seu local original e os copia para uma única pasta.  Para este aplicativo, eles são copiados para `/static`.

Isso é feito porque os arquivos estáticos podem vir de Django diferentes 'apps'.  Por exemplo, os arquivos estáticos das interfaces do administrador do Django estão localizados em uma subpasta da biblioteca de Django no ambiente virtual.  Os arquivos estáticos definidos por esse aplicativo estão localizados em `/app/static`.  Conforme você usa mais Django 'apps', você terá arquivos estáticos localizados em vários locais.

Ao executar o aplicativo no modo de depuração, o aplicativo serve os arquivos estáticos a partir de seu local original.

Ao executar o aplicativo no modo da versão, o aplicativo **não** servirá os arquivos estáticos.  É responsabilidade do servidor da Web servir os arquivos.  Para este aplicativo, o IIS servirá os arquivos estáticos a partir de `/static`.

A coleção de arquivos estáticos é feita automaticamente como parte do script de implantação, limpando os arquivos anteriormente coletados.  Isso significa que a coleção ocorre em cada implantação, diminuindo um pouco a implantação, mas garante que os arquivos obsoletos não estarão disponíveis, evitando um possível problema de segurança. 

Se você quiser ignorar a coleção de arquivos estáticos para seu aplicativo Django:

    \.skipDjango 

Então você precisará fazer a coleção manualmente no seu computador local:

    env\scripts\python manage.py collectstatic

Em seguida, remova a pasta `\static` do `.gitignore` e adicione-a ao repositório Git.


<h2><a name="troubleshooting-settings"></a>Solução de problemas - configurações</h2>

Várias configurações para o aplicativo podem ser alteradas na `DjangoWebProject/settings.py`.

Para conveniência do desenvolvedor, o modo de depuração está habilitado.  Um bom efeito colateral disso é que você poderá ver as imagens e outros conteúdos estáticos quando executar localmente, sem precisar coletar arquivos estáticos.

Para desabilitar o modo de depuração:

    DEBUG = False

Quando a depuração está desabilitada, o valor de `ALLOWED_HOSTS` precisa ser atualizado para incluir o nome de host do Azure.  Por exemplo:

    ALLOWED_HOSTS = (
        'pythonapp.azurewebsites.net',
    )

ou para permitir que os:

    ALLOWED_HOSTS = (
        '*',
    )
 
Na prática, você talvez queira fazer algo mais complexo para lidar com a alternância entre a depuração e o modo de versão e obter o nome do host.

Você pode definir variáveis de ambiente por meio da página **CONFIGURAR** do portal do Azure, na seção **configurações do aplicativo**.  Isso pode ser útil para definir valores que você não deseja que apareça nas origens (cadeias de conexão, senhas etc.) ou seja definida de maneira diferente entre o Azure e sua máquina local.  Em `settings.py`, você pode consultar as variáveis de ambiente usando `os.getenv`.


<h2><a name="using-a-database"></a>Usando um banco de dados</h2>

O banco de dados que está incluído com o aplicativo é um banco de dados sqlite.  Este é um banco de dados padrão útil e conveniente usar para o desenvolvimento, pois ele quase não exige configuração.  O banco de dados é armazenado no arquivo db.sqlite3 na pasta do projeto.

O Azure fornece serviços de banco de dados que são fáceis de usar em um aplicativo Django.  Tutoriais de uso do [banco de dados SQL](../web-sites-python-ptvs-django-sql) e [MySQL](../web-sites-python-ptvs-django-mysql) de um aplicativo Django mostram as etapas necessárias para criar o sergiço de banco de dados, alterar as configurações de banco de dados no `DjangoWebProject/settings.py`, e as bibliotecas necessárias para instalar.

É claro que, se você preferir gerenciar seus próprios servidores de banco de dados, você pode fazer isso usando máquinas virtuais do Windows ou Linux em execução no Azure.


<h2><a name="django-admin-interface"></a>Interface de administração do Django</h2>

Depois que você começar a criar seus modelos, você desejará preencher o banco de dados com alguns dados.  Uma maneira fácil de adicionar e editar o conteúdo interativo é usar a interface do administrador do Django.

O código para a interface do administrador é comentado nas origens do aplicativo, mas é claramente marcado para que você possa habilitá-lo facilmente (procurar por 'admin').

Depois de habilitado, sincronize o banco de dados, execute o aplicativo e navegue até `/admin`.


<h2><a name="next-steps"></a>Próximas etapas</h2>

Siga estes links para saber mais sobre as ferramentas de Python e Django para o Visual Studio: 
 
- [Documentação do Django][]
- [Documentação do Python Tools para Visual Studio][]

Para obter informações sobre como usar o banco de dados SQL e MySQL:

- [Django e banco de dados SQL no Azure com Python Tools 2.1 para Visual Studio][]
- [Django e MySQL no Azure com Python Tools 2.1 para Visual Studio][]


<!--Link references-->
[Django e MySQL no Azure com Python Tools 2.1 para Visual Studio]: ../web-sites-python-ptvs-django-mysql
[Django e banco de dados SQL no Azure com Python Tools 2.1 para Visual Studio]: ../web-sites-python-ptvs-django-sql

<!--External Link references-->
[Documentação do Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation 
[Documentação do Django]: https://www.djangoproject.com/



<!--HONumber=42-->
