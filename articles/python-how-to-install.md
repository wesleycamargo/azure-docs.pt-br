<properties linkid="develop-python-install-python" urlDisplayName="Install Python" pageTitle="Install Python and the SDK - Azure" metaKeywords="Azure Python SDK" description="Learn how to install Python and the SDK to use with Azure." metaCanonical="" services="" documentationCenter="Python" title="Installing Python and the SDK" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Instalando o Python e o SDK

O Python é muito fácil de ser instalado no Windows e é fornecido pré-instalado no Mac e no Linux. Este guia o orientará na instalação e na preparação de seu computador para o uso com o Azure. Este guia o ajudará com o seguinte:

-   Novidades no SDK do Python Azure
-   Qual Python e qual versão usar
-   Instalação no Windows
-   Instalação no Mac e no Linux

## Novidades no SDK do Python Azure

O SDK do Azure para o Python inclui componentes que permitem que você desenvolva, implante e gerencie aplicativos do Python para o Azure. O SDK do Azure para o Python inclui especificamente o seguinte:

-   **As Bibliotecas de Cliente do Python para o Azure**. Essas bibliotecas de classe fornecem uma interface para acessar recursos do Azure, como Serviços de Gerenciamento de Dados e Serviços de Nuvem.
-   **Os Emuladores do Azure (somente Windows)**. Os emuladores de computação e armazenamento são emuladores locais de Serviços de Nuvem e Serviços de Gerenciamento de Dados que permitem que você teste um aplicativo localmente. Os Emuladores do Azure são executados somente no Windows.

Os cenários principais para esta versão são:

-   **Windows**: Serviço de nuvem, Site - por exemplo um site Django usando Funções Web
-   **Mac/Linux**: IaaS -Execute o que quiser em uma VM; Utilize os Serviços do Azure por meio do Python

## Qual Python e qual versão usar

Existem vários tipos de interpretadores do Python disponíveis - alguns exemplos incluem:

-   CPython - o interpretador do Python padrão e mais geralmente usado
-   IronPython -interpretador do Python que é executado no .Net/CLR
-   Jython - interpretador do Python que é executado no JVM

Para os fins desta versão, apenas o **CPython** é testado e suportado. Recomendamos a versão 2.7 ou 3.4.

## Onde obter o Python?

Existem várias maneiras de obter o CPython:

-   Diretamente do site www.python.org
-   De um distribuidor respeitável, como www.enthought.com ou www.ActiveState.com, www.continuum.io
-   Criá-lo a partir do código-fonte!

A menos que você tenha uma necessidade específica, recomendamos as duas primeiras opções, conforme descrito abaixo.

## Instalação no Windows

Para o Windows, você pode usar o [instalador do WebPI][instalador do WebPI] fornecido do principal Centro de Desenvolvimento do Python para agilizar a instalação (ela capturará o CPython do site www.python.org).

**Observação:** No Windows Server, para baixar o instalador do WebPI, talvez você precise definir as configurações do IE ESC (Iniciar/Ferramentas administrativas/Gerenciador de servidores e clicar em **Configurar IE ESC**, definindo-o para Desativado)

Você pode escolher instalar o Python 2.7 e/ou Python 3.4 usando o WebPI.

### Python 2.7

O instalador do WebPI fornece todo o que você precisa para desenvolver os aplicativos do Python Azure.

![como-instalar-o-webpi-do-python-27-1][como-instalar-o-webpi-do-python-27-1]

Quando tiver concluído, você visualizará esta tela confirmando suas opções de instalação:

![como-instalar-o-webpi-do-python-27-2][como-instalar-o-webpi-do-python-27-2]

Após a conclusão da instalação, digite "python" no prompt para ter certeza de que tudo funcione bem. Dependendo de como você o instalou, talvez seja necessário definir sua variável de "caminho" para localizar (a versão correta do) Python:

![como-instalar-o-python-no-win-27][como-instalar-o-python-no-win-27]

Após a instalação, você deve ter o Python e as Bibliotecas de Cliente disponíveis no local padrão:

        C:\Python27\Lib\site-packages\azure

### Python 3.4

O instalador do WebPI fornece todo o que você precisa para desenvolver os aplicativos do Python Azure.

![como-instalar-o-webpi-do-python-34-1][como-instalar-o-webpi-do-python-34-1]

Quando tiver concluído, você visualizará esta tela confirmando suas opções de instalação:

![como-instalar-o-webpi-do-python-34-2][como-instalar-o-webpi-do-python-34-2]

Após a conclusão da instalação, digite "python" no prompt para ter certeza de que tudo funcione bem. Dependendo de como você o instalou, talvez seja necessário definir sua variável de "caminho" para localizar (a versão correta do) Python:

![como-instalar-o-python-no-win-34][como-instalar-o-python-no-win-34]

Após a instalação, você deve ter o Python e as Bibliotecas de Cliente disponíveis no local padrão:

        C:\Python34\Lib\site-packages\azure

### Obter mais paquetes

Embora esta versão destina-se principalmente aos Aplicativos da Web, fique à vontade para procurar o [Índice de Pacote do Python (PyPI)][Índice de Pacote do Python (PyPI)] para obter uma seleção avançada de outros softwares. Se você optou por instalar uma Distribuição, você já terá a maior parte dos bits interessantes para uma variedade de cenários, desde o desenvolvimento para a web até Computação Técnica.

### Python Tools para Visual Studio

O Python Tools para Visual Studio (PTVS) é um plug-in gratuito de OSS da Microsoft que transforma o VS em um IDE do Python completo:

![como-instalar-o-webpi-do-ptvs][como-instalar-o-webpi-do-ptvs]

O uso do Python Tools para Visual Studio é opcional, mas recomendável, pois ele oferece suporte ao Projeto/Solução do Python e do Django, depuração, criação de perfil, edição de Modelos e Intellisense.

O PTVS também torna fácil a implantação ao Microsoft Azure, com suporte para [implementação a Serviços de Nuvem e Sites][implementação a Serviços de Nuvem e Sites].

O PTVS funciona com sua instalação existente do Visual Studio 2010, 2012 ou 2013. Para obter mais informações e para downloads, consulte [Python Tools para Visual Studio no CodePlex][Python Tools para Visual Studio no CodePlex].

## Desinstalação no Windows

Os produtos WebPI do **Azure SDK para Python** não são aplicativos no sentido comum, mas, na verdade, um conjunto de produtos separados, como o Python 2.7/3.4 de 32 bits, bibliotecas de cliente do Azure para Python, etc., que estão reunidas. Uma consequência disso é que ele não possui nenhum desinstalador convencional próprio e, portanto, será necessário remover os programas que ele instalar individualmente no Painel de Controle do Windows.

Se você desejar reinstalar o **Azure SDK para Python**, basta abrir um prompt de comando do PowerShell como administrador e executar o seguinte comando:

    rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

e depois executar novamente o WebPI.

## Instalação no Linux e no MacOS

O Python já está provavelmente instalado em seu computador de Des. Você pode verificar isso digitando:

![como-instalar-o-python-no-linux][como-instalar-o-python-no-linux]

Aqui podemos observar que esse Servidor Ubuntu 14.04 LTS VM executado no Azure tem o CPython 2.7.6 instalado. Se precisar atualizar, siga as instruções de upgrade do pacote recomendáveis do SO.

Para instalar as Bibliotecas de Cliente do Python Azure, use **pip** para obtê-las do **PyPI**:

    curl https://bootstrap.pypa.io/get-pip.py | sudo python

O comando acima silenciosamente solicitará a senha raiz. Digite-a e pressione Enter. Avançar:

    sudo pip install azure

Agora você deve visualizar as bibliotecas de cliente instaladas em **pacotes-do-site**. Mo MacOS:

![como-instalar-o-site-do-python-mac][como-instalar-o-site-do-python-mac]

Durante o desenvolvimento do Mac/Linux, existem dois cenários principais suportados:

1.  Usando os Serviços do Azure por meio das bibliotecas de cliente para Python

2.  Executando o seu aplicativo em uma VM com Linux

O primeiro cenário permite que você crie aplicativos da web avançados que aproveitam os recursos do Azure PaaS como armazenamento de blobs, filas, etc., por meio dos invólucros do Pythonic para as APIs REST do Azure. Eles funcionam de forma semelhante no Windows, Mac e Linux. Consulte os Tutoriais e os Guias de Instruções para obter exemplos. Você também pode usar essas bibliotecas de cliente a partir de uma VM com Linux.

Para o cenário da VM, basta iniciar uma VM com Linux de sua escolha (Ubuntu, CentOS, Suse) e executar/gerenciar os itens desejados. Por exemplo, você pode executar [IPython][IPython] REPL/notebook no seu computador com Windows/Mac/Linux e apontar o navegador para uma VM com Linux ou Windows de proc múltiplo que esteja executando o IPython Engine no Azure. Para obter mais informações sobre a instalação do IPython, consulte seu tutorial.

Para obter informações sobre como configurar uma VM com Linux, consulte a [seção Gerenciamento do Linux.][seção Gerenciamento do Linux.]

## Software adicional e recursos:

-   [Distribuição do Python pelo Enthought][Distribuição do Python pelo Enthought]
-   [Distribuição do Python pelo ActiveState][Distribuição do Python pelo ActiveState]
-   [SciPy – Um conjunto de Bibliotecas científicas do Python][SciPy – Um conjunto de Bibliotecas científicas do Python]
-   [NumPy – Uma biblioteca de numéricos para o Python][NumPy – Uma biblioteca de numéricos para o Python]
-   [Projeto Django – Uma estrutura de web/CMS madura][Projeto Django – Uma estrutura de web/CMS madura]
-   [IPython – Um REPL/Notebook avançado para o Python][IPython]
-   [IPython Notebook no Azure][IPython Notebook no Azure]
-   [Python Tools para Visual Studio no CodePlex][Python Tools para Visual Studio no CodePlex]
-   [Virtualenv][Virtualenv]

  [instalador do WebPI]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
  [como-instalar-o-webpi-do-python-27-1]: ./media/python-how-to-install/how-to-install-python-webpi-27-1.png
  [como-instalar-o-webpi-do-python-27-2]: ./media/python-how-to-install/how-to-install-python-webpi-27-2.png
  [como-instalar-o-python-no-win-27]: ./media/python-how-to-install/how-to-install-python-win-run-27.png
  [como-instalar-o-webpi-do-python-34-1]: ./media/python-how-to-install/how-to-install-python-webpi-34-1.png
  [como-instalar-o-webpi-do-python-34-2]: ./media/python-how-to-install/how-to-install-python-webpi-34-2.png
  [como-instalar-o-python-no-win-34]: ./media/python-how-to-install/how-to-install-python-win-run-34.png
  [Índice de Pacote do Python (PyPI)]: http://pypi.python.org/pypi
  [como-instalar-o-webpi-do-ptvs]: ./media/python-how-to-install/how-to-install-python-ptvs.png
  [implementação a Serviços de Nuvem e Sites]: /pt-br/documentation/articles/cloud-services-web-sites-python-django-app-with-ptvs/
  [Python Tools para Visual Studio no CodePlex]: http://pytools.codeplex.com
  [como-instalar-o-python-no-linux]: ./media/python-how-to-install/how-to-install-python-linux-run.png
  [como-instalar-o-site-do-python-mac]: ./media/python-how-to-install/how-to-install-python-mac-site.png
  [IPython]: http://ipython.org
  [seção Gerenciamento do Linux.]: /pt-br/manage/linux/
  [Distribuição do Python pelo Enthought]: http://www.enthought.com
  [Distribuição do Python pelo ActiveState]: http://www.activestate.com
  [SciPy – Um conjunto de Bibliotecas científicas do Python]: http://www.scipy.org
  [NumPy – Uma biblioteca de numéricos para o Python]: http://www.numpy.org
  [Projeto Django – Uma estrutura de web/CMS madura]: http://www.djangoproject.com
  [IPython Notebook no Azure]: /pt-br/documentation/articles/virtual-machines-python-ipython-notebook
  [Virtualenv]: http://pypi.python.org/pypi/virtualenv
