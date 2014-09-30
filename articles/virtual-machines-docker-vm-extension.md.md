<properties title="Using the Docker Virtual Machines Extension for Linux on Azure" pageTitle="Using the Docker VM Extension for Linux on Azure" description="Describes Docker and the Azure Virtual Machines extensions, and shows how to programmatically create Virtual Machines on Azure that are docker hosts from the command line using the azure-cli command interface." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="08/21/2014" ms.author="ralph.squillace@microsoft.com"></tags>

# Como usar a extensão de máquina virtual Docker para Linux no Azure

O [Docker](https://www.docker.com/) é uma das abordagens de virtualização mais populares que utiliza [contêineres Linux](http://en.wikipedia.org/wiki/LXC) em vez de máquinas virtuais como forma de isolar dados e computar recursos compartilhados. Você pode usar a extensão da VM Docker do [Agente Linux do Azure](http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-agent-user-guide/) para criar uma VM Docker que hospede diversos contêineres para seus aplicativos no Azure.

Este tópico segue este [anúncio do blog MS Open Tech](http://msopentech.com/blog/2014/08/15/getting_started_docker_on_microsoft_azure/) e descreve:

-   [Contêineres Docker e Linux][]
-   [Como usar a Extensão de VM Docker com o Azure][]
-   [Extensões de Máquina Virtual para Linux e Windows][]
-   [Recursos de contêiner e gerenciamento de contêineres para o Azure][]

## Contêineres Docker e Linux

O [Docker](https://www.docker.com/) é uma das abordagens de virtualização mais populares que usa [contêineres Linux](http://en.wikipedia.org/wiki/LXC) em vez de máquinas virtuais como uma forma de isolar dados e computar recursos compartilhados, e oferece outros serviços que lhe permitem criar ou compilar aplicativos rapidamente e distribuí-los entre outros contêineres Docker.

Contêineres Docker e Linux não são [Hipervisores](http://en.wikipedia.org/wiki/Hypervisor) como o Windows Hyper-V e o [KVM](http://www.linux-kvm.org/page/Main_Page) no Linux (há muitos outros exemplos). Hipervisores virtualizam o sistema operacional subjacente para permitir que sistemas operacionais completos sejam executados dentro do hipervisor como se fossem um aplicativo.

A abordagem do hipervisor oferece importantes vantagens de segurança porque a máquina virtual “convidada" não tem nenhum acesso ao sistema operacional “hóspede"; em vez disso, ela pode usar somente os recursos do hipervisor. As desvantagens, no entanto, incluem maiores sobrecargas de processamento e armazenamento, bem como um tempo de inicialização um pouco maior para as novas máquinas virtuais, por que - entre outros motivos - elas replicam totalmente os sistemas operacionais convidados.

#### Contêineres Docker e Linux

O Docker e outras abordagens de *contêiner* diminuíram radicalmente o tempo de inicialização e a sobrecarga de processamento e armazenamento necessária para usar os recursos de isolamento de sistema e processo do kernel Linux para expor somente recursos do kernel a um contêiner que, de outra forma, permanece isolado. De dentro do contêiner, os recursos de sistema de arquivos e kernel aparecem para o aplicativo como se fossem o único aplicativo em execução.

Além disso, o Docker oferece diversos recursos de gerenciamento de contêineres que permitem carregar diversas imagens de contêiner da comunidade, bem como compilar e carregar seus próprios contêineres, com muita rapidez. Para obter informações completas sobre o Docker e como ele realmente funciona, consulte [O que é o Docker?](https://www.docker.com/whatisdocker/).

Como a maioria das tecnologias, há vantagens substanciais e algumas desvantagens. Como os contêineres compartilham o acesso ao kernel do computador hóspede, se códigos mal-intencionados conseguirem acessar a raiz, eles poderão também acessar não só o computador hóspede, mas todos os outros contêineres. Para que a segurança do seu sistema seja mais sólida, o [Docker recomenda](https://docs.docker.com/articles/security/) usar também políticas de grupo adicionais ou [segurança baseada em função](http://en.wikipedia.org/wiki/Role-based_access_control), como [SELinux](http://selinuxproject.org/page/Main_Page) ou [AppArmor](http://wiki.apparmor.net/index.php/Main_Page), bem como reduzir ao máximo as capacidades de kernel concedidas aos contêineres. Além disso, há muitos outros documentos na Internet que descrevem abordagens de segurança ao usar contêineres como o Docker.

## Como usar a Extensão de VM Docker com o Azure

Para usar a extensão de VM do Docker com o Azure, você deve instalar a versão do [azure-cli](https://github.com/Azure/azure-sdk-tools-xplat) superior a 0.8.6 (até o momento da redação deste artigo, a versão atual é 0.8.7). Você pode instalar o azure-cli em Mac e Linux.

> [WACOM.NOTE] Você pode também instalar o azure-cli no Microsoft Windows. No entanto, com o Docker foi criado com dependências de kernel Linux, usar o Windows como cliente do Docker requer que você hospede uma distribuição Linux completa dentro de uma máquina virtual dentro do Hyper-V ou de outro hipervisor. Após ter feito isso, você pode usar o azure-cli e os comandos do Docker neste documento e nos do Docker. O Docker tem um programa de configuração, [Boot2Docker](https://docs.docker.com/installation/windows/), que você também pode usar para automatizar a mesma configuração.

O processo completo para usar o Docker no Azure é simples:

-   Instale as ferramentas de linha de comando do Azure e suas dependências no computador a partir do qual você deseja controlar o Azure (no Windows, será uma distribuição Linux sendo executada como uma máquina virtual)
-   Use os comandos do Docker do azure-cli para criar um host do Docker da MV no Azure
-   Use os comandos do Docker local para gerenciar os contêineres do seu Docker na sua VM do Docker no Azure.

> [WACOM.NOTE] O azure-cli (interface de linha de comando) é, atualmente, a única forma de criar uma VM controlada pelo Docker no Azure para hospedar contêineres do Docker. O documento de instalação geral está [aqui](http://azure.microsoft.com/en-us/documentation/articles/xplat-cli/#install); as seções abaixo oferecem algumas sugestões extras para instalar de maneira simples em diversos sistemas operacionais.

### Instalar o azure-cli no Linux

No Linux, a instalação do azure-cli requer o [gerente de pacote de nós (npm)](http://en.wikipedia.org/wiki/Npm_%28software%29), que requer o nodejs, de modo que você usará o seu gerente de pacotes preferido para instalar o nodejs, dependendo da plataforma que escolher. Se tiver o npm instalado, para obter o pacote do azure-cli você digita:

    npm install -g azure-cli

que instalará o pacote do azure-cli globalmente. Para confirmar a instalação, digite `azure` no prompt de comando e, após um breve momento, você deverá ver a arte ASCII do azure-cli, que lista os comandos básicos disponíveis. Se a instalação for bem-sucedida, você deverá poder digitar `azure help vm` e ver que um dos comandos listados é “docker”.

> [WACOM.NOTE] Se estiver usando uma instalação 14.04 LTS do Ubuntu, a imagem tem uma instalação de nó ligeiramente diferente que poderá demandar algum trabalho extra. Uma sugestão que parece funcionar bem está localizada [aqui](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server), na seção **Como instalar usando um PPA**, que descreve como instalar a versão mais recente do nodejs diretamente e parece funcionar bem em uma distribuição 14.04 LTS do Ubuntu.

Como com a maioria dos componentes Linux, você também pode clonar a fonte, compilá-la e instalá-la localmente. As instruções de como fazer isso estão localizadas em [azure-cli](https://github.com/Azure/azure-sdk-tools-xplat).

### Instalar o azure-cli em Mac

Em um Mac, a maneira mais fácil de instalar o azure-cli também é usar o npm com o mesmo comando: `npm install -g azure-cli`. No entanto, você também pode usar o [instalador do Mac](http://go.microsoft.com/fwlink/?linkid=252249&clcid=0x409). Assim como no Linux e no Windows, digite `azure` no prompt de comando associado e confirme se o azure-cli foi instalado.

### Conectar o azure-cli à sua conta do Azure

Antes de poder usar o azure-cli, você deve associar as credenciais de sua conta do Azure com o azure-cli em sua plataforma. A seção [Como se conectar à assinatura do Azure](http://azure.microsoft.com/en-us/documentation/articles/xplat-cli/#configure) explica como baixar e importar o arquivo **.publishsettings** ou associar a linha de comando do azure-cli com uma ID organizacional. As etapas dos dois métodos de autenticação estão descritas no documento acima.

> [WACOM.NOTE] Há algumas diferenças de comportamento ao usar cada um dos dois métodos de autorização, então, certifique-se de ler o documento acima para compreender as diferentes funcionalidades.

### Instalar o Docker e usar a extensão de VM do Docker para Azure

Agora, você tem um computador (ou uma máquina virtual nesse computador) com o azure-cli instalado e conectado à sua conta do Azure. Siga as [Instruções de instalação do Docker](https://docs.docker.com/installation/#installation) para instalar o Docker localmente em seu computador. Para a maioria dos sistemas operacionais e distribuições, isso significa digitar `apt-get install docker.io`. Confirme que a versão do Docker é 1.0 ou superior.

Você instalou o prompt do azure-cli em seu computador, o conectou à sua conta do Azure e instalou o Docker. Criar uma nova VM host do Docker no Azure requer uma imagem de VM Linux que tenha o [Agente de VM Linux do Azure](http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-agent-user-guide/) instalado. Atualmente, as únicas imagens que o têm instalado são

-   uma imagem do Ubuntu da galeria de imagens ou

-   uma imagem Linux personalizada que você criou com o Agente de VM Linux do Azure instalado e configurado; consulte [Agente de VM Linux do Azure](http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-agent-user-guide/) para obter mais informações sobre como criar sua própria VM Linux com o Agente de VM do Azure.

Com o prompt de comando do azure-cli, localize a imagem do Ubuntu mais recente na galeria de VMs a serem usadas digitando

`azure vm image list | grep Ubuntu-14_04`

e copie o nome de uma das imagens mais recentes listadas. No prompt de comando, digite

    azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20140729-alpha2-en-us-30GB" <username> <password>

onde:

-   <em><vm-cloudservice name> é o nome da VM que se tornará o computador host do contêiner Docker no Azure

-   <em><username> é o nome de usuário do usuário raiz padrão da VM

-   <em><password> é a senha da conta do *nome de usuário* que atende aos padrões de complexidade do Azure

> [WACOM.NOTE] Atualmente, uma senha deve conter pelo menos 8 caracteres, com um caractere em letra maiúscula e um em letra minúscula, um número e um caractere especial, como !@\#$%^&+=. Não, o ponto no final da frase anterior NÃO é um caractere especial.

Se o comando for bem-sucedido, você verá algo semelhante ao seguinte, dependendo dos argumentos e das opções precisas utilizadas:

![](./media/virtual-machines-docker/dockercreateresults.png)

> [WACOM.NOTE] Conforme mencionado anteriormente, criar uma máquina virtual pode levar alguns minutos, a criação de uma máquina virtual pode levar alguns minutos, mas após seu provisionamento, o daemon do Docker é iniciado e você pode se conectar ao host do contêiner do Docker.

Para testar a VM do Docker que você criou no Azure, digite

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:4243 info`

em que <em><vm-name-you-used> é o nome da máquina virtual que você usou em sua chamada para `azure vm docker create`. Mais uma vez, dependendo das opções e dos argumentos especificados, você deverá ver algo semelhante à resposta a seguir, que indica que sua VM host do Docker está funcionando no Azure e aguardando seus comandos.

![](./media/virtual-machines-docker/connectingtodockerhost.png)

## Uma observação sobre a autenticação de VM host do Docker

Além de criar a VM do Docker, o comando `azure vm docker create` também cria automaticamente os certificados necessários para permitir que seu computador cliente Docker se conecte ao host do contêiner do Azure usando HTTPS, e os certificados são armazenados nas máquinas cliente e host, conforme adequado. Em execuções subsequentes, os certificados existentes são reutilizados e compartilhados com o novo host.

Por padrão, os certificados são colocados em `~/.docker` e o Docker será configurado para ser executado na porta **4243**. Se você desejar usar uma porta ou diretório diferentes, use uma das opções `azure vm docker create` da linha de comando a seguir para configurar a VM host do contêiner do Docker para usar outra porta ou outros certificados para conectar clientes:

    -dp, --docker-port [port]              Port to use for docker [4243]
    -dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]

o daemon do Docker no host é configurado para escutar e autenticar conexões clientes na porta especificada usando os certificados gerados pelo comando `azure vm docker create`. A máquina cliente deve ter esses certificados para obter acesso ao host do Docker.

> [WACOM.NOTE] De modo oposto, um host em rede sendo executado sem esses certificados ficará vulnerável a qualquer um que possa se conectar à máquina. Antes de modificar a configuração padrão, certifique-se de que você compreende os riscos para seu computador e outros aplicativos.

## Próximas etapas

Agora, você está pronto para emitir comandos do Docker [Guia do usuário do Docker](https://docs.docker.com/userguide/) com relação à sua VM host do Docker no Azure.

## Extensões de Máquina Virtual para Linux e Windows

A extensão de VM do Docker para Azure é apenas uma das diversas extensões que oferecem comportamentos especiais, e outras estão sendo desenvolvidas. Por exemplo, diversos recursos da [Extensão do Agente de VM Linux](http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-agent-user-guide/) permitem que você modifique e gerencie a imagem, incluindo recursos de segurança, recursos de kernel e rede e assim por diante. A extensão VMAccess para imagens do Windows permite que você redefina ou modifique as configurações de Acesso à Área de trabalho remota e redefina a senha do administrador.

Para obter uma lista completa, consulte [Extensões de VM do Azure](http://msdn.microsoft.com/en-us/library/azure/dn606311.aspx).

<!--Anchors-->

  [Docker]: https://www.docker.com/
  [contêineres Linux]: http://en.wikipedia.org/wiki/LXC
  [Agente Linux do Azure]: http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-agent-user-guide/
  [anúncio do blog MS Open Tech]: http://msopentech.com/blog/2014/08/15/getting_started_docker_on_microsoft_azure/
  [Contêineres Docker e Linux]: #Docker-and-Linux-Containers
  [Como usar a Extensão de VM Docker com o Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
  [Extensões de Máquina Virtual para Linux e Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
  [Recursos de contêiner e gerenciamento de contêineres para o Azure]: #Container-and-Container-Management-Resources-for-Azure
  [Hipervisores]: http://en.wikipedia.org/wiki/Hypervisor
  [KVM]: http://www.linux-kvm.org/page/Main_Page
  [O que é o Docker?]: https://www.docker.com/whatisdocker/
  [Docker recomenda]: https://docs.docker.com/articles/security/
  [segurança baseada em função]: http://en.wikipedia.org/wiki/Role-based_access_control
  [SELinux]: http://selinuxproject.org/page/Main_Page
  [AppArmor]: http://wiki.apparmor.net/index.php/Main_Page
  [azure-cli]: https://github.com/Azure/azure-sdk-tools-xplat
  [Boot2Docker]: https://docs.docker.com/installation/windows/
  [aqui]: http://azure.microsoft.com/en-us/documentation/articles/xplat-cli/#install
  [gerente de pacote de nós (npm)]: http://en.wikipedia.org/wiki/Npm_%28software%29
  [1]: https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server
  [instalador do Mac]: http://go.microsoft.com/fwlink/?linkid=252249&clcid=0x409
  [Como se conectar à assinatura do Azure]: http://azure.microsoft.com/en-us/documentation/articles/xplat-cli/#configure
  [Instruções de instalação do Docker]: https://docs.docker.com/installation/#installation
  [!@\#$%^&+=]: mailto:!@#$%^&+=
  []: ./media/virtual-machines-docker/dockercreateresults.png
  [2]: ./media/virtual-machines-docker/connectingtodockerhost.png
  [Guia do usuário do Docker]: https://docs.docker.com/userguide/
  [Extensões de VM do Azure]: http://msdn.microsoft.com/en-us/library/azure/dn606311.aspx
