<properties 
	pageTitle="Sites Python com bulbo - tutorial do Azure" 
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




# Criando sites com bulbo

Este tutorial descreve como começar a execução do Python em sites do Azure.  Os Websites do Azure fornecem hospedagem gratuita limitada e implantação rápida, e você pode usar o Python!  Conforme o aplicativo cresce, você pode alternar para hospedagem paga e também integrar-se com todos os outros serviços do Azure.

Você criará um aplicativo usando a estrutura de web do bulbo (consulte versões alternativas deste tutorial para [Django](../web-sites-python-create-deploy-django-app) e [Garrafa](../web-sites-python-create-deploy-bottle-app)).  Você criará o site a partir da Galeria do Azure, configurará a implantação do Git e clonará o repositório localmente.  Em seguida, você executará o aplicativo localmente, fará alterações, as confirmará e as enviará para o Azure.  O tutorial mostra como fazer isso no Windows ou Mac/Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]


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

![](./media/web-sites-python-create-deploy-flask-app/portal-create-site.png)

Uma janela será exibida, listando os aplicativos disponíveis na Galeria. Clique na categoria **ESTRUTURAS DO APLICATIVO** à esquerda e selecione **Bulbo**.

![](./media/web-sites-python-create-deploy-flask-app/portal-gallery-flask.png)

Na próxima página, insira um nome e uma região do seu site e clique no botão Concluir.

O site será rapidamente configurado. Você pode clicar no botão **PROCURAR** a partir da barra de ferramentas inferior e verá o novo aplicativo Bulbo em execução no Azure.

![](./media/web-sites-python-create-deploy-flask-app/portal-website-flask.png)
 
Em seguida, você adicionará suporte para publicação via Git.  Isso pode ser feito escolhendo **Configurar a implantação do controle do código-fonte**.

![](./media/web-sites-python-create-deploy-flask-app/portal-site-created.png)

Da caixa de diálogo **Configurar implantação**, role para baixo e selecione a opção **Repositório Git Local** Clique na seta direita para continuar.

![](./media/web-sites-python-create-deploy-flask-app/portal-setup-deployment.png)

Depois de configurar a publicação Git, você verá momentaneamente uma página informando que o repositório está sendo criado. Quando estiver pronto, você verá instruções sobre como se conectar.  

![](./media/web-sites-python-create-deploy-flask-app/portal-repo-created.png)

Vamos acompanhar essas instruções nas próximas seções.


<h2><a name="application-overview"></a>Visão Geral do Aplicativo</h2>

### Conteúdos do repositório Git

Aqui está uma visão geral dos arquivos que você encontrará no repositório Git inicial, que clonaremos na próxima seção.

    \FlaskWebProject\__init__.py
    \FlaskWebProject\views.py
    \FlaskWebProject\static\content\
    \FlaskWebProject\static\fonts\
    \FlaskWebProject\static\scripts\
    \FlaskWebProject\templates\about.html
    \FlaskWebProject\templates\contact.html
    \FlaskWebProject\templates\index.html
    \FlaskWebProject\templates\layout.html

Fontes principais para o aplicativo.  Consiste em 3 páginas (índice, sobre, contato) com um layout mestre.  Conteúdo estátivo e scripts incluem bootstrap, jquery, o modernizr e responder.

    \runserver.py

Suporte do servidor de desenvolvimento local. Use isto para executar o aplicativo localmente.

    \FlaskWebProject.pyproj
    \FlaskWebProject.sln

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

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-clone.png)

Abra o arquivo de solução (.sln) que está incluído na raiz do repositório.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-solution-flask.png)

### Criar ambiente virtual

Agora criaremos um ambiente virtual para desenvolvimento local.  Clique com o botão direito do mouse em **ambientes Python**, selecione **Adicionar ambiente Virtual...**.

- Verifique se o nome do ambiente é `env`.

- Selecione o interpretador de base.  Certifique-se de usar a mesma versão do Python que é selecionada para seu site (em runtime.txt ou na página de configuração do site).

- Verifique se a opção para baixar e instalar pacotes está marcada.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-add-virtual-env-27.png)

Clique em **Criar**.  Isso criará o ambiente virtual e instalará as dependências listadas em requirements.txt.

### Execute usando o servidor de desenvolvimento

Pressione F5 para iniciar a depuração e o navegador da web abrirá automaticamente para a página ser executada localmente.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

Você pode definir pontos de interrupção nas origens, usar as janelas de observação, etc. Consulte a [documentação PTVS](http://pytools.codeplex.com/documentation) para obter mais informações sobre os vários recursos.

### Fazer alterações

Agora você pode experimentar, fazendo alterações de origens do aplicativos e/ou modelos.

Depois que você testou as alterações, confirme-as no repositório do Git:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-commit-flask.png)

### Instalar mais pacotes

Seu aplicativo pode ter dependências além de Python e Bulbo.

Você pode instalar pacotes adicionais usando o pip.  Para instalar um pacote, clique com o botão direito do mouse no ambiente virtual e selecione **instalar pacote Python**.

Por exemplo, para instalar o SDK do Azure para Python, que fornece acesso ao armazenamento do Azure, barramento de serviço e outros serviços do Azure, digite `azure`:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-install-package-dialog.png)

Clique com o botão direito do mouse no ambiente virtual e selecione **gerar requirements.txt** para atualizar o requirements.txt.

Em seguida, confirme as alterações do requirements.txt ao repositório Git.

### Implantar no Azure

Para disparar uma implantação, clique em **Sincronização** ou **Enviar por push**.  A sincronização faz um envio por push e efetua um pull.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-push.png)

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

### Execute usando o servidor de desenvolvimento

Você pode iniciar o aplicativo em um servidor de desenvolvimento com o seguinte comando:

    env\scripts\python runserver.py

O console exibirá a URL e a porta do servidor escuta para:

![](./media/web-sites-python-create-deploy-flask-app/windows-run-local-flask.png)

Em seguida, abra o navegador da web para essa URL.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

### Fazer alterações

Agora você pode experimentar, fazendo alterações de origens do aplicativos e/ou modelos.

Depois que você testou as alterações, confirme-as no repositório do Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalar mais pacotes

Seu aplicativo pode ter dependências além de Python e Bulbo.

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

### Execute usando o servidor de desenvolvimento

Você pode iniciar o aplicativo em um servidor de desenvolvimento com o seguinte comando:

    env/bin/python runserver.py

O console exibirá a URL e a porta do servidor escuta para:

![](./media/web-sites-python-create-deploy-flask-app/mac-run-local-flask.png)

Em seguida, abra o navegador da web para essa URL.

![](./media/web-sites-python-create-deploy-flask-app/mac-browser-flask.png)

### Fazer alterações

Agora você pode experimentar, fazendo alterações de origens do aplicativos e/ou modelos.

Depois que você testou as alterações, confirme-as no repositório do Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalar mais pacotes

Seu aplicativo pode ter dependências além de Python e Bulbo.

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


<h2><a name="next-steps"></a>Próximas etapas</h2>

Siga estes links para saber mais sobre as ferramentas de Bulbo e Python para o Visual Studio: 
 
- [Documentação do Flask][]
- [Documentação do Python Tools para Visual Studio][]

Para obter informações sobre como usar o armazenamento de tabela do Azure e o MongoDB:

- [Flask e MongoDB no Azure com Python Tools 2.1 for Visual Studio][]
- [Flask e armazenamento de tabela do Azure no Azure com ferramentas 2.1 do Python para Visual Studio][]


<!--Link references-->
[Flask e MongoDB no Azure com Python Tools 2.1 for Visual Studio]: ../web-sites-python-ptvs-flask-table-storage
[Flask e armazenamento de tabela do Azure no Azure com ferramentas 2.1 do Python para Visual Studio]: ../web-sites-python-ptvs-flask-mongodb

<!--External Link references-->
[Documentação do Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation 
[Documentação do Flask]: http://flask.pocoo.org/ 


<!--HONumber=42-->
