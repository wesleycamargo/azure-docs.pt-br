<properties 
	pageTitle="Tutorial sobre IPython Notebook - Azure" 
	description="Um tutorial que mostra como implantar o IPython Notebook no Azure, usando o Linux ou as máquinas virtuais (VMs) do Windows." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="huvalo"/>





# IPython Notebook no Azure

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>O <a href="http://ipython.org">projeto IPython</a> fornece uma coleção de ferramentas para computação científica que incluem shells interativos avançados, bibliotecas paralelas de alto desempenho e fáceis de usar e um ambiente baseado na Web chamado IPython Notebook. O Notebook fornece um ambiente de trabalho para computação interativa, que combina a execução de códigos com a criação de um ambiente computacional dinâmico. Esses arquivos de notebook podem conter textos arbitrários, fórmulas matemáticas, códigos de entrada, resultados, gráficos, vídeos e qualquer outro tipo de mídia que um navegador da Web moderno é capaz de exibir.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/python/ipy-youtube2.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="dev-onpage-video"><span class="icon">Executar o vídeo</span></a> <span class="time">05:22</span></div>
</div>

Quer você seja absolutamente novo no Python e queira aprendê-lo em um ambiente
interativo divertido, quer você faça um pouco de computação técnica/paralela, o
IPython Notebook é uma ótima opção. Para ilustrar seus recursos, a
captura de tela a seguir mostra o IPython Notebook sendo usado, combinado com
os pacotes Scipy e matplotlib, para analisar a estrutura de uma gravação
de som:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png)

Este documento mostrará como implantar o IPython Notebook no Microsoft
Azure, usando máquinas virtuais (VMs) Windows ou Linux.  Usando o IPython
Notebook no Azure, você pode fornecer facilmente uma interface acessível pela Web para
recursos computacionais escalonáveis com toda a potência do Python e suas diversas
bibliotecas.  Como toda a instalação é feita na nuvem, os usuários podem acessar esses
recursos sem a necessidade de qualquer configuração local - basta um navegador da Web
moderno.

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Criar e configurar uma VM no Azure

A primeira etapa é criar uma Máquina Virtual (VM) que seja executada no Azure.
Essa VM é um sistema operacional completo na nuvem e será usada para
executar o IPython Notebook. O Azure é capaz de executar máquinas virtuais tanto Linux, quanto Windows,
e nós vamos abordar a configuração do IPython em ambos os tipos de máquinas virtuais.

### VM Linux

Siga as instruções fornecidas [aqui][portal-vm-linux] para criar uma máquina virtual da distribuição *OpenSUSE* ou do  *Ubuntu*. Este tutorial usa o OpenSUSE 13.2 e o Ubuntu Server 14.04 LTS. Vamos pressupor que o nome de usuário padrão é *azureuser*.

### VM Windows

Siga as instruções fornecidas [aqui][portal-vm-windows] para criar uma máquina virtual da distribuição do*Windows Server 2012 R2 Datacenter*.  Neste tutorial, vamos pressupor que o nome de usuário é  *azureuser*.

## Criar um ponto de extremidade para o IPython Notebook

Esta etapa se aplica tanto a VMs Linux, quanto a VMs Windows. Mais adiante, configuraremos
o IPython para executar seu Notebook Server na porta 9999. Para tornar essa porta publicamente
disponível, devemos criar um ponto de extremidade no Portal de Gerenciamento do Azure. Este
ponto de extremidade abre uma porta no firewall do Azure e mapeia a porta pública (HTTPS,
443) para a porta privada na VM (9999).

Para criar um ponto de extremidade, vá para o painel da VM, clique em "Pontos de Extremidade", "Adicionar
Ponto de Extremidade" e crie um novo ponto de extremidade (chamado`ipython_nb` neste exemplo). Escolha
TCP como protocolo, 443 como porta pública e 9999 como porta privada:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png)

Após essa etapa, a guia Painel "Pontos de Extremidade" terá esta aparência:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png)

## Instalar software necessário na VM

Para executar o IPython Notebook na nossa VM, devemos primeiro instalar o IPython e
suas dependências.

### Linux (OpenSUSE)

Para instalar o IPython e suas dependências, use SSH na VM Linux e siga 
as etapas a seguir.

Instale [NumPy][numpy], [Matplotlib][matplotlib], [Tornado][tornado] e as outras dependências do IPython, fazendo o seguinte:

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install python-jinja2
    sudo zypper install ipython

### Linux (Ubuntu)

Para instalar o IPython e suas dependências, use SSH na VM Linux e siga 
as etapas a seguir.

Primeiramente, recupere novas listas de pacotes:

    sudo apt-get update

Instale [NumPy][numpy], [Matplotlib][matplotlib], [Tornado][tornado] e as outras dependências do IPython, fazendo o seguinte:

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

Para instalar o IPython e suas dependências na VM Windows, Acesse a Área de Trabalho remota para conectar-se à VM. Depois, siga as etapas a seguir, 
usando o Windows PowerShell para executar todas as ações de linha de comando.

**Observação**: Para baixar qualquer coisa usando o Internet Explorer, será necessário alterar algumas configurações de segurança.  No **Gerenciador de Servidores**, clique em **Servidor Local**, clique em **Configuração de Segurança Aprimorada do IE** e desative-a para administradores.  Você poderá habilitá-la novamente depois que a instalação do IPython for concluída.

1.  Baixe e instale a versão de 32 bits mais recente do [Python 2.7][].  Você precisará adicionar `C:\Python27` e `C:\Python27\Scripts` para a variável de ambiente `PATH` .

1.  Instale [Tornado][tornado]e [PyZMQ][pyzmq] e as outras dependências do IPython, fazendo o seguinte:

        easy_install tornado
        easy_install pyzmq
        easy_install jinja2
        easy_install six
        easy_install python-dateutil
        easy_install pyparsing

1.  Baixe e instale o [NumPy][numpy] usando o instalador binário`.exe` disponível no site.  Quando este artigo foi escrito, a versão mais recente era **numpy-1.9.1-win32-superpack-python2.7.exe**.

1.  Instale o [Matplotlib][matplotlib] fazendo o seguinte:

        pip install matplotlib==1.4.2

1.  Baixe e instale o [OpenSSL][].

	* Você encontrará o **Pacote Redistribuível do Visual C++ 2008** na mesma página de download.

	* Você também precisará adicionar `C:\OpenSSL-Win32\bin` à sua variável de ambiente `PATH`.

	> [AZURE.NOTE] Ao instalar o OpenSSL, use a versão 1.0.1g ou posterior, pois essas versões incluem uma correção para a vulnerabilidade de segurança Heartbleed.

1.  Instale o IPython usando o comando:

        pip install ipython==2.4

1.  Abra uma porta no Firewall do Windows.  No Windows Server 2012, o firewall bloqueará as conexões de entrada por padrão.  Para abrir a porta 9999, siga estas etapas:

    - Inicie o **Firewall do Windows com Segurança Avançada** da tela Iniciar.

    - Clique em **Regras de Entrada** no painel esquerdo.

	- Clique em **Nova Regra...** no painel Ações.

	- No Assistente para Nova Regra de Entrada, selecione **Porta**.

	- Na próxima tela, selecione **TCP** e digite **9999** em **Portas locais específicas**.

	- Aceite os padrões, dê um nome para a regra e clique em Concluir.

### Configurar o IPython Notebook

Em seguida, nós configuramos o IPython Notebook. A primeira etapa é criar um personalizado
Perfil de configuração do IPython para encapsular as informações de configuração:

    ipython profile create nbserver

Em seguida, vamos usar`cd` para o diretório de perfil para criar nosso certificado SSL e editar
o arquivo de configuração de perfis.

No Linux:

    cd ~/.ipython/profile_nbserver/

No Windows:

    cd \users\azureuser\.ipython\profile_nbserver

Crie o certificado SSL da seguinte maneira (Linux e Windows):

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Observe que, uma vez que estamos criando um certificado SSL autoassinado, ao se conectar
ao notebook seu navegador exibirá um aviso de segurança.  Para uso de produção
de longo prazo, convém usar um certificado devidamente assinado associado
à sua organização.  Como o gerenciamento de certificados está fora do escopo
desta demonstração, manteremos um certificado autoassinado por enquanto.

Além de usar um certificado, você também deve fornecer uma senha para proteger
seu notebook contra uso não autorizado.  Por motivos de segurança, o IPython usa
senhas criptografadas em seu arquivo de configuração, por isso você precisa criptografar sua
senha primeiro.  O IPython fornece um utilitário para isso, em uma execução de prompt de comando:

    python -c "import IPython;print IPython.lib.passwd()"

Isto solicitará uma senha e uma confirmação e imprimirá a
senha da seguinte maneira:

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)
    
Em seguida, editaremos o arquivo de configuração do perfil, que é o
arquivo `ipython_notebook_config.py` no diretório de perfil em que você está. Observe que este arquivo pode não existir, basta criá-lo.  Esse
arquivo tem diversos campos, todos comentados por padrão.  Você pode abrir
este arquivo com qualquer editor de texto de sua preferência e você deve garantir que ele
tenha pelo menos o seguinte conteúdo:

    c = get_config()
    
    # This starts plotting support always with matplotlib
    c.IPKernelApp.pylab = 'inline'
    
    # You must give the path to the certificate file.
    
    # If using a Linux VM:
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

Neste momento, estamos prontos para iniciar o IPython Notebook. Para fazer isto,
navegue até o diretório no qual você deseja armazenar notebooks e inicie
o servidor do IPython Notebook:

    ipython notebook --profile=nbserver

Agora, você deve poder acessar o seu IPython Notebook no endereço
`https://[o nome escolhido aqui].cloudapp.net`.

Quando você acessar seu notebook pela primeira vez, a página de logon pedirá a senha:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png)

Após o logon, você verá o "Painel do IPython Notebook", que é
o centro de controle para todas as operações de notebook.  Nessa página, você pode criar
novos notebooks, abrir os existentes, etc.:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png)

Se clicar no botão "Novo Notebook", você verá uma página de abertura desta
forma:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png)

A área marcada com um prompt `In []:` é a área de entrada, e nela você pode
digitar qualquer código Python válido e ele será executado quando você pressionar `Shift-Enter` ou
clicar no ícone "Reproduzir" (o triângulo apontando para a direita na barra de ferramentas).

Como já configuramos o notebook para iniciar com suporte a NumPy e matplotlib
automaticamente, você pode até produzir figuras, por exemplo:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png)

## Um paradigma avançado: documentos computacionais dinâmicos com mídia rica

O próprio notebook deve parecer muito natural para qualquer pessoa que tenha usado o Python e
um processador de texto, porque, de certa forma, ele é uma mistura de ambos: você pode executar
blocos de código Python, mas também pode manter anotações e outros textos, alterando
o estilo de uma célula de "Código" para "Redução" usando o menu suspenso da
barra de ferramentas:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png)


Mas ele é muito mais do que um processador de texto, já que o IPython Notebook permite a
combinação de computação e mídia avançada (texto, gráficos, vídeo e praticamente
qualquer coisa que um navegador da Web moderno consiga exibir). Por exemplo, você pode combinar
vídeos explicativos com computação para fins educativos:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png)

ou inserir sites externos que permanecem ativos e utilizáveis dentro de um
arquivo do Notebook:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png)

E, com a potência das muitas excelentes bibliotecas do Python para computação científica e
técnicas, um cálculo simples pode ser executado com a mesma facilidade
que uma análise de rede complexa, tudo no mesmo ambiente:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png)

Esse paradigma de combinar a potência da Web moderna com computação dinâmica
oferece muitas possibilidades e é idealmente adequada para a nuvem; o Notebook
pode ser usado:

* como um bloco de anotações eletrônico para gravar soluções para um problema,

* para compartilhar resultados com colegas em'live' forma computacional ou em
  formatos de cópia impressa (HTML, PDF),

* para distribuir e apresentar materiais de ensino ao vivo que envolvem computação,
  para que os alunos possam imediatamente experimentar o código real, modificá-lo e
  executá-lo novamente de forma interativa,

* para fornecer "trabalhos executáveis" que apresentam os resultados da pesquisa de forma
  que podem ser imediatamente reproduzido, validado e estendidos por outras pessoas,

* como uma plataforma para computação colaborativa: vários usuários podem fazer logon no
  mesmo servidor de notebook para compartilhar uma sessão computacional ao vivo

* e mais...

Se acessar o [repositório][] de código-fonte do IPython, você encontrará um diretório inteiro
com exemplos de Notebook que você pode baixar e experimentar na sua própria VM IPython do Azure.  Basta baixar os arquivos `.ipynb` do site e carregá-los no painel de sua VM Notebook do Azure (ou baixá-los diretamente na VM).

## Conclusão

O IPython Notebook fornece uma interface avançada para acessar interativamente
o poder do ecossistema Python no Azure.  Ele abrange uma ampla gama de
casos de uso, incluindo exploração simples e aprendizagem de Python, análise e visualização de dados,
simulação e computação paralela. Os documentos de Notebook resultantes
contêm um registro completo da computação executada e
podem ser compartilhados com outros usuários do IPython.  O IPython Notebook pode ser usado como um
aplicativo local, mas ele é idealmente adequado para implantações de nuvem no Azure

Os principais recursos do IPython também estão disponíveis no Visual Studio por meio do 
[Python Tools for Visual Studio][] (PTVS). O PTVS é um plug-in gratuito e de software livre 
da Microsoft que transforma o Visual Studio em ambiente de desenvolvimento Python avançado 
que inclui um editor avançado com IntelliSense, depuração, 
criação de perfis e integração com computação paralela.



[tornado]:      http://www.tornadoweb.org/          "Tornado"
[PyZMQ]:        https://github.com/zeromq/pyzmq     "PyZMQ"
[NumPy]:        http://www.numpy.org/               "NumPy"
[Matplotlib]:   http://matplotlib.sourceforge.net/  "Matplotlib"

[portal-vm-windows]: /manage/windows/tutorials/virtual-machine-from-gallery/
[portal-vm-linux]: /manage/linux/tutorials/virtual-machine-from-gallery/

[repositório]: https://github.com/ipython/ipython
[Python Tools for Visual Studio]: http://aka.ms/ptvs

[Python 2.7]: http://www.python.org/download
[OpenSSL]: http://slproweb.com/products/Win32OpenSSL.html

<!--HONumber=47-->
