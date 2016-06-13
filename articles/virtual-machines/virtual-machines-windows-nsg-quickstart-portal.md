<properties
   pageTitle="Permitir o acesso externo a uma VM usando o portal | Microsoft Azure"
   description="Saiba como abrir uma porta/criar um ponto de extremidade que permite o acesso à sua VM usando o modelo de implantação do Gerenciador de Recursos no Portal do Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="05/24/2016"
   ms.author="iainfou"/>

# Permitir o acesso externo à sua VM usando o Portal do Azure
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## Comandos rápidos
Você também pode [executar essas etapas usando o Azure PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

Primeiro, crie o seu Grupo de Segurança de Rede. Selecione um grupo de recursos no portal, clique em 'Adicionar' e, em seguida, pesquise por um 'Grupo de Segurança de Rede' de seleção:

![Adicionar um Grupo de Segurança de Rede](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

Insira um nome para o seu Grupo de Segurança de Rede e para a seleção de uma localização:

![Criar um grupo de segurança de rede](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

Selecione o novo Grupo de Segurança de Rede. Agora, você criou uma regra de entrada:

![Adicionar uma regra de entrada](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

Forneça um nome para sua nova regra. Observe que a porta 80 já está inserida por padrão. Ela é o local no qual você alteraria a origem, o protocolo e o destino ao adicionar regras extras ao seu Grupo de Segurança de Rede:

![Criar uma regra de entrada](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

A etapa final é associar o Grupo de Segurança de Rede com uma sub-rede ou uma interface de rede específica. Vamos associar o Grupo de Segurança de Rede com uma sub-rede:

![Associar um Grupo de Segurança de Rede a uma sub-rede](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

Selecione sua rede virtual e, então, selecione a sub-rede apropriada:

![Associar um Grupo de Segurança de Rede à rede virtual](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

Você criou um Grupo de Segurança de Rede, uma regra de entrada que permite o tráfego na porta 80 e o associou a uma sub-rede. Todas as VMs que você conectar a essa sub-rede estarão acessíveis na porta 80.


## Mais informações sobre os Grupos de Segurança de Rede
Os comandos rápidos aqui permitem que você coloque tudo em funcionamento com o tráfego que flui para sua VM. Os Grupos de Segurança de Rede fornecem muitos recursos excelentes e granularidade para controlar o acesso aos seus recursos. Você pode ler mais sobre a [criação de um Grupo de Segurança de Rede e as regras ACL aqui](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Os Grupos de Segurança de Rede e as regras ACL também podem ser definidos como parte dos modelos do Azure Resource Manager. Leia mais sobre a [criação de Grupos de Segurança de Rede com modelos](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se precisar usar o encaminhamento de porta para mapear uma porta externa exclusiva para uma porta interna em sua VM, você precisará usar um balanceador de carga e regras de NAT (Conversão de Endereços de Rede). Por exemplo, talvez você queira expor a porta TCP 8080 externamente e direcionar o tráfego para a porta TCP 80 em uma VM. Você pode aprender sobre a [criação de um balanceador de carga para a Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## Próximas etapas
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:

- [Visão geral do Gerenciador de Recursos do Azure](../resource-group-overview.md)
- [O que é um NSG (grupo de segurança de rede)?](../virtual-network/virtual-networks-nsg.md)
- [Visão Geral do Azure Resource Manager para Balanceadores de Carga](../load-balancer/load-balancer-arm.md)

<!---HONumber=AcomDC_0601_2016-->