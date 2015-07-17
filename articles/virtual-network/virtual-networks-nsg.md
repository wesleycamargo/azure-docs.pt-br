<properties 
   pageTitle="O que é um NSG (grupo de segurança de rede)?"
   description="Saiba mais sobre grupos de segurança de rede"
   services="traffic-manager"
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

# O que é um NSG (grupo de segurança de rede)?

Você pode usar um NSG para controlar o tráfego em uma ou mais instâncias de máquina virtual (VM) em sua rede virtual. Um grupo de segurança de rede é um objeto de nível superior que está associado à sua assinatura. Um NSG contém regras de controle de acesso que permitem ou negam o tráfego para instâncias de VM. As regras de um NSG podem ser alteradas a qualquer momento e as alterações se aplicam a todas as instâncias associadas. Para usar um NSG, você deve ter uma VNet associada a uma região (local).

>[AZURE.WARNING]Os NSGs não são compatíveis com VNets que estão associadas a um grupo de afinidades. Se você não tiver uma VNet regional e quiser controlar o tráfego para os pontos de extremidade, confira [O que é uma lista de controle de acesso (ACL) de rede?](../virtual-networks-acl).

Você pode associar um NSG a uma máquina virtual ou a uma sub-rede em uma VNet. Quando associado a uma máquina virtual, o NSG se aplica a todo o tráfego que é enviado e recebido pela instância de VM. Quando aplicado a uma sub-rede em sua VNet, ele se aplica a todo o tráfego que é enviado e recebido por todas as instâncias de VM na sub-rede. Uma VM ou sub-rede pode ser associada a apenas um NSG e cada NSG pode conter até 200 regras. Você pode ter 100 NSGs por assinatura.

>[AZURE.NOTE]Não há suporte para grupos de segurança de rede e ACLs baseadas em ponto de extremidade na mesma instância de VM. Se você quiser usar um NSG e já tiver uma ACL de ponto de extremidade à disposição, primeiro remova a ACL de ponto de extremidade. Para saber mais sobre como fazer isso, confira [Gerenciando listas de controle de acesso (ACLs) para pontos de extremidade usando o PowerShell](https://msdn.microsoft.com/library/azure/dn376543.aspx).

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
| PERMITIR A ENTRADA DA VNET | 65000 | REDE_VIRTUAL | * | REDE_VIRTUAL | * | * | PERMITIR |
| PERMITIR A ENTRADA DO BALANCEADOR DE CARGA DO AZURE | 65001 | BALANCEADORDECARGA_AZURE | * | * | * | * | PERMITIR |
| NEGAR TODAS AS ENTRADAS | 65500 | * | * | * | * | * | NEGAR |

**Saída**

| Nome | Prioridade | IP de origem | Porta de origem | IP de destino | Porta de destino | Protocolo | Access |
|-------------------------|----------|-----------------|-------------|-----------------|------------------|----------|--------|
| PERMITIR SAÍDA DA VNET | 65000 | REDE_VIRTUAL | * | REDE_VIRTUAL | * | * | PERMITIR |
| PERMITIR SAÍDA DA INTERNET | 65001 | * | * | INTERNET | * | * | PERMITIR |
| NEGAR TODAS AS SAÍDAS | 65500 | * | * | * | * | * | NEGAR |

### Regras especiais de infraestrutura

As regras NSG são explícitas. Nenhum tráfego é permitido ou negado além do que é especificado nas regras de NSG. No entanto, há dois tipos de tráfego sempre permitidos, independentemente da especificação do grupo de segurança de rede. Estas provisões são feitas para dar suporte à infraestrutura.

- **IP virtual do nó do host:** serviços básicos de infraestrutura, como DHCP, DNS e integridade de monitoramento, são fornecidos pelo endereço IP virtualizado host 168.63.129.16. Este endereço IP público pertence à Microsoft e será o único endereço IP virtualizado usado em todas as regiões para essa finalidade. Esse endereço IP é mapeado para o endereço IP físico da máquina do servidor (nó do host) que hospeda a máquina virtual. O nó do host atua como a retransmissão DHCP, o solucionador de DNS recursivo e a fonte de sonda para a investigação de integridade do balanceador de carga e a investigação de integridade da máquina. A comunicação com esse endereço IP não deve ser considerada como um ataque.

- **Licenciamento (Serviço de Gerenciamento de Chaves):** as imagens do Windows em execução nas máquinas virtuais devem ser licenciadas. Para fazer isso, uma solicitação de licenciamento é enviada para os servidores de host do serviço de gerenciamento de chaves que lidar com essas consultas. Isso sempre será na porta de saída 1688.

### Marcas padrão

Marcas padrão são identificadores fornecidos pelo sistema para atender a uma categoria de endereços IP. As marcas padrão podem ser especificadas nas regras definidas pelo cliente. As marcas padrão são os seguintes:

- **REDE_VIRTUAL:** essa marca padrão indica todo o espaço de endereço de rede. Ela inclui o espaço de endereço da rede virtual (CIDR de IP no Azure), bem como todo o espaço de endereço local conectado (redes locais). Isso também inclui espaços de endereço VNet para VNet .

- **BALANCEADORDECARGA_AZURE:** essa marca padrão denota o balanceador de carga de infraestrutura do Azure. Isso significa um IP de datacenter do Azure de onde se originarão as investigações de integridade do Azure. Ele somente é necessário se a VM ou um conjunto de máquinas virtuais associado ao NSG estiver participando de um conjunto de balanceamento de carga.

- **INTERNET:** essa marca padrão denota o espaço de endereço IP que está fora da rede virtual e acessível através da Internet pública. Esse intervalo também inclui o espaço de IP público de propriedade do Azure.

### Portas e intervalos de porta

As regras de grupo de segurança de rede podem ser especificadas em uma porta de origem/destino única ou em um intervalo de portas. Isso é especialmente útil quando você deseja abrir uma grande variedade de portas para um aplicativo, como FTP. O intervalo somente pode ser sequencial e não pode ser combinado com uma especificação de porta individual.

Para especificar um intervalo de portas, use o sinal '-', conforme mostrado abaixo no parâmetro *DestinationPortRange*:

	Get-AzureNetworkSecurityGroup -Name ApptierSG `
	| Set-AzureNetworkSecurityRule -Name FTP -Type Inbound -Priority 600 -Action Allow `
		-SourceAddressPrefix INTERNET -SourcePortRange * `
		-DestinationAddressPrefix * -DestinationPortRange 100-500 -Protocol *

### Tráfego ICMP

As atuais regras de NSG permitem apenas protocolos 'TCP' ou 'UDP'. Não há uma marca específica para 'ICMP'. No entanto, o tráfego com protocolo ICMP é permitido em uma rede virtual por padrão através das Regras de entrada VNet que permitem o tráfego de/para qualquer porta e protocolo ' *' na VNet.

## Associando NSGs

Associando um NSG a uma VM: quando um NSG está diretamente associado a uma máquina virtual, as regras de acesso de rede no NSG são aplicadas diretamente a todo o tráfego destinado à máquina virtual. Sempre que o NSG é atualizado por conta de alterações de regra, as alterações são refletidas no tratamento do tráfego dentro de minutos. Quando o NSG for desassociado da VM, o estado de volta para o que era antes do NSG, ou seja, os padrões do sistema antes da introdução do NSG, se usado.

Associando um NSG a uma sub-rede: quando um NSG é associado a uma sub-rede, as regras de acesso de rede no NSG são aplicadas a todas as VMs na sub-rede. Sempre que as regras de acesso no NSG são atualizadas, as alterações são aplicadas a todas as máquinas virtuais na sub-rede dentro de minutos.

Associando um NSG a uma sub-rede e a uma VM: é possível associar um NSG a uma máquina virtual e um NSG diferente à sub-rede onde reside a VM. Há suporte para isso e, nesse caso, a VM obtém duas camadas de proteção. No tráfego de Entrada, o pacote percorre as regras de acesso especificadas na sub-rede seguidas por regras na VM e, em caso de Saída, ele percorre as regras especificadas na VM primeiro e depois passa pelas regras especificadas na sub-rede, conforme ilustrado no diagrama a seguir.

![NSG ACLs](./media/virtual-networks-nsg/figure1.png)

Quando um NSG está associado uma VM ou sub-rede, as regras de controle de acesso de rede se tornam bastante explícitas. A plataforma não irá inserir regras implícitas para permitir o tráfego em uma porta específica. Nesse caso, se você criar um ponto de extremidade na VM, também precisa criar uma regra para permitir o tráfego da Internet. Se você não fizer isso, o VIP:<Port> não estará acessível de fora.

Por exemplo: você cria uma nova VM e também cria um novo NSG. Em seguida, associa o NSG à máquina virtual. A máquina virtual pode se comunicar com outras VMs na rede virtual por meio da regra PERMITIR ENTRADA DA VNET. A VM também pode fazer conexões de saída com a Internet usando a regra PERMITIR SAÍDA DA INTERNET. Posteriormente, você cria um ponto de extremidade na porta 80 para receber o tráfego para o site em execução na VM. Os pacotes destinados à porta 80 no VIP (endereço IP virtual público) da Internet não chegarão a VM até que você adicione uma regra semelhante à regra abaixo para o NSG.

| Nome | Prioridade | IP de origem | Porta de origem | IP de destino | Porta de destino | Protocolo | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
| WEB | 100 | INTERNET | * | * | 80 | TCP | PERMITIR |


## Planejamento: fluxo de trabalho de grupo de segurança de rede

Aqui estão as etapas do fluxo de trabalho básico para usar grupos de segurança de rede.

### Fluxo de trabalho: criar e associar um NSG

1. Crie um NSG (grupo de segurança de rede)

1. Adicione regras de segurança de rede, a menos que as regras padrão sejam suficientes.

1. Associe um NSG a uma máquina virtual.

1. Atualize a máquina virtual.

1. Após a atualização, as regras de NSG entrarão em vigor imediatamente.

### Fluxo de trabalho: atualizar um NSG existente

1. Adicione, exclua ou atualize uma regra em uma NSG existente.

1. Todas as máquinas virtuais associadas ao NSG obterão as atualizações em minutos. Uma atualização de VM não é necessária quando a regra do NSG já estiver associada à VM.

### Fluxo de trabalho: alterando uma associação de NSG

1. Associe um novo NSG a uma máquina virtual que já está associada a outro NSG.

1. Atualize a máquina virtual.

1. As regras do NSG novo entrarão em vigor em minutos.

## Como criar, configurar e gerenciar seus grupos de segurança de rede

Nesse momento, os NSGs somente podem ser configurados e modificados usando cmdlets do PowerShell e APIs REST. Você não pode configurar NSGs usando o Portal de Gerenciamento. Os cmdlets do PowerShell abaixo o ajudarão a criar, a configurar e a gerenciar seus NSGs.

**Criar um grupo de segurança de rede**

	New-AzureNetworkSecurityGroup -Name "MyVNetSG" -Location uswest `
		-Label "Security group for my Vnet in West US"

**Adicionar ou atualizar regras**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityRule -Name WEB -Type Inbound -Priority 100 `
		-Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' `
		-DestinationAddressPrefix '*' -DestinationPortRange '*' -Protocol TCP


**Excluir uma regra de um NSG**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityRule -Name WEB

**Associar um NSG a uma máquina virtual**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Set-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**Remover um NSG de uma VM**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Remove-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**Associar um NSG a uma sub-rede**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**Remover um NSG da sub-rede**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**Excluir um NSG**

	Remove-AzureNetworkSecurityGroup -Name "MyVNetSG"

**Obter os detalhes de um NSG e suas regras**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" -Detailed
 

<!---HONumber=62-->