<properties
   pageTitle="Endereçamento IP público e privado no Gerenciador de Recursos do Azure | Microsoft Azure"
   description="Saiba mais sobre endereçamento IP público e privado no Gerenciador de Recursos do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# Endereços IP no Azure
Você pode atribuir endereços IP aos recursos do Azure para se comunicar com outros recursos do Azure, sua rede local e a Internet. Há dois tipos de endereços IP que você pode usar no Azure:

- **Endereços IP públicos**: usados para comunicação com a Internet, incluindo serviços públicos do Azure
- **Endereços IP privados**: usados para comunicação em uma VNet (rede virtual) do Azure e na sua rede local quando você usa um gateway de VPN ou circuito de Rota Expressa para estender sua rede para o Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-ip-addresses-overview-classic.md).

Se você estiver familiarizado com o modelo de implantação clássico, verifique as [diferenças de endereçamento IP entre o clássico e o Gerenciador de Recursos](virtual-network-ip-addresses-overview-classic.md#Differences-between-Resource-Manager-and-classic-deployments).

## Endereços IP públicos
Endereços IP públicos permitem que os recursos do Azure comuniquem-se com os serviços públicos do Azure, como [Cache Redis do Azure](https://azure.microsoft.com/services/cache/), [Hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/), [bancos de dados SQL](../sql-database/sql-database-technical-overview.md) e [armazenamento do Azure](../storage/storage-introduction.md).

No Gerenciador de recursos do Azure, um endereço [IP público](resource-groups-networking.md#public-ip-address) é um recurso com as próprias propriedades. Você pode associar um recurso de endereço IP público a qualquer um dos seguintes recursos:

- VM (máquinas virtuais)
- Balanceadores de carga para Internet
- Gateways VPN
- Application gateways

### Método de alocação
Há dois métodos de alocar um endereço IP para um *recurso IP público*: *dinâmico* ou *estático*. O método de alocação padrão é *dinâmico*, em que o endereço IP **não** é alocado no momento de sua criação. Em vez disso, o endereço IP público é alocado quando você inicia (ou cria) o recurso associado (como um balanceador de carga ou VM). O endereço IP é liberado quando você para (ou exclui) o recurso. Isso faz com que o endereço IP mude quando você interrompe e inicia um recurso.

Para garantir que o endereço IP para o recurso associado permaneça o mesmo, você pode definir o método de alocação explicitamente como *estático*. Nesse caso, um endereço IP é atribuído imediatamente. Ele é liberado apenas quando você exclui o recurso ou altera seu método de alocação para *dinâmico*.

>[AZURE.NOTE] Mesmo quando você define o método de alocação para *estático*, não é possível especificar o endereço IP real atribuído ao *recurso IP público*. Em vez disso, ele é alocado de um pool de endereços IP disponíveis no local do Azure em que o recurso é criado.

Os endereços IP públicos estáticos são comumente usados nas seguintes situações:

- Os usuários finais precisam atualizar regras de firewall para comunicarem-se com os recursos do Azure.
- Resolução de nome DNS, em que uma alteração no endereço IP exigiria a atualização de registros A.
- Seus recursos do Azure comunicam-se com outros aplicativos ou serviços que usam um endereço IP baseado em um modelo de segurança.
- Você usa certificados SSL vinculados a um endereço IP.

>[AZURE.NOTE] A lista de intervalos IP do qual os endereços IP públicos (dinâmicos ou estáticos) são alocados a recursos do Azure é publicada a [intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Resolução de nome de host DNS
Você pode especificar um rótulo de nome de domínio DNS para um recurso IP público, que cria um mapeamento para *domainnamelabel*.*location*.cloudapp.azure.com para o endereço IP público nos servidores DNS gerenciados pelo Azure. Por exemplo, se você criar um recurso IP público com **contoso** como *domainnamelabel* no **local** do Azure *Oeste dos EUA*, o nome de domínio totalmente qualificado (FQDN) **contoso.westus.cloudapp.azure.com** será resolvido para o endereço IP público do recurso. Você pode usar o FQDN para criar um registro CNAME do domínio personalizado apontando para o endereço IP público no Azure.

>[AZURE.IMPORTANT] Cada rótulo de nome do domínio criado deve ser exclusivo dentro de seu local do Azure.

### Máquinas virtuais
Você pode associar um endereço IP público a uma VM [Windows](../virtual-machines/virtual-machines-windows-about.md) ou [Linux](../virtual-machines/virtual-machines-linux-about.md) atribuindo-o à sua **interface de rede**. No caso de uma VM com várias interfaces de rede, você pode atribuí-lo somente à interface de rede *primária*. Você pode atribuir um endereço IP público dinâmico ou estático a uma VM.

### Balanceadores de carga para Internet
Você pode associar um endereço IP público com um [Azure Load Balancer](../load-balancer/load-balancer-overview.md), atribuindo-o à configuração de **front-end** do balanceador de carga. Este endereço IP público serve como um balanceamento de carga de endereço VIP (IP virtual). Você pode atribuir um endereço IP público estático ou dinâmico a um front-end de balanceador de carga. Você também pode atribuir vários endereços IP públicos a um front-end de balanceador de carga, que permite cenários [multi-VIP](../load-balancer/load-balancer-multivip.md) como um ambiente de multilocatário com sites baseados em SSL.

### Gateways VPN
O [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) é usado para conectar uma rede virtual do Azure (VNet) a outras VNets do Azure ou uma rede local. É necessário atribuir um endereço IP público à sua **configuração IP** para permitir a comunicação com a rede remota. No momento, você pode atribuir apenas um endereço IP público *dinâmico* a um gateway de VPN.

### Application gateways
Você pode associar um endereço IP público do Azure [Application Gateway](../application-gateway/application-gateway-introduction.md), atribuindo-o à configuração de **front-end** do gateway. Esse endereço IP público serve como um VIP com balanceamento de carga. No momento, você pode atribuir apenas um endereço IP público *dinâmico* a uma configuração de front-end de do Application Gateway.

### Imediato
A tabela a seguir mostra a propriedade específica por meio da qual um endereço IP público pode ser associado a um recurso de nível superior e os métodos possíveis de alocação (dinâmico ou estático) que podem ser usados.

|Recurso de nível superior|Associação de Endereço IP|Dinâmico|Estático|
|---|---|---|---|
|Máquina virtual|Interface de rede|Sim|Sim|
|Balanceador de carga|Configuração de front-end|Sim|Sim|
|Gateway de VPN|Configuração de IP do gateway|Sim|Não|
|Application Gateway|Configuração de front-end|Sim|Não|

## Endereços IP privados
Endereços IP privados permitem que os recursos do Azure comuniquem-se com outros recursos de uma [rede virtual](virtual-networks-overview.md), ou na rede local por meio de um gateway de VPN ou circuito de Rota Expressa, sem usar um endereço IP acessível pela Internet.

No modelo de implantação do Azure Resource Manager, um endereço IP privado é associado aos seguintes tipos de recursos do Azure:

- VMs
- ILBs (balanceadores de carga internos)
- Application gateways

### Método de alocação
Um endereço IP privado é alocado do intervalo de endereços da sub-rede à qual o recurso está conectado. O intervalo de endereços da sub-rede em si faz parte do intervalo de endereços da rede virtual.

Há dois métodos de alocar um endereço IP privado: *dinâmico* ou *estático*. O método de alocação padrão é *dinâmico*, em que o endereço IP é alocado automaticamente da sub-rede do recurso (usando DHCP). Esse endereço IP pode mudar quando você interromper e iniciar o recurso.

Você pode definir o método de alocação para *estático* para garantir que o endereço IP permaneça o mesmo. Nesse caso, você também precisa fornecer um endereço IP válido que faça parte da sub-rede do recurso.

Os endereços IP privados estáticos costumam ser usados para:

- VMs que atuam como controladores de domínio ou servidores DNS.
- Recursos que exigem regras de firewall usando endereços IP.
- Recursos acessados por outros aplicativos/recursos por meio de um endereço IP.

### Máquinas virtuais
Um endereço IP privado é atribuído à **interface de rede** de uma VM [Windows](../virtual-machines/virtual-machines-windows-about.md) ou [Linux](../virtual-machines/virtual-machines-linux-about.md). No caso de uma VM com várias interfaces de rede, é atribuído um endereço IP privado a cada interface. Você pode especificar o método de alocação como dinâmico ou estático para uma interface de rede.

#### Resolução do nome do host DNS interno (para VMs)
Todas as VMs do Azure são configuradas com [servidores DNS gerenciados Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) por padrão, a menos que você explicitamente configure servidores DNS personalizados. Esses servidores DNS fornecem resolução de nomes interna para VMs que residem na mesma VNet.

Quando você cria uma VM, um mapeamento para o nome de host para seu endereço IP privado é adicionado aos servidores DNS gerenciados pelo Azure. No caso de uma VM com várias interfaces de rede, o nome do host é mapeado para o endereço IP privado da interface de rede principal.

Máquinas virtuais configuradas com servidores DNS gerenciados do Azure serão capazes de resolver os nomes de host de todas as VMs em sua rede virtual para seus endereços IP privados.

### Balanceadores de carga internos (ILB) e gateways de aplicativo
Você pode atribuir um endereço IP privado para a configuração de **front-end** de um [balanceador de carga interno do Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou um [Azure Application Gateway](../application-gateway/application-gateway-introduction.md). Esse endereço IP privado serve como ponto de extremidade interno, acessível somente aos recursos dentro da sua rede virtual (VNet) e de redes remotas conectadas à VNet. Você pode atribuir a um endereço IP privado dinâmico ou estático à configuração de front-end.

### Imediato
A tabela a seguir mostra a propriedade específica por meio da qual um endereço IP privado pode ser associado a um recurso de nível superior e os métodos possíveis de alocação (dinâmico ou estático) que podem ser usados.

|Recurso de nível superior|Associação de endereço IP|Dinâmico|Estático|
|---|---|---|---|
|Máquina virtual|Interface de rede|Sim|Sim|
|Balanceador de carga|Configuração de front-end|Sim|Sim|
|Application Gateway|Configuração de front-end|Sim|Sim|

## Limites

Os limites impostos sobre o endereçamento IP são indicados em todo o conjunto de [limites de rede](azure-subscription-service-limits.md#networking-limits) no Azure. Esses limites são por região e por assinatura. Você pode [entrar em contato com o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar os limites padrão até os limites máximos com base nas necessidades de sua empresa.

## Preços

Na maioria dos casos, endereços IP públicos são gratuitos. Há um custo nominal para usar endereços IP públicos adicionais e/ou estáticos. Verifique se você entende a [estrutura de preços para IPs públicos](https://azure.microsoft.com/pricing/details/ip-addresses/).

## Próximas etapas
- [Implantar uma VM com um IP público estático usando o Portal do Azure](virtual-network-deploy-static-pip-arm-portal.md)
- [Implantar uma VM com um IP público estático usando um modelo](virtual-network-deploy-static-pip-arm-template.md)
- [Implantar uma VM com um endereço IP privado estático](virtual-networks-static-private-ip-arm-pportal.md) usando o Portal do Azure

<!---HONumber=AcomDC_0810_2016-->