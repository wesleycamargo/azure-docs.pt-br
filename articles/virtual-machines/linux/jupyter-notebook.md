---
title: "Criar um Bloco de Anotações Jupyter/IPython | Microsoft Docs"
description: "Saiba como implantar o Bloco de Anotações Jupyter/IPython em uma máquina virtual Linux criada com o modelo de implantação do gerenciador de recursos no Azure."
services: virtual-machines-linux
documentationcenter: python
author: crwilcox
manager: wpickett
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 519f36dd-865e-4c1d-abe7-b87037796aa7
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: python
ms.topic: article
ms.date: 11/10/2015
ms.author: crwilcox
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: b5940190822cd5c5b78ea0e8f5c8695608d351d6
ms.lasthandoff: 04/03/2017

---
# <a name="creating-an-azure-vm-installing-jupyter-and-running-a-jupyter-notebook-on-azure"></a>Criando uma VM do Azure, instalando o Jupyter, e executando um Notebook do Jupyter no Azure
O [projeto Jupyter](http://jupyter.org), anteriormente conhecido como [projeto IPython](http://ipython.org), fornece um conjunto de ferramentas para a computação científica usando shells interativos avançados que combinam a execução do código com a criação de um ambiente computacional dinâmico. Esses arquivos de notebook podem conter textos arbitrários, fórmulas matemáticas, códigos de entrada, resultados, gráficos, vídeos e qualquer outro tipo de mídia que um navegador da Web moderno é capaz de exibir. Caso você não tenha experiência alguma com o Python e deseja aprender a usá-lo em um ambiente divertido e interativo, ou deseja fazer trabalhos de computação técnico ou complexos, o Bloco de Anotações Jupyter é a escolha ideal.

![Captura de tela](./media/jupyter-notebook/ipy-notebook-spectral.png) Usando os pacotes SciPy e Matplotlib para analisar a estrutura de uma gravação de som.

## <a name="jupyter-two-ways-azure-notebooks-or-custom-deployment"></a>Duas maneiras para o Jupyter: Blocos de nota do Azure ou implantação personalizada
O Azure fornece um serviço que você pode usar para [começar a usar rapidamente o Jupyter ](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).  Ao usar o Serviço Bloco de Anotações do Azure, você poderá obter acesso fácil a uma interface acessível pela Web do Jupyter para recursos computacionais escalonáveis com toda a tecnologia Python e suas diversas bibliotecas.  Como a instalação é manipulada pelo serviço, os usuários podem acessar esses recursos sem a necessidade de administração e configuração por parte do usuário.

Se o serviço de bloco de anotações não funcionar para seu cenário, continue a ler este artigo e veja como implantar o Bloco de Anotações Jupyter no Microsoft Azure usando VMs (máquinas virtuais) do Linux.

[!INCLUDE [create-account-and-vms-note](../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-configure-a-vm-on-azure"></a>Criar e configurar uma VM no Azure
A primeira etapa é criar uma VM (máquina virtual) executada no Azure.
Essa VM é um sistema operacional completo na nuvem e será usada para executar o Bloco de Anotações Jupyter. O Azure é capaz de executar máquinas virtuais Linux e Windows, e abordaremos a configuração do Jupyter em ambas as VMs.

### <a name="create-a-linux-vm-and-open-a-port-for-jupyter"></a>Criar uma VM Linux e abrir uma porta para o Jupyter
Siga as instruções fornecidas [aqui][portal-vm-linux] para criar uma máquina virtual da distribuição *Ubuntu*. Este tutorial usa o Ubuntu Server 14.04 LTS. Vamos supor que o nome de usuário seja *azureuser*.

Após a implantação da máquina virtual, precisamos abrir uma regra de segurança no grupo de segurança de rede.  No portal do Azure, acesse **Grupos de Segurança de Rede** e abra a guia do Grupo de Segurança correspondente à sua VM. Você precisa adicionar uma regra de Segurança de Entrada com as seguintes configurações: **TCP** para o protocolo **\*** para a porta de origem (pública) e **9999** para a porta de destino (privada).

![Captura de tela](./media/jupyter-notebook/azure-add-endpoint.png)

No Grupo de Segurança da Rede, clique em **Interfaces da Rede** e observe o **Endereço IP público**, pois ele será necessário para conectar sua VM na próxima etapa.

## <a name="install-required-software-on-the-vm"></a>Instalar software necessário na VM
Para executar o Bloco de Anotações Jupyter em nossa VM, devemos instalar primeiro o Jupyter e suas dependências. Conecte-se à sua VM Linux usando ssh e o par nome de usuário/senha que você escolheu durante a criação da máquina virtual. Neste tutorial, usaremos PuTTY e conectaremos do Windows.

### <a name="installing-jupyter-on-ubuntu"></a>Instalando o Jupyter no Ubuntu
Instale a Anaconda, uma distribuição python popular de ciência de dados, usando um dos links fornecidos em [Continuum Analytics](https://www.continuum.io/downloads).  No momento da elaboração deste documento, os links a seguir eram os mais atualizados.

#### <a name="anaconda-installs-for-linux"></a>Instalações de Anaconda para Linux
<table>
  <th>Python 3.4</th>
  <th>Python 2,7</th>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64 bits</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64 bits</href>
    </td>
  </tr>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32 bits</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32 bits</href>
    </td>  
  </tr>
</table>


#### <a name="installing-anaconda3-230-64-bit-on-ubuntu"></a>Instalando o Anaconda3 2.3.0 de 64 bits no Ubuntu
Veja um exemplo de como você pode instalar o Anaconda no Ubuntu

    # install anaconda
    cd ~
    mkdir -p anaconda
    cd anaconda/
    curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
    sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

    # clean up home directory
    cd ..
    rm -rf anaconda/

    # Update Jupyter to the latest install and generate its config file
    sudo /anaconda3/bin/conda install jupyter -y
    /anaconda3/bin/jupyter-notebook --generate-config


![Captura de tela](./media/jupyter-notebook/anaconda-install.png)

### <a name="configuring-jupyter-and-using-ssl"></a>Configurando o Jupyter e usando SSL
Após a instalação, precisamos reservar um momento para definir os arquivos de configuração do Jupyter. Se você enfrentar problemas com a configuração do Jupyter, talvez seja útil examinar a [Documentação do Jupyter para execução de um Servidor de Bloco de Anotações](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html).

Em seguida, digite `cd` para o diretório do perfil a fim de criar nosso certificado SSL e edita o arquivo de configuração de perfis.

No Linux, use o comando a seguir.

    cd ~/.jupyter

Use o comando a seguir para criar o certificado SSL (Linux e Windows).

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Já que estamos criando um certificado SSL autoassinado, quando você se conectar ao notebook, o navegador exibirá um aviso de segurança.  Para uso de produção a longo prazo, use um certificado devidamente assinado associado à sua organização.  Como o gerenciamento de certificado está além do escopo desta demonstração, manteremos um certificado autoassinado por enquanto.

Além de usar um certificado, você também deve fornecer uma senha para proteger seu notebook de uso não autorizado.  Por motivos de segurança, o Jupyter usa senhas criptografadas em seu arquivo de configuração; por isso, será necessário criptografar sua senha primeiro.  O IPython fornece um utilitário para isso; em um prompt de comando, execute o comando a seguir.

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

Você receberá de solicitação de uma senha e uma confirmação e, em seguida, uma senha será impressa. Observe isso na etapa a seguir.

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

Em seguida, editaremos o arquivo de configuração do perfil, que é o arquivo `jupyter_notebook_config.py` no diretório atual.  Observe que este arquivo talvez não exista; neste caso, basta criá-lo.  Esse arquivo tem diversos campos, todos comentados por padrão.  Você pode abrir esse arquivo em qualquer editor de texto da sua preferência e deve garantir que ele tenha pelo menos o conteúdo a seguir. **Lembre-se de substituir a senha c.NotebookApp.password na configuração pelo sha1 da etapa anterior**.

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### <a name="run-the-jupyter-notebook"></a>Execute o Bloco de Anotações do Jupyter.
Neste momento, estamos prontos para iniciar o Bloco de Anotações Jupyter. Para fazer isso, navegue até o diretório onde deseja armazenar os blocos de nota e inicie o servidor do Bloco de Anotações Jupyter com o comando a seguir.

    /anaconda3/bin/jupyter-notebook

Agora, você deve poder acessar seu Bloco de Anotações Jupyter no endereço `https://[PUBLIC-IP-ADDRESS]:9999`.

Quando você acessar seu notebook pela primeira vez, a página de logon pedirá a senha. E, depois de entrar, você verá "Painel do Bloco de Anotações Jupyter", que é o centro de controle de todas as operações de bloco de notas.  Nessa página, é possível criar novos notebooks e abrir os que já existem.

![Captura de tela](./media/jupyter-notebook/jupyter-tree-view.png)

### <a name="using-the-jupyter-notebook"></a>Usando o Bloco de Anotações do Jupyter
Se você clicar no botão **Novo** , você verá a seguinte página de abertura.

![Captura de tela](./media/jupyter-notebook/jupyter-untitled-notebook.png)

A área marcada com um prompt `In []:` é a área de entrada, e nela você poderá digitar qualquer código de Python válido e ele será executado quando você pressionar `Shift-Enter` ou clicar no ícone "Reproduzir" (o triângulo que aponta para a direita na barra de ferramentas).

## <a name="a-powerful-paradigm-live-computational-documents-with-rich-media"></a>Um paradigma avançado: documentos computacionais dinâmicos com mídia rica
O notebook em si deve ser muito natural para qualquer pessoa que usou o Python e um processador de texto, pois ele é de certa forma uma mistura de ambos: você pode executar blocos de código do Python, mas também manter anotações e outros textos alterando o estilo de uma célula de "Código" para "Redução" usando o menu suspenso na barra de ferramentas.

O Jupyter é muito mais que um processador de texto, pois permite a combinação de computação e mídia rica (texto, gráficos, vídeos e praticamente qualquer coisa que um navegador da Web moderno pode exibir). Você pode misturar texto, códigos, vídeos e muito mais!

![Captura de tela](./media/jupyter-notebook/jupyter-editing-experience.png)

E com a tecnologia das excelentes bibliotecas do Python para computação científica e técnica, na captura de tela a seguir, um cálculo simples pode ser executado com a mesma facilidade de uma análise de rede complexa, tudo no mesmo ambiente.

Esse paradigma de mistura de tecnologia da Web moderna com computação dinâmica oferece várias possibilidades e é ideal para a nuvem. O Notebook pode ser usado:

* Como um bloco de anotações eletrônico para registrar soluções para um problema.
* Para compartilhar resultados com colegas, seja em forma computacional 'dinâmica' ou em formatos de cópia impressa (HTML, PDF).
* Para distribuir e apresentar materiais de ensino ao vivo relacionados à computação para que os alunos possam imediatamente experimentar o código real, modificá-lo e executá-lo novamente de forma interativa.
* Para fornecer "trabalhos executáveis" que apresentam os resultados da pesquisa de uma forma que possa ser prontamente reproduzida, validada e estendida por outras pessoas.
* Como uma plataforma para computação colaborativa: vários usuários podem entrar no mesmo servidor de notebook para compartilhar uma sessão computacional ao vivo.

Caso acesse o [repositório][repository] de código-fonte do IPython, você encontrará um diretório inteiro com exemplos de notebook que você pode baixar e experimentar em sua própria VM Jupyter do Azure.  Basta baixar os arquivos `.ipynb` do site e carregá-los no painel de sua VM Notebook do Azure (ou baixá-los diretamente na VM).

## <a name="conclusion"></a>Conclusão
O Bloco de Anotações do Jupyter fornece uma interface avançada para acesso interativo à tecnologia do ecossistema Python no Azure.  Ela abrange diversos casos de uso, incluindo exploração simples, aprendizado do Python, análise de dados e visualização, simulação e computação paralela. Os documentos resultantes do Bloco e notas contêm um registro completo das computações executadas e podem ser compartilhados com outros usuários do Jupyter.  O Bloco de Anotações do Jupyter pode ser usado como um aplicativo local e é ideal para implantações em nuvem no Azure

Os recursos principais do Jupyter também estão disponíveis no Visual Studio por meio da PTVS ([Ferramentas Python para Visual Studio][Python Tools for Visual Studio]). O PTVS é um plug-in gratuito e de código aberto da Microsoft que transforma o Visual Studio em um ambiente de implantação avançado do Python, incluindo um editor avançado com IntelliSense, depuração, criação de perfil e integração de computação paralela.

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte o [Centro de Desenvolvedores do Python](/develop/python/).

[portal-vm-linux]: https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[repository]: https://github.com/ipython/ipython
[Python Tools for Visual Studio]: http://aka.ms/ptvs

