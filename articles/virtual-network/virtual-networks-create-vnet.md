<properties 
   pageTitle="Como criar uma rede virtual (VNet)"
   description="Saiba como criar uma rede virtual (VNet)"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# Como criar uma rede virtual (VNet)

Quando você cria uma rede virtual, seus serviços e máquinas virtuais na VNet podem se comunicar com segurança entre si sem ter que utilizar a Internet. A criação de uma VNet do Azure é um processo relativamente rápido e fácil se a ideia não for conectá-la a outras VNets ou à sua rede local, já que não será necessário adquirir e configurar um dispositivo VPN ou coordenar os endereços IP que você escolher com outras VNets ou com a rede local.

>[AZURE.WARNING]Não use esse procedimento para criar uma VNet que será conectada mais tarde a outras VNets ou à sua rede local. Se você deseja criar uma conexão híbrida ou segura entre locais, confira [Sobre conectividade segura entre locais na rede virtual](https://msdn.microsoft.com/library/azure/dn133798.aspx). Se você quiser criar uma VNet que se conecta a outra VNet, confira [Configurar uma conexão VNet com VNet](https://msdn.microsoft.com/library/azure/dn690122.aspx).

## Configurar a rede virtual

1. Faça logon no **Portal de Gerenciamento do Azure**.

1. No canto inferior esquerdo da tela, clique em **Nova**. No painel de navegação, clique em **Serviços de Rede** e, em seguida, clique em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.

1. Na página **Detalhes da Rede Virtual**, insira as informações a seguir.

	![Detalhes de rede virtual](./media/virtual-networks-create-vnet/IC736054.png)

	- **Nome:** dê um nome à VNet. Por exemplo, criamos o nome *EastUSVNet*. Você pode criar qualquer nome desejado. Esse nome da rede virtual deve ser usado quando você implantar suas VMs e serviços; portanto, é melhor usar um nome simples.

	- **Local:** selecione o local (região) na lista suspensa. O local está diretamente relacionado ao local físico (região) onde você deseja que os recursos (VMs) residam. Por exemplo, se você deseja que suas VMs fiquem localizadas fisicamente no *Leste dos EUA*, selecione essa região local. Você não pode alterar a região associada à sua rede virtual depois de criá-la.

1. Na página **Servidores DNS e Conectividade VPN**, não faça nenhuma alteração. Simplesmente avance para a próxima página clicando na seta. Por padrão, o Azure fornece solução de nomes básica para a sua rede virtual. É possível que seus requisitos de resolução de nome sejam mais complexos do que pode ser tratado pela resolução de nomes básica do Azure. Nesse caso, você pode querer adicionar uma máquina virtual executando DNS à sua rede virtual posteriormente Para saber mais sobre DNS e solução de nomes do Azure, confira [Solução de nome (DNS)](https://msdn.microsoft.com/library/azure/jj156088.aspx).

1. A página **Espaços de Endereço de Rede Virtual** é onde você insere o espaço de endereço que deseja usar para esse VNet. A menos que você precise de um determinado intervalo de endereço IP interno para suas VMs ou deseja criar uma subrede específica para VMs que receberão um DIP estático, você não precisa fazer nenhuma alteração nessa página. Se você quiser criar várias sub-redes, pode fazer isso nessa página clicando em **Adicionar sub-rede**.

	Informações adicionais:

	- Os intervalos nessa página contêm os endereços IP internos dinâmicos (DIPs) que suas VMs receberão quando você implantá-las nessa VNet. Esses endereços IP servem apenas para comunicação com a VNet. Eles não são endereços IP para pontos de extremidade de Internet.

	- Como você não conectará essa rede virtual privada à sua rede local usando uma configuração de VPN entre locais, não precisará coordenar essas configurações com seus intervalos de endereços IP de rede existentes no local. Se você acha que talvez queira criar uma configuração entre locais posteriormente, precisará coordenar os espaços de endereços agora com os intervalos que já existem no seu site local para evitar problemas de roteamento. A alteração dos intervalos mais tarde pode ser um pouco complicada, especialmente se houver sobreposição de intervalos de endereços.

	![Espaço de endereço](./media/virtual-networks-create-vnet/IC716778.png)

1. Clique na marca de seleção no canto inferior direito da página **Espaços de Endereço da Rede Virtual** e sua rede virtual começará a ser criada. Quando a sua rede virtual tiver sido criada, você verá a denominação **Criada** listada embaixo de **Status** na página **Redes** no Portal de Gerenciamento.

1. Quando a sua VNet tiver sido criada, você pode criar VMs e instâncias de PaaS na VNet. Selecione **Da galeria** durante a criação de uma nova VM para ter a opção de selecionar a sua rede virtual. Observe que se você tiver instâncias existentes de VMs e PaaS implantadas, elas não podem simplesmente ser movidas para sua nova VNet. Isso ocorre porque as configurações de rede necessárias são adicionadas durante a implantação.

## Adicionando máquinas virtuais ao seu VNet

Depois de criar a sua VNet, você pode adicionar novas VMs a ela. É importante criar a VNet primeiro e depois implantar a VM. Após a implantação de uma VM, você não pode movê-la para uma VNet sem reimplantá-la. Se você usar o Portal de Gerenciamento para criar suas VMs, a interface para implantar a VM em uma VNet só fica disponível quando você seleciona **Novo/Computação/Máquina virtual/Da galeria/**. Quando você entrar no Assistente para criar a sua VM na página **Configuração de máquina Virtual**, verá **Região/Grupo de afinidades/Rede virtual**. Na lista suspensa, selecione a VNet já criada. Para saber mais sobre a criação de máquinas virtuais, confira [Máquinas virtuais do Azure](../virtual-machines).

## Próximas etapas

[Como gerenciar as propriedades da rede virtual (VNet)](../virtual-networks-settings)

[Como gerenciar servidores DNS usados por uma rede virtual (VNet)](../virtual-networks-manage-dns-in-vnet)

[Como usar endereços IP públicos em uma rede virtual](../virtual-networks-public-ip-within-vnet)

[Como excluir uma rede virtual (VNet)](../virtual-networks-delete-vnet)
 

<!---HONumber=62-->