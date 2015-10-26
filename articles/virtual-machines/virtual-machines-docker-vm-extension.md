<properties
	pageTitle="A extensão de máquina virtual Docker para Linux no Azure"
	description="Descreve o Docker e contêineres, as extensões de Máquinas virtuais do Azure, além de indicar mais recursos para criar contêineres Docker por meio da CLI do Azure e do portal."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="06/29/2015"
	ms.author="rasquill"/>

# A extensão de máquina virtual Docker para Linux no Azure
O [Docker](https://www.docker.com/) é uma das abordagens de virtualização mais populares que utiliza [contêineres Linux](http://en.wikipedia.org/wiki/LXC) em vez de máquinas virtuais como forma de isolar dados e computar recursos compartilhados. Você pode usar a [extensão de VM do Azure Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) no [Agente Azure Linux](virtual-machines-linux-agent-user-guide.md) para criar uma VM Docker que hospede diversos contêineres para seus aplicativos no Azure.

Este tópico descreve:

+ [Contêineres Docker e Linux]
+ [Como usar a Extensão de VM do Docker com o Azure]
+ [Extensões de máquina virtual para Linux e Windows]

Para criar máquinas virtuais habilitadas para Docker agora mesmo, confira:

+ [Como usar a extensão de VM do Docker a partir da interface de linha de comando do Azure (CLI do Azure)]
+ [Como usar a extensão de VM do Docker com o Portal do Azure]
+ [Como começar rapidamente com o Docker no Marketplace do Azure]

Para saber mais sobre a extensão e como ela funciona, consulte o [Guia do usuário de extensão do Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md).

## Contêineres Docker e Linux
O [Docker](https://www.docker.com/) é uma das abordagens de virtualização mais populares que usa [contêineres Linux](http://en.wikipedia.org/wiki/LXC) em vez de máquinas virtuais como uma forma de isolar dados e computar recursos compartilhados, e oferece outros serviços que lhe permitem criar ou compilar aplicativos rapidamente e distribuí-los entre outros contêineres Docker.

Contêineres Docker e Linux não são [Hipervisores](http://en.wikipedia.org/wiki/Hypervisor) como o Windows Hyper-V e o [KVM](http://www.linux-kvm.org/page/Main_Page) no Linux (há muitos outros exemplos). Hipervisores virtualizam o sistema operacional subjacente para habilitar sistemas operacionais completos (chamados de *máquinas virtuais*) a serem executados dentro do hipervisor como se fossem um aplicativo.

O Docker e outras abordagens de *contêiner* diminuíram radicalmente o tempo de inicialização e a sobrecarga de processamento e armazenamento necessária para usar os recursos de isolamento de sistema e processo do kernel Linux para expor somente recursos do kernel a um contêiner que, de outra forma, permanece isolado.

A tabela a seguir descreve detalhadamente as diferenças dos recursos dos hipervisores e dos contêineres Linux. Alguns recursos podem ser mais ou menos úteis, dependendo das necessidades dos aplicativos.

| Recurso | Hipervisores | Contêineres |
| :------------- |-------------| ----------- |
| Isolamento do processo | Parcial | Se a raiz for obtida, o contêiner será comprometido |
| Requisição de memória em disco | SO completo, mais aplicativos | Somente os requisitos dos aplicativos |
| Tempo necessário para iniciar | Muito mais longo: inicialização do sistema operacional e carregamento do aplicativo | Muito mais curto: somente os aplicativos precisam ser iniciados porque o kernel já está em execução |
| Automação do contêiner | Varia muito de acordo com o SO e os aplicativos | [Galeria de imagens do Docker](https://registry.hub.docker.com/); outros

Para participar de um debate de alto nível sobre contêineres e suas vantagens, confira o [Quadro de comunicações de alto nível do Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

Para obter mais informações sobre o Docker e seu funcionamento, confira [O que é o Docker?](https://www.docker.com/whatisdocker/)

#### Práticas recomendadas para a segurança do contêiner do Linux e do Docker

Como os contêineres compartilham o acesso ao kernel do computador hóspede, se códigos mal-intencionados conseguirem acessar a raiz, eles poderão também acessar não só o computador hóspede, mas todos os outros contêineres. Para ir além da segurança proporcionada ao sistema de contêineres pela configuração padrão, a [Docker recomenda](https://docs.docker.com/articles/security/) o uso de uma política de grupo para adição ou [segurança baseada em funções](http://en.wikipedia.org/wiki/Role-based_access_control), como [SELinux](http://selinuxproject.org/page/Main_Page) ou [AppArmor](http://wiki.apparmor.net/index.php/Main_Page), por exemplo, além de recomendar a redução ao máximo das funcionalidades do kernel concedidas aos contêineres. Além disso, há muitos outros documentos na Internet que descrevem abordagens de segurança ao usar contêineres como o Docker.

## Como usar a Extensão de VM Docker com o Azure

A extensão de máquina virtual Docker é um componente que, quando instalado na instância VM que você criou, instala o mecanismo Docker e gerencia a comunicação remota com a máquina virtual. Há duas maneiras de instalar a extensão de VM: você pode criar sua VM usando o portal de gerenciamento ou você pode criá-la a partir da interface de linha de comando do Azure (CLI do Azure).

Você pode usar o portal para adicionar a extensão de máquina virtual Docker a qualquer máquina virtual Linux compatível (no momento, somente as imagens Ubuntu 14.04 LTS posteriores a julho têm suporte). No entanto, ao usar a linha de comando CLI do Azure, você pode instalar a extensão de VM do Docker e criar e fazer upload de seus certificados de comunicação Docker enquanto cria a instância VM.

Para criar máquinas virtuais habilitadas para Docker agora mesmo, confira:

+ [Como usar a extensão de VM do Docker a partir da interface de linha de comando do Azure (CLI do Azure)]
+ [Como usar a extensão de VM do Docker com o Portal do Azure]

## Extensões de máquina virtual para Linux e Windows
A [extensão de VM do Docker para Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md) é apenas uma das diversas extensões que oferecem comportamentos especiais, e outras estão sendo desenvolvidas. Por exemplo, diversos recursos da [extensão do Agente de VM Linux](virtual-machines-linux-agent-user-guide.md) permitem que você modifique e gerencie a máquina virtual, incluindo recursos de segurança, recursos de kernel e rede e assim por diante. A extensão VMAccess, por exemplo, permite redefinir a senha de administrador ou a chave SSH.

Para obter uma lista completa, consulte [Extensões de VM do Azure](http://msdn.microsoft.com/library/azure/dn606311.aspx).

<!--Anchors-->
[Como usar a extensão de VM do Docker a partir da interface de linha de comando do Azure (CLI do Azure)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Como usar a extensão de VM do Docker com o Portal do Azure]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/
[Como começar rapidamente com o Docker no Marketplace do Azure]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/
[Contêineres Docker e Linux]: #Docker-and-Linux-Containers
[Como usar a Extensão de VM do Docker com o Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Extensões de máquina virtual para Linux e Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
 

<!---HONumber=Oct15_HO3-->