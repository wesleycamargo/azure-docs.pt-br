<properties
   pageTitle="Visão geral do balanceador de carga do Azure | Microsoft Azure"
   description="Visão geral dos recursos do Balanceador de Carga do Azure, arquitetura e implementação. É importante entender como o Balanceador de Carga funciona e como aproveitá-lo na nuvem."
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


# O que é Balanceador de Carga do Azure?

O Balanceador de Carga do Azure oferece alta disponibilidade e desempenho de rede para seus aplicativos. É um balanceador de carga do tipo Camada 4 (TCP, UDP) que distribui o tráfego de entrada entre as instâncias de serviço íntegras nos serviços de nuvem ou nas máquinas virtuais definidos em um conjunto de balanceadores de carga.

Ele pode ser configurado para:

- Balancear carga de tráfego de entrada na Internet para máquinas virtuais. Chamamos isso de [balanceamento de carga para a Internet](load-balancer-internet-overview.md).
- Balanceie o tráfego de carga entre as máquinas virtuais em uma rede virtual, entre as máquinas virtuais nos serviços de nuvem ou entre os computadores locais e as máquinas virtuais em uma rede virtual entre as instalações. Chamamos isso de [balanceamento de carga interno](load-balancer-internal-overview.md).
- Encaminhe o tráfego externo para uma máquina virtual específica.

## Balanceador de Carga nos dois modelos de implantação

Todos os recursos na nuvem precisam de um endereço IP público para serem acessíveis pela Internet. A infraestrutura de nuvem no Microsoft Azure usa endereços IP não roteáveis dentro de seus recursos. Ele usa a conversão de endereços de rede (NAT) com os endereços IP públicos para se comunicar com a Internet.

### Azure clássico

No modelo de implantação clássico do Azure, um endereço IP público e um FQDN são atribuídos a um serviço de nuvem. As máquinas virtuais que são implantadas em um limite de serviço de nuvem podem ser agrupadas para usar um balanceador de carga. O Balanceador de Carga do Azure fará a conversão da porta e balanceará a carga do tráfego da rede utilizando o endereço IP público do serviço de nuvem.

Em um modelo de implantação clássico, a conversão da porta é feita usando pontos de extremidade que têm uma relação de um para um entre a porta pública atribuída do endereço IP público e a porta local atribuída para enviar o tráfego para uma máquina virtual específica.

O balanceamento da carga é feito usando os pontos de extremidade definidos pelo Balanceador de Carga. Esses pontos de extremidade são uma relação de um para muitos entre o endereço IP público e as portas locais atribuídas a todas as máquinas virtuais no conjunto que responderá pelo tráfego de rede com balanceamento de carga.

O rótulo do domínio para o endereço IP público que um Balanceador de Carga usaria nesse modelo de implantação é `<cloud service name>.cloudapp.net`.

Esta é uma representação gráfica de um Balanceador de Carga no modelo de implantação clássico:

![Balanceador de Carga no modelo de implantação clássico](./media/load-balancer-overview/asm-lb.png)

### Gerenciador de Recursos do Azure

O conceito de Balanceador de Carga muda para o modelo de implantação do Gerenciador de Recursos do Azure porque não há necessidade de um serviço de nuvem para criar um balanceador de carga.

No Gerenciador de Recursos, um endereço IP público é seu próprio recurso e pode ser associado a um rótulo do domínio ou um nome DNS. Nesse caso, o IP público é associado ao recurso do Balanceador de Carga. Dessa forma, as regras do Balanceador de Carga e as regras NAT de entrada usarão o endereço IP público como o ponto de extremidade da Internet para os recursos que estão recebendo o tráfego de rede com balanceamento de carga.

Um recurso da interface de rede (NIC) contém a configuração do endereço IP (IP público ou privado) para uma máquina virtual. Quando uma NIC for adicionada a um pool de endereços IP de back-end do Balanceador de Carga, o Balanceador começará a enviar o tráfego de rede com carga balanceada baseado nas regras de balanceamento de carga criadas.

Esta é uma representação gráfica de um Balanceador de Carga no Gerenciador de Recursos:

![Balanceador de Carga no Gerenciador de Recursos](./media/load-balancer-overview/arm-lb.png)

## Recursos do balanceador de carga

### Distribuição baseada em hash

O Balanceador de Carga do Azure usa um algoritmo de distribuição baseado em hash. Por padrão, ele usa um hash de tupla 5 (IP de origem, porta de origem, IP de destino, porta de destino, tipo de protocolo) para mapear o tráfego para os servidores disponíveis. Ele fornece permanência somente dentro de uma sessão de transporte. Os pacotes na mesma sessão TCP ou UDP serão direcionados para a mesma instância do DIP (IP do Datacenter), atrás do ponto de extremidade com balanceamento de carga. Quando o cliente fecha e reabre a conexão ou inicia uma nova sessão no mesmo IP de origem, a porta de origem é alterada. Isso pode fazer com que o tráfego vá para um ponto de extremidade DIP diferente.


Para obter mais detalhes, consulte [Modo de distribuição do Balanceador de Carga](load-balancer-distribution-mode.md).

Este gráfico ilustra uma distribuição baseada em hash:

![Distribuição baseada em hash](./media/load-balancer-overview/load-balancer-distribution.png)

### Encaminhamento de porta

O Balanceador de Carga do Azure oferece-lhe controle sobre como a comunicação de entrada é gerenciada. Essa comunicação pode incluir o tráfego que é iniciado a partir de hosts da Internet ou máquinas virtuais em outros serviços de nuvem ou redes virtuais. Esse controle é representado por um ponto de extremidade (também chamado de ponto de extremidade de entrada).

Um ponto de extremidade escuta em uma porta pública e encaminha o tráfego para uma porta interna. Você pode mapear as mesmas portas para um ponto de extremidade interno ou externo ou usar uma porta diferente para eles. Por exemplo: você pode ter uma escuta configurada no servidor Web para a porta 81 enquanto o mapeamento do ponto de extremidade público é a porta 80. A criação de um ponto de extremidade público inicializa a criação de uma instância do Balanceador de Carga do Azure.

O uso e a configuração padrão dos pontos de extremidade em uma máquina virtual que você cria com o portal do Azure são para o protocolo RDP e o tráfego de sessão remota do Windows PowerShell. Você pode usar esses pontos de extremidade para administrar remotamente a máquina virtual pela Internet.


### Reconfiguração automática

O Balanceador de Carga do Azure reconfigura-se instantaneamente quando você aumenta ou diminui as instâncias. Por exemplo, essa reconfiguração pode acontecer quando você aumenta a contagem de instâncias da função de trabalho/Web ou quando coloca máquinas virtuais adicionais no mesmo conjunto de balanceamento de carga.


### Monitoramento do serviço
O Balanceador de Carga do Azure oferece a capacidade de investigar a integridade de várias instâncias do servidor. Quando uma investigação não responde, o Balanceador de Carga interrompe o envio de novas conexões para as instâncias não íntegras. As conexões existentes não são afetadas.

Existem três tipos de investigação com suporte:

- Investigação de agente convidado (nas VMs de PaaS somente). O Balanceador de Carga do Azure utiliza o agente convidado dentro da máquina virtual. Ele escuta e responde com uma resposta HTTP 200 OK somente quando a instância está no estado Pronto (ou seja, a instância não está em um estado Ocupado, Reciclando ou Interrompendo). Se o agente convidado não responder com HTTP 200 OK, o Balanceador de Carga do Azure marcará a instância como sem resposta e interromperá o envio do tráfego para ela. O Balanceador de Carga continuará a executar o ping para a instância. Se o agente convidado responder com um HTTP 200, o Balanceador de Carga enviará o tráfego para essa instância novamente. Quando você está usando uma função web, o código do site geralmente é executado no w3wp.exe, que não é monitorado pela malha do Azure nem pelo agente convidado. Isso significa que as falhas no w3wp.exe (por exemplo, as respostas HTTP 500) não serão relatadas para o agente convidado e o Balanceador de Carga não saberá retirar essa instância da rotação.

- Investigação personalizada do HTTP. A investigação personalizada do Balanceador de Carga substitui a investigação padrão (agente convidado). Você pode usá-la para criar sua própria lógica personalizada para determinar a integridade da instância de função. O Balanceador de Carga investigará regularmente seu ponto de extremidade (a cada 15 segundos, por padrão). A instância será considerada em rotação se responder com um TCP ACK ou HTTP 200 dentro do período de tempo limite (padrão de 31 segundos). Isso pode ser útil para implementar sua própria lógica para remover as instâncias da rotação do Balanceador de Carga. Por exemplo, você pode configurar a instância para retornar um status não 200 se a instância estiver acima de 90% da CPU. Para as funções da Web que usam o w3wp.exe, você também obtém um monitoramento automático do seu site, pois as falhas no código do site retornarão um status não 200 para a investigação do Balanceador de Carga.

- Investigação personalizada do TCP. Investigações de TCP dependem do estabelecimento bem-sucedido da sessão de TCP em uma porta de investigação definida.

Para obter mais informações, consulte [Investigação da integridade do Balanceador de Carga](https://msdn.microsoft.com/library/azure/jj151530.aspx).

### NAT de Origem


Todo o tráfego de saída para a Internet proveniente de seu serviço passa pela SNAT (NAT de Origem), que usa o mesmo endereço VIP do tráfego de entrada. A SNAT fornece benefícios importantes:

- Permite uma fácil atualização e recuperação de desastres dos serviços, já que o VIP pode ser mapeado dinamicamente para outra instância do serviço.

- Facilita o gerenciamento de ACLs, já que a ACL pode ser expressa em termos de VIPs e, portanto, não mudará quando os serviços escalarem verticalmente, ou forem reimplantados.

A configuração do Balanceador de Carga do Azure oferece suporte ao Cone NAT completo para UDP. Cone NAT completo é um tipo de NAT em que a porta permite conexões de entrada de qualquer host externo (em resposta a uma solicitação de saída).


>[AZURE.NOTE] Para cada nova conexão de saída que inicia uma VM, uma porta de saída também é alocada pelo Balanceador de Carga do Azure. O host externo verá o tráfego chegando como uma porta alocada do IP virtual (VIP). Se os cenários exigirem um grande número de conexões de saída, recomendamos que as VMs usem endereços IP públicos em nível de instância para que tenham um endereço IP de saída dedicado para a SNAT. Isso reduzirá o risco de esgotamento de porta.
>
>O número máximo de portas que podem ser usadas por VIP ou IP público em nível de instância (PIP) é 64.000. Essa é uma limitação de padrão de TCP.


**Suporte para vários endereços IP com balanceamento de carga para máquinas virtuais**

Você pode ter mais de um endereço IP público com balanceamento de carga atribuído a um conjunto de máquinas virtuais. Com esse recurso, você pode hospedar vários sites SSL e/ou ouvintes do Grupo de Disponibilidade AlwaysOn do SQL Server no mesmo conjunto de máquinas virtuais. Saiba mais em [Vários VIPs por serviço de nuvem](load-balancer-multivip.md).

**Implantações baseadas em modelo por meio do Gerenciador de Recursos do Azure**

O Gerenciador de Recursos do Azure é a nova estrutura de gerenciamento dos serviços no Azure. Agora, o Balanceador de Carga do Azure pode ser gerenciado usando ferramentas e APIs baseadas no Gerenciador de Recursos. Para saber mais sobre o Gerenciador de Recursos, confira [O IaaS ficou mais fácil com o Gerenciador de Recursos do Azure](https://azure.microsoft.com/blog/2015/04/29/iaas-just-got-easier-again/).


## Próximas etapas

[Visão geral do balanceador de carga para a Internet](load-balancer-internet-overview.md)

[Visão geral do balanceador de carga interno](load-balancer-internal-overview.md)

[Introdução à criação de um balanceador de carga para a Internet](load-balancer-internet-getstarted.md)

<!---HONumber=AcomDC_0128_2016-->