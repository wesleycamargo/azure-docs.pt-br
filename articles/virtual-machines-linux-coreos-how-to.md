<properties title="Como usar o CoreOS no Azure" pageTitle="Como usar o CoreOS no Azure" description="Descreve o CoreOS, como criar uma m&aacute;quina virtual CoreOS no Azure e seu uso b&aacute;sico." metaKeywords="linux, virtual machines, vm, azure, CoreOS, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/27/2014" ms.author="rasquill" />

<!--This is a basic template that shows you how to use mark down to create a topic that includes a TOC, sections with subheadings, links to other azure.microsoft.com topics, links to other sites, bold text, italic text, numbered and bulleted lists, code snippets, and images. For fancier markdown, find a published topic and copy the markdown or HTML you want. For more details about using markdown, see http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->
<!--Properties section (above): this is required in all topics. Please fill it out! See http://sharepoint/sites/azurecontentguidance/wiki/Pages/Markdown%20tagging%20-%20add%20a%20properties%20section%20to%20your%20markdown%20file%20to%20specify%20metadata%20values.aspx for details. -->
<!-- Tags section (above): this is required in all topics. Please fill it out! See http://sharepoint/sites/azurecontentguidance/wiki/Pages/Markdown%20tagging%20-%20add%20a%20tags%20section%20to%20your%20markdown%20file%20to%20specify%20metadata%20for%20reporting.aspx for details. -->
<!--The next line, with one pound sign at the beginning, is the page title-->

# Como usar o CoreOS no Microsoft Azure

Este tópico descreve o [CoreOS][CoreOS] e mostra como criar um cluster de três máquinas virtuais CoreOS no Azure rapidamente. Ele usa os elementos mais básicos das implantações do CoreOS e os exemplos do [CoreOS com Azure][CoreOS com Azure], o [Tutorial de Tim Park sobre o CoreOS][Tutorial de Tim Park sobre o CoreOS] e o [Tutorial de Patrick Chanezon sobre o CoreOS][Tutorial de Patrick Chanezon sobre o CoreOS] a fim de demonstrar os requisitos mínimos para compreender a estrutura básica de uma implantação CoreOS e obter um cluster de três máquinas virtuais que executam sem problemas.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

Este tópico contém estas seções:

-   [CoreOS, clusters e contêineres Linux][CoreOS, clusters e contêineres Linux]
-   [Considerações de segurança][Considerações de segurança]
-   [Como usar o CoreOS no Azure][Como usar o CoreOS no Azure]
-   [Próximas etapas][Próximas etapas]

## <span id="intro"></span>CoreOS, clusters e contêineres Linux</a>

O CoreOS é uma versão leve do Linux, criada para fornecer suporte à criação rápida de clusters de máquinas virtuais possivelmente muito grandes e que usam contêineres Linux como único mecanismo de empacotamento, incluindo contêineres [Docker][Docker]. O CoreOS deve proporcionar:

-   altíssimo nível de automação
-   implantação de aplicativos mais fácil e mais consistente
-   escalabilidade dos aplicativos e sistemas

Os recursos do CoreOS que oferecem suporte a essas metas são:

1.  Um sistema em pacote: o CoreOS só executa imagens de contêineres Linux que são executadas em contêineres Linux a fim de garantir a velocidade, a uniformidade e a facilidade da implantação
2.  O sistema operacional é atualizado automaticamente; assim, os sistemas operacionais são atualizados como uma única entidade e é possível reverter as atualizações para um estado conhecido com facilidade
3.  Daemons (serviços) internos [etcd][etcd] e [fleet][fleet] para gerenciamento e comunicação dinâmica da máquina virtual e do cluster

Essa é a descrição genérica do CoreOS e de seus recursos. Para obter informações mais detalhadas sobre o CoreOS, confira [Visão geral do CoreOS][Visão geral do CoreOS].

## <span id="security"></span>Considerações de segurança</a>

No momento, o CoreOS parte do pressuposto de que todos que podem executar SSH no cluster têm permissão para gerenciá-los. Como resultado, sem modificações, os clusters CoreOS são excelentes para ambientes de teste e desenvolvimento, mas é necessário aplicar outras medidas de segurança em ambientes de produção.

## <span id="usingcoreos"></span>Como usar o CoreOS no Azure</a>

Esta seção descreve como criar um serviço de nuvem do Azure com três máquinas virtuais CoreOS por meio da [interface de linha de comando de plataforma cruzada (xplat-cli)][interface de linha de comando de plataforma cruzada (xplat-cli)]. Estas são as etapas básicas:

1.  Criar certificados SSH e chaves para garantir a segurança da comunicação com a máquina virtual CoreOS
2.  Obter a ID etcd do cluster para intercomunicação
3.  Criar um arquivo de configuração de nuvem no formato [YAML][YAML]
4.  Usar a xplat-cli para criar um novo serviço de nuvem do Azure e três máquinas virtuais CoreOS
5.  Testar o cluster CoreOS na máquina virtual do Azure
6.  Testar o cluster CoreOS no host local

### Criar chaves públicas e privadas para comunicação

Usar as instruções presentes em [Como usar SSH com Linux no Azure][Como usar SSH com Linux no Azure] a fim de criar chaves pública e privada para SSH. As etapas básicas estão descritas nas instruções abaixo. Você vai usar essas chaves para conectar-se às máquinas virtuais no cluster e verificar se elas estão funcionando e podem comunicar-se umas com as outras.

> [WACOM.NOTE] Este tópico parte do pressuposto de que você não tem essas chaves e precisa criar arquivos **`myPrivateKey.pem`** e **`myCert.pem`** para garantir a clareza. Se já salvou as chaves pública e privada em **`~/.ssh/id_rsa`**, basta digitar `openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem` para obter o arquivo .pem que você precisa carregar no Azure.

1.  Em um diretório ativo, digite `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem` para criar a chave privada e o certificado X.509 associado a ela.

2.  Para que o proprietário da chave privada possa ler ou editar o arquivo, digite `chmod 600 myPrivateKey.key`.

Agora, você deve ter um arquivo **`myPrivateKey.key`** e um arquivo **`myCert.pem`** em seu diretório ativo.

### Obter a ID etcd do cluster

O daemon **etcd** do CoreOS requer uma ID de descoberta para consultar todos os nós automaticamente no cluster. Para recuperar sua ID de descoberta e salvá-la em um arquivo **etcdid**, digite

    curl https://discovery.etcd.io/new | grep ^http.* > etcdid

### Criar um arquivo de configuração de nuvem

No mesmo diretório ativo, use o editor de texto de sua preferência para criar um arquivo com o texto exibido a seguir e salve-o como **`cloud-config.yaml`**. Você pode atribuir o nome que desejar a ele, mas precisará fazer referência ao nome desse arquivo na opção **--custom-data** do comando **azure create vm** ao criar suas máquinas virtuais na próxima etapa.

> [WACOM.NOTE] Lembre-se de digitar `cat etcdid` para recuperar a ID de descoberta do etcd do arquivo `etcdid` criado anteriormente e substituir **`<token>`** no arquivo **cloud-config.yaml** com o número geral por meio de seu arquivo `etcdid`. Se não puder validar o cluster ao final, é possível que você tenha pulado uma das etapas.

    #cloud-config

    coreos:
      etcd:
        # generate a new token for each unique cluster from https://discovery.etcd.io/new
        discovery: https://discovery.etcd.io/<token>
        # deployments across multiple cloud services will need to use $public_ipv4
        addr: $private_ipv4:4001
        peer-addr: $private_ipv4:7001
      units:
        - name: etcd.service
          command: start
        - name: fleet.service
          command: start

Para obter informações mais detalhadas sobre o arquivo de configuração de nuvem, confira [Usando a configuração de nuvem][Usando a configuração de nuvem] na documentação do CoreOS.

### Usar a xplat-cli para criar uma nova máquina virtual CoreOS

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

1.  Instale a [interface de linha de comando de plataforma cruzada (xplat-cli)][interface de linha de comando de plataforma cruzada (xplat-cli)] se ela ainda não estiver instalada. Em seguida, faça logon usando uma ID profissional ou escolar ou baixe um arquivo .publishsettings e importe-o em sua conta.
2.  Localize sua imagem CoreOS. No momento, há apenas uma imagem — **`2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0`**. Para localizar as imagens disponíveis a qualquer momento, digite `azure vm image list | grep .*CoreOS.*` para ver um ou mais resultados com esta aparência:
    `data:    2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0              Public    Linux`
3.  Digite
    `azure service create <cloud-service-name>` a fim de criar um serviço de nuvem para seu cluster básico, em que *cloud-service-name* é o nome de seu serviço de nuvem CoreOS. O nome desta amostra é **`coreos-cluster`**. Você precisará reutilizar o nome escolhido na criação das suas instâncias de máquina virtual CoreOS no serviço de nuvem.

Observação: Se observar o que já fez no [novo portal][novo portal], você encontrará um nome de serviço de nuvem que é um grupo de recursos e um domínio, como mostra a imagem abaixo:

![][0]

1.  Conecte-se a seu serviço de nuvem e crie uma nova máquina virtual CoreOS usando o comando **azure vm create**. Você indicará o local do certificado X.509 na opção **--ssh-cert**. Digite o código a seguir para criar a primeira imagem da máquina virtual. Lembre-se de substituir **coreos-cluster** pelo nome que você atribuiu ao serviço de nuvem:

<!-- -->

    azure vm create --custom-data=cloud-config.yaml --ssh=22 --ssh-cert=./myCert.pem --no-ssh-password --vm-name=node-1 --connect=coreos-cluster --location='West US' 2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0 core

1.  Repita o comando da etapa 4, substituindo o valor de **--vm-name** por **node-2** e o valor da porta de **--ssh** por 2022, para criar o segundo nó.

2.  Repita o comando da etapa 4, substituindo o valor de **--vm-name** por **node-3** e o valor da porta de **--ssh** por 3022, para criar o terceiro nó.

A imagem abaixo mostra como o cluster CoreOS aparece no novo portal.

![][1]

### Testar o cluster CoreOS na máquina virtual do Azure

Para testar o cluster, você deve estar no diretório ativo e conectar-se a **node-1** usando **ssh**, transmitindo a chave privada com este código:

`ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key`

Após a conexão, digite `sudo fleetctl list-machines` para ver se o cluster já identificou todas as máquinas virtuais que constam nele. Você deve receber uma resposta com esta aparência:

    core@node-1 ~ $ sudo fleetctl list-machines
    MACHINE     IP      METADATA
    442e6cfb... 100.71.168.115  -
    a05e2d7c... 100.71.168.87   -
    f7de6717... 100.71.188.96   -

### Testar o cluster CoreOS no host local

Por fim, vamos testar o cluster CoreOS no cliente Linux local por meio da instalação de **fleet**. O **fleet** requer o **golang**, que você pode instalar ao digitar o seguinte:

`sudo apt-get install golang`

Em seguida, clone o repositório **fleet** do github ao digitar:

`git clone https://github.com/coreos/fleet.git`

Crie o **fleet** ao digitar

`./build`

Por fim, disponibilize o **fleet** para que seja possível usá-lo com facilidade. Dependendo da sua configuração, você pode ou não precisar executar o **sudo**:

`cp bin/fleetctl /usr/local/bin`

Verifique se o **fleet** tem acesso a `myPrivateKey.key` no diretório ativo. Para isso, digite:

`ssh-add ./myPrivateKey.key`

> [WACOM.NOTE] Se já estiver usando a chave **~/.ss h/id\_rsa**, adicione-a com `ssh-add ~/.ssh/id_rsa`.

Agora você já pode fazer o teste remoto, usando o mesmo comando **fleetctl** usado em **node-1**, mas transmitindo alguns argumentos remotos:

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

Os resultados devem ser exatamente os mesmos:

    MACHINE     IP      METADATA
    442e6cfb... 100.71.168.115  -
    a05e2d7c... 100.71.168.87   -
    f7de6717... 100.71.188.96   -

## Próximas etapas

Agora você deve ter um cluster CoreOS com três nós no Azure. Deste ponto, você pode explorar como criar clusters mais complexos, usar o Docker e criar aplicativos mais interessantes. Para isso, leia o [Tutorial de Tim Park sobre o CoreOS][Tutorial de Tim Park sobre o CoreOS], o [Tutorial de Patrick Chanezon sobre o CoreOS Tutorial][Tutorial de Patrick Chanezon sobre o CoreOS], a documentação do [Docker][Docker] e a [Visão geral do CoreOS][Visão geral do CoreOS].

<!--Anchors-->
<!--Image references-->
<!--Link references-->

  [CoreOS]: https://coreos.com/
  [CoreOS com Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
  [Tutorial de Tim Park sobre o CoreOS]: https://github.com/timfpark/coreos-azure
  [Tutorial de Patrick Chanezon sobre o CoreOS]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
  [CoreOS, clusters e contêineres Linux]: #intro
  [Considerações de segurança]: #security
  [Como usar o CoreOS no Azure]: #usingcoreos
  [Próximas etapas]: #next-steps
  [Docker]: http://docker.io
  [etcd]: https://github.com/coreos/etcd
  [fleet]: https://github.com/coreos/fleet
  [Visão geral do CoreOS]: https://coreos.com/using-coreos/
  [interface de linha de comando de plataforma cruzada (xplat-cli)]: ../xplat-cli/
  [YAML]: http://yaml.org/
  [Como usar SSH com Linux no Azure]: http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-linux-use-ssh-key/
  [Usando a configuração de nuvem]: https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/
  [novo portal]: https://portal.azure.com
  [0]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
  [1]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png
