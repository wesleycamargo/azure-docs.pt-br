---
title: "Visão geral do Azure Load Balancer | Microsoft Docs"
description: "Visão geral dos recursos do Balanceador de Carga do Azure, arquitetura e implementação. Saiba como o balanceador de carga funciona e como aproveitá-lo na nuvem."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 0f313dc0-f007-4cee-b2b9-f542357925a3
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 2f0b7a04f4b732277abc65dd5fe0844d6e906496
ms.lasthandoff: 03/30/2017

---

# <a name="azure-load-balancer-overview"></a>Visão geral do Balanceador de Carga do Azure

O Balanceador de Carga do Azure oferece alta disponibilidade e desempenho de rede para seus aplicativos. É um balanceador de carga do tipo Camada 4 (TCP, UDP) que distribui o tráfego de entrada entre as instâncias de serviço íntegras definidas em um conjunto de balanceadores de carga.

O Azure Load Balancer pode ser configurado para:

* Balancear carga de tráfego de entrada na Internet para máquinas virtuais. Essa configuração é conhecida como [balanceamento de carga voltada para a Internet](load-balancer-internet-overview.md).
* Balanceie o tráfego de carga entre as máquinas virtuais em uma rede virtual, entre as máquinas virtuais nos serviços de nuvem ou entre os computadores locais e as máquinas virtuais em uma rede virtual entre as instalações. Essa configuração é conhecida como [balanceamento de carga interno](load-balancer-internal-overview.md).
* Encaminhe o tráfego externo para uma máquina virtual específica.

Todos os recursos na nuvem precisam de um endereço IP público para serem acessíveis pela Internet. A infraestrutura de nuvem no Azure usa endereços IP não roteáveis para seus recursos. O Azure usa a conversão de endereços de rede (NAT) com os endereços IP públicos para se comunicar com a Internet.

## <a name="azure-deployment-models"></a>Modelos de implantação do Azure

É importante entender as diferenças entre os [modelos de implantação](../azure-resource-manager/resource-manager-deployment-model.md)clássico e de Resource Manager do Azure. O Azure Load Balancer está configurado de forma diferente em cada modelo.

### <a name="azure-classic-deployment-model"></a>modelo de implantação clássico do Azure

As máquinas virtuais implantadas dentro de um limite de serviço de nuvem podem ser agrupadas para usar um balanceador de carga. Nesse modelo, um endereço IP público e um FQDN (Nome de Domínio Totalmente Qualificado) são atribuídos a um serviço de nuvem. O balanceador de carga fará a conversão da porta e balanceará a carga do tráfego da rede utilizando o endereço IP público do serviço de nuvem.

O tráfego de balanceamento de carga é definido por pontos de extremidade. Os pontos de extremidade de conversão da porta têm uma relação de um para um entre a porta pública atribuída do endereço IP público e a porta local atribuída ao serviço em uma máquina virtual específica. Os pontos de extremidade de balanceamento de carga são uma relação de um para muitos entre o endereço IP público e as portas locais atribuídas aos serviços nas máquinas virtuais no serviço de nuvem.

![Azure Load Balancer no modelo de implantação clássico](./media/load-balancer-overview/asm-lb.png)

Figura 1 – Azure Load Balancer no modelo de implantação clássico

O rótulo do domínio para o endereço IP público que um balanceador de carga usa nesse modelo de implantação é \<nome do serviço de nuvem\>.cloudapp.net. O gráfico a seguir mostra o Azure Load Balancer nesse modelo.

### <a name="azure-resource-manager-deployment-model"></a>modelo de implantação do Azure Resource Manager

No modelo de implantação do Resource Manager não é necessário criar um serviço de nuvem. O balanceador de carga é criado para rotear explicitamente o tráfego entre várias máquinas virtuais.

Um endereço IP público é um recurso individual que tem um rótulo de domínio (nome DNS). O endereço IP público é associado ao recurso do balanceador de carga. As regras do balanceador de carga e as regras NAT de entrada usam o endereço IP público como o ponto de extremidade da Internet para os recursos que estão recebendo o tráfego de rede com balanceamento de carga.

Um endereço IP privado ou público é atribuído ao recurso de interface de rede anexado à máquina virtual. Quando uma interface de rede é adicionada a um pool de endereços IP de back-end do balanceador de carga, o balanceador de carga consegue enviar o tráfego de rede com carga balanceada baseado nas regras de balanceamento de carga criadas.

O gráfico a seguir mostra o Azure Load Balancer nesse modelo:

![Azure Load Balancer no Gerenciador de Recursos](./media/load-balancer-overview/arm-lb.png)

Figura 2 – Azure Load Balancer no Resource Manager

O balanceador de carga pode ser gerenciado usando ferramentas, modelos e APIs baseadas no Resource Manager. Para saber mais sobre o Resource Manager, consulte [Resource Manager overview (Visão geral do Resource Manager)](../azure-resource-manager/resource-group-overview.md).

## <a name="load-balancer-features"></a>Recursos do balanceador de carga

* Distribuição baseada em hash

    O Balanceador de Carga do Azure usa um algoritmo de distribuição baseado em hash. Por padrão, ele usa um hash de 5 tuplas composto por IP de origem, porta de origem, IP de destino, porta de destino e tipo de protocolo para mapear o tráfego até os servidores disponíveis. Ele fornece permanência somente *dentro* de uma sessão de transporte. Os pacotes na mesma sessão TCP ou UDP serão direcionados para a mesma instância atrás do ponto de extremidade de balanceamento de carga. Quando o cliente fecha e reabre a conexão ou inicia uma nova sessão no mesmo IP de origem, a porta de origem é alterada. Isso pode fazer com que o tráfego vá para um ponto de extremidade diferente em um datacenter diferente.

    Para obter mais detalhes, consulte [Modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md). O gráfico a seguir mostra a distribuição baseada em hash:

    ![Distribuição baseada em hash](./media/load-balancer-overview/load-balancer-distribution.png)

    Figura 3 – Distribuição baseada em hash

* Encaminhamento de porta

    O Balanceador de Carga do Azure oferece-lhe controle sobre como a comunicação de entrada é gerenciada. Essa comunicação inclui o tráfego iniciado de hosts da Internet ou de máquinas virtuais em outros serviços de nuvem ou redes virtuais. Esse controle é representado por um ponto de extremidade (também chamado de ponto de extremidade de entrada).

    Um ponto de extremidade de entrada escuta em uma porta pública e encaminha o tráfego para uma porta interna. Você pode mapear as mesmas portas para um ponto de extremidade interno ou externo ou usar uma porta diferente para eles. Por exemplo, você pode ter uma escuta configurada no servidor Web para a porta 81 enquanto o mapeamento do ponto de extremidade público é a porta 80. A criação de um ponto de extremidade público dispara a criação de uma instância do balanceador de carga.

    Quando ele é criado usando o portal do Azure, o portal cria automaticamente pontos de extremidade na máquina virtual para o protocolo RDP (Remote Desktop Protocol) e para o tráfego de sessão remota do Windows PowerShell. Você pode usar esses pontos de extremidade para administrar remotamente a máquina virtual pela Internet.

* Reconfiguração automática

    O Balanceador de Carga do Azure reconfigura-se instantaneamente quando você aumenta ou diminui as instâncias. Por exemplo, essa reconfiguração acontece quando você aumenta a contagem de instâncias da função de trabalho/Web em um serviço de nuvem ou quando coloca máquinas virtuais adicionais no mesmo conjunto de balanceamento de carga.

* Monitoramento do serviço

    O Azure Load Balancer pode testar a integridade de várias instâncias de servidor. Quando uma investigação não responde, o balanceador de carga interrompe o envio de novas conexões para as instâncias não íntegras. As conexões existentes não são afetadas.

    Existem três tipos de investigação com suporte:

    + **Investigação de agente convidado (somente em máquinas virtuais de plataforma como serviço):** o balanceador de carga utiliza o agente convidado dentro da máquina virtual. O agente convidado escuta e responde com uma resposta HTTP 200 OK somente quando a instância está no estado Pronto (ou seja, a instância não está em um estado ocupado, reciclando ou interrompendo). Se o agente não responder com HTTP 200 OK, o balanceador de carga marcará a instância como sem resposta e interromperá o envio de tráfego para essa instância. O balanceador de carga continuará executando o ping nessa instância. Se o agente convidado responder com um HTTP 200, o balanceador de carga enviará o tráfego para essa instância novamente. Quando você está usando uma função web, o código do site geralmente é executado no w3wp.exe, que não é monitorado pela malha do Azure nem pelo agente convidado. Isso significa que as falhas no w3wp.exe (por exemplo, as respostas HTTP 500) não serão relatadas para o agente convidado e o balanceador de carga não saberá retirar essa instância da rotação.
    + **Investigação HTTP personalizada:** essa investigação substitui a investigação padrão (agente convidado). Você pode usá-la para criar sua própria lógica personalizada para determinar a integridade da instância de função. O balanceador de carga investigará regularmente seu ponto de extremidade (a cada 15 segundos, por padrão). A instância é considerada em rotação se responder com um TCP ACK ou HTTP 200 dentro do período de tempo limite (padrão de 31 segundos). Isso é útil para implementar sua própria lógica para remover as instâncias da rotação do balanceador de carga. Por exemplo, você pode configurar a instância para retornar um status não 200 se a instância estiver acima de 90% da CPU. Para as funções da Web que usam o w3wp.exe, você também obtém um monitoramento automático do seu site, pois as falhas no código do site retornam um status não 200 para a investigação.
    + **Investigação TCP personalizada:** essa investigação depende do estabelecimento bem-sucedido da sessão de TCP em uma porta de investigação definida.

    Para obter mais informações, consulte o [esquema LoadBalancerProbe](https://msdn.microsoft.com/library/azure/jj151530.aspx).

* NAT de Origem

    Todo o tráfego de saída para a Internet proveniente de seu serviço passa pela SNAT (NAT de origem), que usa o mesmo endereço VIP do tráfego de entrada. A SNAT fornece benefícios importantes:

    + Permite uma fácil atualização e recuperação de desastres dos serviços, já que o VIP pode ser mapeado dinamicamente para outra instância do serviço.
    + Facilita o gerenciamento de ACL (lista de controle de acesso). ACLs expressadas em termos de VIPs não mudam à medida que os serviços são escalados verticalmente ou reimplantados.

    A configuração do balanceador de carga dá suporte para NAT de cone completo para UDP. Cone NAT completo é um tipo de NAT em que a porta permite conexões de entrada de qualquer host externo (em resposta a uma solicitação de saída).

    Para cada nova conexão de saída que inicia uma máquina virtual, uma porta de saída também é alocada pelo balanceador de carga. O host externo vê o tráfego com uma porta alocada do IP virtual (VIP). Para cenários que exigem um grande número de conexões de saída, recomendamos o uso de endereços [IP públicos em nível de instância](../virtual-network/virtual-networks-instance-level-public-ip.md) , para que as VMs tenham um endereço IP de saída dedicado para SNAT. Isso reduz o risco de esgotamento da porta.

    Consulte [conexões de saída](load-balancer-outbound-connections.md) para obter mais detalhes sobre esse tópico.

### <a name="support-for-multiple-load-balanced-ip-addresses-for-virtual-machines"></a>Suporte para vários endereços IP com balanceamento de carga para máquinas virtuais
Você pode atribuir mais de um endereço IP público com balanceamento de carga a um conjunto de máquinas virtuais. Com esse recurso, você pode hospedar vários sites SSL e/ou ouvintes do Grupo de Disponibilidade AlwaysOn do SQL Server no mesmo conjunto de máquinas virtuais. Para obter mais informações, consulte [Vários VIPs por serviço de nuvem](load-balancer-multivip.md).

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>Próximas etapas

[Visão geral do balanceador de carga para a Internet](load-balancer-internet-overview.md)

[Visão geral do balanceador de carga interno](load-balancer-internal-overview.md)

[Introdução à criação de um balanceador de carga para a Internet](load-balancer-get-started-internet-arm-ps.md)


