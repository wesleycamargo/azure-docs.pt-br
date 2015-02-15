<properties 
	pageTitle="A extensão de máquina virtual Docker para Linux no Azure" 
	description="Descreve o Docker, os contêineres e as extensões de máquina virtual do Azure, além de indicar mais recursos para criar contêineres do Docker por meio da xplat-cli e do portal." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services" 
	ms.date="10/21/2014" 
	ms.author="rasquill"/>

# A extensão de máquina virtual Docker para Linux no Azure
[O Docker](https://www.docker.com/) é uma das abordagens de virtualização mais populares que utiliza [contêineres Linux](http://en.wikipedia.org/wiki/LXC) em vez de máquinas virtuais como forma de isolar dados e computar recursos compartilhados. Você pode usar a extensão da VM Docker do [Agente Linux do Azure](http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-linux-agent-user-guide/) para criar uma VM Docker que hospede diversos contêineres para seus aplicativos no Azure.

Este tópico descreve:

+ [Contêineres Docker e Linux]
+ [Como usar a Extensão de VM Docker com o Azure]
+ [Extensões de máquina virtual para Linux e Windows]

Para criar máquinas virtuais habilitadas para Docker agora mesmo, confira:

+ [Como usar a extensão de máquina virtual Docker na interface de plataforma cruzada (xplat-cli) do Azure]
+ [Como usar a extensão de máquina virtual Docker com o Portal do Azure]
+ [Como começar rapidamente com Docker no Azure Marketplace]

## <a id='Docker and Linux Containers'>Contêineres Docker e Linux</a>
O [Docker](https://www.docker.com/) é uma das abordagens de virtualização mais populares que usa [contêineres Linux](http://en.wikipedia.org/wiki/LXC) em vez de máquinas virtuais como uma forma de isolar dados e computar recursos compartilhados, e oferece outros serviços que lhe permitem criar ou compilar aplicativos rapidamente e distribuí-los entre outros contêineres Docker.

Contêineres Docker e Linux não são [Hipervisores](http://en.wikipedia.org/wiki/Hypervisor) como o Windows Hyper-V e o [KVM](http://www.linux-kvm.org/page/Main_Page) no Linux (há muitos outros exemplos). Hipervisores virtualizam o sistema operacional subjacente para permitir que sistemas operacionais completos sejam executados dentro do hipervisor como se fossem um aplicativo. 

O Docker e outras abordagens de  *container* diminuíram radicalmente o tempo de inicialização e a sobrecarga de processamento e armazenamento necessária para usar os recursos de isolamento de sistema e processo do kernel Linux para expor somente recursos do kernel a um contêiner que, de outra forma, permanece isolado.

A tabela a seguir descreve detalhadamente as diferenças dos recursos dos hipervisores e dos contêineres Linux. Alguns recursos podem ser mais ou menos úteis, dependendo das necessidades dos aplicativos.

|   Recurso      | Hipervisores | Contêineres  |
| :------------- |-------------| ----------- |
| Isolamento de processo | Mais ou menos completa | Se a raiz é obtida, o host do contêiner pode ser comprometido |
| Memória em disco necessária | Conclua o sistema operacional e aplicativos | Somente os requisitos de aplicativos |
| Tempo necessário para iniciar | Um tempo consideravelmente maior: Inicialização do sistema operacional e o carregamento do aplicativo | Substancialmente mais curto: É necessário iniciar apenas os aplicativos, pois o kernel já está em execução
| Automação do contêiner | Varia muito, dependendo do sistema operacional e aplicativos | [Galeria de imagens Docker](https://registry.hub.docker.com/); outros 

Para participar de um debate de alto nível sobre contêineres e suas vantagens, confira o [Quadro de comunicações de alto nível do Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

Para obter mais informações sobre o Docker e seu funcionamento, confira [O que é o Docker?](https://www.docker.com/whatisdocker/)

#### Práticas recomendadas para a segurança do contêiner do Linux e do Docker

Como os contêineres compartilham o acesso ao kernel do computador hóspede, se códigos mal-intencionados conseguirem acessar a raiz, eles poderão também acessar não só o computador hóspede, mas todos os outros contêineres. Para ir além da segurança proporcionada ao sistema de contêineres pela configuração padrão, a [Docker recomenda](https://docs.docker.com/articles/security/) o uso de uma política de grupo para adição ou [segurança baseada em funções](http://en.wikipedia.org/wiki/Role-based_access_control), como [SELinux](http://selinuxproject.org/page/Main_Page) ou [AppArmor](http://wiki.apparmor.net/index.php/Main_Page), por exemplo, além de recomendar a redução ao máximo das funcionalidades do kernel concedidas aos contêineres. Além disso, há muitos outros documentos na Internet que descrevem abordagens de segurança ao usar contêineres como o Docker.

## <a id='How to use the Docker VM Extension with Azure'>Como usar a Extensão de VM Docker com o Azure</a>

A extensão de máquina virtual Docker é um componente que, quando instalado na instância de máquina virtual que você criou, instala o mecanismo Docker e gerencia a comunicação remota com a máquina virtual. Há duas maneiras de instalar essa extensão: Você pode usar o portal de gerenciamento para criar a máquina virtual ou criar a imagem por meio da interface de linha de comando de plataforma cruzada do Azure (xplat-cli). 

Você pode usar o portal para adicionar a extensão de máquina virtual Docker a qualquer máquina virtual Linux compatível (no momento, somente as imagens Ubuntu 14.04 LTS posteriores a julho são compatíveis). No entanto, ao usar a linha de comando xplat-cli, você pode instalar a extensão de máquina virtual Docker para criar e fazer upload dos seus certificados de comunicação Docker enquanto cria a instância da máquina virtual.

Para criar máquinas virtuais habilitadas para Docker agora mesmo, confira:

+ [Como usar a extensão de máquina virtual Docker na interface de plataforma cruzada (xplat-cli) do Azure]
+ [Como usar a extensão de máquina virtual Docker com o Portal do Azure]

## <a id='Virtual Machine Extensions for Linux and Windows'>Extensões de máquina virtual para Linux e Windows</a>
A extensão de VM do Docker para Azure é apenas uma das diversas extensões que oferecem comportamentos especiais, e outras estão sendo desenvolvidas. Por exemplo, diversos recursos da [Extensão do Agente de VM Linux](http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-linux-agent-user-guide/) permitem que você modifique e gerencie a imagem, incluindo recursos de segurança, recursos de kernel e rede e assim por diante. A extensão VMAccess para imagens do Windows permite que você redefina ou modifique as configurações de Acesso à Área de trabalho remota e redefina a senha do administrador. 

Para obter uma lista completa, consulte [Extensões de VM do Azure](http://msdn.microsoft.com/pt-br/library/azure/dn606311.aspx).

<!--Anchors-->
[Como usar a extensão de máquina virtual Docker na interface de plataforma cruzada (xplat-cli) do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Como usar a extensão de máquina virtual Docker com o Portal do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-docker-with-portal/
[Como começar rapidamente com Docker no Azure Marketplace]: http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-docker-ubuntu-quickstart/
[Contêineres Docker e Linux]: #Docker-and-Linux-Containers
[Como usar a Extensão de VM Docker com o Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Extensões de máquina virtual para Linux e Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows

<!--HONumber=42-->
