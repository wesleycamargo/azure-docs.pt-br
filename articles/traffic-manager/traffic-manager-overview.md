<properties 
   pageTitle="O que é o Gerenciador de Tráfego| Microsoft Azure"
   description="Este artigo o ajudará a entender o que é o Gerenciador de Tráfego e se é a opção de roteamento do tráfego correta para seu aplicativo"
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/09/2016"
   ms.author="jtuliani" />

# O que é o Gerenciador de Tráfego?

O Gerenciador de Tráfego do Microsoft Azure permite que você controle a distribuição do tráfego do usuário para seus pontos de extremidade do serviço em execução em diferentes data centers em todo o mundo.

O pontos de extremidade do serviço suportados pelo Gerenciador de Tráfego incluem as VMs do Azure, Aplicativos Web e serviços de nuvem. Você também pode usar o Gerenciador de Tráfego com pontos de extremidade externos e não do Azure.

O Gerenciador de Tráfego funciona usando o DNS (Sistema de Nomes de Domínio) para direcionar as solicitações do usuário final para o ponto de extremidade mais apropriado, com base no método de roteamento do tráfego configurado e na exibição atual da integridade do ponto de extremidade. Então, os clientes se conectam ao devido ponto de extremidade de serviço diretamente.

O Gerenciador de Tráfego suporta [vários métodos de roteamento do tráfego](traffic-manager-routing-methods.md) de acordo com as diferentes necessidades do aplicativo. O Gerenciador de Tráfego fornece [verificações de integridade do ponto de extremidade e failover automático do ponto de extremidade](traffic-manager-monitoring.md), permitindo que você crie aplicativos com alta disponibilidade que são resistentes a falhas, incluindo a falha de uma região inteira do Azure.

## Benefícios do Gerenciador de Tráfego

O Gerenciador de Tráfego pode ajudá-lo a:

- **Melhorar a disponibilidade dos aplicativos críticos** – o Gerenciador de Tráfego permite aumentar a disponibilidade de seus aplicativos críticos monitorando seus pontos de extremidade no Azure e fornecendo o failover automático quando um ponto de extremidade fica inativo.
- **Melhorar a capacidade de resposta dos aplicativos de alto desempenho** – o Azure permite executar serviços de nuvem ou sites em data centers espalhados pelo mundo inteiro. O Gerenciador de Tráfego pode melhorar a capacidade de resposta de seus aplicativos direcionando os usuários finais para o ponto de extremidade com a menor latência de rede do cliente.
- **Atualizar e realizar a manutenção do serviço sem inatividade** – você pode executar diretamente a atualização e outras operações de manutenção planejadas em seus aplicativos sem inatividade para os usuários finais usando o Gerenciador de Tráfego para direcionar o tráfego para os pontos de extremidade alternativos quando a manutenção está em andamento.
- **Combinar aplicativos locais e baseados na nuvem** – o Gerenciador de Tráfego suporta os pontos de extremidade externos e não do Azure habilitando seu uso com implantações locais e de nuvem híbridas, incluindo os cenários de "intermitência para a nuvem", "migrar para a nuvem" e "failover para a nuvem".
- **Distribuir o tráfego para implantações grandes e complexas** – os métodos de roteamento de tráfego podem ser combinados usando os [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md) para criar configurações do roteamento de tráfego sofisticadas e flexíveis para atender às necessidades de implantações maiores e mais complexas. 

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## Próximas etapas

- Saiba mais sobre [como o Gerenciador de Tráfego funciona](traffic-manager-how-traffic-manager-works.md).

- Saiba como desenvolver aplicativos de alta disponibilidade usando o [monitoramento do ponto de extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md).

- Saiba mais sobre os [métodos de roteamento do tráfego](traffic-manager-routing-methods.md) suportados pelo Gerenciador de Tráfego.

- [Criar um perfil do Gerenciador de Tráfego](traffic-manager-manage-profiles.md).
 

<!---HONumber=AcomDC_0615_2016-->