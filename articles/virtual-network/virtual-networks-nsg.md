<properties 
   pageTitle="O que é um NSG (grupo de segurança de rede)?"
   description="Saiba mais sobre grupos de segurança de rede"
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
   ms.date="09/22/2015"
   ms.author="telmos" />

# O que é um NSG (grupo de segurança de rede)?

Você pode usar um NSG para controlar o tráfego em uma ou mais instâncias de máquina virtual (VM) em sua rede virtual. Um NSG contém regras de controle de acesso que permitem ou negam o tráfego com base na direção do tráfego, no protocolo, no endereço e porta de origem e no endereço e porta de destino. As regras de um NSG podem ser alteradas a qualquer momento e as alterações se aplicam a todas as instâncias associadas.

>[AZURE.WARNING]NSGs só podem ser usados em VNets regionais. Se estiver tentando proteger pontos de extremidade em uma implantação sem uma VNet ou que usa uma VNet associada a um grupo de afinidades, confira [O que é uma ACL (Lista de Controle de Acesso) do ponto de extremidade?](./virtual-networks-acl.md). Você também pode [migrar sua VNet para uma VNet regional](./virtual-networks-migrate-to-regional-vnet.md).

![NSGs](./media/virtual-network-nsg-overview/figure1.png)

A figura acima mostra uma rede virtual com duas sub-redes, com um NSG associado a cada sub-rede para o controle de tráfego.

>[AZURE.NOTE]Não há suporte para grupos de segurança de rede e ACLs baseadas em ponto de extremidade na mesma instância de VM. Se você quiser usar um NSG e já tiver uma ACL de ponto de extremidade à disposição, primeiro remova a ACL de ponto de extremidade. Para saber mais sobre como fazer isso, confira [Gerenciando listas de controle de acesso (ACLs) para pontos de extremidade usando o PowerShell](virtual-networks-acl-powershell.md).

## Como funciona um grupo de segurança de rede?

Os grupos de segurança de rede são diferentes das ACLs baseadas em ponto de extremidade. As ACLs de ponto de extremidade funcionam apenas na porta pública que fica exposta através do ponto de extremidade de entrada. Um NSG funciona em uma ou mais instâncias de VM e controla todo o tráfego de entrada e saída na VM.

Um grupo de segurança de rede tem um *nome*, está associado a um *região* e tem um rótulo descritivo. Ele contém dois tipos de regras: **entrada** e **saída**. As Regras de entrada são aplicadas aos pacotes de entrada de uma máquina virtual e as Regras de saída são aplicadas a pacotes de saída de uma VM. As regras são aplicadas no host onde a VM está localizada. Um pacote de entrada ou de saída tem que corresponder a uma regra **Permitir** para ser aceito; do contrário, será descartado.

As regras são processadas na ordem de prioridade. Por exemplo, uma regra com um número de prioridade mais baixo (por exemplo, 100) é processada antes das regras com números de prioridade mais altos (por exemplo, 200). Quando uma correspondência é encontrada, nenhuma outra regra é processada.

Uma regra especifica o seguinte:

- **Nome:** um identificador exclusivo para a regra

- **Tipo:** Entrada/Saída

- **Prioridade:** <You can specify an integer between 100 and 4096>

- **Endereço IP de origem:** CIDR do intervalo de IP de origem

- **Intervalo de porta de origem:** <integer or range between 0 and 65536>

- **Intervalo de IP de destino:** CIDR do intervalo de IP de destino

- **Intervalo de porta de destino:** <integer or range between 0 and 65536>

- **Protocolo:** <TCP, UDP ou ' *' são permitidos>

- **Acesso:** Permitir/Negar

### Regras padrão

Um NSG contém regras padrão. As regras padrão não podem ser excluídas, mas como recebem a prioridade mais baixa, elas podem ser substituídas pelas regras que você criar. As regras padrão descrevem as configurações padrão recomendadas pela plataforma. Como ilustrado pelas regras padrão abaixo, o tráfego que começa e termina em uma VNet é permitido tanto na Entrada quanto na Saída.

Enquanto a conectividade com a Internet é permitida na Saída, ela é por padrão bloqueada na Entrada. Há uma regra padrão para permitir que o BL (balanceador de carga) do Azure investigue a integridade da VM. Você pode substituir essa regra se a VM ou o conjunto de VMs no NSG não participar do conjunto de balanceamento de carga.

As regras padrão são:

**Entrada**

| Nome | Prioridade | IP de origem | Porta de origem | IP de destino | Porta de destino | Protocolo | Access |
|-----------------------------------|----------|--------------------|-------------|-----------------|------------------|----------|--------|
| PERMITIR A ENTRADA DA VNET | 65000 | REDE\_VIRTUAL | * | REDE\_VIRTUAL | * | * | PERMITIR |
| PERMITIR A ENTRADA DO BALANCEADOR DE CARGA DO AZURE | 65001 | BALANCEADORDECARGA\_AZURE | * | * | * | * | PERMITIR |
| NEGAR TODAS AS ENTRADAS | 65500 | * | * | * | * | * | NEGAR |

**Saída**

| Nome | Prioridade | IP de origem | Porta de origem | IP de destino | Porta de destino | Protocolo | Access |
|-------------------------|----------|-----------------|-------------|-----------------|------------------|----------|--------|
| PERMITIR SAÍDA DA VNET | 65000 | REDE\_VIRTUAL | * | REDE\_VIRTUAL | * | * | PERMITIR |
| PERMITIR SAÍDA DA INTERNET | 65001 | * | * | INTERNET | * | * | PERMITIR |
| NEGAR TODAS AS SAÍDAS | 65500 | * | * | * | * | * | NEGAR |

### Marcas padrão

Marcas padrão são identificadores fornecidos pelo sistema para atender a uma categoria de endereços IP. As marcas padrão podem ser especificadas nas regras definidas pelo cliente. As marcas padrão são os seguintes:

- **REDE\_VIRTUAL:** essa marca padrão indica todo o espaço de endereço de rede. Ela inclui o espaço de endereço da rede virtual (CIDR de IP no Azure), bem como todo o espaço de endereço local conectado (redes locais). Isso também inclui espaços de endereço VNet para VNet .

- **BALANCEADORDECARGA\_AZURE:** essa marca padrão denota o balanceador de carga de infraestrutura do Azure. Isso significa um IP de datacenter do Azure de onde se originarão as investigações de integridade do Azure. Ele somente é necessário se a VM ou um conjunto de máquinas virtuais associado ao NSG estiver participando de um conjunto de balanceamento de carga.

- **INTERNET:** essa marca padrão denota o espaço de endereço IP que está fora da rede virtual e acessível através da Internet pública. Esse intervalo também inclui o espaço de IP público de propriedade do Azure.

### Tráfego ICMP

As atuais regras do NSG permitem apenas protocolos *TCP* ou *UDP*. Não há uma marca específica para *ICMP*. No entanto, o tráfego ICMP é permitido em uma Rede Virtual por padrão por meio das regras da VNet de entrada que permitem o tráfego de/para qualquer porta e protocolo na VNet.

## Associando NSGs

É possível associar um NSG a VMs, a NICs e a sub-redes.

- **Associando um NSG a uma VM.** Quando um NSG é associado a uma VM, as regras de acesso à rede no NSG as regras são aplicadas a todo o tráfego de entrada e saída na VM. 

- **Associando um NSG a uma NIC.** Quando um NSG é associado a uma NIC, as regras de acesso à rede no NSG são aplicadas somente a essa NIC. Isso significa que em uma VM com várias NICs, se um NSG for aplicado a uma única NIC, ele não afetará o tráfego associado a outras NICs.

- **Associando um NSG a uma sub-rede**. Quando um NSG é associado a uma sub-rede, as regras de acesso à rede no NSG são aplicadas a todas as VMs na sub-rede.

É possível associar NSGs diferentes a uma VM, a uma NIC usada pela VM e à sub-rede à qual a NIC está associada. Quando isso acontece, todas as regras de acesso à rede são aplicadas ao tráfego na seguinte ordem:

- **Tráfego de entrada**
	1. NSG da sub-rede.
	2. NSG da NIC.
	3. NSG da VM.
- **Tráfego de saída**
	1. NSG da VM.
	2. NSG da NIC.
	3. NSG da sub-rede.

![ACLs de NSG](./media/virtual-network-nsg-overview/figure2.png)

>[AZURE.NOTE]Embora seja possível associar apenas um único NSG a uma sub-rede, VM ou NIC, é possível associar o mesmo NSG a quantos recursos você desejar.

## Considerações sobre o design

Você deve entender como as VMs se comunicam com os serviços de infraestrutura e com os serviços de PaaS hospedados pelo Azure ao projetar seus NSGs. A maioria dos serviços de PaaS do Azure, como bancos de dados SQL e armazenamento, só pode ser acessada por meio de um endereço de Internet pública voltado para o público. O mesmo vale para testes de balanceamento de carga.

Um cenário comum no Azure é a segregação de funções de VMs e PaaS em sub-redes com base em se esses objetos são necessários ou não para acessar a Internet. Em tal cenário, você pode ter uma sub-rede com máquinas virtuais ou instâncias de função que exigem acesso aos serviços PaaS do Azure, como bancos de dados SQL e armazenamento, mas que não exigem qualquer comunicação de entrada ou saída com a Internet pública.

Imagine a seguinte regra NSG para esse cenário:

| Nome | Prioridade | IP de origem | Porta de origem | IP de destino | Porta de destino | Protocolo | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|NO INTERNET|100| REDE\_VIRTUAL|&#42;|INTERNET|&#42;|TCP|NEGAR| 

Como a regra está negando todos os acessos da rede virtual à Internet, as VMs não poderão acessar qualquer serviço de PaaS do Azure que requeira um ponto de extremidade de Internet público, como bancos de dados SQL.

Em vez de usar uma regra de negação, considere o uso de uma regra para permitir o acesso da rede virtual à Internet, mas negue o acesso da Internet à rede virtual, conforme mostrado abaixo:

| Nome | Prioridade | IP de origem | Porta de origem | IP de destino | Porta de destino | Protocolo | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|TO INTERNET|100| REDE\_VIRTUAL|&#42;|INTERNET|&#42;|TCP|PERMITIR|
|FROM INTERNET|110| INTERNET|&#42;|REDE\_VIRTUAL|&#42;|TCP|NEGAR| 

>[AZURE.WARNING]O Azure usa uma sub-rede especial conhecida como sub-rede de **Gateway** para manipular o gateway de VPN para outras VNets e redes locais. A associação de um NSG a essa sub-rede fará com que o gateway de VPN pare de funcionar como esperado. NÃO associe NSGs a sub-redes de gateway!

Você também precisa levar em conta as regras especiais listadas abaixo. Certifique-se de não bloquear o tráfego permitido por essas regras, caso contrário sua infraestrutura não poderá se comunicar com os serviços essenciais do Azure.

- **IP virtual do nó do host:** serviços básicos de infraestrutura, como DHCP, DNS e integridade de monitoramento, são fornecidos pelo endereço IP virtualizado host 168.63.129.16. Este endereço IP público pertence à Microsoft e será o único endereço IP virtualizado usado em todas as regiões para essa finalidade. Esse endereço IP é mapeado para o endereço IP físico da máquina do servidor (nó do host) que hospeda a máquina virtual. O nó do host atua como a retransmissão DHCP, o solucionador de DNS recursivo e a fonte de sonda para a investigação de integridade do balanceador de carga e a investigação de integridade da máquina. A comunicação com esse endereço IP não deve ser considerada como um ataque.

- **Licenciamento (Serviço de Gerenciamento de Chaves):** as imagens do Windows em execução nas máquinas virtuais devem ser licenciadas. Para fazer isso, uma solicitação de licenciamento é enviada para os servidores de host do serviço de gerenciamento de chaves que lidar com essas consultas. Isso sempre será na porta de saída 1688.

## Limites

Você precisa considerar os limites abaixo ao projetar seus NSGs.

|**Descrição**|**Limite**|
|---|---|
|Número de NSGs que podem ser associados a uma sub-rede, VM ou NIC|1|
|NSGs por região e assinatura|100|
|Regras de NSG por NSG|200|

Lembre-se de exibir todos os [limites relacionados aos serviços de rede no Azure](../azure-subscription-service-limits/#networking-limits) antes de projetar sua solução.

## Próximas etapas

- [Implantar NSGs no modelo de implantação clássico](virtual-networks-create-nsg-classic-ps.md).
- [Implantar NSGs no Gerenciador de Recursos](virtual-networks-create-nsg-arm-pportal.md).

<!---HONumber=Oct15_HO1-->