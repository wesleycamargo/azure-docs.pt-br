<properties
	pageTitle="Computação Linux e Software Livre no Azure"
	description="Este tópico contém uma lista de Computação Linux e de software livre no Azure, incluindo o uso básico do Linux, alguns fundamentos sobre como executar ou carregar imagens do Linux no Azure e outro conteúdo sobre tecnologias e otimizações específicas."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="05/08/2015"
	ms.author="rasquill"/>



# Computação Linux e Software Livre no Azure

Este documento tenta listar, em um único local, todos os tópicos escritos pela Microsoft e seus parceiros sobre a execução de máquinas virtuais baseadas em Linux, bem como outros ambientes e aplicativos de software livre no Microsoft Azure. Como tanto o Azure, quanto o mundo da computação de software livre são alvos em rápida evolução, é quase certo que este documento esteja desatualizado, *apesar* do fato de que sempre fazemos o possível para adicionar continuamente tópicos mais recentes e remover aqueles desatualizados. Se tivermos deixado passar algo, avise nos comentários ou envie uma solicitação pull para nosso [repositório GitHub](https://github.com/Azure/azure-content/).

## Observações gerais
As seções são divididas à direita desta página. (Os links podem ocorrer em mais de uma seção, assim como os tópicos podem abordar mais de um conceito, distribuição ou tecnologia.) Além disso, há vários tópicos que descrevem diversas opções do Linux, repositórios de imagens, estudos de caso e tópicos de instruções para carregar suas próprias imagens personalizadas:

- [Azure Marketplace](http://azure.microsoft.com/marketplace/virtual-machines/)
- [VM Depot do MSOpenTech](https://vmdepot.msopentech.com/List/Index)
- [Eventos e demonstrações: Microsoft Openness CEE](http://www.opennessatcee.com/)
- [Como carregar a sua própria imagem de distribuição](virtual-machines-linux-create-upload-vhd.md) (além de instruções usando uma [Distribuição endossada pelo Azure](virtual-machines-linux-endorsed-distributions.md))
- [Observações: requisitos gerais de Linux para execução no Azure](virtual-machines-linux-create-upload-vhd-generic.md)
- [Anotações: introdução geral ao Linux no Azure](virtual-machines-linux-introduction.md)

<!--
- [Distros](#distros) &mdash; Topics to do with a specific distro.
- [The Basics](#basics) &mdash; A lot of the basic things to do that you either know or need to know.
- [Community Images and Repositories](#images) &mdash; Other places for very useful information, repositories, and binaries.
- [Languages and Platforms](#langsandplats)
- [Samples and Scripts](#samples)
- [Auth and Encryption](#security) &mdash; Important security-related topics, not necessarily specific to Azure.
- [Devops, Management, and Optimization](#devops) &mdash; A big category, changing rapidly.
- [Support, Troubleshooting, and "It Just Doesn't Work"](#supportdebug) &mdash; Really.
-->

## Distribuições

Há diversas distribuições Linux, geralmente divididas pelos sistemas de gerenciamento de pacotes: algumas são baseadas em dpgk, como o Debian e o Ubuntu, e outras são baseadas em rpm, como ao CentOS, o SUSE e o RedHat. Algumas empresas fornecem imagens de distribuição como parceiros formais da Microsoft e são endossadas. Outras são fornecidas pela comunidade. As distribuições nesta seção têm artigos formais sobre elas, mesmo se foram usadas apenas nos exemplos de outras tecnologias.

### [Ubuntu](http://azure.microsoft.com/marketplace/partners/Canonical/)

O Ubuntu é uma distribuição Linux muito popular e endossada pelo Azure, baseada em gerenciamento de pacotes dkpg e apt-get.

1. [Como carregar sua própria imagem do Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)
2. [Como usar a pilha LAMP do Ubuntu](virtual-machines-linux-install-lamp-stack.md)
2. [Imagens: pilha LAPP](http://azure.microsoft.com/marketplace/partners/bitnami/lappstack54310ubuntu1404/)
3. [Como usar clusters MySQL](virtual-machines-linux-mysql-cluster.md)
4. [Como usar Node.js e Cassandra](virtual-machines-linux-nodejs-running-cassandra.md)
5. [Como usar o IPython Notebook](virtual-machines-python-ipython-notebook.md)
6. [Conversas de nerd: executando ASP.NET 5 em Linux usando contêineres do Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
7. [Imagens: servidor Redis](http://azure.microsoft.com/marketplace/partners/cognosys/redisserver269ubuntu1204lts/)
8. [Imagens: servidor do Minecraft](http://azure.microsoft.com/marketplace/partners/bitnami/craftbukkitminecraft179r030ubuntu1210/)
9. [Imagens: Moodle](http://azure.microsoft.com/marketplace/partners/bitnami/moodle270ubuntu1404/)
11. [Imagens: Mono como um serviço](http://azure.microsoft.com/marketplace/partners/aegis/monoasaserviceubuntu1204/)


### [Debian](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=Debian)

O Debian é uma distribuição importante para o Linux e o mundo do software livre e se baseia no gerenciamento de pacotes dpgk e apt-get. O Repositório de VM MSOpenTech tem várias imagens que podem ser usadas.

### CentOS

A distribuição CentOS Linux é uma plataforma estável, previsível, gerenciável e reprodutível derivada das fontes do Red Hat Enterprise Linux (RHEL).

1. [VM Depot do MSOpenTech](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=centos)
2. [Galeria de Imagens](http://azure.microsoft.com/en-in/marketplace/partners/OpenLogic/)
3. [Como preparar uma VM personalizada baseada em CentOS para o Azure](virtual-machines-linux-create-upload-vhd-centos.md)
4. [Blog: Como implantar uma imagem de VM CentOS da OpenLogic](http://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [Como instalar o Apache Qpid Proton-C para AMQP e barramento de serviço](http://msdn.microsoft.com/library/azure/dn235560.aspx)
7. [Imagens: Apache 2.2.15 no OpenLogic CentOS 6.3](http://azure.microsoft.com/marketplace/partners/cognosys/apache2215onopenlogiccentos63/)
8. [Imagens: Drupal 7.2, LAMP Server em OpenLogic CentOS 6.3](http://azure.microsoft.com/marketplace/partners/cognosys/drupal720lampserveronopenlogiccentos63/)

### SUSE Linux Enterprise Server e openSUSE

9. [VM Depot do MSOpenTech](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=OpenSUSE)
11. [Como instalar e executar o MySQL](virtual-machines-linux-mysql-use-opensuse.md)
12. [Como preparar uma VM SLES ou openSUSE personalizada](virtual-machines-linux-create-upload-vhd-suse.md)  
13. [[Fórum SUSE] Como mover para um novo servidor de patch](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [Imagens: SUSE Linux Enterprise Server para SAP Cloud Appliance Library](http://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

### CoreOS

CoreOS é uma distribuição pequena e otimizada para escala de computação pura com um alto grau de controle de personalização.

10. [Galeria de Imagens](http://azure.microsoft.com/en-in/marketplace/partners/coreos/)  
11. [Como usar o CoreOS no Azure](virtual-machines-linux-coreos-how-to.md)
12. [Como começar com a Frota e o Docker em CoreOS no Azure](virtual-machines-linux-coreos-fleet-get-started.md)
13. [Blog: TechEd Europe – Windows Docker Client e contêineres do Linux](http://azure.microsoft.com/blog/2014/10/28/new-docker-coreos-topics-linux-on-azure/)
14. [Blog: O Azure está ficando maior, mais rápido e mais aberto](http://azure.microsoft.com/blog/2014/10/20/azures-getting-bigger-faster-and-more-open/)
15. [GitHub: guia de início rápido para implantar o CoreOS no Azure](https://github.com/timfpark/coreos-azure)
16. [GitHub: implantando um aplicativo Java com Spring Boot, MongoDB e CoreOS](https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init)

#### [Oracle Linux](http://azure.microsoft.com/marketplace/?term=Oracle+Linux)
  2. [Preparar uma máquina virtual Oracle Linux para o Azure](virtual-machines-linux-create-upload-vhd-oracle.md)

### FreeBSD

12. [VM Depot do MSOpenTech](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [Blog: Executando FreeBSD no Azure](http://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [Blog: FreeBSD fácil para implantar](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [Blog: Implantando uma imagem de FreeBSD personalizada](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
17. [Como instalar o Agente Linux do Azure](virtual-machines-linux-agent-user-guide.md)
18. [Marketplace: Kaspersky AV para servidor de arquivos do Linux](http://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

## Noções básicas

1. [Noções básicas: a interface de linha de comando do Azure (CLI do Azure)](../xplat-cli.md)
4. [Noções básicas: gerenciamento e uso de certificados](http://msdn.microsoft.com/library/azure/gg981929.aspx)
5. [Noções básicas: selecionando nomes de usuário do Linux](virtual-machines-linux-usernames.md)
6. [Noções básicas: faça logon em uma VM do Linux usando o Portal do Azure](virtual-machines-linux-how-to-log-on.md)
7. [Noções básicas: SSH](virtual-machines-linux-use-ssh-key.md)
8. [Noções básicas: como redefinir uma senha ou propriedades SSH para Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)
9. [Noções básicas: usando a raiz](virtual-machines-linux-use-root-privileges.md)
10. [Noções básicas: anexando um disco de dados a uma VM do Linux](virtual-machines-linux-how-to-attach-disk.md)
11. [Noções básicas: desanexando um disco de dados de uma VM do Linux](virtual-machines-linux-how-to-detach-disk.md)
12. [Noções básicas de blogs: otimizando o armazenamento, os discos e o desempenho com o Linux e o Azure](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [Noções básicas: RAID](virtual-machines-linux-configure-raid.md)
14. [Noções básicas: capturando uma VM do Linux para criar um modelo](virtual-machines-linux-capture-image.md)
15. [Noções básicas: O agente Linux do Azure](virtual-machines-linux-agent-user-guide.md)
16. [Noções básicas: recursos e extensões de VM do Azure](http://msdn.microsoft.com/library/azure/dn606311.aspx)
17. [Noções básicas: injetando dados personalizados em uma VM para usar com Cloud-init](virtual-machines-how-to-inject-custom-data.md)
18. [Noções básicas de blogs: criando Linux altamente disponível no Azure em 12 etapas](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [Noções básicas de blogs: automatizar provisionamento Linux no Azure com CLI do Azure, Node. js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
19. [Criar uma implantação de várias VMs usando a CLI do Azure](virtual-machines-create-multi-vm-deployment-../xplat-cli.md)
20. [Noções básicas: extensão Docker Azure da VM](virtual-machines-docker-vm-extension.md)
23. Referência [API REST do Gerenciamento de Serviços do Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx)
24. [GlusterFS no Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

## Repositórios e imagens da comunidade
3. [Repositório de VM MSOpenTech](https://vmdepot.msopentech.com/List/Index) de imagens de máquinas virtuais fornecidas pela comunidade.
4. [GitHub](https://github.com/Azure/) &mdash; para a CLI do Azure e muitas outras ferramentas e projetos.
5. [Registro de hub Docker](https://registry.hub.docker.com/) &mdash; o registro para imagens de contêineres Docker.

## Linguagens e plataformas
### [Centro de desenvolvimento de Java do Azure](http://azure.microsoft.com/develop/java/)

1. [Imagens](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=java)
2. [Como usar o barramento de serviço do Java com AMQP 1.0](http://msdn.microsoft.com/library/azure/jj841073.aspx)
3. [Como configurar Tomcat7 no Linux usando o Portal do Azure](virtual-machines-linux-setup-tomcat7-linux.md)
4. [Vídeo: SDK do Java do Azure para o Gerenciamento de Serviços](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-157-The-Java-SDK-for-Azure-Management-with-Brady-Gaster)
5. [Blog: Guia de Introdução às bibliotecas de gerenciamento do Azure para Java](http://azure.microsoft.com/blog/2014/09/15/getting-started-with-the-azure-java-management-libraries/)
5. [Repositório GitHub: kit de ferramentas do Azure para Eclipse com Java](https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava)
6. [Referência: kit de ferramentas do Azure para Eclipse com Java](http://msdn.microsoft.com/library/azure/hh694271.aspx)
7. [Repositório GitHub: plug-in Ferramentas do MS Open Tech para IntelliJ IDEA e Android Studio](https://github.com/MSOpenTech/msopentech-tools-for-intellij)
7. [Blog: MSOpenTech contribui para o OpenJDK](http://msopentech.com/blog/2014/10/21/ms-open-techs-first-contribution-openjdk/)
8. [Imagens: WebSphere](http://azure.microsoft.com/marketplace/partners/msopentech/was-8-5-was-8-5-5-3/)
9. [Imagens: WebLogic](http://azure.microsoft.com/marketplace/?term=weblogic)
10. [Imagens: JDK6 no Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk6onwindowsserver2012/)
11. [Imagens: JDK7 no Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk7onwindowsserver2012/)
12. [Imagens: JDK8 no Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk8onwindowsserver2012r2/)

### Linguagens JVM

1. [Escala: executando aplicativos de estrutura de reprodução nos serviços de nuvem do Azure](http://msopentech.com/blog/2014/09/25/tutorial-running-play-framework-applications-microsoft-azure-cloud-services-2/)

### Tipos, instalações e upgrades de SDK
4. [SDK de Gerenciamento de Serviços do Azure: Java](http://dl.windowsazure.com/javadoc/)
5. [SDK de Gerenciamento de Serviços do Azure: Ir](https://github.com/MSOpenTech/azure-sdk-for-go)
5. [SDK de Gerenciamento de Serviços do Azure: Ruby](https://github.com/MSOpenTech/azure-sdk-for-ruby)
    - [Como instalar o Ruby on Rails](virtual-machines-ruby-rails-web-app-linux.md)
6. [SDK de Gerenciamento de Serviços do Azure: Python](https://github.com/Azure/azure-sdk-for-python)
    - [Como usar o aplicativo Web Django Hello World (Mac-Linux)](virtual-machines-python-django-web-app-linux.md)
7. [SDK de Gerenciamento de serviços do Azure: Node.js](https://github.com/MSOpenTech/azure-sdk-for-node)
8. [SDK de Gerenciamento de Serviços do Azure: PHP](https://github.com/MSOpenTech/azure-sdk-for-php)
    - [Como instalar a pilha LAMP em uma VM do Azure](virtual-machines-linux-install-lamp-stack.md)
    - [Vídeo: instale uma pilha LAMP em uma VM do Azure](http://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman)
9. [SDK de Gerenciamento de Serviços do Azure: .NET](https://github.com/Azure/azure-sdk-for-net)
10. [Blog: Mono, ASP.NET 5, Linux e Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

## Exemplos e scripts

Procure nesta seção para preenchimento rápido. Se você tiver sugestões, envie-em um RP ou deixe nos comentários abaixo.

1. [Criar uma implantação de várias VMs usando a CLI do Azure](virtual-machines-create-multi-vm-deployment-../xplat-cli.md)
2. [Repositório GitHub de Linux no Azure, de Patrick Chanezon](https://github.com/chanezon/azure-linux)
3. [Como mover dados USB locais no Linux para o Azure usando **usbip**](http://channel9.msdn.com/Blogs/Open/On-premises-USB-devices-on-Linux-on-Azure-via-usbip)
4. [Vídeo: acessando a GUI baseada em Linux no Azure no navegador com fernapp](http://channel9.msdn.com/Blogs/Open/Accessing-Linux-based-GUI-on-Azure-over-browser-with-fernapp)
5. [Vídeo: armazenamento compartilhado em Linux usando a visualização de arquivos do Azure – Parte 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)
6. [Vídeo: Adotando dispositivos Linux no Azure usando o barramento de serviço e sites da Web](http://channel9.msdn.com/Blogs/Open/Embracing-Linux-devices-on-Azure-via-Service-Bus-and-Web-Sites)
7. [Vídeo: Conectando um aplicativo em cache de memória baseado no Linux nativo ao Azure](http://channel9.msdn.com/Blogs/Open/Connecting-a-Linux-based-native-memcache-application-to-Windows-Azure)
8. [Vídeo: Serviços altamente disponíveis do Linux no Azure de balanceamento de carga: OpenLDAP e MySQL](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)


## Dados

Esta seção contém informações sobre várias abordagens e tecnologias de armazenamento diferentes, incluindo NoSQL, dados relacionais e Big Data.

### NoSQL

1. [Blog: Oito bancos de dados NoSql de software livre para o Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
    - [Slideshare (MSOpenTech): experiências com o CouchDb no Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
    - [Blog: Executando o CouchDB como serviço com node.js, CORS e Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
3. MongoDB
    - [Como criar um aplicativo do Node.js no Azure com MongoDB usando o suplemento MongoLab](store-mongolab-web-sites-nodejs-store-data-mongodb.md)
4. Cassandra
    - [Como executar Cassandra com Linux no Azure e acessá-la do Node.js](virtual-machines-linux-nodejs-running-cassandra.md)
5. Redis
    - [Blog: Redis no Windows no serviço de cache Redis do Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
    - [Blog: Anunciando o provedor de estado de sessão ASP.NET para a versão de teste do Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
    - [Blog: RavenHQ já disponível no Azure Marketplace](http://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### Big Data
2. Hadoop/Cloudera  
	- [Blog: Instalando Hadoop em VMs Linux do Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
	- [Introdução a Hadoop e Hive usando HDInsight](hdinsight-get-started.md)  
3. [Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) – um serviço Hadoop totalmente gerenciado no Azure.

### Banco de dados relacional
2. MySQL
    - [Como instalar e executar o MySQL](virtual-machines-linux-mysql-use-opensuse.md)
    - [Como otimizar o desempenho do MySQL no Azure](virtual-machines-linux-optimize-mysql-perf.md)
    - [Como usar clusters MySQL](virtual-machines-linux-mysql-cluster.md)
    - [Como criar um banco de dados MySQL usando o Marketplace](store-php-create-mysql-database.md)
    - [Como usar Django e MySQL em sites da Web do Azure com Python e Visual Studio](web-sites-python-ptvs-django-mysql.md)
    - [Como usar PHP e MySQL em sites da Web do Azure com o WebMatrix](web-sites-php-mysql-use-webmatrix.md)
    - [Arquitetura de alta disponibilidade do MySQL no Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
7. MariaDB
    - [Como criar um cluster multimestre de MariaDbs](virtual-machines-mariadb-cluster.md)
8. [Instalando o Postgres com corosync, pg_bouncer usando ILB](https://github.com/chgeuer/postgres-azure)


## Autenticação e criptografia

Autenticação e criptografia são tópicos importantes no desenvolvimento de software, e há muitos, muitos tópicos na Web que descrevem como aprender e usar técnicas de segurança apropriadas para ambos. Nós descrevemos alguns dos usos básicos para colocá-los em funcionamento rapidamente com cargas de trabalho Linux e de software livre, assim como apontamos as ferramentas a usar para redefinir ou remover recursos de segurança remota no Azure. Esses são procedimentos básicos e iremos adicionais cenários mais complexos em breve.

4. [Noções básicas: gerenciamento e uso de certificados](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [Noções básicas: SSH](virtual-machines-linux-use-ssh-key.md)
8. [Noções básicas: como redefinir uma senha ou propriedades SSH para Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)
9. [Noções básicas: usando a raiz](virtual-machines-linux-use-root-privileges.md)

## Desenvolvimentos, gerenciamento e otimização

Esta seção começa com uma entrada de blog que contém uma série de vídeos em [vídeo: máquinas virtuais do Azure: usando Chef, Puppet e Docker para gerenciar VMs Linux](http://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/). No entanto, o mundo dos desenvolvimentos, gerenciamento e otimização é muito extenso e muda muito rapidamente; logo, você deve considerar a lista abaixo um ponto de partida.

1. Docker
	- [Extensão VM Docker para Linux no Azure](virtual-machines-docker-vm-extension.md)
	- [Usando a extensão de VM Docker da interface de linha de comando do Azure (CLI do Azure)](virtual-machines-docker-with-../xplat-cli.md)
	- [Usando a extensão de VM Docker no Portal de Visualização do Azure](virtual-machines-docker-with-portal.md)
	- [Introdução rápida ao Docker no Azure Marketplace](virtual-machines-docker-ubuntu-quickstart.md)
	- [Como usar a máquina docker no Azure](virtual-machines-docker-machine.md)
	- [Como usar o docker com avanço no Azure](virtual-machines-docker-swarm.md)
	- [Introdução ao Docker e Redija no Azure](virtual-machines-docker-compose-quickstart.md)

2. [Fleet com CoreOS](virtual-machines-linux-coreos-how-to.md)
3. Deis
	- [Repositório GitHub: instalando Deis em um cluster CoreOS no Azure](https://github.com/chanezon/azure-linux/tree/master/coreos/deis)
4. Kubernetes
	- [Guia completo para a implantação automatizada do cluster Kubernetes com CoreOS e Trança](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Visualizador do Kubernetes](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure)
5. Jenkins e Hudson
	- [Blog: Plug-in de subordinado Jenkins para o Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [Repositório GitHub: plug-in de armazenamento Jenkins do Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Terceiros: plug-in de subordinado Hudson do Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Terceiros: plug-in de armazenamento Hudson do Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [Chef e máquinas virtuais](virtual-machines-windows-install-chef-client.md)
	- [Vídeo: o que é o Chef e como ele funciona?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

12. Automação do Azure
	- [Vídeo: Como usar a automação do Azure com VMs Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
13. PowerShell DSC para Linux
    - [Blog: Como fazer Powershell DSC para Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub: Cliente Docker DSC](https://github.com/anweiss/DockerClientDSC)
13. [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)
14. [Plug-in Packer para o Azure](https://github.com/msopentech/packer-azure)

## Suporte, solução de problemas e "Simplesmente não funciona"

1. Documentação de suporte da Microsoft
	- [Suporte: suporte para imagens Linux no Microsoft Azure](http://support2.microsoft.com/kb/2941892)

<!--Anchors-->
[Distros]: #distros
[The Basics]: #basics
[Community Images and Repositories]: #images
[Languages and Platforms]: #langsandplats
[Samples and Scripts]: #samples
[Auth and Encryption]: #security
[Devops, Management, and Optimization]: #devops
[Support, Troubleshooting, and "It Just Doesn't Work"]: #supportdebug

<!--Link references--In actual articles, you only need a single period before the slash. -->
[How to use docker-machine on Azure]: virtual-machines-docker-machine.md
[How to use docker with swarm on Azure]: virtual-machines-docker-swarm.md
 

<!---HONumber=July15_HO4-->