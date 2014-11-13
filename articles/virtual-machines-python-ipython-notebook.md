<properties urlDisplayName="IPython Notebook" pageTitle="Tutorial sobre IPython Notebook - Azure" metaKeywords="" description="Um tutorial que mostra como implantar o IPython Notebook no Azure, usando o Linux ou as m&aacute;quinas virtuais (VMs) do Windows." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="IPython Notebook no Azure" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="python" ms.topic="article" ms.date="09/25/2014" ms.author="huvalo" />

# IPython Notebook no Azure

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>O <a href="http://ipython.org">projeto IPython</a> fornece uma cole&ccedil;&atilde;o de ferramentas para computa&ccedil;&atilde;o cient&iacute;fica que incluem shells interativos avan&ccedil;ados, bibliotecas paralelas de alto desempenho e f&aacute;ceis de usar e um ambiente baseado na Web chamado IPython Notebook. O Notebook fornece um ambiente de trabalho para computa&ccedil;&atilde;o interativa, que combina a execu&ccedil;&atilde;o de c&oacute;digos com a cria&ccedil;&atilde;o de um ambiente computacional din&acirc;mico. Esses arquivos de notebook podem conter textos arbitr&aacute;rios, f&oacute;rmulas matem&aacute;ticas, c&oacute;digos de entrada, resultados, gr&aacute;ficos, v&iacute;deos e qualquer outro tipo de m&iacute;dia que um navegador da Web moderno &eacute; capaz de exibir.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/python/ipy-youtube2.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o v&iacute;deo</span></a> <span class="time">05:22:00</span></div>

</div>

Caso você não tenha experiência alguma com o Python e deseja aprendera usá-lo em um
ambiente interativo ou fazer trabalhos de computação técnico ou complexos, o
IPython Notebook é a escolha ideal. Para ilustrar seus recursos, a
captura de tela a seguir mostra o IPython Notebook em uso, em conjunto com os pacotes
SciPy e matplotlib, para analisar a estrutura de uma
gravação de som:

![Captura de tela][Captura de tela]

Este documento mostrará a você como implantar o IPython Notebook no Microsoft
Azure, usando o Linux ou as máquinas virtuais (VMs) do Windows. Ao usar o IPython
Notebook no Azure, você pode fornecer facilmente uma interface acessível pela Web
para recursos computacionais escalonáveis com toda a tecnologia da Python e de suas
bibliotecas. Como toda a instalação é feita na nuvem, os usuários podem acessar
esses recursos sem precisar de qualquer configuração além de um navegador
moderno.

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Criar e configurar uma VM no Azure

A primeira etapa é criar uma Máquina Virtual (VM) que seja executada no Azure.
Esta VM é um sistema operacional completo na nuvem e será usada para
executar o IPython Notebook. O Azure pode executar máquinas virtuais
tanto do Windows quanto do Linux, e irá abranger a configuração do IPython em ambas as VMs.

### VM do Linux

Siga as instruções fornecidas [aqui][aqui] para criar uma máquina virtual da distribuição do *OpenSUSE* ou *Ubuntu*. Este tutorial usa o OpenSUSE 13.1 e o Ubuntu Server 14.04 LTS. Vamos pressupor que o nome de usuário padrão é *azureuser*.

### VM do Windows

Siga as instruções fornecidas [aqui][1] para criar uma máquina virtual da distribuição do *Windows Server 2012 R2 Datacenter*. Neste tutorial, vamos pressupor que o nome de usuário é *usuárioazure*.

## Criar um ponto de extremidade para o IPython Notebook

Esta etapa se aplica à VM do Linux e do Windows. Mais adiante, configuraremos o
IPython para executar seu servidor de notebook na porta 9999. Para tornar essa porta disponível
publicamente, devemos criar um ponto de extremidade no Portal de Gerenciamento do Azure. Esse
ponto de extremidade abre uma porta no firewall do Azure e mapeia a porta pública (HTTPS,
443) para a porta privada na VM (9999).

Para criar um ponto de extremidade, vá para o painel da VM, clique em "Pontos de Extremidade", clique em "Adicionar
Ponto de Extremidade" e crie um novo ponto (chamado `ipython_nb` neste exemplo). Escolha
TCP para o protocolo, 443 para a porta pública e 9999 para a porta privada:

![Captura de tela][2]

Após essa etapa, a guia Painel "Pontos de Extremidade" terá esta aparência:

![Captura de tela][3]

## Instalar software necessário na VM

Para executar o IPython Notebook na nossa VM, devemos primeiro instalar o IPython e
suas dependências.

### Linux (OpenSUSE)

Para instalar o IPython e suas dependências, use o SSH na VM do Linux e siga
as etapas abaixo.

Instale [NumPy][NumPy], [Matplotlib][Matplotlib], [Tornado][Tornado] e as outras dependências do IPython, fazendo o seguinte:

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install python-jinja2
    sudo zypper install ipython

### Linux (Ubuntu)

Para instalar o IPython e suas dependências, use o SSH na VM do Linux e siga
as etapas abaixo.

Instale [NumPy][NumPy], [Matplotlib][Matplotlib], [Tornado][Tornado] e as outras dependências do IPython, fazendo o seguinte:

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

Para instalar o IPython e suas dependências na VM do Windows, Acesse a Área de Trabalho remota para conectar-se à VM. Em seguida, siga as etapas abaixo,
usando o PowerShell do Windows para executar todas as ações da linha de comando.

**Observação**: Para baixar qualquer coisa usando o Internet Explorer, será necessário alterar algumas configurações de segurança. No **Gerenciador de Servidores**, clique em **Servidor Local**, clique em **Configuração de Segurança Aprimorada do IE** e desative-a para administradores. Você poderá habilitá-la novamente depois que a instalação do IPython for concluída.

1.  Instale o Python 2.7.8 (32 bits) de [python.org][python.org].
    Você também precisará adicionar o `C:\Python27` e `C:\Python27\Scripts` à sua variável de ambiente `PATH`
    .

2.  Instale o pip e as ferramentas de configuração baixando o arquivo **get-pip.py**
    de <https://pip.pypa.io/en/latest/installing.html> e executando o
    comando:

        python get-pip.py

3.  Instale [Tornado][Tornado], [PyZMQ][PyZMQ] e as outras dependências do IPython, fazendo o seguinte:

        easy_install tornado
        easy_install pyzmq
        easy_install jinja2
        easy_install six
        easy_install python-dateutil
        easy_install pyparsing

4.  Baixe e instale [NumPy][NumPy] usando o instalador binário
    `.exe` disponível no site. No momento em que este documento está sendo escrito, a versão mais recente é **numpy-1,90,0-win32-superpack-python2.7.exe**.

5.  Baixe e instale [Matplotlib][Matplotlib] usando o instalador binário
    `.exe` disponível no site. No momento em que este documento está sendo escrito, a versão mais recente é **matplotlib-1,40,0.win32-py2.7.exe**.

6.  Baixe e instale o OpenSSL. Você pode encontrar as versões do Windows do OpenSSL em <http://slproweb.com/products/Win32OpenSSL.html>.

    -   Se você instalar uma versão **Light**, será necessário instalar também o **Visual C++ 2008 Redistributable** (também disponível nesta página.)

    -   Você também precisará adicionar `C:\OpenSSL-Win32\bin` à sua variável de ambiente `PATH`.

    > [WACOM.NOTE] Ao instalar o OpenSSL, use a versão 1.0.1g ou posterior, pois essas versões incluem uma correção para a vulnerabilidade de segurança Heartbleed.

7.  Instale o IPython usando o comando:

        easy_install ipython

8.  Abra uma porta no Windows Firewall. No Windows Server 2012, o firewall bloqueará as conexões de entrada por padrão. Para abrir a porta 9999, siga estas etapas:

    -   Inicie o **Firewall do Windows com Segurança Avançada** da tela Iniciar.

    -   Clique em **Regras de Entrada** no painel esquerdo.

    -   Clique em **Nova Regra...** no painel Ações.

    -   No Assistente para Nova Regra de Entrada, selecione **Porta**.

    -   Na próxima tela, selecione **TCP** e digite **9999** em **Portas locais específicas**.

    -   Aceite os padrões, dê um nome para a regra e clique em Concluir.

### Configurar o IPython Notebook

Em seguida, nós configuramos o IPython Notebook. A primeira etapa é criar um perfil
de configuração personalizada do IPython para encapsular as informações de configuração:

    ipython profile create nbserver

Em seguida, digite `cd` para o diretório do perfil a fim de criar nosso certificado SSL e edita
o arquivo de configuração de perfis.

No Linux (OpenSUSE):

    cd ~/.config/ipython/profile_nbserver/

No Linux (Ubuntu):

    cd ~/.ipython/profile_nbserver/

No Windows:

    cd \users\azureuser\.ipython\profile_nbserver

Em ambas as plataformas, crie o certificado SSL conforme a seguir:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Já que estamos criando um certificado SSL autoassinado, quando você se conectara
o notebook, o navegador exibirá um aviso de segurança. Para uso de produção
a longo prazo, use um certificado devidamente assinado associado
à sua organização. Como o gerenciamento de certificado está além do escopo
desta demonstração, manteremos um certificado autoassinado por enquanto.

Além de usar um certificado, você também deve fornecer uma senha para proteger
seu notebook de uso não autorizado. Por razões de segurança, o IPython usa
senhas criptografadas em seu arquivo de configuração; por isso, será necessário criptografar sua
senha primeiro. O IPython fornece um utilitário para isso, em uma execução de prompt de comando:

    python -c "import IPython;print IPython.lib.passwd()"

Isso solicitará uma senha e uma confirmação e imprimirá a
senha conforme a seguir:

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

Em seguida, editaremos o arquivo de configuração do perfil, que é o arquivo
`ipython_notebook_config.py` no diretório do perfil onde você está. Se esse arquivo ainda não existir, basta criá-lo. Esse
arquivo tem diversos campos, todos comentados por padrão. Você pode abrir
esse arquivo de qualquer editor de texto da sua preferência e deve garantir que ele
tenha pelo menos o conteúdo a seguir:

    c = get_config()

    # This starts plotting support always with matplotlib
    c.IPKernelApp.pylab = 'inline'

    # You must give the path to the certificate file.

    # If using a Linux VM (OpenSUSE):
    c.NotebookApp.certfile = u'/home/azureuser/.config/ipython/profile_nbserver/mycert.pem'

    # If using a Linux VM (Ubuntu):
    c.NotebookApp.certfile = u'/home/azureuser/.ipython/profile_nbserver/mycert.pem'

    # And if using a Windows VM:
    c.NotebookApp.certfile = r'C:\Users\azureuser\.ipython\profile_nbserver\mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port

    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### Executar o IPython Notebook

Neste momento, estamos prontos para iniciar o IPython Notebook. Para fazer isso,
navegue até o diretório onde você deseja armazenar notebooks e inicie
o servidor do IPython Notebook:

    ipython notebook --profile=nbserver

Agora, você deve poder acessar o seu IPython Notebook no endereço
`https://[Your Chosen Name Here].cloudapp.net`.

Quando você acessar seu notebook pela primeira vez, a página de logon pedirá a senha:

![Captura de tela][4]

Depois de fazer logon, você verá o "Painel do IPython Notebook", que é
a central de controle de todas as operações de notebook. Nessa página, é possível criar
novos notebooks, abrir os já existentes etc.:

![Captura de tela][5]

Ao clicar no botão "Novo Notebook", você verá uma página de abertura como
a seguir:

![Captura de tela][6]

A área marcada com um prompt `In []:` é a área de entrada, e nela você poderá digitar qualquer código de Python válido e ele será executado quando você pressionar `Shift-Enter`
 ou
clicar no ícone "Reproduzir" (o triângulo que aponta para a direita na barra de ferramentas).

Como já configuramos o notebook para iniciar com o suporte de NumPy e matplotlib
automaticamente, você pode até produzir figuras, por exemplo:

![Captura de tela][7]

## Um poderoso paradigma: documentos computacionais dinâmicos com mídia rica

O notebook deve funcionar naturalmente para todos que já usaram o Python e
um processador de texto, porque ele é, de certa forma, uma mistura de ambos: você pode executar
blocos de código do Python, bem como inserir anotações e outros textos alterando
o estilo de uma célula de "Código" para "Redução" usando o menu suspenso na
barra de ferramentas:

![Captura de tela][8]

Mas ele é muito mais que um processador de texto, pois o IPython Notebook permite
a mistura de computação e mídia rica (texto, gráficos, vídeos e praticamente
qualquer coisa que um navegador da Web pode exibir). Por exemplo, você pode misturar
vídeos explanatórios com computação para fins educativos:

![Captura de tela][9]

ou incorporar sites externos que continuam dinâmicos e utilizáveis dentro deum
arquivo do notebook:

![Captura de tela][10]

E com a tecnologia das excelentes bibliotecas do Python para computação científica e
técnica, um cálculo simples pode ser executado com a mesma facilidade
de uma análise de rede complexa, tudo no mesmo ambiente:

![Captura de tela][11]

Esse paradigma de mistura de tecnologia da Web moderna com computação dinâmica
oferece várias possibilidades e é ideal para a nuvem. O Notebook
pode ser usado:

-   como um bloco de anotações eletrônico para gravar soluções para um problema,

-   para compartilhar resultados com colegas, seja em forma computacional 'dinâmica' ou em
    formatos de cópia impressa (HTML, PDF),

-   para distribuir e apresentar materiais de ensino ao vivo relacionados à computação,
    para que os alunos possam imediatamente experimentar o código real, modificá-lo e
    executá-lo novamente de forma interativa,

-   para fornecer "trabalhos executáveis" que apresentam os resultados de pesquisa de uma forma
    que possa ser prontamente reproduzida, validade e estendida por outras pessoas,

-   como uma plataforma para computação de colaboração: vários usuários podem entrar no
    mesmo servidor de notebook para compartilhar uma sessão computacional ao vivo,

-   e mais...

Se acessar o repositório de código-fonte do IPython, você encontrará um diretório
completo com [exemplos
de notebooks][exemplos
de notebooks]
que poderá baixar e experimentar na sua própria VM do IPython do Azure.
Basta baixar os arquivos `.ipynb` do site e carregá-los no
painel da VM do Azure no seu notebook (ou baixá-los diretamente na VM).

## Conclusão

O IPython Notebook fornece uma interface avançada para acesso interativo
à tecnologia do ecossistema Python no Azure. Ela abrange diversos
casos de uso, incluindo exploração simples, aprendizado do Python, análise de dados e
visualização, simulação e computação paralela. Os documentos resultantes
do Notebook contêm um registro completo das computações executadas e
podem ser compartilhados com outros usuários do IPython. O IPython Notebook pode ser usado como um
aplicativo local e é ideal para implantações em nuvem no Azure

Os recursos principais do IPython também estão disponíveis no Visual Studio através do
[Python Tools for Visual Studio][Python Tools for Visual Studio] (PTVS). O PTVS é um plug-in
gratuito e de código aberto da Microsoft que transforma o Visual Studio em um ambiente de implantação
avançado do Python, incluindo um editor avançado com IntelliSense,
depuração, criação de perfil e integração de computação paralela.

  [Captura de tela]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png
  [aqui]: /pt-br/manage/linux/tutorials/virtual-machine-from-gallery/
  [1]: /pt-br/manage/windows/tutorials/virtual-machine-from-gallery/
  [2]: ./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png
  [3]: ./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png
  [NumPy]: http://www.numpy.org/ "NumPy"
  [Matplotlib]: http://matplotlib.sourceforge.net/ "Matplotlib"
  [Tornado]: http://www.tornadoweb.org/ "Tornado"
  [python.org]: http://www.python.org/download
  [PyZMQ]: https://github.com/zeromq/pyzmq "PyZMQ"
  [4]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png
  [5]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png
  [6]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png
  [7]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png
  [8]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png
  [9]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png
  [10]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png
  [11]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png
  [Python Tools for Visual Studio]: http://pytools.codeplex.com
