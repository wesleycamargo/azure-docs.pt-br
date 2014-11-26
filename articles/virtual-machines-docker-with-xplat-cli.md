<properties title="Como usar a extens&atilde;o de m&aacute;quina virtual Docker na interface de plataforma cruzada (xplat-cli) do Azure" pageTitle="Usando a extens&atilde;o de m&aacute;quina virtual Docker para Linux no Azure" description="Descreve as extens&otilde;es de m&aacute;quinas virtuais Docker e Azure Virtual, al&eacute;m de mostrar como criar m&aacute;quinas virtuais que sejam hosts do Docker programaticamente no Azure, por meio da linha de comando, usando a interface de comandos azure-cli." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill" />

# Usando a extensão de máquina virtual Docker na interface de plataforma cruzada (xplat-cli) do Azure

Este tópico descreve como criar uma máquina virtual com a extensão Docker na xplat-cli, em qualquer plataforma. O [Docker][Docker] é uma das abordagens de virtualização mais populares que utiliza [contêineres Linux][contêineres Linux] em vez de máquinas virtuais como forma de isolar dados e computar recursos compartilhados. Você pode usar a extensão da VM Docker do [Agente Linux do Azure][Agente Linux do Azure] para criar uma VM Docker que hospede diversos contêineres para seus aplicativos no Azure. Para participar de um debate de alto nível sobre contêineres e suas vantagens, confira o [Quadro de comunicações de alto nível do Docker][Quadro de comunicações de alto nível do Docker].

-   [Como usar a Extensão de VM Docker com o Azure][Como usar a Extensão de VM Docker com o Azure]
-   [Extensões de Máquina Virtual para Linux e Windows][Extensões de Máquina Virtual para Linux e Windows]
-   [Recursos de contêiner e gerenciamento de contêineres para o Azure][Recursos de contêiner e gerenciamento de contêineres para o Azure]
-   [Próximas etapas][Próximas etapas]

## <span id="How to use the Docker VM Extension with Azure"></span>Como usar a Extensão de VM Docker com o Azure</a>

Para usar a extensão de máquina virtual Docker com o Azure, você deve instalar uma [interface de linha de comando de plataforma cruzada do Azure][interface de linha de comando de plataforma cruzada do Azure] (que chamamos de **xplat-cli** neste tópico) posterior à versão 0.8.6. Até a edição deste artigo, a versão 0.8.10 era a mais atual. Você pode instalar a xplat-cli em Mac, Linux e Windows.

> [WACOM.NOTE] Embora seja possível instalar a xplat-cli no Microsoft Windows, o Docker foi criado com dependências de kernel específicas para o Linux. Por isso, para usar o Windows como cliente Docker, é necessário hospedar uma distribuição Linux completa como máquina virtual no Hyper-V ou em outro hipervisor. Em seguida, você pode usar a xplat-cli e os comandos do Docker nesse documento e nos documentos do Docker. Você pode usar o programa de configuração do Docker, o [Boot2Docker][Boot2Docker], para automatizar a mesma configuração.

O processo completo para usar o Docker no Azure é simples:

-   Instale as ferramentas de linha de comando da xplat-cli e suas dependências no computador que deverá controlar o Azure (no Windows, você deve instalar uma distribuição Linux executada como máquina virtual)
-   Use os comandos do Docker da xplat-cli para criar um host do Docker da máquina virtual no Azure
-   Use os comandos do Docker local para gerenciar os contêineres do seu Docker na sua VM do Docker no Azure.

> [WACOM.NOTE] No momento, a xplat-cli (interface de linha de comando) é a única forma de criar uma máquina virtual controlada pelo Docker no Azure para hospedar contêineres do Docker.

### Instalar a interface de linha de comando de plataforma cruzada (xplat-cli)

Para instalar e configurar a interface de linha de comando de plataforma cruzada, confira [Como instalar a interface de linha de comando entre plataformas do Azure][Como instalar a interface de linha de comando entre plataformas do Azure]. Para confirmar a instalação, digite `azure` no prompt de comando. Em instantes, você deverá ver a arte ASCII da xplat-cli, que lista os comandos básicos disponíveis. Se a instalação for bem-sucedida, você deverá poder digitar `azure help vm` e ver que um dos comandos listados é “docker”.

> [WACOM.NOTE] Se estiver usando uma instalação 14.04 LTS do Ubuntu, a imagem tem uma instalação de nó ligeiramente diferente que poderá demandar algum trabalho extra. Uma sugestão que parece funcionar bem está localizada [aqui][aqui], na seção **Como instalar usando um PPA**, que descreve como instalar a versão mais recente do nodejs diretamente e parece funcionar bem em uma distribuição 14.04 LTS do Ubuntu.

### Conectar a xplat-cli à sua conta do Azure

Antes de poder usar a xplat-cli, você deve associar as credenciais de sua conta do Azure à xplat-cli em sua plataforma. A seção [Como se conectar à assinatura do Azure][Como se conectar à assinatura do Azure] explica como baixar e importar o arquivo **.publishsettings** ou associar a linha de comando da xplat-cli a uma ID organizacional.

> [WACOM.NOTE] Há algumas diferenças no comportamento com o uso de cada um dos dois métodos de autenticação. Por isso, leia o documento acima para compreender as diferentes funcionalidades.

### Instalar o Docker e usar a extensão de VM do Docker para Azure

Siga as [Instruções de instalação do Docker][Instruções de instalação do Docker] para instalar o Docker localmente em seu computador. Para a maioria dos sistemas operacionais e distribuições, isso significa digitar `apt-get install docker.io`. Confirme que a versão do Docker é 1.0 ou superior.

Para usar o Docker com uma máquina virtual do Azure, o [Agente de máquina virtual Linux do Azure][Agente Linux do Azure] deve estar instalado na imagem Linux usada para a máquina virtual. No momento, apenas dois tipos de imagens possibilitam isso:

-   Uma imagem do Ubuntu da Galeria de Imagens do Azure ou

-   Uma imagem Linux personalizada criada por você, na qual o Agente de máquina virtual Linux do Azure esteja instalado e configurado. Confira [Agente de máquina virtual Linux do Azure][Agente Linux do Azure] para obter mais informações sobre como criar uma máquina virtual Linux personalizada com o agente.

### Usando a Galeria de Imagens do Azure

Em uma sessão bash ou de terminal, use o comando de xplat-cli a seguir para localizar a imagem mais recente do Ubuntu na galeria da máquina virtual.

`azure vm image list | grep Ubuntu-14_04`

Em seguida, selecione o nome de uma das imagens, como `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-pt-br-30GB`, e use o comando a seguir para criar uma nova máquina virtual por meio da imagem em questão.

    azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-pt-br-30GB" <username> <password>

onde:

-   *\<vm-cloudservice name\>* é o nome da máquina virtual que se tornará o computador host do contêiner Docker no Azure

-   *\<username\>* é o nome de usuário do usuário raiz padrão da máquina virtual

-   *\<password\>* é a senha da conta de *username* que atende aos padrões de complexidade do Azure

> [WACOM.NOTE] Atualmente, as senhas devem conter pelo menos oito caracteres, com um caractere em letra maiúscula e um em letra minúscula, um número e um caractere especial, como: `!@#$%^&+=`. Não, o ponto no final da frase anterior NÃO é um caractere especial.

Se o comando for bem-sucedido, você verá algo semelhante ao seguinte, dependendo dos argumentos e das opções precisas utilizadas:

![][0]

> [WACOM.NOTE] A criação de uma máquina virtual pode levar alguns minutos, mas após seu provisionamento, o daemon do Docker (o serviço Docker) é iniciado e você pode se conectar ao host do contêiner do Docker.

Para testar a VM do Docker que você criou no Azure, digite

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:4243 info`

em que *<vm-name-you-used>* é o nome da máquina virtual que você usou em sua chamada para `azure vm docker create`. Você deve ver algo semelhante ao código a seguir, que indica que sua máquina virtual host do Docker está funcionando no Azure e aguardando seus comandos.

![][1]

### Autenticação da máquina virtual host do Docker

Além de criar a VM do Docker, o comando `azure vm docker create` também cria automaticamente os certificados necessários para permitir que seu computador cliente Docker se conecte ao host do contêiner do Azure usando HTTPS, e os certificados são armazenados nas máquinas cliente e host, conforme adequado. Em execuções subsequentes, os certificados existentes são reutilizados e compartilhados com o novo host.

Por padrão, os certificados são colocados em `~/.docker` e o Docker será configurado para ser executado na porta **4243**. Se você desejar usar uma porta ou diretório diferentes, use uma das opções `azure vm docker create` da linha de comando a seguir para configurar a VM host do contêiner do Docker para usar outra porta ou outros certificados para conectar clientes:

    -dp, --docker-port [port]              Port to use for docker [4243]
    -dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]

o daemon do Docker no host é configurado para escutar e autenticar conexões clientes na porta especificada usando os certificados gerados pelo comando `azure vm docker create`. A máquina cliente deve ter esses certificados para obter acesso ao host do Docker.

> [WACOM.NOTE] Um host em rede em execução sem esses certificados ficará vulnerável a qualquer um que possa conectar-se à máquina. Antes de modificar a configuração padrão, certifique-se de que você compreende os riscos para seu computador e outros aplicativos.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Próximas etapas

Você está pronto para conferir o [Guia do usuário do Docker][Guia do usuário do Docker] e usar sua máquina virtual Docker. Para criar uma máquina virtual habilitada para o Docker no novo portal, confira [Como usar a extensão de máquina virtual Docker com o Portal][Como usar a extensão de máquina virtual Docker com o Portal].

<!--Anchors-->
<!--Image references-->
<!--Link references-->

  [Docker]: https://www.docker.com/
  [contêineres Linux]: http://en.wikipedia.org/wiki/LXC
  [Agente Linux do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-linux-agent-user-guide/
  [Quadro de comunicações de alto nível do Docker]: http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard
  [Como usar a Extensão de VM Docker com o Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
  [Extensões de Máquina Virtual para Linux e Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
  [Recursos de contêiner e gerenciamento de contêineres para o Azure]: #Container-and-Container-Management-Resources-for-Azure
  [Próximas etapas]: #next-steps
  [interface de linha de comando de plataforma cruzada do Azure]: https://github.com/Azure/azure-sdk-tools-xplat
  [Boot2Docker]: https://docs.docker.com/installation/windows/
  [Como instalar a interface de linha de comando entre plataformas do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/xplat-cli/#install
  [aqui]: https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server
  [Como se conectar à assinatura do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/xplat-cli/#configure
  [Instruções de instalação do Docker]: https://docs.docker.com/installation/#installation
  [0]: ./media/virtual-machines-docker/dockercreateresults.png
  [1]: ./media/virtual-machines-docker/connectingtodockerhost.png
  [Guia do usuário do Docker]: https://docs.docker.com/userguide/
  [Como usar a extensão de máquina virtual Docker com o Portal]: http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-docker-with-portal/
