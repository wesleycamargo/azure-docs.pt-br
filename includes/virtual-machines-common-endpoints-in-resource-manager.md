A abordagem para pontos de extremidade do Azure funciona um pouco diferente entre os modelos de implantação Clássico e do Resource Manager. Agora você tem a flexibilidade para criar filtros de rede que controlam o fluxo do tráfego que entra e sai de suas VMs, permitindo a criação de ambientes de rede complexos, indo além de um ponto de extremidade simples, como é o caso do modelo de implantação Clássico. Este artigo fornece uma visão geral dos grupos de segurança de rede e como eles são diferentes da utilização de pontos de extremidade Clássicos, criando regras de filtragem e exemplos de cenários de implantação.


## Visão geral das implantações do Resource Manager
Os pontos de extremidade no modelo de implantação Clássico são substituídos por Grupos de Segurança de Rede e regras de ACL (lista de controle de acesso). As etapas rápidas para implementação de regras de ACL de Grupo de Segurança de Rede são:

- Criar um grupo de segurança de rede
- Definir suas regras de ACL de Grupo de Segurança de Rede para permitir ou negar o tráfego
- Atribuir o Grupo de Segurança de Rede a uma interface de rede ou sub-rede da rede virtual

Se você também quiser executar o encaminhamento de porta, será necessário colocar um balanceador de carga na frente de sua VM e usar regras de NAT. As etapas rápidas para implementar regras de NAT e um balanceador de carga seriam as seguintes:

- Criar um balanceador de carga
- Criar um pool de back-end e adicionar suas VMs ao pool
- Definir regras de NAT para o encaminhamento de porta necessário
- Atribuir as regras NAT às suas VMs


## Visão geral do Grupo de Segurança de Rede
Grupos de Segurança de Rede são um novo recurso que fornece uma camada de segurança a fim de permitir que portas específicas e sub-redes acessem suas VMs. Normalmente, sempre há um Grupo de Segurança de Rede fornecendo essa camada de segurança entre suas VMs e o mundo externo. O Grupos de Segurança de Rede podem ser aplicados a uma sub-rede de rede virtual ou interface de rede específica para uma VM. Em vez de criar regras de ACL do ponto de extremidade, agora é possível criar regras de ACL de Grupo de Segurança de Rede. Essas regras de ACL fornecem um controle muito maior do que simplesmente criar um ponto de extremidade para encaminhar uma determinada porta. Você pode [ler mais sobre os Grupos de Segurança de Rede](../articles/virtual-network/virtual-networks-nsg.md).

> [AZURE.TIP] É possível atribuir Grupos de Segurança de Rede a várias sub-redes ou interfaces de rede. Não há qualquer mapeamento 1:1, ou seja, você pode criar um Grupo de Segurança de Rede com um conjunto comum de regras de ACL e aplicá-lo a várias sub-redes ou adaptadores de rede. Além disso, o Grupo de Segurança de Rede pode ser aplicado a recursos em sua assinatura (com base em [Controles de Acesso Baseados em Função](../articles/active-directory/role-based-access-control-what-is.md).


## Visão geral do balanceador de carga
No modelo de implantação Clássico, o Azure executa para você toda NAT (Conversão de endereço de rede) e encaminhamento de porta em um Serviço de Nuvem. Ao criar um ponto de extremidade, você especificaria a porta externa a ser exposta, juntamente com a porta interna para a qual direcionar o tráfego. Os Grupos de Segurança de Rede por si só não executam essa mesma NAT e o encaminhamento de porta.

Para que você possa criar regras NAT para tal encaminhamento de porta, crie um Azure Load Balancer no seu grupo de recursos. Novamente, o balanceador de carga é suficientemente granular para ser aplicado apenas a VMs específicas, se for necessário. As regras de NAT do Azure Load Balancer trabalham em conjunto com as regras de ACL de Grupo de Segurança de Rede para fornecer mais flexibilidade e controle do que pode ser obtido com os pontos de extremidade do Serviço de Nuvem. Leia mais sobre [Visão geral do balanceador de carga](../articles/load-balancer/load-balancer-overview.md).


## Regras de ACL de Grupo de Segurança de Rede
As regras de ACL permitem que você defina o tráfego que pode fluir dentro e fora de sua VM com base em protocolos, intervalos de porta ou portas específicas. As regras são atribuídas às VMs individuais ou a uma sub-rede. A captura de tela a seguir mostra um exemplo de regras de ACL para um servidor Web comum:

![Lista de regras de ACL de Grupo de Segurança de Rede](./media/virtual-machines-common-endpoints-in-resource-manager/example-acl-rules.png)

As regras de ACL são aplicadas com base em uma métrica de prioridade especificada por você. Quanto maior o valor, menor a prioridade. Cada Grupo de Segurança de Rede tem três regras padrão projetadas para lidar com o fluxo do tráfego de rede do Azure, com uma `DenyAllInbound` explícita como a regra final. As regras de ACL padrão recebem uma prioridade baixa para não interferir com as regras criadas.


## Atribuição dos Grupos de Segurança de Rede
Você pode atribuir um Grupo de Segurança de Rede a uma sub-rede ou a uma interface de rede. Essa abordagem permite que o detalhamento necessário ao aplicar as regras de ACL a apenas uma VM específica, ou garante que um conjunto comum de regras de ACL seja aplicado a todas as VMs que fazem parte de uma sub-rede:

![Aplicar NSGs a sub-redes ou interfaces de rede](./media/virtual-machines-common-endpoints-in-resource-manager/apply-nsg-to-resources.png)

O comportamento do Grupo de Segurança de Rede não muda se estiver sendo atribuído a uma sub-rede ou a uma interface de rede. Um cenário de implantação comum tem o Grupo de Segurança de Rede atribuído a uma sub-rede a fim de garantir a conformidade de todas as VMs conectadas à sub-rede. Leia mais sobre[como aplicar os Grupos de Segurança de Rede aos recursos](../virtual-nework/virtual-networks-nsg.md#associating-nsgs).


## Comportamento padrão dos Grupos de Segurança de Rede
Dependendo de como e quando você cria o grupo de segurança de rede, as regras padrão podem ser criadas a fim de permitir o acesso RDP na porta TCP 3389. As VMs do Linux permitem o acesso por protocolo SSH na porta TCP 22. Essas regras de ACL automáticas são criadas sob as seguintes condições:

- Se você criar uma VM do Windows por meio do portal e aceitar a ação padrão para criar um Grupo de Segurança de Rede, uma regra de ACL para permitir a porta TCP 3389 (RDP) será criada.
- Se você criar uma VM do Linux por meio do portal e aceitar a ação padrão para criar um Grupo de Segurança de Rede, uma regra de ACL para permitir a porta TCP 22 (SSH) será criada.

Em todas as outras condições, essas regras ACL padrão não são criadas. Você não conseguirá se conectar à sua VM sem a criação das regras de ACL apropriadas. Isso inclui as seguintes ações comuns:

- Criação de um Grupo de Segurança de Rede por meio do portal como uma ação separada para criar a VM.
- Criação de um Grupo de Segurança de Rede por meio de programação no PowerShell, na CLI do Azure, APIs Rest etc.
- Criação e atribuição de uma VM a um Grupo de Segurança de Rede existente que ainda não tem a regra de ACL apropriada definida.

Em todos os casos acima, você precisa criar regras de ACL para sua VM a fim de permitir as conexões de gerenciamento remoto apropriadas.


## Comportamento padrão de uma VM sem um Grupo de Segurança de Rede
Você pode criar uma VM sem criar um Grupo de Segurança de Rede. Nessas situações, você pode se conectar à sua VM usando o RDP ou SSH sem criar regras de ACL. Da mesma forma, se você instalou um serviço Web na porta 80, esse serviço pode ser acessado automaticamente remotamente. A VM tem todas as portas abertas.

> [AZURE.NOTE] Você ainda precisa ter um endereço IP público atribuído a uma VM para qualquer conexão remota. Não ter um Grupo de Segurança de Rede para a interface de rede ou sub-rede não expõe a VM a qualquer tráfego externo. A ação padrão ao criar uma VM por meio do portal é criar um novo IP público. Para todas as outras formas de criação de uma VM, como o PowerShell, CLI do Azure ou o modelo do Resource Manager, um IP público não será criado automaticamente se não tiver sido solicitado explicitamente. A ação padrão por meio do portal também é criar um Grupo de Segurança de Rede. Portanto, você não deve terminar em uma situação com uma VM exposta sem qualquer filtragem de rede em vigor.


## Noções básicas sobre regras de NAT e balanceadores de carga
No modelo de implantação Clássico, você pode criar pontos de extremidade que também realizam o encaminhamento de porta. Quando você cria uma máquina virtual no modelo de implantação clássico, as regras ACL para RDP ou SSH são criadas automaticamente. Elas não expõem a porta TCP 3389 ou a porta TCP 22, respectivamente, para o mundo exterior. Em vez disso, uma porta TCP de alto valor seria exposta, mapeando para a porta interna apropriada. Você também pode criar suas próprias regras de ACL de uma maneira semelhante, por exemplo, expondo um servidor Web na porta TCP 4280 para o mundo exterior. Você pode ver essas regras de ACL e os mapeamentos de porta na seguinte captura de tela do Portal Clássico:

![Encaminhamento de porta com pontos de extremidade Clássicos](./media/virtual-machines-common-endpoints-in-resource-manager/classic-endpoints-port-forwarding.png)

Com os Grupos de Segurança de Rede, essa função de encaminhamento de porta é tratada por um balanceador de carga. Leia mais sobre [balanceadores de carga no Azure](../articles/load-balancer/load-balancer-overview.md). Veja na seguinte captura de tela do portal um exemplo de um balanceador de carga com uma regra de NAT para executar o encaminhamento de porta da porta TCP 4222 à porta TCP 22 interna de uma VM:

![Regras NAT do Balanceador de carga para encaminhamento de porta](./media/virtual-machines-common-endpoints-in-resource-manager/load-balancer-nat-rules.png)

> [AZURE.NOTE] Quando você implementa um balanceador de carga, normalmente não atribui à própria VM um endereço IP público. Em vez disso, o balanceador de carga tem um endereço IP público atribuído a ele. Você ainda precisa criar o Grupo de Segurança de Rede e a regra de ACL para definir o fluxo de tráfego que entra e sai de sua VM. As regras de NAT do balanceador de carga são basicamente para definir quais portas têm permissão no balanceador de carga e como elas são distribuídas entre as VMs de back-end. Assim, você precisa criar uma regra de NAT para o tráfego fluir por meio do balanceador de carga e, em seguida, criar uma regra de ACL de Grupo de Segurança de Rede para permitir que o tráfego realmente alcance a VM.

<!---HONumber=AcomDC_0810_2016-->