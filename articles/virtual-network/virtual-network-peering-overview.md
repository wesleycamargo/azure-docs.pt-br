
<properties
   pageTitle="Emparelhamento de rede virtual do Azure | Microsoft Azure"
   description="Saiba mais sobre o emparelhamento VNet no Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="NarayanAnnamalai"
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

O emparelhamento VNet é um mecanismo que conecta duas redes virtuais na mesma região por meio da rede principal do Azure. Uma vez emparelhadas, as duas redes virtuais aparecerão como uma para todos os fins de conectividade. Elas ainda são gerenciadas como recursos separados, mas as máquinas virtuais nessas redes virtuais podem se comunicar diretamente usando o endereço IP privado.

O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas será roteado por meio da infraestrutura do Azure, assim como o tráfego é roteado entre as VMs na mesma rede virtual. Entre os benefícios de usar o emparelhamento VNet estão:

- Baixa latência, conexão com largura de banda alta entre os recursos em redes virtuais diferentes.
- A capacidade de usar recursos, como dispositivos de rede e gateways de VPN como pontos de trânsito na VNet emparelhada.
- A capacidade de conectar uma rede virtual que usa o modelo do Azure Resource Manager a uma rede virtual que usa o modelo de implantação clássico, e permite a conectividade total entre recursos nestas redes virtuais.

Requisitos e principais aspectos do emparelhamento VNet:

- As duas redes virtuais emparelhadas devem estar na mesma região do Azure.
- As redes virtuais emparelhadas não devem ter espaços de endereço IP sobrepostos.
- O emparelhamento VNet ocorre entre duas redes virtuais, e não há nenhuma relação transitiva derivada. Por exemplo, se a rede virtual A estiver emparelhada com a rede virtual B, e se a rede virtual B estiver emparelhada com a rede virtual C, isso não significa que a rede virtual A estará emparelhada com a rede virtual C.
- O emparelhamento pode ser estabelecido as entre redes virtuais em duas assinaturas diferentes desde que um usuário privilegiado das duas assinaturas autorize o emparelhamento e as assinaturas estejam associadas ao mesmo locatário do Active Directory.
- Uma rede virtual que usa o modelo de implantação do Resource Manager pode ser emparelhada com outra rede virtual que usa esse modelo, ou com uma rede virtual que usa o modelo de implantação clássico. No entanto, as redes virtuais que usam o modelo de implantação clássico não podem ser emparelhadas entre si.
- Embora a comunicação entre as máquinas virtuais nas redes virtuais emparelhadas não tenha nenhuma restrição de largura de banda extra, o limite da largura de banda com base no tamanho da VM ainda se aplica.


![Emparelhamento VNet básico](./media/virtual-networks-peering-overview/figure01.png)

## Conectividade
Após o emparelhamento de duas redes virtuais, uma máquina virtual (função web/de trabalho) na rede virtual pode conectar diretamente com outras máquinas virtuais na rede virtual emparelhada. Essas duas redes terão conectividade total no nível do IP.

A latência de rede para uma viagem de ida e volta entre duas máquinas virtuais em redes virtuais emparelhadas, é a mesma para uma viagem de ida e volta em uma rede virtual local. A taxa de transferência da rede tem base na largura de banda permitida para a máquina virtual, que é proporcional ao seu tamanho. Não há restrição adicional de largura de banda.

O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas é roteado diretamente por meio da infraestrutura de back-end do Azure, e não por um gateway.

As máquinas virtuais em uma rede virtual podem acessar os pontos de extremidade de ILB (balanceamento de carga interno) na rede virtual emparelhada. NSGs (Grupos de segurança de rede) podem ser aplicados em qualquer rede virtual a fim de bloquear o acesso a outras redes virtuais, ou sub-redes se for desejado.

Quando os usuários configuram o emparelhamento, eles podem abrir ou fechar as regras NSG entre as redes virtuais. Se o usuário optar por abrir a conectividade total entre as redes virtuais emparelhadas (opção padrão), ele poderá usar os NSGs em sub-redes ou máquinas virtuais específicas para bloquear ou negar o acesso específico.

A resolução de nome DNS interna fornecida pelo Azure para as Máquinas virtuais não funcionam nas redes virtuais emparelhadas. As máquinas virtuais têm nomes DNS internos que podem ser resolvidos apenas na rede virtual local. No entanto, os usuários podem configurar máquinas virtuais em execução em redes virtuais emparelhadas como servidores DNS de uma rede virtual.

## Encadeamento de serviços
Os usuários podem configurar tabelas de rotas definidas pelo usuário que apontam para máquinas virtuais em redes virtuais emparelhadas como o endereço IP de "próximo salto", conforme mostra o diagrama posteriormente neste artigo. Isso permite que os usuários realizem o encadeamento de serviço, por meio do qual podem direcionar o tráfego de uma rede virtual para um dispositivo virtual em execução em uma rede virtual emparelhada por meio das tabelas de rotas definidas pelo usuário.

Os usuários também podem criar efetivamente ambientes do tipo hub e spoke, nos quais o hub pode hospedar componentes de infraestrutura, como um dispositivo de rede virtual. Assim, todas as redes virtuais de spoke podem emparelhar com ele, bem como um subconjunto de tráfego para dispositivos que estão em execução na rede virtual do hub. Resumindo, o emparelhamento VNet permite que o endereço IP de próximo salto na “Tabela de rotas definida pelo usuário” seja o endereço IP de uma máquina virtual na rede virtual emparelhada.

## Gateways e conectividade local
Cada rede virtual, independentemente de estar emparelhada com outra rede virtual ou não, ainda pode ter seu próprio gateway e usá-lo para se conectar ao local. Os usuários também podem configurar a [conexão VNet para VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) usando gateways, mesmo que as redes virtuais estejam emparelhadas.

Quando as duas opções para a interconectividade de rede virtual estiverem configuradas, o tráfego entre as redes virtuais fluirá através da configuração do emparelhamento (isto é, por meio do backbone do Azure).

Quando as redes virtuais estiverem emparelhadas, os usuários também poderão configurar o gateway na rede virtual emparelhada como um ponto de trânsito para o local. Nesse caso, a rede virtual que está usando um gateway remoto não pode ter seu próprio gateway. Uma rede virtual pode ter apenas um gateway. Ele pode ser um gateway local ou remoto (na rede virtual emparelhada), conforme mostra a figura a seguir.

Não há suporte para o tráfego do gateway na relação de emparelhamento entre redes virtuais usando o modelo do Resource Manager e as que usam o modelo de implantação clássico. As duas redes virtuais na relação de emparelhamento precisam usar o modelo de implantação do Resource Manager para que uma trânsito de gateway funcione.

Quando as redes virtuais que compartilham uma única conexão de Rota Expressa do Azure forem emparelhadas, o tráfego entre elas passará pela relação de emparelhamento (isto é, pela rede de backbone do Azure). Os usuários ainda podem usar gateways locais em cada rede virtual para se conectar ao circuito local. Como alternativa, eles podem usar um gateway compartilhado e configurar o trânsito para conectividade local.

![Trânsito de emparelhamento VNet](./media/virtual-networks-peering-overview/figure02.png)

## Provisionamento
O Emparelhamento VNet é uma operação privilegiada. É uma função separada sob o namespace da VirtualNetworks. Um usuário pode receber direitos específicos para autorizar o emparelhamento. Um usuário que tem acesso de leitura e gravação para a rede virtual herda automaticamente esses direitos.

Um usuário que é um administrador ou um usuário com privilégios da capacidade de emparelhamento pode iniciar uma operação de emparelhamento em outra VNet. Se houver uma solicitação correspondente para o emparelhamento no outro lado e outros requisitos forem atendidos, o emparelhamento será estabelecido.

Consulte os artigos nas “Próximas etapas” para saber mais sobre como estabelecer um emparelhamento VNet entre duas redes virtuais.

## Limites
Há limites no número de emparelhamentos permitidos para uma única rede virtual. Confira os [Limites de rede do Azure](../azure-subscription-service-limits.md#networking-limits) para saber mais.

## Preços
O Emparelhamento VNet será gratuito durante o período de análise. Após a liberação, haverá um custo nominal no tráfego de entrada e saída que utiliza o emparelhamento. Para saber mais, consulte a [página de preço](https://azure.microsoft.com/pricing/details/virtual-network).


## Próximas etapas
- [Configuração do emparelhamento entre redes virtuais](virtual-networks-create-vnetpeering-arm-portal.md).
- Saiba mais sobre [NSGs](virtual-networks-nsg.md).
- Saiba mais sobre [encaminhamento IP e rotas definidas pelo usuário](virtual-networks-udr-overview.md).

<!---HONumber=AcomDC_0928_2016-->