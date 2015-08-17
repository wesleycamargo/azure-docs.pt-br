<properties
	pageTitle="Instalar o Python e o SDK - Azure"
	description="Saiba como instalar o Python e o SDK para usar com o Azure."
	services=""
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="02/05/2015"
	ms.author="huvalo"/>




# Instalando o Python e o SDK

O Python é muito fácil de ser instalado no Windows e é fornecido pré-instalado no Mac e no Linux. Este guia o orientará na instalação e na preparação de seu computador para o uso com o Azure. Este guia o ajudará com o seguinte:

* Novidades no SDK do Python Azure
* Qual Python e qual versão usar
* Instalação no Windows
* Instalação no Mac e no Linux

## Novidades no SDK do Python Azure

O SDK do Azure para o Python inclui componentes que permitem que você desenvolva, implante e gerencie aplicativos do Python para o Azure. O SDK do Azure para o Python inclui especificamente o seguinte:

* **As Bibliotecas de Cliente do Python para o Azure**. Essas bibliotecas de classe fornecem uma interface para acessar recursos do Azure, como armazenamento e barramento de serviço, e gerenciar recursos do Azure, como contas de armazenamento, máquinas virtuais, etc.
* **Os Emuladores do Azure (somente Windows)**. Os emuladores de computação e armazenamento são emuladores locais de Serviços de Nuvem e Serviços de Gerenciamento de Dados que permitem que você teste um aplicativo localmente. Os Emuladores do Azure são executados somente no Windows.

## Qual Python e qual versão usar

Existem vários tipos de interpretadores do Python disponíveis - alguns exemplos incluem:

* CPython - o interpretador do Python padrão e mais geralmente usado
* IronPython -interpretador do Python que é executado no .Net/CLR
* Jython - interpretador do Python que é executado no JVM

Somente **CPython** é testado e suportado para o SDK do Python Azure e serviços do Azure como os Sites e Serviços de Nuvem. Recomendamos a versão 2.7 ou 3.4.

## Onde obter o Python?

Existem várias maneiras de obter o CPython:

* [Diretamente][] do site www.python.org
* De um distribuidor respeitável, como [www.continuum.io][], [www.enthought.com][] ou [www.activestate.com][]
* Criá-lo a partir do código-fonte!

A menos que você tenha uma necessidade específica, recomendamos as duas primeiras opções, conforme descrito abaixo.

## Instalação no Windows

Para o Windows, você pode usar o Web Platform Installer para agilizar a instalação. Que inclui o CPython do [www.python.org][].

* [Microsoft Azure SDK para Python 2.7][]
* [Microsoft Azure SDK para Python 3.4][]

**Nota:** no Windows Server, para baixar o instalador do WebPI, talvez você precise definir as configurações do IE ESC (Iniciar/Ferramentas administrativas/Gerenciador de servidores/Servidor local e clicar em **Configuração de Segurança Aprimorada do IE**, definindo-o como Desativado)


### Python 2,7

O instalador do WebPI fornece todo o que você precisa para desenvolver os aplicativos do Python Azure.

![como-instalar-o-webpi-do-python-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

Quando tiver concluído, você visualizará esta tela confirmando suas opções de instalação:

![como-instalar-o-webpi-do-python-27-2](./media/python-how-to-install/how-to-install-python-webpi-27-2.png)

Após a conclusão da instalação, digite `python` no prompt para ter certeza de que tudo funciona bem. Dependendo de como você o instalou, talvez seja necessário definir sua variável de "caminho" para localizar (a versão correta do) Python:

![como-instalar-o-python-no-win-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

Após a instalação, você deve ter o Python e as Bibliotecas de Cliente disponíveis no local padrão:

		C:\Python27\Lib\site-packages\azure


### Python 3.4

O instalador do WebPI fornece todo o que você precisa para desenvolver os aplicativos do Python Azure.

![como-instalar-o-webpi-do-python-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)

Quando tiver concluído, você visualizará esta tela confirmando suas opções de instalação:

![como-instalar-o-webpi-do-python-34-2](./media/python-how-to-install/how-to-install-python-webpi-34-2.png)

Após a conclusão da instalação, digite python no prompt para ter certeza de que tudo funcione bem. Dependendo de como você o instalou, talvez seja necessário definir sua variável de "caminho" para localizar (a versão correta do) Python:

![como-instalar-o-python-no-win-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

Após a instalação, você deve ter o Python e as Bibliotecas de Cliente disponíveis no local padrão:

		C:\Python34\Lib\site-packages\azure


### Obter mais paquetes

O [Índice de Pacote do Python][] (PyPI) tem uma seleção completa de bibliotecas do Python. Se você optou por instalar uma Distribuição, você já terá a maior parte dos bits interessantes para uma variedade de cenários, desde o desenvolvimento para a web até Computação Técnica.


### Python Tools para Visual Studio

O [Python Tools para Visual Studio][] (PTVS) é um plug-in gratuito/OSS da Microsoft que transforma o VS em um IDE completo para o Python:

![como-instalar-o-webpi-do-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

O uso do Python Tools para Visual Studio é opcional, mas recomendável, pois ele oferece suporte ao Projeto/Solução do Python e Web, depuração, criação de perfil, edição de Modelos e Intellisense.

O PTVS também torna fácil a implantação ao Microsoft Azure, com suporte para implementação em [Serviços de Nuvem][] e [Sites][].

O PTVS funciona com sua instalação existente do Visual Studio 2010, 2012 ou 2013. Para documentação, downloads e discussões, consulte [Ferramentas do Python Visual Studio no CodePlex][].

## Desinstalação no Windows

Os produtos WebPI do **Azure SDK para Python** não são aplicativos no sentido comum, mas, na verdade, um conjunto de produtos separados, como o Python 2.7/3.4 de 32 bits, bibliotecas de cliente do Azure para Python, etc., que estão reunidas. Uma consequência disso é que ele não possui nenhum desinstalador convencional próprio e, portanto, será necessário remover os programas que ele instalar individualmente no Painel de Controle do Windows.

Se você desejar reinstalar o **Azure SDK para Python**, basta abrir um prompt de comando do PowerShell como administrador e executar o seguinte comando:

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

e depois executar novamente o WebPI.

## Instalação no Linux e no MacOS

Python provavelmente já está instalado no computador de desenvolvimento. Você pode verificar isso digitando:

![como-instalar-o-python-no-linux](./media/python-how-to-install/how-to-install-python-linux-run.png)

Aqui podemos observar que esse Servidor Ubuntu 14.04 LTS VM executado no Azure tem o CPython 2.7.6 instalado. Se precisar atualizar, siga as instruções de upgrade do pacote recomendáveis do SO.

Para instalar as Bibliotecas de Cliente do Python Azure, use **pip** para obtê-las do **PyPI**.

Se você não tiver o **pip** instalado, use este comando para instalá-lo:

	curl https://bootstrap.pypa.io/get-pip.py | sudo python

O comando acima silenciosamente solicitará a senha raiz. Digite-a e pressione Enter.

Em seguida, use o comando `pip` para instalar as Bibliotecas Cliente do Python Azure:

	sudo pip install azure

Agora você deve visualizar as bibliotecas de cliente instaladas em **pacotes-do-site**. Mo MacOS:

![como-instalar-o-site-do-python-mac](./media/python-how-to-install/how-to-install-python-mac-site.png)

## Cenários do Python Azure para Linux e MacOS

Para Linux ou MacOS, estes são os principais cenários do Azure são suportados:

1. Usando os Serviços do Azure por meio das bibliotecas de cliente para Python

2. Executando o seu aplicativo em uma VM com Linux

3. Desenvolver e publicar sites do Azure usando Git

O primeiro cenário permite que você crie aplicativos da web avançados que aproveitam os recursos do Azure PaaS como [armazenamento de blobs][], [armazenamento de fila][], [armazenamento de tabela][], etc., por meio dos wrappers do Pythonic para as APIs REST do Azure. Eles funcionam de forma semelhante no Windows, Mac e Linux. Você também pode usar essas bibliotecas cliente de sua máquina de desenvolvimento local ou em uma VM do Linux em execução no Azure.

Para o cenário da VM, basta iniciar uma VM com Linux de sua escolha (Ubuntu, CentOS, Suse) e executar/gerenciar os itens desejados. Por exemplo, você pode executar [IPython][] REPL/notebook no seu computador com Windows/Mac/Linux e apontar o navegador para uma VM com Linux ou Windows de proc múltiplo que esteja executando o IPython Engine no Azure. Consulte o tutorial [IPython Notebook no Azure][] para obter mais informações.

Para obter informações sobre como configurar uma VM do Linux, consulte o tutorial [Criar uma Máquina Virtual Executando o Linux][].

Usando a implantação do Git, você pode desenvolver um aplicativo da Web Python e publicá-lo em um site do Azure de qualquer sistema operacional. Quando você envia seu repositório para o Azure, ele criará automaticamente um ambiente virtual e o pip instalará os pacotes necessários.

Para obter mais informações sobre como desenvolver e publicar Sites do Azure, consulte os tutoriais [Criando Sites com Django][], [Criando Sites com Bottle][] e [Criando sites com Flash][]. Para obter mais informações sobre como usar qualquer estrutura compatível com WSGI, consulte [Configurando Python com os Sites do Azure][].


## Software adicional e recursos:

* [Distribuição do Python de Análise de Continuidade][]
* [Distribuição do Python de Enthought][]
* [Distribuição do Python de ActiveState][]
* [SciPy – um conjunto de Bibliotecas científicas para Python][]
* [NumPy – uma biblioteca de numéricos para o Python][]
* [Projeto Django – uma estrutura da Web/CMS sólida][]
* [IPython – um REPL/Notebook avançado para o Python][]
* [IPython Notebook no Azure][]
* [Python Tools para Visual Studio no CodePlex][]


[Distribuição do Python de Análise de Continuidade]: http://continuum.io
[Distribuição do Python de Enthought]: http://www.enthought.com
[Distribuição do Python de ActiveState]: http://www.activestate.com
[www.python.org]: http://www.python.org
[Diretamente]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy – um conjunto de Bibliotecas científicas para Python]: http://www.scipy.org
[NumPy – uma biblioteca de numéricos para o Python]: http://www.numpy.org
[Projeto Django – uma estrutura da Web/CMS sólida]: http://www.djangoproject.com
[IPython – um REPL/Notebook avançado para o Python]: http://ipython.org
[IPython]: http://ipython.org
[IPython Notebook no Azure]: virtual-machines-python-ipython-notebook.md
[Serviços de Nuvem]: cloud-services-python-ptvs.md
[Sites]: web-sites-python-ptvs-django-mysql.md
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Ferramentas do Python Visual Studio no CodePlex]: http://pytools.codeplex.com
[Python Tools para Visual Studio no CodePlex]: http://pytools.codeplex.com
[Índice de Pacote do Python]: http://pypi.python.org/pypi
[Microsoft Azure SDK para Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Microsoft Azure SDK para Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Criar uma Máquina Virtual Executando o Linux]: virtual-machines-linux-tutorial.md
[Criando Sites com Django]: web-sites-python-create-deploy-django-app.md
[Criando Sites com Bottle]: web-sites-python-create-deploy-bottle-app.md
[Criando sites com Flash]: web-sites-python-create-deploy-flask-app.md
[Configurando Python com os Sites do Azure]: web-sites-python-configure.md
[armazenamento de tabela]: storage-python-how-to-use-table-storage.md
[armazenamento de fila]: storage-python-how-to-use-queue-storage.md
[armazenamento de blobs]: storage-python-how-to-use-blob-storage.md

<!---HONumber=August15_HO6-->