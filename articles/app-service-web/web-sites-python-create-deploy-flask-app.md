---
title: Criando aplicativos Web com Flask no Azure
description: "Um tutorial que apresenta a execução de um aplicativo Web Python no Azure."
services: app-service\web
documentationcenter: python
tags: python
author: huguesv
manager: wpickett
editor: 
ms.assetid: b7f4ca3a-0b52-4108-90a7-f7e07ac73da0
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/20/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6a0aa48be45b5d0cbac7953e0106c465161e7228


---
# <a name="creating-web-apps-with-flask-in-azure"></a>Criando aplicativos Web com Flask no Azure
Este tutorial descreve como começar a execução de Python em [Aplicativos Web do Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).  Os Aplicativos Web fornecem hospedagem gratuita limitada e implantação rápida, além permitirem a você usar o Python!  Conforme o aplicativo cresce, você pode alternar para hospedagem paga e também integrar-se com todos os outros serviços do Azure.

Você criará um aplicativo usando a estrutura da Web Flask (confira versões alternativas deste tutorial para [Django](web-sites-python-create-deploy-django-app.md) e [Bottle](web-sites-python-create-deploy-bottle-app.md)).  Você vai criar o site na Galeria do Azure, configurar a implantação do Git e clonar o repositório localmente.  Em seguida, você vai executar o aplicativo localmente, fazer alterações, confirmá-las e enviá-las por push para o Azure.  O tutorial mostra como fazer isso por meio do Windows ou Mac/Linux.

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* Windows, Mac ou Linux
* Python 2.7 ou 3.4
* setuptools, pip, virtualenv (somente Python 2.7)
* Git
* PTVS ([Python Tools para Visual Studio][Python Tools para Visual Studio]) – Observação: isso é opcional

**Observação**: atualmente não há suporte à a publicação do TFS em projetos de Python.

### <a name="windows"></a>Windows
Caso você ainda não tenha o Python 2.7 ou 3.4 instalado (32 bits), recomendamos instalar o [SDK do Azure para Python 2.7] ou [SDK do Azure para Python 3.4] usando o Web Platform Installer.  Isso instala a versão de 32 bits do Python, setuptools, pip, virtualenv, etc (Python de 32 bits é o que está instalado nos computadores host do Azure).  Como alternativa, você pode obter o Python por meio de [python.org].

Para Git, recomendamos [Git para Windows] ou [GitHub para Windows].  Se você usar o Visual Studio, você pode usar o suporte integrado a Git.

Também recomendamos a instalação das [Ferramentas Python 2.2 para Visual Studio].  Isso é opcional, mas se você tiver o [Visual Studio], incluindo o Visual Studio Community 2013 ou o Visual Studio Express 2013 para Web gratuitos, isso lhe dará um excelente IDE (ambiente de desenvolvimento integrado) do Python.

### <a name="maclinux"></a>Mac/Linux
Você deve ter o Python e Git já instalados, mas certifique-se de ter uma das versões 2.7 ou 3.4 do Python.

## <a name="web-app-create-on-the-azure-portal"></a>Criar um aplicativo Web no Portal do Azure
A primeira etapa na criação de seu aplicativo é criar o aplicativo Web por meio do [Portal do Azure](https://portal.azure.com). 

1. Faça logon no Portal do Azure e clique no botão **Novo** no canto inferior esquerdo. 
2. Clique em **Web + móvel**.
3. Na caixa de pesquisa, digite "python".
4. Nos resultados da pesquisa, selecione **Flask** e clique em **Criar**.
5. Configure o novo aplicativo Flask, como a criação de um novo plano do Serviço de Aplicativo e um novo grupo de recursos para ele. Em seguida, clique em **Criar**.
6. Configure a publicação de Git para seu aplicativo Web recém-criado seguindo as instruções em [Implantação de GIT local no Serviço de Aplicativo do Azure](app-service-deploy-local-git.md).

## <a name="application-overview"></a>Visão geral do aplicativo
### <a name="git-repository-contents"></a>Conteúdos do repositório Git
Eis aqui uma visão geral dos arquivos que você encontrará no repositório Git inicial, o qual iremos clonar na próxima seção.

    \FlaskWebProject\__init__.py
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

Suporte ao servidor de desenvolvimento local. Use esta opção para executar o aplicativo localmente.

    \FlaskWebProject.pyproj
    \FlaskWebProject.sln

Arquivos de projeto para uso com [Python Tools para Visual Studio].

    \ptvs_virtualenv_proxy.py

Proxy de IIS para ambientes virtuais e suporte à depuração remota de PTVS.

    \requirements.txt

Pacotes externos requeridos por este aplicativo. O script de implantação fará a instalação por pip dos pacotes listados nesse arquivo.

    \web.2.7.config
    \web.3.4.config

Arquivos de configuração do IIS.  O script de implantação usará o web.x.y.config apropriado e o copiará como web.config.

### <a name="optional-files---customizing-deployment"></a>Arquivos opcionais - personalizando a implantação
[!INCLUDE [web-sites-python-customizing-deployment](../../includes/web-sites-python-customizing-deployment.md)]

### <a name="optional-files---python-runtime"></a>Arquivos opcionais - tempo de execução do Python
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### <a name="additional-files-on-server"></a>Arquivos adicionais no servidor
Alguns arquivos existem no servidor, mas não são adicionados ao repositório git.  Eles são criados pelo script de implantação.

    \web.config

Arquivo de configuração do IIS.  Criado por meio de web.x.y.config em toda implantação.

    \env\

Ambiente virtual do Python.  Criado durante a implantação, se ainda não existir um ambiente virtual compatível no aplicativo.  Pacotes listados em requirements.txt são instalados por pip, mas o pip ignorará a instalação se os pacotes já estiverem instalados.

As próximas três seções descrevem como prosseguir com o desenvolvimento de aplicativo Web em três ambientes diferentes:

* Windows, com Python Tools para Visual Studio
* Windows, com linha de comando
* Mac/Linux, com linha de comando

## <a name="web-app-development---windows---python-tools-for-visual-studio"></a>Desenvolvimento de aplicativo Web - Windows - Python Tools para Visual Studio
### <a name="clone-the-repository"></a>Clonar o repositório
Primeiro, clone o repositório usando a URL fornecida no Portal do Azure. Para saber mais, consulte a [Implantação de Git local no Serviço de Aplicativo do Azure](app-service-deploy-local-git.md).

Abra o arquivo da solução (.sln) que está incluído na raiz do repositório.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-solution-flask.png)

### <a name="create-virtual-environment"></a>Criar um ambiente virtual
Agora vamos criar um ambiente virtual para desenvolvimento local.  Clique com o botão direito do mouse em **Ambientes Python** e selecione **Adicionar ambiente virtual...**.

* Verifique se o nome do ambiente é `env`.
* Selecione o interpretador de base.  Use a mesma versão do Python selecionada para seu aplicativo Web (em runtime.txt ou na folha **Configurações do Aplicativo** de seu aplicativo Web no Portal do Azure).
* Verifique se a opção para baixar e instalar pacotes está marcada.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-add-virtual-env-27.png)

Clique em **Criar**.  Isso criará o ambiente virtual e instalar dependências listadas em requirements.txt.

### <a name="run-using-development-server"></a>Executar usando o servidor de desenvolvimento
Pressione F5 para iniciar a depuração e o navegador da Web abrirá automaticamente na página sendo executada localmente.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

Você pode definir pontos de interrupção nas fontes, usar as janelas de observação etc.  Consulte [Ferramentas Python para Documentação] do Visual Studio para obter mais informações sobre os vários recursos.

### <a name="make-changes"></a>Fazer alterações
Agora você pode experimentar, fazendo alterações às fontes e/ou modelos de aplicativos.

Após ter testado as alterações, confirme-as no repositório do Git:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-commit-flask.png)

### <a name="install-more-packages"></a>Instalar mais pacotes
Seu aplicativo pode ter dependências além de Python e Flask.

Você pode instalar pacotes adicionais usando pip.  Para instalar um pacote, clique no ambiente virtual e selecione **Instalar pacote Python**.

Por exemplo, para instalar o SDK do Azure para Python, que fornece acesso ao armazenamento do Azure, ao barramento de serviço e a outros serviços do Azure, digite `azure`:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-install-package-dialog.png)

Clique com o botão direito do mouse em no ambiente virtual e selecione **Gerar requirements.txt** para atualizar requirements.txt.

Em seguida, confirme as alterações a requirements.txt no repositório Git.

### <a name="deploy-to-azure"></a>Implantar no Azure
Para disparar uma implantação, clique em **Sincronização** ou **Push**.  A opção Sincronização faz um envio por push e a extração.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-push.png)

A primeira implantação levará algum tempo, pois isso criará um ambiente virtual, pacotes de instalação, etc.

O Visual Studio não mostra o progresso da implantação.  Se você quiser revisar a saída, consulte a seção sobre [Solução de problemas - Implantação](#troubleshooting-deployment).

Navegue até a URL do Azure para exibir suas alterações.

## <a name="web-app-development---windows---command-line"></a>Desenvolvimento de aplicativos Web - Windows - linha de comando
### <a name="clone-the-repository"></a>Clonar o repositório
Primeiro, clone o repositório usando a URL fornecida no Portal do Azure e adicione o repositório do Azure como um remoto. Para saber mais, consulte a [Implantação de Git local no Serviço de Aplicativo do Azure](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### <a name="create-virtual-environment"></a>Criar um ambiente virtual
Criaremos um novo ambiente virtual para fins de desenvolvimento (não o adicione ao repositório).  Ambientes virtuais em Python não são relocáveis, portanto, todo desenvolvedor trabalhando no aplicativo criará seu próprio ambiente virtual localmente.

Use a mesma versão do Python selecionada para seu aplicativo Web (em runtime.txt ou na folha **Configurações do Aplicativo** de seu aplicativo Web no Portal do Azure).

Para o Python 2.7:

    c:\python27\python.exe -m virtualenv env

Para o Python 3.4:

    c:\python34\python.exe -m venv env

Instale quaisquer pacotes externos exigidos pelo seu aplicativo. Você pode usar o arquivo requirements.txt na raiz do repositório para instalar os pacotes no seu ambiente virtual:

    env\scripts\pip install -r requirements.txt

### <a name="run-using-development-server"></a>Executar usando o servidor de desenvolvimento
Você pode iniciar o aplicativo em um servidor de desenvolvimento com o seguinte comando:

    env\scripts\python runserver.py

O console exibirá a URL e a porta que o servidor escuta:

![](./media/web-sites-python-create-deploy-flask-app/windows-run-local-flask.png)

Em seguida, abra o navegador da Web para essa URL.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

### <a name="make-changes"></a>Fazer alterações
Agora você pode experimentar, fazendo alterações às fontes e/ou modelos de aplicativos.

Após ter testado as alterações, confirme-as no repositório do Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Instalar mais pacotes
Seu aplicativo pode ter dependências além de Python e Flask.

Você pode instalar pacotes adicionais usando pip.  Por exemplo, para instalar o SDK do Azure para Python, que fornece acesso ao armazenamento do Azure, ao barramento de serviço e a outros serviços do Azure, digite:

    env\scripts\pip install azure

Certifique-se de atualizar requirements.txt:

    env\scripts\pip freeze > requirements.txt

Confirme as alterações:

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Implantar no Azure
Para disparar uma implantação, envie as alterações por push para o Azure:

    git push azure master

Você verá a saída do script de implantação, incluindo a criação do ambiente virtual, instalação de pacotes, criação do web.config.

Navegue até a URL do Azure para exibir suas alterações.

## <a name="web-app-development---maclinux---command-line"></a>Desenvolvimento de aplicativos Web - Mac/Linux - linha de comando
### <a name="clone-the-repository"></a>Clonar o repositório
Primeiro, clone o repositório usando a URL fornecida no Portal do Azure e adicione o repositório do Azure como um remoto. Para saber mais, consulte a [Implantação de Git local no Serviço de Aplicativo do Azure](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### <a name="create-virtual-environment"></a>Criar um ambiente virtual
Criaremos um novo ambiente virtual para fins de desenvolvimento (não o adicione ao repositório).  Ambientes virtuais em Python não são relocáveis, portanto, todo desenvolvedor trabalhando no aplicativo criará seu próprio ambiente virtual localmente.

Use a mesma versão do Python selecionada para seu aplicativo Web (em runtime.txt ou na folha **Configurações do Aplicativo** de seu aplicativo Web no Portal do Azure).

Para o Python 2.7:

    python -m virtualenv env

Para o Python 3.4:

    python -m venv env
ou pyvenv env

Instale quaisquer pacotes externos exigidos pelo seu aplicativo. Você pode usar o arquivo requirements.txt na raiz do repositório para instalar os pacotes no seu ambiente virtual:

    env/bin/pip install -r requirements.txt

### <a name="run-using-development-server"></a>Executar usando o servidor de desenvolvimento
Você pode iniciar o aplicativo em um servidor de desenvolvimento com o seguinte comando:

    env/bin/python runserver.py

O console exibirá a URL e a porta que o servidor escuta:

![](./media/web-sites-python-create-deploy-flask-app/mac-run-local-flask.png)

Em seguida, abra o navegador da Web para essa URL.

![](./media/web-sites-python-create-deploy-flask-app/mac-browser-flask.png)

### <a name="make-changes"></a>Fazer alterações
Agora você pode experimentar, fazendo alterações às fontes e/ou modelos de aplicativos.

Após ter testado as alterações, confirme-as no repositório do Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Instalar mais pacotes
Seu aplicativo pode ter dependências além de Python e Flask.

Você pode instalar pacotes adicionais usando pip.  Por exemplo, para instalar o SDK do Azure para Python, que fornece acesso ao armazenamento do Azure, ao barramento de serviço e a outros serviços do Azure, digite:

    env/bin/pip install azure

Certifique-se de atualizar requirements.txt:

    env/bin/pip freeze > requirements.txt

Confirme as alterações:

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Implantar no Azure
Para disparar uma implantação, envie as alterações por push para o Azure:

    git push azure master

Você verá a saída do script de implantação, incluindo a criação do ambiente virtual, instalação de pacotes, criação do web.config.

Navegue até a URL do Azure para exibir suas alterações.

## <a name="troubleshooting---package-installation"></a>Solução de problemas - Instalação de pacotes
[!INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]

## <a name="troubleshooting---virtual-environment"></a>Solução de problemas - Ambiente virtual
[!INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>Próximas etapas
Siga esses links para saber mais sobre o Flask e Python Tools para o Visual Studio: 

* [Documentação do Flask]
* [Ferramentas Python para Documentação]

Para obter informações sobre como usar o Armazenamento de Tabela do Azure e o MongoDB:

* [Flask e MongoDB no Azure com Ferramentas Python para Visual Studio]
* [Flask e Armazenamento de Tabela do Azure no Azure com Ferramentas Python para Visual Studio]

Para obter mais informações, consulte também o [Python Developer Center](/develop/python/).

## <a name="whats-changed"></a>O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!--Link references-->
[Flask e MongoDB no Azure com Ferramentas Python para Visual Studio]: https://github.com/microsoft/ptvs/wiki/Flask-and-MongoDB-on-Azure
[Flask e Armazenamento de Tabela do Azure no Azure com Ferramentas Python para Visual Studio]: web-sites-python-ptvs-flask-table-storage.md

<!--External Link references-->
[SDK do Azure para Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[SDK do Azure para Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git para Windows]: http://msysgit.github.io/
[GitHub para Windows]: https://windows.github.com/
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Ferramentas Python 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Visual Studio]: http://www.visualstudio.com/
[Ferramentas Python para Documentação]: http://aka.ms/ptvsdocs
[Documentação do Flask]: http://flask.pocoo.org/ 




<!--HONumber=Nov16_HO3-->


