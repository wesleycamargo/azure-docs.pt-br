---
title: "Visão geral de segurança de rede do Azure | Microsoft Docs"
description: "Saiba mais sobre opções de segurança para controlar o fluxo do tráfego de rede entre recursos do Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: jdial
ms.openlocfilehash: 89a4a165690c7a308d5bc7222b8a5a9716d22785
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2018
---
# <a name="network-security"></a>Segurança de rede

Você pode limitar o tráfego de rede para recursos em uma rede virtual usando um grupo de segurança de rede. Um grupo de segurança de rede contém uma lista de regras de segurança que permitem ou negam o tráfego de rede de entrada ou saída com base no endereço IP de origem ou destino, na porta e no protocolo. 

## <a name="network-security-groups"></a>Grupos de segurança de rede

Cada interface de rede não tem nenhum ou tem um grupo de segurança de rede associado. Cada interface de rede existe em uma sub-rede de [rede virtual](virtual-networks-overview.md). Uma sub-rede também pode não ter nenhum ou ter um grupo de segurança de rede associado. 

Quando aplicadas a uma sub-rede, as regras de segurança são aplicadas a todos os recursos na sub-rede. Além dos adaptadores de rede, você pode ter instâncias de outros serviços do Azure, como o HDInsight, Conjuntos de Dimensionamento de Máquinas Virtuais e Ambientes de Serviço de Aplicativo, implantados na sub-rede.

Abaixo mostramos como os grupos de segurança de rede são aplicados quando um grupo de segurança de rede está associado a um adaptador de rede e em que sub-rede está o adaptador de rede:

- **Tráfego de entrada**: o grupo de segurança de rede associado à sub-rede onde está o adaptador de rede é avaliado primeiro. O tráfego permitido pelo grupo de segurança de rede associado à sub-rede é avaliado pelo grupo de segurança de rede associado ao adaptador de rede. Por exemplo, você pode exigir acesso de entrada a uma máquina virtual pela porta 80 da Internet. Se você associar um grupo de segurança de rede ao adaptador de rede e à sub-rede onde está o adaptador de rede, o grupo de segurança de rede associado à sub-rede e o adaptador de rede devem permitir a porta 80. Se você apenas permitiu a porta 80 por meio do grupo de segurança de rede associado à sub-rede ou ao adaptador de rede em que está a sub-rede, a comunicação falhará devido a regras de segurança padrão. Confira as [regras de segurança padrão](#default-security-rules) para obter mais detalhes. Se você aplicou um grupo de segurança de rede apenas à sub-rede ou ao adaptador de rede, e o grupo de segurança e rede continha uma regra que permitia o tráfego de entrada da porta 80, por exemplo, a comunicação será bem-sucedida. 
- **Tráfego de saída**: o grupo de segurança de rede associado ao adaptador de rede é avaliado primeiro. O tráfego permitido pelo grupo de segurança de rede associado ao adaptador de rede é avaliado pelo grupo de segurança de rede associado à sub-rede.

Você pode não saber quando os grupos de segurança de rede são aplicados a um adaptador de rede e a uma sub-rede. É possível exibir facilmente as regras de agregação aplicadas a um adaptador de rede exibindo as [regras de segurança efetiva](virtual-network-manage-nsg-arm-portal.md) para determinado adaptador de rede. Você também pode usar o recurso [Verificar o fluxo de IP](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) no Observador de Rede do Azure para determinar se a comunicação é permitida na entrada ou saída de um adaptador de rede. A ferramenta informa se a comunicação é permitida e qual regra de segurança de rede permite ou nega o tráfego.
 
> [!NOTE]
> Os grupos de segurança de rede estão associados a sub-redes ou a máquinas virtuais e serviços de nuvem com o modelo de implantação clássica, em vez de associados a adaptadores de rede no modelo de implantação do Gerenciador de Recursos. Para saber mais sobre os modelos de implantação do Azure, confira [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

O mesmo grupo de segurança de rede poderá ser aplicado a quantos adaptadores de rede e sub-redes individuais você desejar.

## <a name="security-rules"></a>Regras de segurança

Um grupo de segurança de rede pode conter nenhuma ou quantas regras você desejar, dentro dos [limites](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) da assinatura do Azure. Cada regra especifica as seguintes propriedades:

|Propriedade  |Explicação  |
|---------|---------|
|NOME|Um nome exclusivo dentro do grupo de segurança de rede.|
|Prioridade | Um número entre 100 e 4096. As regras são processadas na ordem de prioridade, com números mais baixos processados antes de números mais altos, pois os números mais baixos têm prioridade mais alta. Depois que o tráfego corresponde a uma regra, o processamento é interrompido. Assim, as regras existentes com baixa prioridade (números mais altos) que têm os mesmos atributos das regras com prioridades mais altas não são processadas.|
|Origem ou destino| Qualquer um: um endereço IP individual, um bloco CIDR (10.0.0.0/24, por exemplo), uma marca de serviço ou um grupo de segurança do aplicativo. Saiba mais sobre [marcas de serviço](#service-tags) e [grupos de segurança de aplicativo](#application-security-groups). A especificação de um intervalo, uma etiqueta de serviço ou um grupo de segurança de aplicativos permite que você crie menos regras de segurança. A capacidade de especificar vários endereços IP individuais e intervalos (você não pode especificar várias marcas de serviço ou grupos de aplicativos) em uma regra é conhecida como regras de segurança aumentadas. Saiba mais sobre [regras de segurança aumentadas](#augmented-security-rules). As regras de segurança aumentadas só podem ser criadas em grupos de segurança de rede criados pelo modelo de implantação do Gerenciador de Recursos. Você não pode especificar vários endereços IP e intervalos de endereços IP em grupos de segurança de rede criados pelo modelo de implantação clássica.|
|Protocolo     | TCP, UDP ou Qualquer um, o que inclui TCP, UDP e ICMP. Você não pode especificar o ICMP sozinho e, portanto, se precisar de ICMP, use a opção Qualquer um. |
|Direção| Se a regra se aplica ao tráfego de entrada ou de saída.|
|Intervalo de portas     |Você pode especificar uma porta individual ou um intervalo de portas. Por exemplo, você pode especificar 80 ou 10000-10005. A especificação de intervalos permite que você crie menos regras de segurança. As regras de segurança aumentadas só podem ser criadas em grupos de segurança de rede criados pelo modelo de implantação do Gerenciador de Recursos. Você não pode especificar várias portas ou intervalos de porta na mesma regra de segurança em grupos de segurança de rede criados pelo modelo de implantação clássica.   |
|Ação     | Permitir ou negar        |

As regras de segurança são com estado. Se você especificar uma regra de segurança de saída para algum endereço pela porta 80, por exemplo, não será necessário especificar uma regra de segurança de entrada para a resposta ao tráfego de saída. Você precisa especificar uma regra de segurança de entrada se a comunicação for iniciada externamente. O oposto também é verdadeiro. Se o tráfego de entrada é permitido por uma porta, não é necessário especificar uma regra de segurança de saída para responder ao tráfego pela porta. Para saber mais sobre limites durante a criação de regras de segurança, confira [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Regras de segurança aumentadas

As regras aumentadas simplificam a definição de segurança para redes virtuais, permitindo que você defina políticas de segurança de rede maiores e mais complexas com menos regras. Você pode combinar várias portas, vários endereços IP explícitos, marcas de serviço e grupos de segurança de aplicativo em uma regra de segurança simples e fácil de entender. Use regras aumentadas nos campos de origem, destino e porta de uma regra. Ao criar uma regra, você pode especificar vários endereços IP, intervalos de CIDR e portas explícitos. Para simplificar a manutenção da sua definição de regra de segurança, combine as regras de segurança aumentadas com marcas de serviço ou grupos de segurança de aplicativo. 

Para saber mais sobre limites durante a criação de regras de segurança aumentadas, confira [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="default-security-rules"></a>Regras de segurança padrão

Se um grupo de segurança de rede não está associado a um adaptador de rede ou a uma sub-rede, todo o tráfego será permitido na entrada e na saída da sub-rede ou do adaptador de rede. Assim que um grupo de segurança de rede é criado, o Azure cria as seguintes regras padrão no grupo de segurança de rede:

### <a name="inbound"></a>Entrada

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioridade|Fonte|Portas de origem|Destino|Portas de destino|Protocolo|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Todos|PERMITIR|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioridade|Fonte|Portas de origem|Destino|Portas de destino|Protocolo|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Todos|PERMITIR|

#### <a name="denyallinbound"></a>DenyAllInBound

|Prioridade|Fonte|Portas de origem|Destino|Portas de destino|Protocolo|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Todos|Negar|

### <a name="outbound"></a>Saída

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioridade|Fonte|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Todos | PERMITIR |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioridade|Fonte|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Todos | PERMITIR |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioridade|Fonte|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Todos | Negar |

Nas colunas **Origem** e **Destino**, *VirtualNetwork*, *AzureLoadBalancer* e *Internet* são [marcas de serviço](#tags) em vez de endereços IP. Na coluna de protocolo, a opção **Todos** abrange TCP, UDP e ICMP. Ao criar uma regra, você pode especificar TCP, UDP ou Tudo, mas não é possível especificar somente ICMP. Portanto, se sua regra requer ICMP, você deverá selecionar *Tudo* como protocolo. *0.0.0.0/0* nas colunas **Origem** e **Destino** representa todos os endereços.
 
Não é possível remover as regras padrão, mas você pode substituí-las criando regras com prioridades mais altas.

## <a name="service-tags"></a>Marcas de serviço

 Uma marca de serviço representa um grupo de prefixos de endereço IP para ajudar a minimizar a complexidade da criação de regra de segurança. Você não pode criar sua própria marca de serviço ou especificar quais endereços IP estão incluídos em uma marca. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. As marcas de serviço a seguir estão disponíveis para uso na definição da regra de segurança. Os nomes variam ligeiramente entre [modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

* **VirtualNetwork** (*Resource Manager) (**VIRTUAL_NETWORK** para clássico): essa marca inclui o espaço de endereço de rede virtual (todos os intervalos de CIDR definidos para a rede virtual), todos os espaços de endereço locais conectados e redes virtuais [emparelhadas](virtual-network-peering-overview.md) ou rede virtual conectada a um [gateway de rede virtual](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** para clássico): essa marca denota o balanceador de carga de infraestrutura do Azure. A marca significa um [endereço IP de datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) de onde se originam as investigações de integridade do Azure. Se não estiver usando um balanceador de carga do Azure, você poderá substituir essa regra.
* **Internet** (Resource Manager) (**INTERNET** para clássico): essa marca denota o espaço de endereço IP que está fora da rede virtual e é acessível pela Internet pública. O intervalo de endereços inclui o [espaço de endereço IP público de propriedade do Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* **AzureTrafficManager** (somente no Gerenciador de Recursos): essa marca denota o espaço de endereço IP para os IPs de investigação do Gerenciador de Tráfego do Azure. Saiba mais sobre os IPs de investigação no Gerenciador de Tráfego nas [Perguntas frequentes sobre o Azure Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-faqs).
* **Armazenamento** (somente no Gerenciador de Recursos): essa marca denota o espaço de endereço IP para o serviço Armazenamento do Azure. Se você especificar *Armazenamento* como valor, o tráfego será permitido ou negado para o armazenamento. Se deseja permitir o acesso ao armazenamento em uma determinada [região](https://azure.microsoft.com/regions), você pode especificar a região. Por exemplo, se você quiser permitir o acesso somente para o Armazenamento do Azure na região Leste dos EUA, poderá especificar *Storage.EastUS* como uma marca de serviço. A marca representa o serviço, mas não as instâncias específicas do serviço. Por exemplo, a marca representa o serviço Armazenamento do Azure, mas não uma conta do Armazenamento do Azure específica.
* **SQL** (somente no Gerenciador de Recursos): essa marca indica os prefixos de endereço dos serviços Banco de Dados SQL do Azure e Azure SQL Data Warehouse. Se você especificar *Sql* como valor, o tráfego será permitido ou negado para o Sql. Se só deseja permitir o acesso ao Sql em uma [região](https://azure.microsoft.com/regions) específica, é possível especificar a região. Por exemplo, se você quiser permitir o acesso somente para o Banco de Dados SQL do Azure na região Leste dos EUA, poderá especificar *Sql.EastUS* como uma marca de serviço. A marca representa o serviço, mas não as instâncias específicas do serviço. Por exemplo, a marca representa o serviço Banco de Dados SQL do Azure, mas não um banco de dados ou servidor SQL específico.

> [!NOTE]
> Se você implementar um [ponto de extremidade do serviço de rede virtual](virtual-network-service-endpoints-overview.md) a um serviço, como o Armazenamento do Azure ou o Banco de Dados SQL do Azure, o Azure adiciona uma rota para uma sub-rede de rede virtual para o serviço. Os prefixos de endereço na rota são os mesmos prefixos de endereço, ou intervalos CIDR, que a marca de serviço correspondente.

## <a name="application-security-groups"></a>Grupos de segurança do aplicativo

Os grupos de segurança de aplicativo permitem a você configurar a segurança de rede como uma extensão natural da estrutura de um aplicativo, permitindo o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos. Esse recurso permite que você reutilize sua política de segurança em escala sem precisar manter endereços IP explícitos manualmente. A plataforma lida com a complexidade de endereços IP explícitos e vários conjuntos de regras, permitindo que você se concentre na sua lógica de negócios.

Você pode especificar um grupo de segurança do aplicativo como a origem e o destino em uma regra de segurança. Quando a política de segurança é definida, você pode criar máquinas virtuais e atribuir os adaptadores de rede na máquina virtual a um grupo de segurança de aplicativo. A política é aplicada com base na associação do grupo de segurança de aplicativo de cada adaptador de rede em uma máquina virtual. O exemplo a seguir ilustra como você pode usar um grupo de segurança de aplicativo para todos os servidores Web na sua assinatura:

1. Crie um grupo de segurança de aplicativo chamado *WebServers*.
2. Crie um grupo de segurança de rede chamado *MyNSG*.
3. Crie uma regra de segurança de entrada no grupo de segurança de rede especificando a marca de serviço *Internet* para o endereço de origem e o grupo de segurança de aplicativo *WebServers* como o endereço de destino e permitindo as portas 80 e 443.
4. Implante uma máquina virtual que executa um aplicativo de servidor Web. Especifique que o adaptador de rede na máquina virtual é um membro do grupo de segurança de aplicativo *WebServers*. As portas 80 e 443 serão permitidas para a máquina virtual. As portas também são permitidas para todos os servidores Web criados posteriormente que se tornarem membros do grupo de segurança de aplicativo *WebServers*. 

Se você criar outras regras, especificando outros grupos de segurança de aplicativo como destino, essas regras não serão aplicadas aos servidores Web no exemplo anterior. As regras que especificam um grupo de segurança de aplicativo somente se aplicam a adaptadores de rede que sejam associados ao grupo de segurança de aplicativo. Os grupos de segurança de aplicativo, combinados com as regras de segurança aumentadas e as marcas de serviço, possibilitam a criação de um número mínimo de grupos de segurança de rede para gerenciar a segurança de rede dentro de sua assinatura.
 
Para saber mais sobre limites durante a criação de grupos de segurança de aplicativo e sua especificação nas regras de segurança, confira [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Os grupos de segurança de aplicativo estão disponíveis na versão prévia. Os recursos da versão prévia não têm o mesmo nível de disponibilidade e confiabilidade dos recursos da versão geral. Antes de usar grupos de segurança de aplicativo, primeiro você deve se registrar para usá-los concluindo as etapas de 1 a 5 nas seções do Azure ou PowerShell de [Criar um grupo de segurança de rede com grupos de segurança de aplicativo](create-network-security-group-preview.md). Os grupos de segurança do aplicativo têm as seguintes restrições:

-   Todas as interfaces de rede dentro de um grupo de segurança do aplicativo devem existir na mesma rede virtual. Você não pode adicionar interfaces de rede de redes virtuais diferentes ao mesmo grupo de segurança do aplicativo. A primeira interface de rede da rede virtual atribuída ao grupo de segurança do aplicativo define a rede virtual em que todas as interfaces de rede subsequentes deverão existir.
- Se você especificar grupos de segurança do aplicativo como a origem e o destino em uma regra de segurança, as interfaces de rede em ambos os grupos de segurança do aplicativo deverão existir na mesma rede virtual. Por exemplo, se ASG1 contiver interfaces de rede da VNet1 e se ASG2 contiver interfaces de rede da VNet2, você não poderá atribuir ASG1 como a origem e o ASG2 como o destino em uma regra, todas as interfaces de rede precisam existir em VNet1.

## <a name="azure-platform-considerations"></a>Considerações sobre a plataforma do Azure

- **IP virtual do nó do host:** serviços básicos de infraestrutura, como DHCP, DNS e integridade de monitoramento, são fornecidos pelos endereços IP de host virtualizados 168.63.129.16 e 169.254.169.254. Esses endereços IP públicos pertencem à Microsoft e são os únicos endereços IP virtualizados usado em todas as regiões para essa finalidade. Os endereços são mapeados para o endereço IP físico do computador do servidor (nó do host) que hospeda a máquina virtual. O nó do host atua como a retransmissão DHCP, o solucionador de DNS recursivo e a fonte de sonda para a investigação de integridade do balanceador de carga e a investigação de integridade da máquina. A comunicação com esses endereços IP não é um ataque. Se você bloquear o tráfego de entrada ou saída desses endereços IP, uma máquina virtual pode não funcionar corretamente.
- **Licenciamento (Serviço de Gerenciamento de Chaves)**: as imagens do Windows em execução nas máquinas virtuais devem ser licenciadas. Para garantir o licenciamento, uma solicitação de licenciamento é enviada para os servidores de host do serviço de gerenciamento de chaves que lidar com essas consultas. A solicitação é feita para a saída pela porta 1688.
- **Máquinas virtuais em pools de carga balanceada**: o intervalo de porta e endereço de origem aplicado é do computador de origem, não do balanceador de carga. Os intervalos de porta e endereço de destino são para o computador de destino, não o balanceador de carga.
- **Instâncias de serviço do Azure**: instâncias de vários serviços do Azure, como o HDInsight, Ambientes de Serviço de Aplicativo e Conjuntos de Dimensionamento de Máquinas Virtuais são implantadas em sub-redes de rede virtual. Para obter uma lista completa de serviços que podem ser implantados em uma rede virtual, confira a [Rede virtual para os serviços do Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Familiarize-se com os requisitos de porta para cada serviço antes da aplicação de um grupo de segurança de rede à sub-rede em que o recurso está implantado. Se você negar portas exigidas pelo serviço, o serviço não funcionará corretamente.
- **Enviar um email**: a Microsoft recomenda que você utilize os serviços de retransmissão de SMTP autenticados (normalmente conectados via porta TCP 587, mas geralmente outras, também) para enviar email de máquinas virtuais do Azure. Os serviços de retransmissão de SMTP são especializados em reputação do remetente, para minimizar a possibilidade dos provedores de email de terceiros rejeitarem mensagens. Esses serviços de retransmissão de SMTP incluem, mas não estão limitados à proteção do Exchange Online e do SendGrid. O uso de serviços de retransmissão de SMTP não é de modo algum restrito no Azure, independentemente de seu tipo de assinatura. 

  Se você criou sua assinatura do Azure antes de 15 de novembro de 2017, além de poder usar os serviços de retransmissão de SMTP, você pode enviar um email diretamente pela porta TCP 25. Se você criou sua assinatura depois do dia 15 de novembro de 2017, não poderá enviar emails diretamente pela porta 25. O comportamento de comunicação de saída pela porta 25 depende do tipo de assinatura que você tem, da seguinte maneira:

     - **Contrato Enterprise**: a comunicação de saída da porta 25 é permitida. É possível enviar emails de saída diretamente a partir de máquinas virtuais para provedores de email externos, sem restrições da plataforma do Azure. 
     - **Pré-pago:** a comunicação de saída da porta 25 está bloqueada de todos os recursos. Se você precisar enviar emails de uma máquina virtual diretamente para os provedores de email externos (não usando uma retransmissão SMTP autenticada), pode fazer uma solicitação para remover a restrição. As solicitações são examinadas e aprovadas a critério da Microsoft e somente são concedidas após a realização de verificações antifraude. Para fazer uma solicitação, abra um caso de suporte com o tipo de problema *Técnico*, *Conectividade de rede virtual*, *Não é possível enviar email (SMTP/Porta 25)*. No seu caso de suporte, isso inclui detalhes sobre por que você precisa enviar emails diretamente aos provedores de email, em vez de passar por um retransmissor SMTP autenticado. Se sua assinatura for isenta, somente as máquinas virtuais criadas após a data de isenção serão capazes de se comunicar pela porta 25.
     - **CSP (provedor de serviços de nuvem), MSDN, Azure Pass, Azure via Open, Educação, BizSpark, e Avaliação gratuita**: a comunicação de saída da porta 25 está bloqueada para todos os recursos. Nenhuma solicitação para remover a restrição pode ser feita, pois as solicitações não foram concedidas. Se você tiver que enviar um email de sua máquina virtual, deve usar um serviço de retransmissão de SMTP.

  Se o Azure permitir que você envie emails pela porta 25, a Microsoft não garante que os provedores de email aceitarão emails de entrada provenientes de sua máquina virtual. Se um provedor específico rejeitar um email de sua máquina virtual, você deve trabalhar diretamente com o provedor para resolver qualquer entrega de mensagens ou problemas de filtragem de spam ou usar um serviço de retransmissão de SMTP autenticado. 


## <a name="next-steps"></a>Próximas etapas

* Concluir o tutorial [Criar um grupo de segurança de rede](virtual-networks-create-nsg-arm-pportal.md)
* Concluir o tutorial [Criar um grupo de segurança de rede com grupos de segurança de aplicativo](create-network-security-group-preview.md)
