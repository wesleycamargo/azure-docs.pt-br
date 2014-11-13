<properties title="Como usar a extens&atilde;o de m&aacute;quina virtual Docker com o Portal do Azure" pageTitle="Usando a extens&atilde;o de m&aacute;quina virtual Docker para Linux no Azure" description="Descreve as extens&otilde;es de m&aacute;quinas virtuais Docker e Azure Virtual, al&eacute;m de mostrar como criar m&aacute;quinas virtuais que sejam hosts do Docker programaticamente no Azure, por meio da linha de comando, usando a interface de comandos azure-cli." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill" />
<!--The next line, with one pound sign at the beginning, is the page title-->

# Usando a extensão de máquina virtual Docker com o Portal do Azure

O [Docker][Docker] é uma das abordagens de virtualização mais populares que utiliza [contêineres Linux][contêineres Linux] em vez de máquinas virtuais como forma de isolar dados e computar recursos compartilhados. Você pode usar a extensão da VM Docker do [Agente Linux do Azure][Agente Linux do Azure] para criar uma VM Docker que hospede diversos contêineres para seus aplicativos no Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

Nesta seção

-   [Criar uma nova máquina virtual por meio da Galeria de Imagens][Criar uma nova máquina virtual por meio da Galeria de Imagens]
-   [Criar certificados do Docker][Criar certificados do Docker]
-   [Adicionar a extensão de máquina virtual Docker][Adicionar a extensão de máquina virtual Docker]
-   [Testar cliente do Docker e host do Docker do Azure][Testar cliente do Docker e host do Docker do Azure]
-   [Próximas etapas][Próximas etapas]

> [WACOM.NOTE] Este tópico descreve como criar uma máquina virtual Docker no Portal do Azure. Para ver como criar uma máquina virtual Docker na linha de comando, confira [Como usar a extensão de máquina virtual na interface de plataforma cruzada do Azure (xplat-cli)][Como usar a extensão de máquina virtual na interface de plataforma cruzada do Azure (xplat-cli)]. Para participar de um debate de alto nível sobre contêineres e suas vantagens, confira o [Quadro de comunicações de alto nível do Docker][Quadro de comunicações de alto nível do Docker].

## <span id="createvm"></span>Criar uma nova máquina virtual por meio da Galeria de Imagens</a>

A primeira etapa requer uma máquina virtual do Azure obtida por meio de uma imagem Linux compatível com a extensão de máquina virtual Docker, usando uma imagem Ubuntu 14.04 LTS da Galeria de Imagens como exemplo de imagem do servidor e um desktop Ubuntu 14.04 como cliente. No portal, clique em **+ Novo** no canto inferior esquerdo para criar uma nova instância de máquina virtual e selecione uma imagem Ubuntu 14.04 LTS dentre as opções disponíveis ou na Galeria de Imagens, como mostramos abaixo.

> [WACOM.NOTE] No momento, apenas as imagens Ubuntu 14.04 LTS posteriores a julho de 2014 são compatíveis com a extensão de máquina virtual Docker.

![Criar uma nova imagem Ubuntu][Criar uma nova imagem Ubuntu]

## <a id'dockercerts'>Criar certificados do Docker</a>

Depois de criar a máquina virtual, verifique se o Docker está instalado no computador cliente. Para obter detalhes, confira [Instruções de instalação do Docker][Instruções de instalação do Docker].

Crie os arquivos de certificado e de chave para comunicação do Docker de acordo com as instruções em [Executando Docker com https][Executando Docker com https] e coloque-os no diretório **`~/.docker`** do computador cliente.

> [WACOM.NOTE] A extensão de máquina virtual Docker do portal requer credenciais codificadas em base64.

Na linha de comando, use **`base64`** ou outra ferramenta de codificação de sua preferência para criar tópicos codificados em base64. Para executar essas ações com um conjunto simples de arquivos de certificado e de chave, use um código parecido com este:

     ~/.docker$ l
     ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
     ~/.docker$ base64 ca.pem > ca64.pem
     ~/.docker$ base64 server-cert.pem > server-cert64.pem
     ~/.docker$ base64 server-key.pem > server-key64.pem
     ~/.docker$ l
     ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
     ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem

## <a id'adddockerextension'>Adicionar a extensão de máquina virtual Docker</a>

Para adicionar a extensão de máquina virtual Docker, localize a instância de máquina virtual criada e clique em **Extensões** para visualizar as extensões de máquina virtual, como mostramos abaixo.

![][0]

### Adicionar uma extensão

Clique em **+ Adicionar** para visualizar as extensões de máquina virtual que podem ser adicionadas à máquina em questão.

![][1]

### Selecionar a extensão de máquina virtual Docker

Escolha a extensão de máquina virtual Docker, que apresenta a descrição do Docker e links importantes. Em seguida, clique em **Criar** na parte inferior da página para iniciar o procedimento de instalação.

![][2]

![][3]

### Adicionar seus arquivos de certificado e de chaves:

Nos campos do formulário, insira as versões codificadas em base64 do certificado de autoridade de certificação, do certificado do servidor e da chave do servidor, como mostramos na imagem abaixo.

![][4]

> [WACOM.NOTE] Como a imagem acima mostra, a porta 4243 já aparece indicada por padrão. Você pode inserir qualquer ponto de extremidade nesse campo, mas precisará abrir o ponto de extremidade correspondente na próxima etapa. Se alterar o padrão, lembre-se de abrir o ponto de extremidade correspondente na etapa seguinte.

## Adicionar o ponto de extremidade de comunicação do Docker

Ao visualizar sua máquina virtual no grupo de recursos que você criou, navegue para clicar em **Pontos de extremidade** e visualizar os pontos na máquina virtual, como mostramos abaixo.

![][5]

Clique em **+ Adicionar** para adicionar outro ponto de extremidade. No caso padrão, insira o nome do ponto de extremidade (neste exemplo, **Docker**) e informe 4243 como porta privada e pública. Selecione **TCP** como valor do protocolo e clique em **OK** para criar o ponto de extremidade.

![][6]

## <span id="testclientandserver"></span>Testar cliente do Docker e host do Docker do Azure</a>

Localize e copie o nome do domínio da sua máquina virtual. Na linha de comando do computador cliente, insira `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:4243 info` (onde *dockerextension* é substituído pelo subdomínio da máquina virtual).

O resultado deve ter esta aparência:

    $ docker --tls -H tcp://dockerextension.cloudapp.net:4243 info
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
     Pool Name: docker-8:1-131214-pool
     Pool Blocksize: 65.54 kB
     Data file: /var/lib/docker/devicemapper/devicemapper/data
     Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
     Data Space Used: 305.7 MB
     Data Space Total: 107.4 GB
     Metadata Space Used: 729.1 kB
     Metadata Space Total: 2.147 GB
     Library Version: 1.02.82-git (2013-10-04)
    Execution Driver: native-0.2
    Kernel Version: 3.13.0-36-generic
    WARNING: No swap limit support

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Próximas etapas

Você está pronto para conferir o [Guia do usuário do Docker][Guia do usuário do Docker] e usar sua máquina virtual Docker. Para ver como começar a criar máquinas virtuais Docker no Azure com rapidez e de forma contínua, confira [Como usar a extensão de máquina virtual na interface de plataforma cruzada do Azure (xplat-cli)][Como usar a extensão de máquina virtual na interface de plataforma cruzada do Azure (xplat-cli)]

<!--Anchors--> <!--Image references--> <!--Link references-->

  [Docker]: https://www.docker.com/
  [contêineres Linux]: http://en.wikipedia.org/wiki/LXC
  [Agente Linux do Azure]: ../virtual-machines-linux-agent-user-guide/
  [Criar uma nova máquina virtual por meio da Galeria de Imagens]: #createvm
  [Criar certificados do Docker]: #dockercerts
  [Adicionar a extensão de máquina virtual Docker]: #adddockerextension
  [Testar cliente do Docker e host do Docker do Azure]: #testclientandserver
  [Próximas etapas]: #next-steps
  [Como usar a extensão de máquina virtual na interface de plataforma cruzada do Azure (xplat-cli)]: http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-docker-with-xplat-cli/
  [Quadro de comunicações de alto nível do Docker]: http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard
  [Criar uma nova imagem Ubuntu]: ./media/virtual-machines-docker-with-portal/ChooseUbuntu.png
  [Instruções de instalação do Docker]: https://docs.docker.com/installation/#installation
  [Executando Docker com https]: http://docs.docker.com/articles/https/
  [0]: ./media/virtual-machines-docker-with-portal/ClickExtensions.png
  [1]: ./media/virtual-machines-docker-with-portal/ClickAdd.png
  [2]: ./media/virtual-machines-docker-with-portal/ChooseDockerExtension.png
  [3]: ./media/virtual-machines-docker-with-portal/CreateButtonFocus.png
  [4]: ./media/virtual-machines-docker-with-portal/AddExtensionFormFilled.png
  [5]: ./media/virtual-machines-docker-with-portal/AddingEndpoint.png
  [6]: ./media/virtual-machines-docker-with-portal/AddEndpointFormFilledOut.png
  [Guia do usuário do Docker]: https://docs.docker.com/userguide/
