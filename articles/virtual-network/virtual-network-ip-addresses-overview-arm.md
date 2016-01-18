<properties
   pageTitle="Endereçamento IP público e privado no Gerenciador de Recursos do Azure | Microsoft Azure"
   description="Saiba mais sobre endereçamento IP público e privado no Gerenciador de Recursos do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/23/2015"
   ms.author="telmos" />

# Endereços IP no Azure
Você pode atribuir endereços IP aos recursos do Azure para se comunicar com outros recursos do Azure, sua rede local e a Internet. Há dois tipos de endereços IP que você pode usar no Azure: público e privado.

Endereços IP públicos são usados para comunicação com a Internet, incluindo serviços públicos do Azure.

Endereços IP privados são usados para comunicação em uma VNet (rede virtual) do Azure e na sua rede local quando você usa um gateway de VPN ou circuito de Rota Expressa para estender sua rede para o Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-ip-addresses-overview-classic.md).

Se você estiver familiarizado com o modelo de implantação clássico, verifique as [diferenças de endereçamento IP entre o clássico e o Gerenciador de Recursos](virtual-network-ip-addresses-overview-classic.md#Differences-between-Resource-Manager-and-classic-deployments).

## Endereços IP públicos
Endereços IP públicos permitem que os recursos do Azure comuniquem-se com os serviços públicos do Azure, como [Cache Redis do Azure](https://azure.microsoft.com/services/cache), [Hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs), [bancos de dados SQL](sql-database-technical-overview.md) e [armazenamento do Azure](storage-introduction.md).

No Gerenciador de recursos do Azure, um endereço [IP público](resource-groups-networking.md#public-ip-address) é um recurso com as próprias propriedades. Você pode associar um recurso de endereço IP público a qualquer um dos seguintes recursos:

- VMs
- Balanceadores de carga para Internet
- Gateways VPN
- Application gateways

### Método de alocação
Há dois métodos de alocar um endereço IP para um *recurso IP público*: *dinâmico* ou *estático*. O método de alocação padrão é *dinâmico*, em que o endereço IP **não** é alocado no momento de sua criação. Em vez disso, o endereço IP público é alocado quando você inicia (ou cria) o recurso associado (como o balanceador de carga ou VM). O endereço IP é liberado quando você para (ou exclui) o recurso. Isso faz com que o endereço IP mude quando você interrompe e inicia um recurso.

Para garantir que o endereço IP para o recurso associado permaneça o mesmo, você pode definir o método de alocação explicitamente como *estático*. Nesse caso, um endereço IP é atribuído imediatamente. Ele é liberado apenas quando você exclui o recurso ou altera seu método de alocação para *dinâmico*.

>[AZURE.NOTE]Mesmo quando você define o método de alocação para *estático*, não é possível especificar o endereço IP real atribuído ao *recurso IP público*. Em vez disso, ele é alocado de um pool de endereços IP disponíveis no local do Azure em que o recurso é criado.

Os endereços IP públicos estáticos são comumente usados nas seguintes situações:

- os usuários finais precisam atualizar regras de firewall para comunicarem-se com os recursos do Azure.
- Resolução de nome DNS, em que uma alteração no endereço IP exigiria a atualização de registros A.
- seus recursos do Azure comunicam-se com outros aplicativos ou serviços que usam um endereço IP baseado em um modelo de segurança.
- você usa certificados SSL vinculados a um endereço IP.

>[AZURE.NOTE]A lista de intervalos IP do qual os endereços IP públicos (dinâmicos ou estáticos) são alocados a recursos do Azure é publicada a [intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Resolução de nome de host DNS
Você pode especificar um rótulo de nome de domínio DNS para um recurso IP público, que cria um mapeamento para *domainnamelabel*.*location*.cloudapp.azure.com para o endereço IP público nos servidores DNS gerenciados pelo Azure. Por exemplo, se você criar um recurso IP público com **contoso** como *domainnamelabel* no **local** do Azure *Oeste dos EUA*, o nome de domínio totalmente qualificado (FQDN) **contoso.westus.cloudapp.azure.com** será resolvido para o endereço IP público do recurso. Você pode usar o FQDN para criar um registro CNAME do domínio personalizado apontando para o endereço IP público no Azure.

>[AZURE.IMPORTANT]Cada rótulo de nome do domínio criado deve ser exclusivo dentro de seu local do Azure.

### VMs
Você pode associar um endereço IP público a uma VM ([Máquina Virtual](virtual-machines-about.md)) atribuindo-o à sua NIC (**placa de interface de rede**). No caso de uma VM de várias NICs, você pode atribuí-la apenas para a NIC *primária*. Você pode atribuir um endereço IP público dinâmico ou estático a uma VM.

### Balanceadores de carga para Internet
Você pode associar um endereço IP público com um [balanceador de carga](load-balancer-overview.md), atribuindo-o à configuração de **front-end** do balanceador de carga. Este endereço IP público serve como um balanceamento de carga de endereço VIP (IP virtual). Você pode atribuir um endereço IP público estático ou dinâmico a um front-end de balanceador de carga. Você também pode atribuir vários endereços IP públicos a um front-end de balanceador de carga, que permite cenários [multi-vip](load-balancer-multivip.md) como um ambiente de multilocatário com sites baseados em SSL.

### Gateways VPN
O [Gateway de VPN do Azure](vpn-gateway-about-vpngateways.md) é usado para conectar uma rede virtual do Azure (VNet) a outras VNets do Azure ou rede local. É necessário atribuir um endereço IP público à sua **configuração IP** para permitir a comunicação com a rede remota. No momento, você pode atribuir apenas um endereço IP público dinâmico a um gateway de VPN.

### Application gateways
Você pode associar um endereço IP público do Azure [Application Gateway](application-gateway-introduction.md), atribuindo-o à configuração de **front-end** do gateway. Esse endereço IP público serve como um VIP com balanceamento de carga. No momento, você pode atribuir apenas um endereço IP público *dinâmico* a uma configuração de front-end de do Application Gateway. Você também pode atribuir vários endereços IP públicos, o que permite cenários multiVIP.

### Imediato
A tabela a seguir mostra cada tipo de recurso com os métodos de alocação possíveis (dinâmico/estático) e capacidade de atribuir vários endereços IP públicos.

|Recurso|Dinâmico|Estático|Vários endereços IP|
|---|---|---|---|
|Placa de Interface de rede (NIC) (de uma VM)|Sim|Sim|Não|
|Front-end do balanceador de carga|Sim|Sim|Sim|
|Gateway de VPN|Sim|Não|Não|
|Front-end do Application Gateway|Sim|Não|Não|

## Endereços IP privados
Endereços IP privados permitem que os recursos do Azure comuniquem-se com outros recursos de uma VNet ([rede virtual](virtual-networks-overview.md)), ou na rede local por meio de um gateway de VPN ou circuito de Rota Expressa, sem usar um endereço IP acessível pela Internet.

No modelo de implantação do Gerenciador de Recursos do Azure, um endereço IP privado é associado a vários recursos do Azure.

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

### VMs
Um endereço IP privado é atribuído à NIC (**placa de interface de rede**) de uma [máquina virtual](virtual-machines-about.md). No caso de uma VM de várias NICs, é atribuído um endereço IP privado a cada NIC. Você pode especificar o método de alocação como dinâmico ou estático para uma NIC.

#### Resolução do nome do host DNS interno (para VMs)
Todas as VMs do Azure são configuradas com [servidores DNS gerenciados Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) por padrão, a menos que você explicitamente configure servidores DNS personalizados. Esses servidores DNS fornecem resolução de nomes interna para VMs que residem na mesma VNet.

Quando você cria uma VM, um mapeamento para o nome de host para seu endereço IP privado é adicionado aos servidores DNS gerenciados pelo Azure. No caso de uma VM com várias NICs, o nome do host é mapeado para o endereço IP privado da NIC principal.

Máquinas virtuais configuradas com servidores DNS gerenciados do Azure serão capazes de resolver os nomes de host de todas as VMs em sua rede virtual para seus endereços IP privados.

### Balanceadores de carga internos (ILB) e gateways de aplicativo
Você pode atribuir um endereço IP privado para a configuração de **front-end** de um [balanceador de carga interno do Azure](load-balancer-internal-overview.md) (ILB) ou um [Azure Application Gateway](application-gateway-introduction.md). Esse endereço IP privado serve como ponto de extremidade interno, acessível somente aos recursos dentro da sua rede virtual (VNet) e de redes remotas conectadas à VNet. Você pode atribuir a um endereço IP privado dinâmico ou estático à configuração de front-end. Você também pode atribuir vários endereços IP privados para habilitar cenários de multi-vip.

### Imediato
A tabela a seguir mostra cada tipo de recurso com os métodos de alocação possíveis (dinâmico/estático) e capacidade de atribuir vários endereços IP privados.

|Recurso|Estático|Dinâmico|Vários endereços IP|
|---|---|---|---|
|VM (máquina virtual)/NIC (placa de interface de rede)|Sim|Sim|Sim|
|Front-end do balanceador de carga interno|Sim|Sim|Sim|
|Front-end do Application Gateway|Sim|Sim|Sim|

## Limites

A tabela abaixo mostra os limites impostos ao endereçamento IP no Azure por região, por assinatura. Você pode [entrar em contato com o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar os limites padrão até os limites máximos com base nas necessidades de sua empresa.

||Limite padrão|Limite máximo| |---|---|---| |Endereços IP públicos (dinâmicos)|60|entrar em contato com o suporte| |Endereços IP públicos (estáticos)|20|entrar em contato com o suporte| |IP de front-end público por balanceador de carga|5|entrar em contato com o suporte| |IP de front-end privado por balanceador de carga|1|entrar em contato com o suporte|

Certifique-se de ler o conjunto completo de [limites de rede](azure-subscription-service-limits.md#networking-limits) do Azure.

## Preços

Na maioria dos casos, endereços IP públicos são gratuitos. Há um custo nominal para usar endereços IP públicos adicionais e/ou estáticos. Verifique se você entende a [estrutura de preços para IPs públicos](https://azure.microsoft.com/pricing/details/ip-addresses/).

Em resumo, a estrutura de preços a seguir se aplica a recursos IP públicos:

- Gateways de VPN e gateways de aplicativo usam apenas um IP público dinâmico, que é gratuito.
- As VMs usam apenas um IP público, que é gratuito desde que seja um endereço IP dinâmico. Se uma VM usar um endereço IP público estático, ele é contabilizado no uso de IP de público estático (reservado).
- Cada balanceador de carga pode usar vários IPs públicos. O primeiro IP público é gratuito. IPs dinâmicos adicionais são cobradas a US$ 0,004/hora. IPs públicos estáticos são contabilizados no uso de IP de público estático (reservado).
- Uso de IP público estático (reservado): 
	- Os primeiros 5 (em uso) são gratuitos. IPs públicos estáticos adicionais são cobradas a US$ 0,004/hora. 
	- IPs públicos estáticos não atribuídos a qualquer recurso são cobradas US$ 0,004/hora.
	- O uso é calculado com base no número total de IPs públicos estáticos na assinatura.

## Próximas etapas
- [Implantar uma VM com um endereço IP público estático](virtual-network-deploy-static-pip-arm-template.md)
- [Criar um endereço IP público para a um balanceador de carga de Internet usando a CLI do Azure](load-balancer-get-started-internet-arm-cli.md#create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool)
- [Criar um endereço IP público para um application gateway usando PowerShell](application-gateway-create-gateway-arm.md#create-public-ip-address-for-front-end-configuration)
- [Criar um endereço IP público para um gateway de VPN usando PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md#4-request-a-public-ip-address-for-the-gateway)
- [Implantar uma VM com um endereço IP privado estático](virtual-networks-static-private-ip-arm-pportal.md)
- [Criar um front-end privado endereço IP estático para um balanceador de carga interno usando PowerShell](load-balancer-get-started-ilb-arm-ps.md#create-front-end-ip-pool-and-backend-address-pool)
- [Criar um pool de back-end com endereços IP estáticos privados para um application gateway usando o PowerShell](application-gateway-create-gateway-arm.md#create-an-application-gateway-configuration-object)

<!---HONumber=AcomDC_0107_2016-->