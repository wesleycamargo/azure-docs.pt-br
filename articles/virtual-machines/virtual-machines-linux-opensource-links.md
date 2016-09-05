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
	ms.date="06/27/2016"
	ms.author="rasquill"/>



# Computação Linux e Software Livre no Azure

Localize toda a documentação necessária para criar e gerenciar máquinas virtuais baseadas no Linux no modelo de implantação clássico.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## Introdução
- [Introdução ao Linux no Azure](virtual-machines-linux-intro-on-azure.md)
- [Perguntas frequentes sobre as Máquinas Virtuais do Azure criadas com o modelo de implantação clássico](virtual-machines-linux-classic-faq.md)
- [Sobre imagens de máquinas virtuais](virtual-machines-linux-classic-about-images.md)
- [Carregar sua própria imagem de distribuição](virtual-machines-linux-classic-create-upload-vhd.md) (e também instruções do uso de uma [Distribuição endossada pelo Azure](virtual-machines-linux-endorsed-distros.md))
- [Fazer logon em uma VM Linux usando o Portal Clássico do Azure](virtual-machines-linux-mac-create-ssh-keys.md)

## Configurar

- [Instalar a CLI do Azure (interface de linha de comando do Azure)](../xplat-cli-install.md)


## Tutoriais

- [Instalar a pilha LAMP em uma máquina virtual Linux no Azure](virtual-machines-linux-install-lamp-stack.md)
- [Aplicativo Web Ruby on Rails Web em uma VM do Azure](virtual-machines-linux-classic-ruby-rails-web-app.md)
- [Como instalar o Apache Qpid Proton-C para AMQP e barramento de serviço](../service-bus/service-bus-amqp-apache.md)

### Bancos de dados
- [Otimizar o desempenho do MySQL no Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [Clusters MySQL](virtual-machines-linux-classic-mysql-cluster.md)
- [Executando Cassandra com Linux no Azure e acessando-a do Node.js](virtual-machines-linux-classic-cassandra-nodejs.md)
- [Criar um cluster multimestre de MariaDbs](virtual-machines-linux-classic-mariadb-mysql-cluster.md)

### HPC
- [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
- [Executar o NAMD com o Microsoft HPC Pack em nós de computação do Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
- [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md)

### Docker
- [Usando a extensão de VM Docker da interface de linha de comando do Azure (CLI do Azure)](virtual-machines-linux-classic-cli-use-docker.md)
- [Usando a extensão de VM Docker do portal do Azure](virtual-machines-linux-classic-portal-use-docker.md)
- [Como usar a máquina docker no Azure](virtual-machines-linux-docker-machine.md)

### Ubuntu
- [Como usar clusters MySQL](virtual-machines-linux-classic-mysql-cluster.md)
- [Como usar Node.js e Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)

### OpenSUSE
- [Como instalar e executar o MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)

### CoreOS
- [Como usar o CoreOS no Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## Planejamento
- [Diretrizes de implementação dos Serviços de Infraestrutura do Azure](virtual-machines-linux-infrastructure-subscription-accounts-guidelines.md)
- [Selecionando nomes de usuário do Linux](virtual-machines-linux-usernames.md)
- [Como configurar um conjunto de disponibilidade para máquinas virtuais no modelo de implantação clássico](virtual-machines-linux-classic-configure-availability.md)
- [Como agendar a manutenção planejada em VMs do Azure](virtual-machines-linux-planned-maintenance-schedule.md)
- [Gerencie a disponibilidade de máquinas virtuais](virtual-machines-linux-manage-availability.md)
- [Manutenção planejada para máquinas virtuais Linux no Azure](virtual-machines-linux-planned-maintenance.md)


## Implantação
- [Como criar uma máquina virtual personalizada que executa o Linux](virtual-machines-linux-classic-createportal.md)
- [Noções básicas: capturando uma VM do Linux para criar um modelo](virtual-machines-linux-classic-capture-image.md)
- [Informações para as distribuições não endossadas](virtual-machines-linux-create-upload-generic.md)


## Gerenciamento

- [SSH](virtual-machines-linux-mac-create-ssh-keys.md)
- [Como redefinir uma senha ou propriedades SSH em Linux](virtual-machines-linux-classic-reset-access.md)
- [Usando raiz](virtual-machines-linux-use-root-privileges.md)


## Recursos do Azure.

- [O agente Linux do Azure](virtual-machines-linux-agent-user-guide.md)
- [Recursos e extensões de VM do Azure](virtual-machines-windows-extensions-features.md)
- [Injetando dados personalizados em uma VM para usar com cloud-init](virtual-machines-windows-classic-inject-custom-data.md)


## Armazenamento

- [Anexando um disco de dados a uma VM Linux](virtual-machines-linux-classic-attach-disk.md)
- [Desanexando um disco de dados de uma VM Linux](virtual-machines-linux-classic-detach-disk.md)
- [RAID](virtual-machines-linux-configure-raid.md)


## Rede
- [Como configurar pontos de extremidade em uma máquina virtual clássica no Azure](virtual-machines-linux-classic-setup-endpoints.md)


## Solucionar problemas
- [Solucionar problemas de conexões SSH (Secure Shell) para uma máquina virtual do Azure baseada em Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)
- [Solucionar problemas de implantação clássica ao criar uma nova máquina virtual Linux no Azure](virtual-machines-linux-classic-troubleshoot-deployment-new-vm.md)
- [Solucionar problemas de implantação clássica ao reinicializar ou redimensionar uma Máquina Virtual Linux existente no Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md)


## Referência

- [Comandos da CLI do Azure no modo ASM (Gerenciamento de Serviços do Azure)](../virtual-machines-command-line-tools.md)
- [API REST de Gerenciamento de Serviços do Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx)




## Links de geral
Os links a seguir são para blogs da Microsoft, páginas da Technet e sites externos em vez da documentação do Azure.com como acima. Como tanto o Azure quanto o mundo da computação de software livre estão evolução, é quase certo que os links a seguir estejam desatualizados, *apesar* de fazermos sempre o possível para adicionar tópicos mais recentes e remover os desatualizados. Se tivermos deixado passar algo, avise nos comentários ou envie uma solicitação pull para nosso [repositório GitHub](https://github.com/Azure/azure-content/).

- [Executando ASP.NET 5 em Linux usando contêineres de Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
- [Como implantar uma imagem de VM CentOS da OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
- [Infraestrutura de atualização SUSE](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
- [SUSE Linux Enterprise Server para SAP Cloud Appliance Library](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
- [Criando Linux altamente disponível no Azure em 12 etapas](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
- [Automatizar o provisionamento do Linux no Azure com a CLI do Azure, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
- [GlusterFS no Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### FreeBSD
- [Executando FreeBSD no Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
- [Implante FreeBSD facilmente](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
- [Implantando uma imagem de FreeBSD personalizada](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
- [Kaspersky AV para servidor de arquivos Linux](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### NoSQL

- [Oito bancos de dados NoSql de software livre para o Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
- [Slideshare (MSOpenTech): experiências com o CouchDb no Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
- [Executando CouchDB como serviço com node.js, CORS e Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)

- [Redis no Windows no serviço de cache Redis do Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
- [Anunciando o provedor de estado de sessão ASP.NET para a versão de teste do Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)

- [Blog: RavenHQ já disponível no Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### Big Data
- [Instalando Hadoop em VMs Linux do Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
- [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### Banco de dados relacional
- [Arquitetura de alta disponibilidade do MySQL no Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
- [Instalando o Postgres com corosync, pg\_bouncer usando ILB](https://github.com/chgeuer/postgres-azure)

### Computação de alto desempenho do Linux (HPC)

- [Modelo de início rápido: criar um cluster SLURM](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm) (e [postagem de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
- [Modelo de início rápido: criar um cluster HPC com nós de computação Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### Desenvolvimentos, gerenciamento e otimização

Como o mundo dos desenvolvimentos, gerenciamento e otimização é muito extenso e muda muito rapidamente, você deve considerar a lista abaixo um ponto de partida.

- [Vídeo: Azure Virtual Machines: Using Chef, Puppet and Docker for managing Linux VMs (Máquinas virtuais do Azure: usando Chef, Puppet e Docker para gerenciar VMs Linux)](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)

- [Guia completo para a implantação automatizada do cluster Kubernetes com CoreOS e Trança](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
- [Visualizador do Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)

- [Plug-in de subordinado Jenkins para o Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
- [Repositório GitHub: plug-in de armazenamento Jenkins do Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)

- [Terceiros: plug-in de subordinado Hudson do Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
- [Terceiros: plug-in de armazenamento Hudson do Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)

- [Vídeo: o que é o Chef e como ele funciona?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

- [Vídeo: Como usar a automação do Azure com VMs Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)

- [Blog: Como fazer Powershell DSC para Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
- [GitHub: Cliente Docker DSC](https://github.com/anweiss/DockerClientDSC)

- [Plug-in Packer para o Azure](https://github.com/msopentech/packer-azure)

<!---HONumber=AcomDC_0824_2016-->