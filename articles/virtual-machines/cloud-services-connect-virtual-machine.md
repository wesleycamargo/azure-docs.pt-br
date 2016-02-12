<properties
	pageTitle="Conectar VMs em um serviço de nuvem | Microsoft Azure"
	description="Conectar uma máquina virtual criada com o modelo clássico de implantação com um serviço de nuvem ou de rede virtual do Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="cynthn"/>


# Conectar máquinas virtuais criadas com o modelo clássico de implantação com um serviço de nuvem ou de rede virtual

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


As máquinas virtuais criadas com o modelo de implantação clássico sempre são colocadas em um serviço de nuvem. O serviço de nuvem funciona como um contêiner e fornece um nome DNS público exclusivo, um endereço IP público e um conjunto de pontos de extremidade para acessar a máquina virtual pela Internet. O serviço de nuvem pode estar em uma rede virtual, mas isso não é um requisito.

Se um serviço de nuvem não estiver em uma rede virtual, ele será chamado de serviço de nuvem *autônomo*. As máquinas virtuais em um serviço de nuvem autônomo só poderão se comunicar com outras máquinas virtuais usando os nomes DNS públicos de outras máquinas virtuais, e o tráfego viajará pela Internet. Se um serviço de nuvem estiver em uma rede virtual, as máquinas virtuais no serviço de nuvem podem se comunicar com todas as outras máquinas virtuais na rede virtual sem enviar tráfego pela Internet.

Ao colocar as máquinas virtuais no mesmo serviço de nuvem autônomo, você ainda pode usar o balanceamento de carga e os conjuntos de disponibilidade. Para obter detalhes, consulte [Máquinas virtuais de balanceamento de carga](load-balance-virtual-machines.md) e [Gerenciar a disponibilidade das máquinas virtuais](virtual-machines-manage-availability.md). No entanto, você não pode organizar as máquinas virtuais em sub-redes ou conectar um serviço de nuvem autônomo à sua rede local. Aqui está um exemplo:

![Máquinas virtuais em um serviço de nuvem autônomo](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)

Ao colocar as máquinas virtuais em uma rede virtual, você pode decidir quantos serviços de nuvem deseja usar para os conjuntos de disponibilidade e o balanceamento de carga. Além disso, você pode organizar as máquinas virtuais em sub-redes da mesma forma como sua rede local e conectar a rede virtual à sua rede local. Aqui está um exemplo:

![Máquinas virtuais em uma rede virtual](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

As redes virtuais são a maneira recomendada para conectar máquinas virtuais no Azure. A prática recomendada é configurar cada camada do aplicativo em um serviço de nuvem separado. No entanto, talvez seja necessário combinar algumas máquinas virtuais de diferentes níveis de aplicativos no mesmo serviço de nuvem para permanecer dentro do máximo de 200 serviços de nuvem por assinatura. Para verificar isso e outros limites, consulte [Assinatura do Azure e limites de serviços, cotas e restrições](azure-subscription-service-limits.md).

## Conectar VMs em uma rede virtual

Para conectar máquinas virtuais em uma rede virtual:

1.	Crie a rede virtual no [portal do Azure](virtual-networks-create-vnet-classic-pportal.md).
2.	Crie o conjunto de serviços de nuvem para sua implantação de modo a refletir seu design para conjuntos de disponibilidade e balanceamento de carga. No portal clássico do Azure, clique em **Novo > Computação > Serviço de Nuvem > Criação Personalizada** para cada serviço de nuvem.
3.	Para criar cada máquina virtual nova, clique em **Novo > Computação > Máquina Virtual > Da Galeria**. Escolha o serviço de nuvem e a rede virtual corretos para a VM. Se o serviço de nuvem já fizer parte de uma rede virtual, seu nome já estará selecionado para você.

![Selecionar um serviço de nuvem para uma máquina virtual](./media/howto-connect-vm-cloud-service/VMConfig1.png)

## Conectar VMs em um serviço de nuvem autônomo

Para conectar máquinas virtuais em um serviço de nuvem autônomo:

1.	Crie o serviço de nuvem no [portal clássico do Azure](http://manage.windowsazure.com). Clique em **Novo > Computação > Serviço de Nuvem > Criação Personalizada**. Ou você pode criar o serviço de nuvem para sua implantação ao criar sua primeira máquina virtual.
2.	Ao criar as máquinas virtuais, escolha o nome do serviço de nuvem criado na etapa anterior. ![Adicionar uma máquina virtual a um serviço de nuvem existente](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##Recursos
[Máquinas virtuais de balanceamento de carga](load-balance-virtual-machines.md)

[Gerencie a disponibilidade de máquinas virtuais](virtual-machines-manage-availability.md)

Após criar uma máquina virtual, é uma boa ideia adicionar um disco de dados para que seus serviços e cargas de trabalho tenham um local para armazenar dados. Veja uma das opções a seguir:

[Como anexar um disco de dados na máquina virtual Linux](virtual-machines-linux-how-to-attach-disk.md)

[Como anexar um disco de dados a uma máquina virtual Windows](storage-windows-attach-disk.md)

<!---HONumber=AcomDC_0204_2016-->