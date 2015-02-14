<properties 
	pageTitle="Criar uma máquina virtual que executa Linux no Azure" 
	description="Saiba como criar uma VM (máquina virtual) Azure que execute o Linux usando uma imagem do Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/19/2015" 
	ms.author="kathydav"/>

#Crie uma máquina virtual que executa Linux 

É fácil criar uma máquina virtual que executa o Linux quando você usa a galeria de imagens no Portal de Gerenciamento do Azure. Este guia mostra como fazer isso e pressupõe que você não tem experiência em utilizar o Azure.

> [AZURE.NOTE] Embora não seja necessário nenhuma experiência com VMs do Azure para concluir este tutorial, você precisa ter uma conta do Azure. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure].(http://www.windowsazure.com/pt-br/develop/php/tutorials/create-a-windows-azure-account/). 

Este tutorial abrange:

- [Sobre máquinas virtuais no Azure] []
- [Como criar a máquina virtual] []
- [Como fazer o logon na máquina virtual após você criá-la] []
- [Como anexar um disco de dados à nova máquina virtual] []

**Importante**: Este tutorial cria uma máquina virtual que não está conectada a uma rede virtual. Se você deseja que sua máquina virtual use uma rede virtual, deve especificar a rede virtual ao criar a máquina virtual. Para mais informações sobre redes virtuais, consulte [Visão geral da Rede Virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

## <a id="virtualmachine"></a>Sobre máquinas virtuais no Azure ##

A máquina virtual no Azure é um servidor na nuvem que pode ser controlado e gerenciado. Após criar uma máquina virtual no Azure, você pode excluí-la e recriá-la sempre que precisar, além de acessá-la da mesma forma faria com um servidor no seu escritório. Arquivos do disco rígido virtual (VHD) são usados para criar uma máquina virtual. Os seguintes tipos de VHDs são usados para uma máquina virtual:

- **Imagem** - Um VHD que é usado como um modelo para criar uma nova máquina virtual. Uma imagem é um modelo porque, não tem configurações específicas como uma máquina virtual em execução, por exemplo, o nome do computador e a conta de usuário. Se você criar uma máquina virtual usando uma imagem, um disco do sistema operacional é criado automaticamente para a nova máquina virtual.
- **Disco** - Um disco é um VHD que pode ser inicializado e montado como uma versão em execução de um sistema operacional. Após uma imagem ser configurada, ela se torna um disco. Um disco sempre é criado quando você usa uma imagem para criar uma máquina virtual. Qualquer VHD que seja anexado ao hardware virtualizado e que esteja em execução como parte de um serviço é um disco

As seguintes opções estão disponíveis para usar imagens para criar uma máquina virtual:

- Crie uma máquina virtual usando uma imagem fornecida na Galeria de Imagens do Portal de Gerenciamento do Azure.
- Crie e carregue no Azure um arquivo .vhd que contém uma imagem e crie uma máquina virtual usando a imagem. Para obter mais informações sobre como criar e fazer upload de uma imagem personalizada, consulte [Criação e upload de um disco rígido virtual que contém o sistema operacional Linux](/pt-BR/gerenciar/linux/tarefas-comuns/carregamento-um-vhd /).

Cada máquina virtual reside em um serviço de nuvem, por si só, ou agrupada com outras máquinas virtuais. Você pode criar várias máquinas virtuais no mesmo serviço de nuvem a fim de ativar as máquinas virtuais para se comunicarem uma com as outras, para balanceamento de carga entre máquinas virtuais e para manter a alta disponibilidade das máquinas. Para obter mais informações sobre os serviços de nuvem e máquinas virtuais, consulte a seção "Modelos de execução" em [Apresentando o Azure](http://go.microsoft.com/fwlink/p/?LinkId=311926).

## <a id="custommachine"></a>Como criar a máquina virtual ##

Este tutorial usa o método **Da Galeria** para criar uma máquina virtual, pois isso lhe dá mais opções do que o método **Criação Rápida**. Você pode escolher recursos conectados, o nome DNS e a conectividade de rede, se necessário.

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../includes/virtual-machines-create-LinuxVM.md)]

[AZURE.INCLUDE [virtual-machines-Linux-tutorial-log-on-attach-disk](../includes/virtual-machines-Linux-tutorial-log-on-attach-disk.md)]

> [AZURE.NOTE] Você também pode se conectar à máquina virtual Linux usando uma chave SSH para identificação. Para obter mais detalhes sobre como configurar e conectar-se a uma chave SSH, consulte [Como usar SSH com Linux no Azure](./virtual-machines-linux-use-ssh-key/).

##Próximas etapas 

Para saber mais sobre o Linux no Azure, consulte os seguintes artigos:

- [Introdução ao Linux no Azure](http://www.windowsazure.com/pt-br/documentation/articles/introduction-linux/)

- [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux](http://www.windowsazure.com/pt-br/documentation/articles/xplat-cli/)

- [Sobre as definições de configuração da VM do Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)

- [A extensão de máquina virtual Docker para Linux no Azure](../virtual-machines-docker-vm-extension/)


[Próximas etapas]: #next
[Sobre máquinas virtuais no Azure]: #virtualmachine
[Como criar a máquina virtual]: #custommachine
[Como fazer o logon na máquina virtual após você criá-la]: #logon
[Como anexar um disco de dados à nova máquina virtual]: #attachdisk
<!--HONumber=42-->
