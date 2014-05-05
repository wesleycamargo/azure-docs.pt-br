<properties linkid="develop-python-install-python" urlDisplayName="Instalar o Python" pageTitle="Instalar o Python e o SDK - Azure" metaKeywords="Azure Python SDK" description="Aprenda a instalar o Python e o SDK para usá-los com o Azure." metaCanonical="" services="" documentationCenter="Python" title="Instalando o Python e o SDK" authors="" solutions="" manager="" editor="" />




# Instalando o Python e o SDK
O Python é muito fácil de ser instalado no Windows e é fornecido pré-instalado no Mac e no Linux.  Este guia o orientará na instalação e na preparação de seu computador para o uso com o Azure.  Este guia o ajudará com o seguinte:

* Novidades no SDK do Python Azure
* Qual Python e qual versão usar
* Instalação no Windows
* Instalação no Mac e no Linux

## Novidades no SDK do Python Azure

O SDK do Azure para o Python inclui componentes que permitem que você desenvolva, implante e gerencie aplicativos do Python para o Azure. O SDK do Azure para o Python inclui especificamente o seguinte:

* **As Bibliotecas de Cliente do Python para o Azure**. Essas bibliotecas de classe fornecem uma interface para acessar recursos do Azure, como Serviços de Gerenciamento de Dados e Serviços de Nuvem.  
* **As Ferramentas de Linha de Comando do Azure para Mac e Linux**. Esse é um conjunto de ferramentas de linha de comando para implantar e gerenciar serviços do Azure, como Sites do Azure e Máquinas Virtuais do Azure. Essas ferramentas funcionam em qualquer plataforma, incluindo Mac, Linux e Windows.
* **PowerShell para Azure (somente Windows)**. Esse é um conjunto de cmdlets do PowerShell para implantar e gerenciar serviços do Azure, como Serviços de Nuvem e Máquinas Virtuais.
* **Os Emuladores do Azure (somente Windows)**. Os emuladores de computação e armazenamento são emuladores locais de Serviços de Nuvem e Serviços de Gerenciamento de Dados que permitem que você teste um aplicativo localmente. Os Emuladores do Azure são executados somente no Windows.


Os cenários principais para esta versão são:

* **Windows**: Serviço de Nuvem -- por exemplo, um site do Django que use Funções da web
* **Mac/Linux**: IaaS -- Execute o que quiser em uma VM; Utilize os Serviços do Azure por meio do Python

## Qual Python e qual versão usar

Existem vários tipos de interpretadores do Python disponíveis - alguns exemplos incluem:

* CPython - o interpretador do Python padrão e mais geralmente usado
* IronPython - interpretador do Python que é executado no .Net/CLR
* Jython - interpretador do Python que é executado no JVM

Para os fins desta versão, apenas o **CPython** é testado e suportado. Também é recomendável pelo menos a versão 2.7. **O suporte para o IronPython** também será adicionado em um futuro próximo.

## Onde obter o Python?

Existem várias maneiras de obter o CPython:

* Diretamente do site www.python.org
* De um distribuidor respeitável, como www.enthought.com ou www.ActiveState.com
* Criá-lo a partir do código-fonte!

A menos que você tenha uma necessidade específica, recomendamos as duas primeiras opções, conforme descrito abaixo.

## Instalação no Windows

Para o Windows, você pode usar o [instalador do WebPI] fornecido do principal Centro de Desenvolvimento do Python para agilizar a instalação (ela capturará o CPython do site www.python.org).

**Nota:** No Windows Server, para baixar o instalador do WebPI, talvez você precise definir as configurações do IE ESC (Iniciar/Ferramentas administrativas/Gerenciador de servidores e clicar em **Configurar IE ESC**, definindo-o para Desativado)

![como-instalar-o-webpi-do-Python-1](./media/python-how-to-install/how-to-install-python-webpi-1.png)

O instalador do WebPI fornece tudo o que você precisa para os aplicativos do Python Azure, bem como o suporte específico para os aplicativos do Django:


![como-instalar-o-webpi-do-Python-2](./media/python-how-to-install/how-to-install-python-webpi-2.png)
 

Quando tiver concluído, você visualizará esta tela confirmando suas opções de instalação:


![como-instalar-o-webpi-do-python-3](./media/python-how-to-install/how-to-install-python-webpi-3.png)


Após a conclusão da instalação, digite "python" no prompt para ter certeza de que tudo funcione bem.  Dependendo de como você o instalou, talvez seja necessário definir sua variável de "caminho" para localizar (a versão correta do) Python:

![como-instalar-o-python-no-win](./media/python-how-to-install/how-to-install-python-win-run.png)


Embora esta versão destina-se principalmente aos Aplicativos da Web com o Django, fique à vontade para procurar o [Índice de Pacote do Python (PyPI)][] para obter uma seleção avançada de outros softwares.  Se você optou por instalar uma Distribuição, você já terá a maior parte dos bits interessantes para uma variedade de cenários, desde o desenvolvimento para a web até Computação Técnica.

Para visualizar quais pacotes do Python estão instalados em **pacotes-do-site**, digite o seguinte para encontrar seu local:

![como-instalar-o-site-do-python-win](./media/python-how-to-install/how-to-install-python-win-site.png)

Isso lhe fornecerá uma lista dos itens que já foram instalados em seu sistema.

Após a instalação, você deve ter o Python, o Django e as Bibliotecas de Cliente disponíveis no local padrão:

		C:\Python27\Lib\site-packages\windowsazure
		C:\Python27\Lib\site-packages\django

### Python Tools para Visual Studio

O Python Tools para Visual Studio é um plug-in gratuito/de OSS da Microsoft que transforma o VS em um IDE do Python completo:

![como-instalar-o ptvs-do-python](./media/python-how-to-install/how-to-install-python-ptvs.png)

O uso do Python Tools para Visual Studio é opcional mas recomendável, pois ele oferece suporte ao Projeto/Solução do Python e do Django, depuração, criação de perfil, edição de Modelos e Intellisense, implantação para nuvem, etc. Esse complemento funciona com sua instalação existente do VS2010.  Se você não tiver o VS2010, o WebPI instalará o Shell Integrado gratuito + PTVS que oferecem basicamente um IDE do "VS Python Express" **totalmente gratuito**.  Para obter mais informações, consulte [Python Tools para Visual Studio no CodePlex][].  

Nota: embora o plug-in do PTVS seja pequeno, o Shell Integrado aumentará os tempos de download. Atualmente, a versão do Shell Integrado também não oferece suporte ao recurso "Adicionar Projeto de Implantação do Azure".

## Desinstalação no Windows

O produto WebPI do **Azure SDK para Python de junho de 2012** não é um aplicativo no sentido comum, mas, na verdade, um conjunto de produtos separados, como o Python 2.7 de 32 bits, as APIs cliente do Azure para Python, Django, etc., que estão reunidas.  Uma consequência disso é que ele não possui nenhum desinstalador convencional próprio e, portanto, será necessário remover os programas que ele instalar individualmente no Painel de Controle do Windows.  

Se você desejar reinstalar o **Azure SDK para Python**, basta abrir um prompt de comando do PowerShell como administrador e executar o seguinte comando:

	rm -force "HKLM:\SOFTWARE\Microsoft\Windows Azure SDK for Python - June 2012"

e depois executar novamente o WebPI.

## Instalação no Linux e no MacOS

O Python já está provavelmente instalado em seu computador de Des.  Você pode verificar isso digitando:

![como-instalar-o-python-no-linux](./media/python-how-to-install/how-to-install-python-linux-run.png)

Aqui podemos observar que essa VM do Azure Suse tem o CPython 2.7.2 instalado, o que é ótimo para executar os tutoriais do Azure e amostras do Django. Se precisar atualizar, siga as instruções de upgrade do pacote recomendáveis do SO.  No entanto, observe que em geral é melhor deixar o sistema Python no estado em que se encontra (pois outras pessoas podem depender dessa versão) e instalar a versão mais recente por meio do [Virtualenv][]

Para instalar as Bibliotecas de Cliente do Python Azure, use **pip** para obtê-las do **PyPI**:

	curl https://raw.github.com/pypa/pip/master/contrib/get-pip.py | sudo python
	
O comando acima silenciosamente solicitará a senha raiz. Digite-a e pressione Enter.  Avançar:
	
	sudo /usr/local/bin/pip-2.7 install azure

Agora você deve visualizar as bibliotecas de cliente instaladas em **pacotes-do-site**.  Mo MacOS:

![Pacotes-do-site do MacOS](./media/python-how-to-install/how-to-install-python-mac-site.png)

Durante o desenvolvimento do Mac/Linux, existem dois cenários principais suportados para essa versão:

1. Usando os Serviços do Azure por meio das bibliotecas de cliente para Python

2. Executando o seu aplicativo em uma VM com Linux

O primeiro cenário permite que você crie aplicativos da web avançados que aproveitam os recursos do Azure PaaS como armazenamento de blobs, filas, etc., por meio dos invólucros do Pythonic para as APIs REST do Azure.  Eles funcionam de forma semelhante no Windows, Mac e Linux.  Consulte os Tutoriais e os Guias de Instruções para obter exemplos.  Você também pode usar essas bibliotecas de cliente a partir de uma VM com Linux.

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
[IPython Notebook no Azure]: http://windowsazure.com/pt-br/documentation/articles/virtual-machines-python-ipython-notebook
[Python Tools para Visual Studio no CodePlex]: http://pytools.codeplex.com 
[Índice de Pacote do Python (PyPI)]: http://pypi.python.org/pypi
[Virtualenv]: http://pypi.python.org/pypi/virtualenv 
[Instalador do WebPI]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Configurando uma VM com Linux por meio do portal do Azure]: ../../../shared/chunks/create-and-configure-opensuse-vm-in-portal
[Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux]: ../../shared/chunks/crossplat-cmd-tools


