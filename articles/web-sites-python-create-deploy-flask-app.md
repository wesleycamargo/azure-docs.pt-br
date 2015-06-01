<properties 
	pageTitle="Sites Python com Flask - Tutorial do Azure" 
	description="Um tutorial que apresenta a execução de um site Python no Azure." 
	services="app-service\web" 
	documentationCenter="python" 
	tags="python"
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Criando sites com Flask

Este tutorial descreve como começar a execução do Python em sites do Azure.  Os Sites do Azure fornecem hospedagem gratuita limitada e rápida implantação, e você pode usar o Python!  Conforme o aplicativo cresce, você pode alternar para hospedagem paga e também integrar-se com todos os outros serviços do Azure.

Você criará um aplicativo usando a estrutura da Web Flask (consulte versões alternativas deste tutorial para [Django](web-sites-python-create-deploy-django-app.md) e [Bottle](web-sites-python-create-deploy-bottle-app.md)).  Você vai criar o site na Galeria do Azure, configurar a implantação do Git e clonar o repositório localmente.  Em seguida, você vai executar o aplicativo localmente, fazer alterações, confirmá-las e enviá-las por push para o Azure.  O tutorial mostra como fazer isso por meio do Windows ou Mac/Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]


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


## Criação de sites no Portal

A primeira etapa na criação de seu aplicativo é criar o site através do Portal de Gerenciamento do Azure.  Para fazer isso, você precisará fazer logon no portal e clicar no botão **NOVO**, no canto inferior esquerdo. Uma janela será exibida. Clique em **COMPUTAR**, em **SITE** e por fim em **DA GALERIA**.

![](./media/web-sites-python-create-deploy-flask-app/portal-create-site.png)

Uma janela será exibida, listando os aplicativos disponíveis na galeria. Clique na categoria **ESTRUTURAS DE APLICATIVO** à esquerda e selecione **Flask**.

![](./media/web-sites-python-create-deploy-flask-app/portal-gallery-flask.png)

Na próxima página, insira um nome e uma região para seu site e clique no botão Concluir.

O site será rapidamente configurado. Você pode clicar no botão **PROCURAR** da barra de ferramentas inferior e então verá o novo aplicativo Flask em execução no Azure.

![](./media/web-sites-python-create-deploy-flask-app/portal-website-flask.png)
 
Em seguida, você adicionará suporte para publicação via Git.  Isso pode ser feito escolhendo **Configurar a implantação do controle do código-fonte**.

![](./media/web-sites-python-create-deploy-flask-app/portal-site-created.png)

Na caixa de diálogo **Configurar implantação**, role para baixo e selecione a opção **Repositório Git Local**. Clique na seta direita para continuar.

![](./media/web-sites-python-create-deploy-flask-app/portal-setup-deployment.png)

Depois de configurar a publicação do Git, você verá momentaneamente uma página informando que o repositório está sendo criado. Quando ele estiver pronto, você verá instruções sobre como se conectar.  

![](./media/web-sites-python-create-deploy-flask-app/portal-repo-created.png)

Vamos seguir essas instruções nas próximas seções.


## Visão geral do aplicativo

### Conteúdos do repositório Git

Eis aqui uma visão geral dos arquivos que você encontrará no repositório Git inicial, o qual iremos clonar na próxima seção.

    \FlaskWebProject__init__.py
    \FlaskWebProject\views.py
    \FlaskWebProject\static\content\
    \FlaskWebProject\static\fonts\
    \FlaskWebProject\static\scripts\
    \FlaskWebProject\templates\about.html
    \FlaskWebProject\templates\contact.html
    \FlaskWebProject\templates\index.html
    \FlaskWebProject\templates\layout.html

Fontes principais para o aplicativo.  Consiste em 3 páginas (índice, sobre, contato) com um layout mestre.  Scripts e conteúdo estático incluem bootstrap, jquery, modernizr e respond.

    \runserver.py

Suporte ao servidor de desenvolvimento local. Use isso para executar o aplicativo localmente.

    \FlaskWebProject.pyproj
    \FlaskWebProject.sln

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

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-clone.png)

Abra o arquivo da solução (.sln) que está incluído na raiz do repositório.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-solution-flask.png)

### Criar um ambiente virtual

Agora vamos criar um ambiente virtual para desenvolvimento local.  Clique com o botão direito do mouse em **Ambientes Python** e selecione **Adicionar ambiente virtual...**.

- Certifique-se de que o nome do ambiente seja `env`.

- Selecione o interpretador de base.  Certifique-se de usar a mesma versão do Python que é selecionada para seu site (em runtime.txt ou a página de configuração do site).

- Verifique se a opção para baixar e instalar pacotes está marcada.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-add-virtual-env-27.png)

Clique em **Criar**.  Isso criará o ambiente virtual e instalar dependências listadas em requirements.txt.

### Executar usando o servidor de desenvolvimento

Pressione F5 para iniciar a depuração e o navegador da Web abrirá automaticamente na página sendo executada localmente.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

Você pode definir pontos de interrupção nas fontes, usar as janelas de observação, etc. Consulte a [documentação PTVS][] para obter mais informações sobre os vários recursos.

### Fazer alterações

Agora você pode experimentar, fazendo alterações às fontes e/ou modelos de aplicativos.

Após ter testado as alterações, confirme-as no repositório do Git:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-commit-flask.png)

### Instalar mais pacotes

Seu aplicativo pode ter dependências além de Python e Flask.

Você pode instalar pacotes adicionais usando pip.  Para instalar um pacote, clique no ambiente virtual e selecione **Instalar pacote Python**.

Por exemplo, para instalar o SDK do Azure para Python, que fornece acesso ao armazenamento do Azure, ao barramento de serviço e a outros serviços do Azure, digite `azure`:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-install-package-dialog.png)

Clique com o botão direito do mouse em no ambiente virtual e selecione **Gerar requirements.txt** para atualizar requirements.txt.

Em seguida, confirme as alterações a requirements.txt no repositório Git.

### Implantar no Azure

Para disparar uma implantação, clique em **Sincronização** ou **Push**.  A opção Sincronização faz um envio por push e a extração.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-push.png)

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

### Executar usando o servidor de desenvolvimento

Você pode iniciar o aplicativo em um servidor de desenvolvimento com o seguinte comando:

    env\scripts\python runserver.py

O console exibirá a URL e a porta a que o servidor escuta:

![](./media/web-sites-python-create-deploy-flask-app/windows-run-local-flask.png)

Em seguida, abra o navegador da Web para essa URL.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

### Fazer alterações

Agora você pode experimentar, fazendo alterações às fontes e/ou modelos de aplicativos.

Após ter testado as alterações, confirme-as no repositório do Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalar mais pacotes

Seu aplicativo pode ter dependências além de Python e Flask.

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

### Executar usando o servidor de desenvolvimento

Você pode iniciar o aplicativo em um servidor de desenvolvimento com o seguinte comando:

    env/bin/python runserver.py

O console exibirá a URL e a porta a que o servidor escuta:

![](./media/web-sites-python-create-deploy-flask-app/mac-run-local-flask.png)

Em seguida, abra o navegador da Web para essa URL.

![](./media/web-sites-python-create-deploy-flask-app/mac-browser-flask.png)

### Fazer alterações

Agora você pode experimentar, fazendo alterações às fontes e/ou modelos de aplicativos.

Após ter testado as alterações, confirme-as no repositório do Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalar mais pacotes

Seu aplicativo pode ter dependências além de Python e Flask.

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


## Troubleshooting - Deployment

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


## Solução de problemas - Instalação de pacotes

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


## Solução de problemas - Ambiente virtual

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## Próximas etapas

Siga esses links para saber mais sobre o Flask e Python Tools para o Visual Studio: 
 
- [Documentação do Flask][]
- [Documentação do Python Tools para Visual Studio][]

Para obter informações sobre como usar o Armazenamento de Tabela do Azure e o MongoDB:

- [Flask e MongoDB no Azure com Python Tools 2.1 for Visual Studio][]
- [Flask e armazenamento de tabela do Azure no Azure com ferramentas 2.1 do Python para Visual Studio][]


<!--Link references-->
[Flask e MongoDB no Azure com Python Tools 2.1 for Visual Studio]: web-sites-python-ptvs-flask-table-storage.md
[Flask e armazenamento de tabela do Azure no Azure com ferramentas 2.1 do Python para Visual Studio]: web-sites-python-ptvs-flask-mongodb.md

<!--External Link references-->
[SDK do Azure para Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[SDK do Azure para Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git para Windows]: http://msysgit.github.io/
[GitHub para Windows]: https://windows.github.com/
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Visual Studio]: http://www.visualstudio.com/
[documentação PTVS]: http://pytools.codeplex.com/documentation
[Documentação do Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation 
[Documentação do Flask]: http://flask.pocoo.org/ 


<!--HONumber=52-->