<properties
	pageTitle="Computação Linux e software livre no Azure | Microsoft Azure"
	description="Lista de artigos sobre Computação Linux e de software livre no Azure, incluindo o uso básico do Linux, alguns fundamentos sobre como executar ou carregar imagens do Linux no Azure e outro conteúdo sobre tecnologias e otimizações específicas."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="03/26/2016"
	ms.author="rasquill"/>



# Computação Linux e Software Livre no Azure

Este documento tenta listar, em um único local, todos os tópicos do modelo de implantação clássico escritos pela Microsoft e seus parceiros sobre a execução de máquinas virtuais baseadas em Linux, bem como outros ambientes de computação e aplicativos de software livre no Microsoft Azure.

Como tanto o Azure, quanto o mundo da computação de software livre são alvos em rápida evolução, é quase certo que este documento esteja desatualizado, *apesar* do fato de que sempre fazemos o possível para adicionar continuamente tópicos mais recentes e remover aqueles desatualizados. Se tivermos deixado passar algo, avise nos comentários ou envie uma solicitação pull para nosso [repositório GitHub](https://github.com/Azure/azure-content/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Observações gerais
As seções são divididas à direita desta página. (Os links podem ocorrer em mais de uma seção, assim como os tópicos podem abordar mais de um conceito, distribuição ou tecnologia.) Além disso, há vários tópicos que descrevem diversas opções do Linux, repositórios de imagens, estudos de caso e tópicos de instruções para carregar suas próprias imagens personalizadas:

- [Azure Marketplace](https://azure.microsoft.com/marketplace/virtual-machines/)
- [VM Depot do MSOpenTech](https://vmdepot.msopentech.com/List/Index)
- [Eventos e demonstrações: Microsoft Openness CEE](http://www.opennessatcee.com/)
- [Como carregar a sua própria imagem de distribuição](virtual-machines-linux-classic-create-upload-vhd.md) (além de instruções usando uma [Distribuição endossada pelo Azure](virtual-machines-linux-endorsed-distros.md))
- [Observações: requisitos gerais de Linux para execução no Azure](virtual-machines-linux-create-upload-generic.md)
- [Anotações: introdução geral ao Linux no Azure](virtual-machines-linux-intro-on-azure.md)

## Distribuições

Há diversas distribuições Linux, geralmente divididas pelos sistemas de gerenciamento de pacotes: algumas são baseadas em dpgk, como o Debian e o Ubuntu, e outras são baseadas em rpm, como ao CentOS, o SUSE e o RedHat. Algumas empresas fornecem imagens de distribuição como parceiros formais da Microsoft e são endossadas. Outras são fornecidas pela comunidade. As distribuições nesta seção têm artigos formais sobre elas, mesmo se foram usadas apenas nos exemplos de outras tecnologias.

### [Ubuntu](https://azure.microsoft.com/marketplace/partners/Canonical/)

O Ubuntu é uma distribuição Linux muito popular e endossada pelo Azure, baseada em gerenciamento de pacotes dkpg e apt-get.

3. [Como usar clusters MySQL](virtual-machines-linux-classic-mysql-cluster.md)
4. [Como usar Node.js e Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)
6. [Conversas de nerd: executando ASP.NET 5 em Linux usando contêineres do Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

### CentOS

A distribuição CentOS Linux é uma plataforma estável, previsível, gerenciável e reprodutível derivada das fontes do Red Hat Enterprise Linux (RHEL).

4. [Blog: Como implantar uma imagem de VM CentOS da OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [Como instalar o Apache Qpid Proton-C para AMQP e barramento de serviço](../service-bus/service-bus-amqp-apache.md/)

### SUSE Linux Enterprise Server e openSUSE

11. [Como instalar e executar o MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)
13. [[Fórum SUSE] Como mover para um novo servidor de patch](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [Imagens: SUSE Linux Enterprise Server para SAP Cloud Appliance Library](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

### CoreOS

CoreOS é uma distribuição pequena e otimizada para escala de computação pura com um alto grau de controle de personalização.

10. [Galeria de Imagens](https://azure.microsoft.com/marketplace/partners/coreos/)  
11. [Como usar o CoreOS no Azure](virtual-machines-linux-classic-coreos-howto.md)
12. [Como começar com a Frota e o Docker em CoreOS no Azure](virtual-machines-linux-classic-coreos-fleet-get-started.md)


### FreeBSD

12. [VM Depot do MSOpenTech](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [Blog: Executando FreeBSD no Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [Blog: FreeBSD fácil para implantar](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [Blog: Implantando uma imagem de FreeBSD personalizada](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
18. [Marketplace: Kaspersky AV para servidor de arquivos do Linux](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

## Noções básicas

1. [Noções básicas: a interface de linha de comando do Azure (CLI do Azure)](../xplat-cli-install.md)
5. [Noções básicas: selecionando nomes de usuário do Linux](virtual-machines-linux-usernames.md)
6. [Noções básicas: faça logon em uma VM do Linux usando o portal clássico do Azure](virtual-machines-linux-classic-log-on.md)
7. [Noções básicas: SSH](virtual-machines-linux-ssh-from-linux.md)
8. [Noções básicas: como redefinir uma senha ou propriedades SSH para Linux](virtual-machines-linux-classic-reset-access.md)
9. [Noções básicas: usando Root](virtual-machines-linux-use-root-privileges.md)
10. [Noções básicas: anexando um disco de dados a uma VM do Linux](virtual-machines-linux-classic-attach-disk.md)
11. [Noções básicas: desanexando um disco de dados de uma VM do Linux](virtual-machines-linux-classic-detach-disk.md)
12. [Noções básicas de blogs: otimizando o armazenamento, os discos e o desempenho com o Linux e o Azure](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [Noções básicas: RAID](virtual-machines-linux-configure-raid.md)
14. [Noções básicas: capturando uma VM do Linux para criar um modelo](virtual-machines-linux-classic-capture-image.md)
15. [Noções básicas: O agente Linux do Azure](virtual-machines-linux-agent-user-guide.md)
16. [Noções básicas: recursos e extensões de VM do Azure](virtual-machines-windows-extensions-features.md)
17. [Noções básicas: injetando dados personalizados em uma VM para usar com Cloud-init](virtual-machines-windows-classic-inject-custom-data.md)
18. [Noções básicas de blogs: criando Linux altamente disponível no Azure em 12 etapas](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [Noções básicas de blogs: automatizar provisionamento Linux no Azure com CLI do Azure, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
23. Referência [API REST do Gerenciamento de Serviços do Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx)
24. [GlusterFS no Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

## Repositórios e imagens da comunidade
4. [GitHub](https://github.com/Azure/) &mdash; para a CLI do Azure e muitas outras ferramentas e projetos.
5. [Registro de hub Docker](https://registry.hub.docker.com/) &mdash; o registro para imagens de contêineres Docker.

## Dados

Esta seção contém informações sobre várias abordagens e tecnologias de armazenamento diferentes, incluindo NoSQL, dados relacionais e Big Data.

### NoSQL

1. [Blog: Oito bancos de dados NoSql de software livre para o Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
    - [Slideshare (MSOpenTech): experiências com o CouchDb no Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
    - [Blog: Executando o CouchDB como serviço com node.js, CORS e Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
4. Cassandra
    - [Como executar Cassandra com Linux no Azure e acessá-la do Node.js](virtual-machines-linux-classic-cassandra-nodejs.md)
5. Redis
    - [Blog: Redis no Windows no serviço de cache Redis do Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
    - [Blog: Anunciando o provedor de estado de sessão ASP.NET para a versão de teste do Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
    - [Blog: RavenHQ já disponível no Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### Big Data
2. Hadoop/Cloudera  
	- [Blog: Instalando Hadoop em VMs Linux do Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
3. [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) – um serviço Hadoop totalmente gerenciado no Azure.

### Banco de dados relacional
2. MySQL
    - [Como instalar e executar o MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)
    - [Como otimizar o desempenho do MySQL no Azure](virtual-machines-linux-classic-optimize-mysql.md)
    - [Como usar clusters MySQL](virtual-machines-linux-classic-mysql-cluster.md)
    - [Arquitetura de alta disponibilidade do MySQL no Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
7. MariaDB
    - [Como criar um cluster multimestre de MariaDbs](virtual-machines-linux-classic-mariadb-mysql-cluster.md)
8. [Instalando o Postgres com corosync, pg\_bouncer usando ILB](https://github.com/chgeuer/postgres-azure)


## Autenticação e criptografia

Autenticação e criptografia são tópicos importantes no desenvolvimento de software, e há muitos, muitos tópicos na Web que descrevem como aprender e usar técnicas de segurança apropriadas para ambos. Nós descrevemos alguns dos usos básicos para colocá-los em funcionamento rapidamente com cargas de trabalho Linux e de software livre, assim como apontamos as ferramentas a usar para redefinir ou remover recursos de segurança remota no Azure. Esses são procedimentos básicos e iremos adicionais cenários mais complexos em breve.

4. [Noções básicas: gerenciamento e uso de certificados](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [Noções básicas: SSH](virtual-machines-linux-ssh-from-linux.md)
8. [Noções básicas: como redefinir uma senha ou propriedades SSH para Linux](virtual-machines-linux-classic-reset-access.md)
9. [Noções básicas: usando Root](virtual-machines-linux-use-root-privileges.md)

## Computação de alto desempenho do Linux (HPC)

Execute cargas de trabalho do HPC em clusters de VM Linux criados com ferramentas de software livre ou com o Microsoft HPC Pack.

1.	[Modelo de início rápido: criar um cluster SLURM](https://azure.microsoft.com/documentation/templates/slurm/) (e [postagem de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
3.	[Modelo de início rápido: criar um cluster HPC com nós de computação Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)
4.	[Tutorial: Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
5.	[Tutorial: executar o NAMD com o Microsoft HPC Pack em nós de computação Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
6.	[Tutorial: configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md)


## Desenvolvimentos, gerenciamento e otimização

Esta seção começa com uma entrada de blog que contém uma série de vídeos em [vídeo: máquinas virtuais do Azure: usando Chef, Puppet e Docker para gerenciar VMs Linux](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/). No entanto, o mundo dos desenvolvimentos, gerenciamento e otimização é muito extenso e muda muito rapidamente; logo, você deve considerar a lista abaixo um ponto de partida.

1. Docker
	- [Usando a extensão de VM Docker da interface de linha de comando do Azure (CLI do Azure)](virtual-machines-linux-classic-cli-use-docker.md)
	- [Usando a extensão de VM Docker do portal do Azure](virtual-machines-linux-classic-portal-use-docker.md)
    - [Introdução rápida ao Docker no Azure Marketplace](virtual-machines-linux-classic-docker-quickstart.md)
	- [Como usar a máquina docker no Azure](virtual-machines-linux-classic-docker-machine.md)

2. [Fleet com CoreOS](virtual-machines-linux-classic-coreos-howto.md)
4. Kubernetes
	- [Guia completo para a implantação automatizada do cluster Kubernetes com CoreOS e Trança](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Visualizador do Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
5. Jenkins e Hudson
	- [Blog: Plug-in de subordinado Jenkins para o Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [Repositório GitHub: plug-in de armazenamento Jenkins do Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Terceiros: plug-in de subordinado Hudson do Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Terceiros: plug-in de armazenamento Hudson do Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
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

<!-----------HONumber=AcomDC_0330_2016-->