<properties 
   pageTitle="Visão geral do balanceador de carga do Azure | Microsoft Azure"
   description="Visão geral dos recursos do balanceador de carga, arquitetura e implementação do Azure. É importante entender como o balanceador de carga funciona e aproveitá-lo na nuvem"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/09/2015"
   ms.author="joaoma" />


# O que é balanceador de carga do Azure?
 
O balanceador de carga do Azure oferece alta disponibilidade e desempenho de rede para seus aplicativos. É um balanceador de carga do tipo Camada 4 (TCP, UDP) que distribui tráfego de entrada entre instâncias íntegras de serviço, em serviços de nuvem ou máquinas virtuais, definidos em um conjunto de balanceador de carga.
 
Ele pode ser configurado para:

- Balancear carga de tráfego de entrada na Internet para máquinas virtuais. Nós nos referimos a isso como [balanceamento de carga para a Internet](load-balancer-internet-overview.md).
- Balancear carga de tráfego entre máquinas virtuais em uma Rede Virtual, entre máquinas virtuais em serviços de nuvem ou entre computadores locais e máquinas virtuais em uma rede virtual entre instalações. Nós nos referimos a isso como [ILB (Balanceamento de Carga Interno)](load-balancer-internal-overview.md).
- 	Encaminhar tráfego externo para uma instância específica de máquina virtual

## Noções básicas sobre o balanceador de carga no Azure clássico e no Gerenciador de Recursos do Azure (ARM)

Todos os recursos na nuvem precisam de um endereço IP público para serem acessíveis pela Internet. A infraestrutura de nuvem no Microsoft Azure usará endereços IP não roteáveis em seus recursos e usará a conversão de endereços de rede (NAT) com endereços IP públicos para se comunicar com a Internet.

Há dois modelos de implantação no Microsoft Azure e suas implementações do balanceador de carga:

 
### Azure clássico

O Azure clássico é o primeiro modelo de implantação implementado no Microsoft Azure. Nesse modelo, um endereço IP público e um FQDN são atribuídos a um serviço de nuvem e as máquinas virtuais implantadas em um limite de serviço de nuvem podem ser agrupadas para usar um balanceador de carga. O balanceador de carga fará a conversão da porta e balanceará a carga do tráfego da rede, utilizando o endereço IP público do serviço de nuvem.

Em um modelo de implantação clássico, a conversão da porta é feita usando pontos de extremidade que são uma relação de um para um entre a porta pública atribuída do endereço IP público e a porta local atribuída para enviar o tráfego para uma máquina virtual específica.

O balanceamento da carga é feito usando pontos de extremidade definidos pelo balanceador de carga. Esses pontos de extremidade são uma relação de um para muitos entre o endereço IP público e as portas locais atribuídas a todas as máquinas virtuais no conjunto que responderá para o tráfego de rede com balanceamento de carga.

O rótulo do domínio para o endereço IP público que um balanceador de carga usaria nesse modelo de implantação seria `<cloud service name>.cloudapp.net`.

Esta é uma representação gráfica de um balanceador de carga em um modelo de implantação clássico: ![balanceador de carga baseado em hash](./media/load-balancer-overview/asm-lb.png)

### Gerenciador de Recursos do Azure
 
O conceito de balanceador de carga é alterado no Gerenciador de Recursos do Azure (ARM) porque não há necessidade de um serviço de nuvem para criar um balanceador de carga.

No ARM, um endereço IP público é seu próprio recurso e pode ser associado a um rótulo do domínio ou nome DNS. O IP público neste caso é associado ao recurso do balanceador de carga para que as regras do balanceador de carga, as regras NAT de entrada, usem o endereço IP público como seu ponto de extremidade de Internet para os recursos que recebem o tráfego de rede com balanceamento de carga.

Um recurso da interface de rede (NIC) contém a configuração do endereço IP (IP público ou privado) para uma máquina virtual. Quando uma NIC for adicionada a um pool de endereços IP de back-end do balanceador de carga, o balanceador de carga começará a enviar o tráfego de rede com carga balanceada baseado nas regras de balanceamento de carga criadas.

Um conjunto de disponibilidade é o método de agrupamento usado para adicionar máquinas virtuais ao balanceador de carga. O conjunto de disponibilidade garante que as máquinas virtuais não residirão no mesmo hardware físico e em caso de falha relacionada à infraestrutura de nuvem física, irá assegurar que o balanceador de carga sempre terá uma máquina virtual recebendo o tráfego de rede com balanceamento de carga.

Esta é uma representação gráfica de um balanceador de carga no Gerenciador de Recursos do Azure (ARM):

![balanceador de carga baseado em hash](./media/load-balancer-overview/arm-lb.png)

## Recursos do balanceador de carga

### Balanceador de carga de Camada 4, distribuição com base em hash

O balanceador de carga do Azure usa um algoritmo de distribuição baseado em hash. Por padrão, ele usa um hash de tupla 5 (IP de origem, porta de origem, IP de destino, porta de destino, tipo de protocolo) para mapear o tráfego até os servidores disponíveis. Ele fornece permanência somente dentro de uma sessão de transporte. Os pacotes na mesma sessão TCP ou UDP serão direcionados para a mesma instância do DIP (IP de Datacenter), atrás do ponto de extremidade de balanceamento de carga. Quando o cliente fecha e reabre a conexão ou inicia uma nova sessão do mesmo IP de origem, a porta de origem é alterada. Isso pode fazer com que o tráfego vá para um ponto de extremidade DIP diferente.


Para obter mais detalhes, consulte [Modo de distribuição de balanceamento de carga](load-balancer-distribution-mode.md)

![balanceador de carga baseado em hash](./media/load-balancer-overview/load-balancer-distribution.png)

### Encaminhamento de porta

O Balanceador de Carga do Azure fornece controle sobre como a comunicação de entrada, como o tráfego iniciado em hosts da Internet ou em máquinas virtuais, em outros serviços de nuvem ou redes virtuais é gerenciado. Esse controle é representado por um ponto de extremidade (também conhecido como ponto de extremidade de entrada).

Um ponto de extremidade escuta em uma porta pública e encaminha o tráfego para uma porta interna. Você pode mapear as mesmas portas para um ponto de extremidade interno ou externo ou usar uma porta diferente para eles. Por exemplo: você pode ter uma escuta configurada no servidor Web para a porta 81 enquanto o mapeamento do ponto de extremidade público é a porta 80. A criação de um ponto de extremidade público dispara a criação de um balanceador de carga do Azure.

O uso e a configuração padrão de pontos de extremidade em uma máquina virtual que você cria com o Portal de Gerenciamento do Azure são para o protocolo RDP e o tráfego de sessão remota do Windows PowerShell. Esses pontos de extremidade permitem que você administre remotamente a máquina virtual pela Internet.


### Reconfiguração automática ao escalar horizontalmente/reduzir verticalmente

O balanceador de carga do Azure reconfigura-se instantaneamente quando você escala/reduz verticalmente as instâncias (devido ao aumento de contagem de instâncias de função Web/de trabalho ou devido à colocação de máquinas virtuais adicionais no mesmo conjunto de balanceamento de carga).


### Monitoramento de serviço
O balanceador de carga do Azure oferece a capacidade de investigar a integridade de várias instâncias de servidor. Quando uma investigação não responde, o balanceador de carga do Azure interrompe o envio de nova conexão para instâncias não íntegras. As conexões existentes não são afetadas.

Existem três tipos de investigação com suporte:
 
- Investigação de agente convidado (em VMs de PaaS somente). O balanceador de carga do Azure utiliza o agente convidado dentro da máquina virtual, escuta e responde com uma resposta HTTP 200 OK somente quando a instância está no estado pronto (ou seja, a instância não está nos estados Ocupado, Reciclando, Parando, etc). Se o agente convidado não responder com HTTP 200 OK, o balanceador de carga do Azure marcará a instância como sem resposta e interromperá o envio de tráfego para essa instância. O balanceador de carga do Azure continuará a executar o ping na instância e, se o agente convidado responder com um HTTP 200, o balanceador de carga do Azure enviará tráfego para essa instância novamente. Ao usar uma função web, seu site geralmente executa em w3wp.exe, que não é monitorado pela malha nem pelo agente convidado do Azure, o que significa que as falhas no w3wp.exe (por exemplo, respostas HTTP 500) não serão reportadas ao agente convidado e o balanceador de carga não saberá colocar essa instância fora da rotação.

- Investigações personalizadas de HTTP. A investigação personalizada do balanceador de carga substitui a investigação do agente convidado padrão e permite que você crie sua própria lógica personalizada para determinar a integridade da instância de função. O balanceador de carga investigará regularmente seu ponto de extremidade (a cada 15 segundos, por padrão) e a instância será considerada em rotação se responder com um TCP ACK ou HTTP 200 dentro do período de tempo limite (padrão de 31 segundos). Isso pode ser útil para implementar sua própria lógica para remover instâncias da rotação do balanceador de carga, por exemplo retornar um status não 200 se a instância estiver acima de 90% da CPU. Para funções web que usam w3wp.exe, isso também significa que você obtém monitoramento automático do seu site, uma vez que as falhas no código do site retornarão um status não 200 para a investigação do balanceador de carga.

- Investigações personalizadas de TCP. Investigações de TCP dependem do estabelecimento bem-sucedido da sessão de TCP em uma porta de investigação definida.

Para obter mais informações, consulte [Investigação e integridade do balanceador de carga](https://msdn.microsoft.com/library/azure/jj151530.aspx).

### SNAT (NAT de origem)


Todo tráfego de saída para a Internet proveniente de seu serviço é verificado quanto ao SNAT (NAT de origem), que usa o mesmo endereço VIP do tráfego de entrada. A SNAT fornece benefícios importantes:

- Permite fácil atualização e recuperação de desastres de serviços, já que o VIP pode ser mapeado dinamicamente para outra instância do serviço.

- Facilita o gerenciamento de ACLs, já que a ACL pode ser expressa em termos de VIPs e, portanto, não se altera à medida que os serviços são escalados/reduzidos verticalmente ou reimplantados

A configuração do balanceador de carga do Azure dá suporte para NAT de cone completo para UDP. Cone NAT completo é um tipo de NAT em que a porta permite conexões de entrada de qualquer host externo (em resposta a uma solicitação de saída).


>[AZURE.NOTE]Observe que para cada nova conexão de saída iniciada por uma VM, uma porta de saída também é alocada pelo balanceador de carga do Azure. O host externo verá o tráfego direcionado como VIP: porta alocada. Se os cenários exigirem um grande número de conexões de saída, é recomendável que as VMs usem IPs Públicos em Nível de Instância para que exista um IP de saída dedicado a SNAT (Conversão de Endereço de Rede de Origem). Isso reduzirá o risco de esgotamento de porta.
>
>O número máximo de portas que podem ser usadas por VIP ou ILPIP é 64 mil. Essa é uma limitação de padrão de TCP.


**Suporte para vários IPs de balanceamento de carga para máquinas virtuais**

Você pode obter mais de um endereço IP público de balanceamento de carga atribuído a um conjunto de máquinas virtuais. Com esse recurso, você pode hospedar vários sites SSL e/ou ouvintes de Grupo de Disponibilidade AlwaysOn do SQL no mesmo conjunto de máquinas virtuais. Saiba mais em [Vários VIPs por serviço de nuvem](load-balancer-multivip.md)

****Implantações baseadas em modelos usando o Gerenciador de Recursos do Azure ** ARM (Gerenciador de Recursos do Azure) é a nova estrutura de gerenciamento de serviços no Azure. O balanceador de carga do Azure agora pode ser gerenciado usando ferramentas e APIs baseadas no Gerenciador de Recursos do Azure. Para saber mais sobre o Gerenciador de Recursos do Azure, consulte [Iaas ficou mais fácil com o Gerenciador de Recursos do Azure](http://azure.microsoft.com/blog/2015/04/29/iaas-just-got-easier-again/)


## Próximas etapas

[Visão geral do balanceador de carga para a Internet](load-balancer-internet-overview.md)

[Visão geral do balanceador de carga interno](load-balancer-internal-overview.md)

[Introdução - Balanceador de carga para a Internet](load-balancer-internet-getstarted.md)
 

<!---HONumber=AcomDC_1217_2015-->