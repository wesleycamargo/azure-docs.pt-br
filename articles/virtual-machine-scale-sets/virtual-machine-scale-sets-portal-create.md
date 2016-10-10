<properties
	pageTitle="Criação de conjuntos de escala com o portal de CLI | Microsoft Azure"
	description="Implantar conjuntos de escala usando o portal do Azure."
	keywords="conjuntos de escala de máquina virtual" 
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gatneil"
	manager="madhana"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machine"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2016"
	ms.author="gatneil"/>

# Criar um conjunto de escala usando o Portal do Azure

Este tutorial mostra como é fácil criar um conjunto de escala de máquina virtual em poucos minutos usando o Portal do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## Escolher a imagem de VM no Marketplace

No portal, você pode implantar facilmente um conjunto de escala com as imagens do CentOS, CoreOS, Debian, Open Suse, Red Hat Enterprise Linux, SUSE Linux Enterprise Server ou Ubuntu Server.

Primeiro, navegue até o [Portal do Azure](https://portal.azure.com) no navegador da Web. Clique em `New`, pesquise `scale set` e selecione a entrada `Virtual machine scale set`:

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## Criar sua primeira máquina virtual do Windows

Agora, você pode usar as configurações padrão e criar rapidamente a máquina virtual.

* Na folha `Basics`, insira um nome para o conjunto de escala. Esse nome se torna a base do FQDN do balanceador de carga na frente do conjunto de escala. Portanto, certifique-se de que o nome seja exclusivo em todo o Azure.

* Selecione o tipo de OS desejado, insira o nome de usuário desejado e selecione o tipo de autenticação que prefere. Se você escolher uma senha, ela deverá ter pelo menos 12 caracteres e atender três dos quatro requisitos de complexidade a seguir: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial. Veja mais sobre os [requisitos de nome de usuário e senha](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm). Se você escolher `SSH public key`, cole somente sua chave pública, NÃO sua chave privada:

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* Digite o nome do grupo de recursos desejado e a localização e clique em `OK`.

* Na folha `Virtual machine scale set service settings`: digite o rótulo de nome de domínio desejado (a base do FQDN para o balanceador de carga na frente do conjunto de escala). Este rótulo deve ser exclusivo em todo o Azure.

* Escolha a imagem de disco do sistema operacional desejada, a contagem de instâncias e o tamanho da máquina.

* Habilitar ou desabilitar o dimensionamento automático e configurar, se habilitado:

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* Na folha `Summary`, após a validação, clique em `OK`.

* Por fim, na folha `Purchase`, clique em `Purchase` para iniciar a implantação do conjunto de escala.

## Conectar-se a uma VM no conjunto de escala

Quando o conjunto de escala estiver implantado, navegue até a guia `Inbound NAT Rules` do balanceador de carga para o conjunto de escala:

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

Você pode se conectar a cada VM no conjunto de escala usando estas regras NAT. Por exemplo, para um conjunto de escala do Windows, se houver uma regra NAT na porta de entrada 50000, você poderá se conectar a essa máquina via RDP em `<load-balancer-ip-address>:50000`. Para um conjunto de escala do Linux, você se conectaria usando o comando `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## Próximas etapas

Para ver a documentação sobre como implantar conjuntos de escala da CLI, consulte [esta documentação](./virtual-machine-scale-sets-cli-quick-create.md).

Para ver a documentação sobre como implantar conjuntos de escala do Powershell, consulte [esta documentação](./virtual-machine-scale-sets-windows-create.md).

Para ver a documentação sobre como implantar conjuntos de escala do Visual Studio, consulte [esta documentação](./virtual-machine-scale-sets-vs-create.md).

Para ver a documentação geral, confira a [página de visão geral da documentação para conjuntos de escala](./virtual-machine-scale-sets-overview.md).

Para obter informações gerais, confira a [página de aterrissagem principal para conjuntos de escala](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

<!---HONumber=AcomDC_0928_2016-->