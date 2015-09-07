<properties 
   pageTitle="Como excluir uma rede Virtual (VNet)"
	description="Saiba como excluir uma VNet existente"
	services="virtual-network"
	documentationCenter="na"
	authors="telmosampaio"
	manager="carolz"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="05/29/2015"
	ms.author="telmos"/>

# Como excluir uma rede Virtual (VNet)

Se você deseja excluir uma VNet, não pode simplesmente clicar em **Excluir**. Há algumas coisas que você deve fazer primeiro:

1. **Salvar configurações (opcional):** se você quiser salvar as configurações da rede virtual em um arquivo local, exporte o arquivo de configuração antes de excluir a rede virtual. Confira [Exportar configurações de rede virtual para um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md) para saber mais. Salvar as configurações permite que você recrie a VNet no futuro, se necessário.

1. **Excluir o Gateway de rede Virtual:** se você tiver configurado um gateway para a VNet, deve excluí-lo antes de excluir a VNet. Para excluir o gateway de rede virtual, vá para a página Painel da sua rede virtual. Na parte inferior da página, clique em **Excluir Gateway**.
						
	Você terá que esperar de 5 a 10 minutos para que o gateway ser excluído antes de continuar com as etapas seguintes.

1. **Excluir serviços de nuvem, sites e máquinas virtuais:** se você tiver implantando algo na VNet, deverá excluir os objetos antes de excluir a VNet.

1. **Excluir sua rede virtual:** clique à direita da linha *Nome* com o botão direito do mouse para realçar a VNet que você deseja excluir e clique em **Excluir** na parte inferior da página. Siga as instruções na tela.

1. **Adicionalmente:** você também pode optar por excluir as configurações de rede local, os servidores DNS e o grupo de afinidades depois de excluir a sua rede virtual.
 

<!---HONumber=August15_HO9-->