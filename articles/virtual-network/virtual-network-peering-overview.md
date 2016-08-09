<properties
   pageTitle="Emparelhamento da Rede Virtual do Azure | Microsoft Azure"
   description="Saiba mais sobre o Emparelhamento VNet no Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="narayanannamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2016"
   ms.author="narayan" />

# Emparelhamento VNet

O Emparelhamento VNet é um mecanismo para conectar duas Redes Virtuais na mesma região por meio da rede de backbone do Azure. Uma vez emparelhadas, as duas Redes Virtuais aparecerão como uma para todas as finalidades de conectividade. Elas ainda serão gerenciadas como recursos separados, mas as máquinas virtuais nas VNets podem comunicar-se diretamente usando o endereço IP privado. O tráfego entre as Máquinas virtuais nas VNets emparelhadas será roteado por meio da infraestrutura do Azure como o tráfego entre as VMs na mesma VNet. Alguns benefícios de usar o Emparelhamento VNet:

- Baixa latência, conexão da largura de banda alta entre os recursos em VNets diferentes.
- Capacidade de usar recursos, como dispositivos da Rede Virtual e gateways de VPN na VNet emparelhada (Trânsito).
- Conectar a VNet do Gerenciador de Recursos a uma VNet clássica e habilitar total conectividade entre os recursos nessas VNet

Requisitos e principais aspectos do emparelhamento VNet:

- As duas Redes Virtuais emparelhadas devem estar na mesma região do Azure
- As VNet emparelhadas não devem ter nenhum espaço do Endereço IP sobrepondo-se
- O Emparelhamento VNet é entre duas redes virtuais e não há nenhuma relação transitiva derivada, ou seja, se a VNet A for emparelhada com a VNet B e a VNet B for emparelhada com a VNet C, ele não converterá a VNet A para ser emparelhada com a VNet C
- O emparelhamento pode ser estabelecido entre as Redes virtuais em duas assinaturas diferentes desde que um usuário privilegiado das respectivas assinaturas autorize o emparelhamento
- Uma VNet do Gerenciador de Recursos pode ser emparelhada com outra VNet do Gerenciador de Recursos ou VNet clássica, mas duas VNets clássicas não podem ser emparelhadas entre si
- Embora a comunicação entre as Máquinas virtuais nas VNets emparelhadas não tenha nenhuma restrição de largura de banda extra, o limite da largura de banda com base no tamanho da VM ainda se aplicará.


![Noções Básicas do Emparelhamento VNet](./media/virtual-networks-peering-overview/figure01.png)

## Conectividade 
Depois de duas VNets serem emparelhadas, uma máquina virtual (função Web/trabalho) na VNet pode conectar-se diretamente às outras máquinas virtuais na VNet emparelhada. Elas terão total conectividade no nível do IP. A latência da rede para a ida e volta entre as duas Máquinas virtuais nas VNets emparelhadas será igual na VNet local. A taxa de transferência da rede se baseará na largura de banda permitida para a Máquina Virtual proporcional ao seu tamanho; não haverá nenhuma restrição extra na largura de banda permitida. O tráfego entre as Máquinas virtuais nas VNets emparelhadas é roteado diretamente por meio da infraestrutura de back-end do Azure, e não por um gateway.

As Máquinas virtuais em uma VNet serão capazes de acessar os pontos de extremidade com balanceamento de carga interno (ILB) na VNet emparelhada. Os Grupos de Segurança da Rede podem ser aplicados em qualquer VNet para bloquear o acesso a outra VNet ou sub-rede, se desejado. Quando o usuário configurar o emparelhamento, ele terá a opção de abrir ou fechar as regras do Grupo de Segurança da Rede entre as VNets. Se o usuário optar por abrir a total conectividade entre as VNets emparelhadas (opção padrão), ele poderá usar os NSGs em sub-redes ou Máquinas virtuais específicas para bloquear ou negar o acesso específico.

O Azure forneceu a resolução de nomes DNS interna para as Máquinas virtuais não funcionarem nas VNets emparelhadas. As Máquinas virtuais terão nomes DNS internos que podem ser determinados apenas na Rede Virtual local. No entanto, os usuários podem configurar as Máquinas virtuais em execução nas VNets emparelhadas como servidores DNS de uma Rede virtual.

## Encadeamento de Serviços
Os usuários podem configurar as tabelas de rotas definidas pelo usuário apontando para as Máquinas virtuais nas VNets emparelhadas como próximo salto (conforme mostrado no diagrama a seguir). Isso permite que os usuários realizem o encadeamento de serviço pelo qual podem direcionar o tráfego de uma VNet para um Dispositivo virtual em execução em uma VNet emparelhada por meio das tabelas de rotas definidas pelo usuário. Os usuários também podem criar efetivamente um Hub e ambientes de spoke, nos quais o Hub pode hospedar o componente de base, como um dispositivo de Rede Virtual, e todas as VNets de spoke podem emparelhar com ele e direcionar um subconjunto de tráfego para os aplicativos em execução na VNet de hub. Resumindo, o emparelhamento VNet permite que o Endereço IP do próximo salto na 'Tabela de rotas definida pelo usuário' seja de uma máquina virtual na VNet emparelhada.

## Conectividade de Gateways e Local
Cada Rede Virtual, independentemente de estar emparelhada com outra VNet ou não, ainda pode ter seu próprio gateway e usá-lo para se conectar ao local. Os usuários também podem configurar a conexão VNet para VNet (fornecer um link) usando gateways, mesmo que as VNet estejam emparelhadas. Quando as duas opções para a interconectividade da VNet estiverem configuradas, o tráfego entre as VNets fluirá através da configuração do emparelhamento (isto é, por meio do backbone do Azure).

Quando as VNets estão emparelhadas, os usuários também podem configurar o uso do gateway na VNet emparelhada como um ponto de trânsito para o local. Neste caso, a VNet que está usando um gateway remoto não poderá ter um gateway próprio, para simplificar, uma VNet pode ter apenas um gateway; ele pode ser um gateway local ou remoto (na VNet emparelhada), conforme ilustrado na figura abaixo. O Trânsito do Gateway não tem suporte entre o Gerenciador de Recursos e a VNet clássica; ambas as VNets na relação de emparelhamento devem ser VNets do Gerenciador de Recursos para o trânsito do gateway funcionar. Quando as VNets que compartilham um único circuito ER forem emparelhadas, o tráfego entre elas passará pela relação de emparelhamento (isto é, por meio da rede de backbone do Azure). Os usuários ainda podem usar os gateways locais em cada VNet para conectar o circuito local ou usar um gateway compartilhado e configurar o trânsito para a conectividade local.

![Trânsito do Emparelhamento VNet](./media/virtual-networks-peering-overview/figure02.png)

## Provisionamento
O Emparelhamento VNet é uma operação privilegiada. É uma função separada sob o namespace da Rede Virtual. Um usuário pode receber direitos específicos para autorizar o emparelhamento. Um usuário que tem acesso de leitura/gravação na VNet herdará isso automaticamente. Um usuário que seja um administrador ou um usuário com privilégios da capacidade de emparelhamento pode iniciar uma operação de emparelhamento para outra VNet. Se houver uma solicitação correspondente para o emparelhamento no outro lado e outros requisitos forem atendidos, o emparelhamento será estabelecido.

Consulte os artigos de instruções para saber mais sobre como estabelecer um emparelhamento VNet entre duas Redes Virtuais.

## Limites
Há limites no número de emparelhamentos permitidos para uma Rede virtual; consulte [Limites da Rede do Azure](../azure-subscription-service-limits.md#networking-limits) para obter mais informações.

## Preços
O Emparelhamento VNet não será cobrado durante o período de revisão. Depois dele ser liberado para a Disponibilidade Geral, haverá um custo nominal no tráfego de entrada e saída que utiliza o emparelhamento. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network)


## Próximas etapas
- [Configuração do emparelhamento entre as Redes Virtuais](virtual-networks-create-vnetpeering-arm-portal.md).
- Saiba mais sobre [NSGs](virtual-networks-nsg.md).
- Saiba mais sobre [encaminhamento IP e rotas definidas pelo usuário](virtual-networks-udr-overview.md).

<!---HONumber=AcomDC_0803_2016-->