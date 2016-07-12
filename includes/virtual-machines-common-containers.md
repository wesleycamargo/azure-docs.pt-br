


O Azure oferece excelentes soluções de nuvem, baseadas em máquinas virtuais - baseadas na emulação do hardware de um computador físico - para habilitar a movimentação ágil de implantações de software e uma consolidação de recursos consideravelmente melhor do que com hardware físico. Nos últimos anos, em grande parte graças à abordagem do [Docker](https://www.docker.com) a contêineres e ao ecossistema de docker, a tecnologia de contêiner do Linux expandiu radicalmente as maneiras como você pode desenvolver e gerenciar softwares distribuídos. O código de aplicativo em um contêiner fica isolado do da VM host do Azure, bem como de outros contêineres na mesma VM, o que lhe dá mais agilidade no desenvolvimento e na implantação no nível de aplicativo - além da agilidade que as VMs do Azure já oferecem.

**Mas isso já não é novidade.** A notícia que é realmente *nova* é que o Azure oferece ainda mais benefícios do Docker:

- [Muitas maneiras](../articles/virtual-machines/virtual-machines-linux-docker-machine.md) [diferentes](../articles/virtual-machines/virtual-machines-linux-dockerextension.md) de criar hosts do Docker para contêineres de acordo com sua situação
- O [Gerenciador de Recursos do Azure](../articles/resource-group-overview.md) e os [modelos do grupo de recursos](../articles/resource-group-authoring-templates.md) para simplificar a implantação e a atualização de aplicativos distribuídos complexos
- Integração com uma ampla variedade de ferramentas de gerenciamento de configuração patenteadas e de código aberto

E como no Azure você pode criar contêineres Linux e VMs programaticamente, também é possível usar ferramentas de *orquestração* de contêiner e VM para criar grupos de VMs (Máquinas Virtuais) e implantar aplicativos em contêineres do Linux e, em breve, [Contêineres do Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

Este artigo discute não apenas esses conceitos em um nível amplo, mas também um monte de links para mais informações, tutoriais e produtos relacionados ao uso de contêineres e clusters no Azure. Se você já sabe de tudo isso e quer apenas os links, eles estão lá, em [ferramentas para trabalhar com contêineres](#tools-for-working-with-containers).

## A diferença entre máquinas virtuais e contêineres

Máquinas virtuais são executadas em um ambiente de virtualização de hardware isolado fornecido por um [hipervisor](http://en.wikipedia.org/wiki/Hypervisor). No Azure, o serviço de [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/) cuida de tudo isso para você: você simplesmente cria as Máquinas Virtuais escolhendo o sistema operacional e configurando-o para ser executado da forma que desejar, ou carregando sua própria imagem de VM personalizada. Máquinas virtuais são uma tecnologia que vem sendo testada há bastante tempo e que ficou mais robusta com o tempo, e há muitas ferramentas disponíveis para gerenciar sistemas operacionais e configurar os aplicativos que você instala e executa. Qualquer coisa em execução em uma máquina virtual fica oculta do sistema operacional host e, do ponto de vista de um aplicativo ou usuário que está executando na máquina virtual, ela parece ser um computador físico autônomo.

[Contêineres Linux](http://en.wikipedia.org/wiki/LXC) - que incluem aqueles criados e hospedados usando ferramentas de docker, além de outras abordagens - não usam ou precisam de um hipervisor para fornecer isolamento. Em vez disso, o host do contêiner usa os recursos de isolamento de sistema de arquivos e processos do kernel do Linux para expor ao contêiner (e seu aplicativo) apenas determinados recursos do kernel e seu próprio sistema de arquivos isolado (no mínimo). Do ponto de vista de um aplicativo em execução dentro de um contêiner, o contêiner parece ser uma instância exclusiva do sistema operacional. Um aplicativo independente não pode ver processos ou quaisquer outros recursos fora de seu contêiner.

Como nesse modelo de isolamento e execução o kernel do computador host do Docker é compartilhado, e os requisitos de disco do contêiner agora não incluem um sistema operacional completo, o tempo de inicialização do contêiner e a sobrecarga de armazenamento em disco necessária são muito, muito menores.

É bem legal.

Os contêineres do Windows oferecem as mesmas vantagens que os contêineres do Linux, mas para aplicativos executados no Windows. Contêineres do Windows tem suporte no formato de imagem e API de Docker, mas também podem ser gerenciados usando o PowerShell. Dois tempos de execução do contêiner estão disponíveis com contêineres do Windows, contêineres do Windows Server e contêineres do Hyper-V. Contêineres de Hyper-V fornecem uma camada adicional de isolamento, hospedando cada contêiner em uma máquina virtual superotimizada. Para saber mais sobre contêineres do Windows, consulte [Sobre contêineres do Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview). Para tentar contêineres do Windows no Azure, consulte o [Início rápido de contêiner do Windows no Azure](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/azure_setup).

Isso também é bem legal.

### Muito bom para ser verdade?

Bem, sim e não. Os contêineres, como qualquer outra tecnologia, não eliminam num passe de mágica todo o trabalho pesado exigido por aplicativos distribuídos. No entanto, eles realmente mudam:

- a velocidade com que o código do aplicativo pode ser desenvolvido e compartilhado amplamente
- a velocidade e a confiabilidade com que ele pode ser testado
- a velocidade e a confiabilidade com que ele pode ser implantado

Dito isso, lembre-se de que os contêineres são executados em um host do contêiner - um sistema operacional, e no Azure isso significa uma Máquina Virtual do Azure. Mesmo que já adore a ideia dos contêineres, você ainda vai precisar de uma infraestrutura de VM que hospede os contêineres, mas o benefício é que os contêineres não se importam com a máquina virtual em que estão sendo executados (embora seja importante se o contêiner quiser um ambiente de execução Linux ou Windows, por exemplo).

## Para que os contêineres são eficazes?

Eles são ótimos para muitas coisas, mas incentivam, assim como os [Serviços de nuvem do Azure](https://azure.microsoft.com/services/cloud-services/) e [Service Fabric do Azure](../articles/service-fabric/service-fabric-overview.md), a criação de aplicativos de serviço único, orientados a [microsserviços], nos quais o design de aplicativos se baseia mais em partes menores, que podem ser compostas, em vez de componentes maiores e mais acoplados.

Isso vale ainda mais em ambientes de nuvem pública como o Azure, nos quais você aluga as VMs onde e quando precisa delas. Você obtém não só ferramentas de orquestração com implantação rápida e isolamento, mas pode tomar decisões mais eficientes de infraestrutura de aplicativo.

Por exemplo, você pode ter atualmente uma implantação que consiste em 9 VMs do Azure de tamanho grande para um aplicativo distribuído de alta disponibilidade. Se os componentes do aplicativo puderem ser implantados em contêineres, você poderá usar somente 4 VMs e implantar os componentes do aplicativo dentro de 20 contêineres para maior redundância e balanceamento de carga.

Este é apenas um exemplo, é claro, mas se puder fazer isso em seu contexto, você poderá se ajustar para os picos de uso com mais contêineres em vez de mais VMs do Azure e usar a carga de CPU restante geral de forma muito mais eficiente.

Além disso, há muitos contextos em que a uma abordagem de microsserviços não é adequada. Você saberá melhor se os microsserviços ou os contêineres é que vão ajudá-lo.

### Benefícios do contêiner para desenvolvedores

É fácil ver de modo geral que a tecnologia de contêineres é um passo à frente, mas também há benefícios mais específicos. Vejamos o exemplo dos contêineres do Docker. Este tópico não tratará profundamente do Docker agora (leia [O que é Docker?](https://www.docker.com/whatisdocker/) ou a [wikipédia](http://wikipedia.org/wiki/Docker_%28software%29) para saber mais), mas o Docker e seu ecossistema oferecem benefícios significativos para desenvolvedores e profissionais de TI.

Os desenvolvedores adotam contêineres do Docker rapidamente, porque sobretudo ele facilita o uso de contêineres do Linux:

- Eles podem usar comandos simples e incrementais para criar uma imagem fixa que é fácil de implantar e pode automatizar a criação dessas imagens usando um dockerfile
- Eles podem compartilhar essas imagens facilmente usando comandos push e pull simples no estilo [git](https://git-scm.com/) para [registros docker públicos](https://registry.hub.docker.com/) ou [privados](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md)
- Pense em componentes de aplicativo isolados em vez de computadores
- Eles podem usar um grande número de ferramentas que compreendem contêineres docker e diferentes imagens de base

### Benefícios do contêiner para profissionais de TI e operações

Profissionais de TI e operações também se beneficiam da combinação de contêineres e máquinas virtuais.

- Serviços independentes ficam isolados do ambiente de execução do host da VM
- Códigos independentes são comprovadamente idênticos
- Serviços independentes podem ser iniciados, interrompidos e movidos rapidamente entre ambientes de produção, teste e desenvolvimento

Recursos como esses - e há mais deles - auxiliam empresas estabelecidas, em que empresas profissionais de tecnologia da informação têm a função de ajustar recursos - incluindo a pura capacidade de processamento - às tarefas necessárias não apenas para permanecer nos negócios, mas para aumentar o alcance e a satisfação do cliente. Pequenas empresas, ISVs e startups têm exatamente os mesmos requisitos, mas podem descrevê-los de formas diferentes.

## Para que as máquinas virtuais são eficazes?

As máquinas virtuais compõem a espinha dorsal da computação em nuvem, e isso não muda. Embora as máquinas virtuais iniciarem mais lentamente, ocuparem mais espaço em disco e não mapearem diretamente para uma arquitetura de microsserviços, eles têm muitos benefícios importantes:

1. Por padrão, elas têm proteções de segurança padrão muito mais robustas para o computador host
2. Elas dão suporte a qualquer uma das principais configurações de aplicativos e sistemas operacionais
3. Elas têm ecossistemas de ferramentas permanentes para comando e controle
4. Elas fornecem o ambiente de execução para contêineres host

O último item é importante, porque um aplicativo independente ainda requer um tipo específico de CPU e sistema operacional, dependendo das chamadas que o aplicativo fará. É importante lembrar-se de que você instala contêineres em VMs porque eles contêm os aplicativos que você deseja implantar; os contêineres não substituem VMs ou sistemas operacionais.

## Comparação geral de recursos de VMs e contêineres

A tabela a seguir descreve de maneira ampla as diferenças entre os recursos que existem - sem que haja muito trabalho extra - entre contêineres Linux e VMs. Observe que alguns recursos podem ser mais ou menos desejáveis dependendo das necessidades do seu aplicativo, e que, assim como acontece com todos os softwares, o trabalho extra dá mais suporte a recursos, especialmente na área de segurança.

| Recurso | VMs | Contêineres |
| :------------- |-------------| ----------- |
| Suporte de segurança "padrão" | em um nível mais alto | em um nível um pouco menor |
| Requisição de memória em disco | SO completo, mais aplicativos | Somente os requisitos dos aplicativos |
| Tempo necessário para iniciar | Muito mais longo: inicialização do sistema operacional e carregamento do aplicativo | Muito mais curto: somente os aplicativos precisam ser iniciados porque o kernel já está em execução |
| Portabilidade | Portátil com a preparação adequada | Portátil em dentro do formato de imagem; normalmente menor |
| Automação de imagem | Varia muito de acordo com o SO e os aplicativos | [Registro Docker](https://registry.hub.docker.com/); outros

## Criando e gerenciando grupos de VMs e contêineres

Neste ponto, qualquer arquiteto, desenvolvedor ou especialista de operações de TI pode estar pensando, "Eu posso automatizar TUDO isso. Esse realmente É um banco de dados como serviço! ".

Você está certo, ele pode ser, e há uma variedade de sistemas, muitos dos quais você talvez já use, que podem gerenciar grupos de VMs do Azure e injetar código personalizado usando scripts, geralmente com a [CustomScriptingExtension para Windows](https://msdn.microsoft.com/library/azure/dn781373.aspx) ou a [CustomScriptingExtension para Linux](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/). Você pode automatizar suas implantações do Azure usando scripts do PowerShell ou CLI do Azure [como esses](../articles/virtual-machines/virtual-machines-windows-ps-create.md).

Essas capacidades frequentemente são migradas para ferramentas como [Puppet](https://puppetlabs.com/) e [Chef](https://www.chef.io/) para automatizar a criação e configuração de VMs em escala. (Estes são alguns links para [usar essas ferramentas com o Azure](#tools-for-working-with-containers).)

### Modelos do grupo de recursos do Azure

Mais recentemente, o Azure lançou a API REST de [Gerenciamento de recursos do Azure](../articles/virtual-machines/virtual-machines-windows-compare-deployment-models.md) e atualizou as ferramentas PowerShell e CLI do Azure para usá-lo facilmente. Você pode implantar, modificar ou reimplantar topologias de aplicativo usando [modelos do Gerenciador de Recursos do Azure](../articles/resource-group-authoring-templates.md) com a API de gerenciamento de recursos do Azure, usando:

- o [portal do Azure usando modelos](https://github.com/Azure/azure-quickstart-templates); dica, use o botão "DeployToAzure"
- [A CLI do Azure](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md)
- os [módulos de Azure PowerShell](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md)


### Implantação e gerenciamento de grupos inteiros de VMs do Azure e contêineres

Há vários sistemas populares que podem implantar grupos inteiros de VMs e instalar o Docker (ou outros sistemas de host de contêineres do Linux) nelas como um grupo automatizável. Para obter os links diretos, consulte a seção [contêineres e ferramentas](#containers-and-vm-technologies) abaixo. Há vários sistemas que fazem isso de forma mais ou menos ampla, e essa lista não é exaustiva. Dependendo das suas habilidades e do contexto, eles podem ou não ser úteis.

O Docker tem seu próprio conjunto de ferramentas de criação de VMs ([docker-machine](../articles/virtual-machines/virtual-machines-linux-docker-machine.md)) e uma ferramenta de balanceamento de carga e gerenciamento de cluster de contêineres do Docker ([swarm](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md)). Além disso, a [Extensão de VM do Docker do Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md) vem com suporte padrão para [`docker-compose`](https://docs.docker.com/compose/), que pode implantar contêineres de aplicativos configurados em vários contêineres.

Além disso, você pode experimentar o [DCOS (Sistema Operacional de Data  
Center) do Mesosphere](http://docs.mesosphere.com/install/azurecluster/). O DCOS baseia-se no "kernel de sistemas distribuídos" de software livre [mesos](http://mesos.apache.org/), que permite que você trate seu data center como um serviço endereçável. O DCOS tem pacotes internos para vários sistemas importantes, como [Spark](http://spark.apache.org/) e [Kafka](http://kafka.apache.org/) (e outros), bem como serviços internos como [Marathon](https://mesosphere.github.io/marathon/) (um sistema de controle de contêineres) e [Chronos](https://mesos.github.io/chronos/) (um agendador distribuído). O Mesos foi derivado de lições aprendidas com o Twitter, AirBnb e outras empresas de escala da Web.

Além disso, o [kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/) é um sistema de software livre para gerenciamento de grupos de VMs e contêineres, derivado de lições aprendidas no Google. Você pode até usar o [kubernetes com trança para fornecer suporte a redes](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave).

O [Deis](http://deis.io/overview/) é uma PaaS (Plataforma como Serviço) de software livre que facilita a implantação e o gerenciamento de aplicativos em seus próprios servidores. O Deis é usado em conjunto com o Docker e o CoreOS para fornecer uma PaaS leve com fluxo de trabalho inspirado no Heroku. Você pode facilmente [criar um grupo de VMs do Azure de 3 nós e instalar o Deis](../articles/virtual-machines/virtual-machines-linux-deis-cluster.md) no Azure e, em seguida, [instalar um aplicativo Hello World Go](../articles/virtual-machines/virtual-machines-linux-deis-cluster.md#deploy-and-scale-a-hello-world-application).

O [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html), uma distribuição do Linux com superfície otimizada, suporte ao Docker e seu próprio sistema de contêineres chamado [rkt](https://github.com/coreos/rkt), também tem uma ferramenta de gerenciamento de grupos de contêineres chamada [fleet](https://coreos.com/using-coreos/clustering/).

O Ubuntu, outra distribuição muito popular do Linux, dá um suporte muito bom ao Docker, mas dá suporte também a [clusters Linux (estilo LXC)](https://help.ubuntu.com/lts/serverguide/lxc.html).

## Ferramentas para trabalhar com máquinas virtuais do Azure e contêineres

Trabalhar com contêineres e VMs do Azure usa ferramentas. Esta seção fornece uma lista de alguns dos conceitos e ferramentas mais úteis ou importantes sobre contêineres, grupos e as ferramentas de configuração e orquestração usadas com elas.

> [AZURE.NOTE] Essa área está mudando com uma velocidade incrível e, embora façamos o melhor para manter este tópico e seus links atualizados, esta pode ser uma tarefa quase impossível. Certifique-se de pesquisar sobre os assuntos interessantes para se manter atualizado!

### Tecnologias de contêineres e VMs

Algumas tecnologias de contêineres do Linux:

- [Docker](https://www.docker.com)
- [LXC](https://linuxcontainers.org/)
- [CoreOS e rkt](https://github.com/coreos/rkt)
- [Open Container Project](http://opencontainers.org/)
- [RancherOS](http://rancher.com/rancher-os/)

Links para contêineres do Windows:

- [Contêineres do Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)

Links do Visual Studio Docker:

- [Ferramentas do Visual Studio 2015 RC para Docker - Visualização](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

Ferramentas do Docker:

- [Docker daemon](https://docs.docker.com/installation/#installation)
- Clientes Docker
	- [Cliente Docker do Windows no Chocolatey](https://chocolatey.org/packages/docker)
	- [Instruções de instalação do Docker](https://docs.docker.com/installation/#installation)


Docker no Microsoft Azure:

- [Extensão VM Docker para Linux no Azure](../articles/virtual-machines/virtual-machines-linux-dockerextension.md)
- [Guia de Usuário da Extensão de VM Docker do Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md)
- [Usando a extensão de VM Docker da interface de linha de comando do Azure (CLI do Azure)](../articles/virtual-machines/virtual-machines-linux-classic-cli-use-docker.md)
- [Usando a extensão de VM Docker do portal do Azure](../articles/virtual-machines/virtual-machines-linux-classic-portal-use-docker.md)
- [Como usar a máquina docker no Azure](../articles/virtual-machines/virtual-machines-linux-docker-machine.md)
- [Como usar o docker com avanço no Azure](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md)
- [Introdução ao Docker e Redija no Azure](../articles/virtual-machines/virtual-machines-linux-docker-compose-quickstart.md)
- [Usando um modelo do grupo de recursos do Azure para criar rapidamente um host Docker no Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)
- [Suporte interno `compose`](https://github.com/Azure/azure-docker-extension#11-public-configuration-keys) para aplicativos independentes
- [Implementar um Registro privado do Docker no Azure](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md)

Distribuições do Linux e exemplos do Azure:

- [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html)

Configuração, gerenciamento de clusters e coordenação de contêineres:

- [Fleet no CoreOS](https://coreos.com/using-coreos/clustering/)

-	Deis
	- [Criar um grupo de VMs de 3 nós no Azure, instalar o Deis e iniciar um aplicativo Go Hello World](../articles/virtual-machines/virtual-machines-linux-deis-cluster.md)

-	Kubernetes
	- [Guia completo para a implantação automatizada do cluster Kubernetes com CoreOS e Trança](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Visualizador do Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)

-	[Mesos](http://mesos.apache.org/)
	-	[DCOS (Sistema operacional de centro de dados) Mesosphere](http://beta-docs.mesosphere.com/install/azurecluster/)

-	[Jenkins](https://jenkins-ci.org/) e [Hudson](http://hudson-ci.org/)
	- [Blog: Plug-in de subordinado Jenkins para o Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [Repositório GitHub: plug-in de armazenamento Jenkins do Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Terceiros: plug-in de subordinado Hudson do Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Terceiros: plug-in de armazenamento Hudson do Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)

-	[Automação do Azure](https://azure.microsoft.com/services/automation/)
	- [Vídeo: Como usar a automação do Azure com VMs Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)

-	PowerShell DSC para Linux
    - [Blog: Como fazer Powershell DSC para Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub: Cliente Docker DSC](https://github.com/anweiss/DockerClientDSC)

## Próximas etapas

Confira o [Docker](https://www.docker.com) e os [Contêineres do Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

<!--Anchors-->
[microservices]: http://martinfowler.com/articles/microservices.html
[microsserviços]: http://martinfowler.com/articles/microservices.html
<!--Image references-->

<!---HONumber=AcomDC_0629_2016-->