<properties urlDisplayName="Install Python" pageTitle="Instalar o Python e o SDK - Azure" metaKeywords="Azure Python SDK" description="Learn how to install Python and the SDK to use with Azure." metaCanonical="" services="" documentationCenter="Python" title="Installing Python and the SDK" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="11/10/2014" ms.author="huvalo" />




# Instalando o Python e o SDK
O Python é muito fácil de ser instalado no Windows e é fornecido pré-instalado no Mac e no Linux.  Este guia o orientará na instalação e na preparação de seu computador para o uso com o Azure.  Este guia o ajudará com o seguinte:

* Novidades no SDK do Python Azure
* Qual Python e qual versão usar
* Instalação no Windows
* Instalação no Mac e no Linux

## Novidades no SDK do Python Azure

O SDK do Azure para o Python inclui componentes que permitem que você desenvolva, implante e gerencie aplicativos do Python para o Azure. O SDK do Azure para o Python inclui especificamente o seguinte:

* **As Bibliotecas de Cliente do Python para o Azure**. Essas bibliotecas de classe fornecem uma interface para acessar recursos do Azure, como Serviços de Gerenciamento de Dados e Serviços de Nuvem.  
* **Os Emuladores do Azure (somente Windows)**. Os emuladores de computação e armazenamento são emuladores locais de Serviços de Nuvem e Serviços de Gerenciamento de Dados que permitem que você teste um aplicativo localmente. Os Emuladores do Azure são executados somente no Windows.


Os cenários principais para esta versão são:

* **Windows**: Serviço de nuvem, Site - por exemplo um site Django usando Funções Web
* **Mac/Linux**: IaaS - Execute o que quiser em uma VM; Utilize os Serviços do Azure por meio do Python

## Qual Python e qual versão usar

Existem vários tipos de interpretadores do Python disponíveis - alguns exemplos incluem:

* CPython - o interpretador do Python padrão e mais geralmente usado
* IronPython -interpretador do Python que é executado no .Net/CLR
* Jython - interpretador do Python que é executado no JVM

Para os fins desta versão, apenas o **CPython** é testado e tem suporte.  Recomendamos a versão 2.7 ou 3.4.

## Onde obter o Python?

Existem várias maneiras de obter o CPython:

* Diretamente do site www.python.org
* De um distribuidor respeitável, como www.enthought.com ou www.ActiveState.com, www.continuum.io
* Criá-lo a partir do código-fonte!

A menos que você tenha uma necessidade específica, recomendamos as duas primeiras opções, conforme descrito abaixo.

## Instalação no Windows

Para o Windows, você pode usar o Web Platform Installer para agilizar a instalação. Isso inclui o CPython do www.python.org.

* [Microsoft Azure SDK para Python 2.7][]
* [Microsoft Azure SDK para Python 3.4][]

**Observação:** No Windows Server, para baixar o instalador do WebPI, talvez você precise definir as configurações do IE ESC (Iniciar/Ferramentas administrativas/Gerenciador de servidores/Servidor local e clicar em **Configuração de Segurança Aprimorada do IE**, definindo-o como Desativado)


### Python 2,7

O instalador do WebPI fornece todo o que você precisa para desenvolver os aplicativos do Python Azure.

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

Quando tiver concluído, você visualizará esta tela confirmando suas opções de instalação:

![how-to-install-python-webpi-27-2](./media/python-how-to-install/how-to-install-python-webpi-27-2.png)

Após a conclusão da instalação, digite python no prompt para ter certeza de que tudo funcione bem.  Dependendo de como você o instalou, talvez seja necessário definir sua variável de "caminho" para localizar (a versão correta do) Python:

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

Após a instalação, você deve ter o Python e as Bibliotecas de Cliente disponíveis no local padrão:

		C:\Python27\Lib\site-packages\azure


### Python 3.4

O instalador do WebPI fornece todo o que você precisa para desenvolver os aplicativos do Python Azure.

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)
 
Quando tiver concluído, você visualizará esta tela confirmando suas opções de instalação:

![how-to-install-python-webpi-34-2](./media/python-how-to-install/how-to-install-python-webpi-34-2.png)

Após a conclusão da instalação, digite python no prompt para ter certeza de que tudo funcione bem.  Dependendo de como você o instalou, talvez seja necessário definir sua variável de "caminho" para localizar (a versão correta do) Python:

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

Após a instalação, você deve ter o Python e as Bibliotecas de Cliente disponíveis no local padrão:

		C:\Python34\Lib\site-packages\azure


### Obter mais paquetes

Embora esta versão destina-se principalmente aos Aplicativos Web, fique à vontade para procurar o [Python Package Index (PyPI)][] para obter uma seleção avançada de outros softwares.  Se você optou por instalar uma Distribuição, você já terá a maior parte dos bits interessantes para uma variedade de cenários, desde o desenvolvimento para a Web até Computação Técnica.


### Python Tools para Visual Studio

O Python Tools para Visual Studio (PTVS) é um plug-in gratuito de OSS da Microsoft que transforma o VS em um IDE do Python completo:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

O uso do Python Tools para Visual Studio é opcional, mas recomendável, pois ele oferece suporte ao Projeto/Solução do Python e do Django, depuração, criação de perfil, edição de Modelos e Intellisense.

O PTVS também torna fácil a implantação ao Microsoft Azure, com suporte para [implementação a Serviços de Nuvem e Sites][].

O PTVS funciona com sua instalação existente do Visual Studio 2010, 2012 ou 2013.  Para obter mais informações e para downloads, consulte [Python Tools para Visual Studio no CodePlex][].  

## Desinstalação no Windows

OS produtos WebPI **Azure SDK para Python** não são aplicativos no sentido comum, mas, na verdade, um conjunto de produtos separados, como o Python 2.7/3.4 de 32 bits, bibliotecas de cliente do Azure para Python etc., que estão reunidas.  Uma consequência disso é que ele não possui nenhum desinstalador convencional próprio e, portanto, será necessário remover os programas que ele instalar individualmente no Painel de Controle do Windows.  

Se você desejar reinstalar o **Azure SDK para Python**, basta abrir um prompt de comando do PowerShell como administrador e executar o seguinte comando:

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

e depois executar novamente o WebPI.

## Instalação no Linux e no MacOS

O Python já está provavelmente instalado em seu computador de Des.  Você pode verificar isso digitando:

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

Aqui podemos observar que esse Servidor Ubuntu 14.04 LTS VM executado no Azure tem o CPython 2.7.6 instalado. Se precisar atualizar, siga as instruções de upgrade do pacote recomendáveis do SO.

Para instalar as Bibliotecas de Cliente do Python Azure, use **pip** para obtê-las do **PyPI**:

	curl https://bootstrap.pypa.io/get-pip.py | sudo python
	
O comando acima silenciosamente solicitará a senha raiz. Digite-a e pressione Enter.  Avançar:
	
	sudo pip install azure

Agora você deve visualizar as bibliotecas de cliente instaladas em **site-packages**.  Mo MacOS:

![how-to-install-python-mac-site](./media/python-how-to-install/how-to-install-python-mac-site.png)

Durante o desenvolvimento do Mac/Linux, existem dois cenários principais suportados:

1. Usando os Serviços do Azure por meio das bibliotecas de cliente para Python

2. Executando o seu aplicativo em uma VM com Linux

O primeiro cenário permite que você crie aplicativos Web avançados que aproveitam os recursos do Azure PaaS como armazenamento de blobs, filas etc., por meio dos invólucros do Pythonic para as APIs REST do Azure. Eles funcionam de forma semelhante no Windows, Mac e Linux.  Consulte os Tutoriais e os Guias de Instruções para obter exemplos.  Você também pode usar essas bibliotecas de cliente por meio de uma VM com Linux.

Para o cenário da VM, basta iniciar uma VM com Linux de sua escolha (Ubuntu, CentOS, Suse) e executar/gerenciar os itens desejados.  Por exemplo, você pode executar [IPython](http://ipython.org) REPL/notebook no seu computador com Windows/Mac/Linux e apontar o navegador para uma VM com Linux ou Windows de proc múltiplo que esteja executando o IPython Engine no Azure. Para obter mais informações sobre a instalação do IPython, consulte seu tutorial.

Para obter informações sobre como configurar uma VM com Linux, consulte a [seção Gerenciamento do Linux.](/pt-br/manage/linux/)

## Software adicional e recursos:

* [Distribuição do Python pelo Enthought][]
* [Distribuição do Python pelo ActiveState][]
* [SciPy - Um conjunto de Bibliotecas científicas do Python][]
* [NumPy - Uma biblioteca de numéricos para o Python][]
* [Projeto Django - Uma estrutura de web/CMS madura][]
* [IPython - Um REPL/Notebook avançado para o Python][]
* [IPython Notebook no Azure][]
* [Python Tools para Visual Studio no CodePlex][]
* [Virtualenv][]


[Distribuição do Python pelo Enthought]: http://www.enthought.com 
[Distribuição do Python pelo ActiveState]: http://www.activestate.com
[SciPy - Um conjunto de Bibliotecas científicas do Python]: http://www.scipy.org
[NumPy - Uma biblioteca de numéricos para o Python]: http://www.numpy.org
[Projeto Django - Uma estrutura de web/CMS madura]: http://www.djangoproject.com 
[IPython - Um REPL/Notebook avançado para o Python]: http://ipython.org
[IPython Notebook no Azure]: /pt-br/documentation/articles/virtual-machines-python-ipython-notebook
[implantação a Serviços de Nuvem e Sites]: /pt-br/documentation/articles/cloud-services-web-sites-python-django-app-with-ptvs/
[Python Tools para Visual Studio no CodePlex]: http://pytools.codeplex.com 
[Índice de Pacote do Python (PyPI)]: http://pypi.python.org/pypi
[Virtualenv]: http://pypi.python.org/pypi/virtualenv 
[Microsoft Azure SDK para Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Microsoft Azure SDK para Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409
[Configurando uma VM com Linux por meio do portal do Azure]: ../../../shared/chunks/create-and-configure-opensuse-vm-in-portal
[Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux]: ../../shared/chunks/crossplat-cmd-tools

