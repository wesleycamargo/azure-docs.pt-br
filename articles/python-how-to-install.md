<properties
	pageTitle="Instalar o Python e o SDK - Azure"
	description="Saiba como instalar o Python e o SDK para usar com o Azure."
	services=""
	documentationCenter="python"
	authors="lmazuel"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="lmazuel"/>

# Instalando o Python e o SDK

O Python é fácil de ser instalado no Windows e é fornecido pré-instalado no Mac, no Linux e no [Bash para Windows](https://msdn.microsoft.com/commandline/wsl/about). Este guia o orientará na instalação e na preparação de seu computador para o uso com o Azure.

## Novidades no SDK do Python Azure

O SDK do Azure para o Python inclui componentes que permitem que você desenvolva, implante e gerencie aplicativos do Python para o Azure. O SDK do Azure para o Python inclui especificamente o seguinte:

* **Bibliotecas de gerenciamento**. Essas bibliotecas de classes fornecem uma interface para gerenciar recursos do Azure, como as contas de armazenamento e as máquinas virtuais.

* **Bibliotecas de tempo de execução**. Essas bibliotecas de classes fornecem uma interface para acessar recursos do Azure, como o armazenamento e o barramento de serviço.

## Qual Python e qual versão usar

Existem vários tipos de interpretadores do Python disponíveis - alguns exemplos incluem:

* CPython - o interpretador do Python padrão e mais geralmente usado
* PyPy – implementação alternativa rápida e compatível para CPython
* IronPython -interpretador do Python que é executado no .Net/CLR
* Jython – interpretador do Python que é executado na Máquina Virtual Java

O **CPython** v2.7 ou v3.3+ e o PyPy 5.4.0 são testados e dão suporte ao SDK do Azure para Python.

## Onde obter o Python?

Existem várias maneiras de obter o CPython:

* [Diretamente][] do site www.python.org
* De um distribuidor respeitável, como [www.continuum.io][], [www.enthought.com][] ou [www.activestate.com][]
* Criá-lo a partir do código-fonte!

A menos que você tenha uma necessidade específica, recomendamos as duas primeiras opções.

## Instalação do SDK no Windows, Linux e MacOS (apenas bibliotecas de cliente)

Se já tiver o Python instalado, você poderá usar pip para instalar um pacote de todas as bibliotecas de cliente em seu ambiente Python 2.7 ou Python 3.3+ existente. Isso baixará os pacotes do [Índice de Pacotes do Python][] (PyPI).

Você pode precisar de direitos de administrador:

- Para Linux e MacOS, use o comando `sudo`: `sudo pip install azure-mgmt-compute`.
- Para Windows: abra o PowerShell/prompt de comando como administrador

Você pode instalar cada biblioteca individualmente para cada serviço do Azure:

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Pacotes de preview podem ser instalados usando o sinalizador `--pre`:

```console
   $ pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

Você também pode instalar um conjunto de bibliotecas do Azure em uma única linha usando o pacote meta `azure`. Como nem todos os pacotes neste pacote meta estão publicados como estáveis, o pacote meta `azure` ainda está em preview. No entanto, os pacotes essenciais podem ser considerados "estáveis" no momento do ponto de vista da integridade e da qualidade do código.
- Ela será oficialmente rotulada como tal em sincronia com outras linguagens assim que possível. Não planejamos outras alterações importantes até então.

Como se trata de uma versão de preview, você precisa usar o sinalizador `--pre`:

```console
   $ pip install --pre azure
```
   
ou diretamente

```console
   $ pip install azure==2.0.0rc6
```

## Obter mais paquetes

O [Índice de Pacote do Python][] (PyPI) tem uma seleção completa de bibliotecas do Python. Caso tenha optado por instalar uma Distribuição, você já terá a maioria das partes interessantes para vários cenários, do desenvolvimento para Web até a Computação Técnica.


## Python Tools para Visual Studio

O [PTVS][] (Ferramentas Python para o Visual Studio) é um plug-in gratuito/OSS da Microsoft que transforma o VS em um IDE completo para Python:

![como-instalar-o-webpi-do-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

O uso do PTVS é opcional, mas recomendável, pois ele dá suporte a Solução/Projeto Web e Python, depuração, criação de perfil, janela interativa, edição de Modelos e Intellisense.

O PTVS também torna fácil a implantação ao Microsoft Azure, com suporte para implementação em [Serviços de Nuvem][] e [Sites][].

O PTVS funciona com sua instalação existente do Visual Studio 2013 ou 2015. Para documentação, downloads e discussões, consulte [Ferramentas do Python Visual Studio].

## Cenários do Python Azure para Linux e MacOS

Para Linux ou MacOS, os principais cenários do Azure têm suporte:

1. Usando os Serviços do Azure por meio das bibliotecas de cliente para Python

2. Executando o seu aplicativo em uma VM com Linux

3. Desenvolver e publicar sites do Azure usando Git

O primeiro cenário permite que você crie aplicativos Web avançados que aproveitam recursos do Azure PaaS como [armazenamento de blobs][], [armazenamento de filas][], [armazenamento de tabela][] etc., por meio de wrappers do Pythonic para as APIs REST do Azure. Eles funcionam de forma idêntica no Windows, Mac e Linux. Você também pode usar essas bibliotecas cliente de sua máquina de desenvolvimento local ou em uma VM do Linux em execução no Azure.

Para o cenário da VM, basta iniciar uma VM com Linux de sua escolha (Ubuntu, CentOS, Suse) e executar/gerenciar os itens desejados. Por exemplo, você pode executar [IPython][] REPL/notebook no seu computador com Windows/Mac/Linux e apontar o navegador para uma VM com Linux ou Windows de proc múltiplo que esteja executando o IPython Engine no Azure. Consulte o tutorial [IPython Notebook no Azure][] para obter mais informações.

Para obter informações sobre como configurar uma VM do Linux, consulte o tutorial [Criar uma Máquina Virtual Executando o Linux][].

Usando a implantação do Git, você pode desenvolver um aplicativo da Web Python e publicá-lo em um site do Azure de qualquer sistema operacional. Quando você envia seu repositório para o Azure, ele criará automaticamente um ambiente virtual e o pip instalará os pacotes necessários.

Para obter mais informações sobre como desenvolver e publicar Sites do Azure, consulte os tutoriais [Criando Sites com Django][], [Criando Sites com Bottle][] e [Criando sites com Flash][]. Para obter mais informações gerais sobre como usar qualquer estrutura compatível com WSGI, consulte [Configurando Python com os Sites do Azure][].


## Software adicional e recursos:

* [SDK do Azure para Python no ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [SDK do Azure para Python no Github](https://github.com/Azure/azure-sdk-for-python)
* [Exemplos oficiais do Azure para Python](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Distribuição do Python de Análise de Continuidade][]
* [Distribuição do Python de Enthought][]
* [Distribuição do Python de ActiveState][]
* [SciPy – um conjunto de Bibliotecas científicas para Python][]
* [NumPy – uma biblioteca de numéricos para o Python][]
* [Projeto Django – uma estrutura da Web/CMS sólida][]
* [IPython – um REPL/Notebook avançado para o Python][]
* [IPython Notebook no Azure][]
* [Python Tools para Visual Studio no GitHub][]
* [Centro de desenvolvedores do Python](/develop/python/)

[Distribuição do Python de Análise de Continuidade]: http://continuum.io
[Distribuição do Python de Enthought]: http://www.enthought.com
[Distribuição do Python de ActiveState]: http://www.activestate.com
[Diretamente]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy – um conjunto de Bibliotecas científicas para Python]: http://www.scipy.org
[NumPy – uma biblioteca de numéricos para o Python]: http://www.numpy.org
[Projeto Django – uma estrutura da Web/CMS sólida]: http://www.djangoproject.com
[IPython – um REPL/Notebook avançado para o Python]: http://ipython.org
[IPython]: http://ipython.org
[IPython Notebook no Azure]: virtual-machines-linux-jupyter-notebook.md
[Serviços de Nuvem]: cloud-services-python-ptvs.md
[Sites]: web-sites-python-ptvs-django-mysql.md
[Ferramentas do Python Visual Studio]: http://aka.ms/ptvs
[PTVS]: http://aka.ms/ptvs
[Python Tools para Visual Studio no GitHub]: https://github.com/microsoft/ptvs
[Índice de Pacote do Python]: http://pypi.python.org/pypi
[Índice de Pacotes do Python]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Criar uma Máquina Virtual Executando o Linux]: virtual-machines-linux-quick-create-cli.md
[Criando Sites com Django]: web-sites-python-create-deploy-django-app.md
[Criando Sites com Bottle]: web-sites-python-create-deploy-bottle-app.md
[Criando sites com Flash]: web-sites-python-create-deploy-flask-app.md
[Configurando Python com os Sites do Azure]: web-sites-python-configure.md
[armazenamento de tabela]: storage-python-how-to-use-table-storage.md
[armazenamento de filas]: storage-python-how-to-use-queue-storage.md
[armazenamento de blobs]: storage-python-how-to-use-blob-storage.md

<!---HONumber=AcomDC_0914_2016-->