<properties
	pageTitle="Como usar o Docker rapidamente com a imagem da VM Ubuntu-Docker"
	description="Descreve e demonstra como usar Docker no servidor do Ubuntu em minutos diretamente da Galeria de Imagens do Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure"
	ms.date="10/04/2015"
	ms.author="rasquill"/>

# Como começar rapidamente com Docker no Azure Marketplace

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.
 

A maneira mais rápida de começar a usar o [Docker] é ir para o Azure Marketplace e criar uma máquina virtual usando o modelo de imagem do **Docker no servidor do Ubuntu** criado por [Canonical] em conjunto com [MSOpenTech]. Isso cria uma VM de servidor do Ubuntu e instala automaticamente a [Extensão de VM do Docker](virtual-machines-docker-vm-extension.md) juntamente com o mecanismo de Docker **mais recente** previamente instalado e em execução no Azure.

Você pode conectar-se à VM imediatamente com SSH e começar a trabalhar com o Docker diretamente sem fazer mais nada.

> [AZURE.NOTE]A VM criada pelo modelo do Azure Marketplace não hospeda a API remota do Docker para o gerenciamento por um cliente remoto do docker. Para habilitar o controle do host do Docker nesta VM remotamente, confira [Executando o Docker com HTTPS](https://docs.docker.com/articles/https/) ou siga as etapas em [Usando a extensão de VM do Docker do Portal do Azure](virtual-machines-docker-with-portal.md) ou [Usando a extensão de VM do Docker da CLI do Azure](virtual-machines-docker-with-xplat-cli-install.md). <!-- --> Se quiser automatizar sua VM do Azure Docker no Windows, você poderá [instalar a Docker Toolbox](https://docs.docker.com/installation/windows/) ou baixar o Docker.exe [da Chocolatey](https://chocolatey.org/packages/docker).

## Faça logon no Portal

Essa parte é fácil, a menos que você não tenha uma conta do Azure. [Obtenha uma gratuita facilmente](http://azure.microsoft.com/pricing/free-trial/)!

## Crie uma máquina virtual com a imagem de Docker de Canonical e MSOpenTech

1. Depois que você fizer logon, clique em **Novo** no canto inferior esquerdo para criar uma nova imagem de VM. Você pode ver a imagem correta na faixa imediatamente:

> ![Escolha a imagem Ubuntu do Docker na faixa](./media/virtual-machines-docker-ubuntu-quickstart/CreateNewDockerBanner.png)

2. Se não, pesquise-a na Galeria de Imagens:

> ![Localize a imagem na Galeria de Imagens](./media/virtual-machines-docker-ubuntu-quickstart/DockerOnUbuntuServerMSOpenTech.png)

3. Forneça o nome de usuário e a senha para a instância, ou o conteúdo de um arquivo **.pub** (formato ssh-rsa) para habilitar SSH usando um certificado. (A imagem mostra como especificar uma combinação de nome de usuário e senha.) Em seguida, pressione **Criar** na parte inferior.

> ![Configure a instância da VM](./media/virtual-machines-docker-ubuntu-quickstart/CreateVMDockerUbuntuPwd.png)

4. E aguarde até que ela esteja sendo executada. Isso não deve demorar.

> ![A imagem do docker em execução no portal](./media/virtual-machines-docker-ubuntu-quickstart/DockerUbuntuRunning.png)

## Conecte-se com o SSH e divirta-se

Agora a diversão começa. Você pode conectar-se imediatamente à máquina virtual usando SSH:

> ![Conectando com o SSH](./media/virtual-machines-docker-ubuntu-quickstart/SSHToDockerUbuntu.png)

e começar a usar comandos do Docker, lembrando que, nesta VM do Azure, a configuração padrão requer **`sudo`**:

> ![Extraindo imagens](./media/virtual-machines-docker-ubuntu-quickstart/DockerPullSmallImages.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Você vai querer começar a usar o [Docker]!

<!--Anchors-->
[Log on to the Portal]: #logon
[Create a VM with the Docker Image from Canonical and MSOpenTech]: #createvm
[Connect with SSH and Have Fun]: #havingfun
[Next steps]: #next-steps


[Docker]: https://www.docker.com/
[BusyBox]: http://en.wikipedia.org/wiki/BusyBox
[Docker scratch image]: https://docs.docker.com/articles/baseimages/#creating-a-simple-base-image-using-scratch
[Canonical]: http://www.canonical.com/
[MSOpenTech]: http://msopentech.com/
 

<!---HONumber=Oct15_HO3-->