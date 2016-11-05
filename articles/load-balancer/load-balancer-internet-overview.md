
---
title: Visão geral do balanceador de carga para a Internet | Microsoft Docs
description: Visão geral do balanceador de carga para a Internet e seus recursos. Como um balanceador de carga funciona no Azure usando máquinas virtuais e serviços de nuvem.
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: tysonn

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2016
ms.author: sewhee

---
# Visão geral do balanceador de carga para a Internet
O balanceador de carga do Azure mapeia o endereço IP público e o número da porta do tráfego de entrada até o endereço IP privado e o número da porta da máquina virtual e vice-versa no tráfego de resposta da máquina virtual. As regras de balanceamento de carga permitem que você distribua tipos específicos de tráfego entre várias máquinas virtuais ou serviços. Por exemplo, você pode difundir a carga de tráfego de solicitação da web em vários servidores web ou funções web.

> [!NOTE]
> O Azure Load Balancer fornecerá um tráfego de rede de distribuição de hash entre várias instâncias de máquina virtual usando as configurações padrão (para obter mais informações sobre a distribuição de hash, veja [Recursos do balanceador de carga](load-balancer-overview.md#load-balancer-features). Se você estiver procurando por afinidade de sessão, confira [modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md).
> 
> 

Para um serviço de nuvem que contenha instâncias de funções web ou funções de trabalho, você pode definir um ponto de extremidade público no arquivo de definição de serviço (.csdef).

O arquivo servicedefinition.csdef contém a configuração do ponto de extremidade e quando existem várias instâncias de função para uma implantação de função de trabalho ou na Web, o balanceador de carga é instalado para ele. A maneira de adicionar instâncias à sua implantação de nuvem está alterando a contagem de instâncias no arquivo de configuração de serviço (.csfg).

A figura a seguir mostra um ponto de extremidade de balanceamento de carga para tráfego criptografado na Web, que é compartilhado entre três máquinas virtuais para a porta TCP pública e privada de 443. Essas três máquinas virtuais estão em um conjunto de balanceamento de carga.

![exemplo de balanceador de carga público](./media/load-balancer-internet-overview/IC727496.png))

Quando os clientes da Internet enviam solicitações de página da Web para o endereço IP público do serviço de nuvem na porta TCP 443, o Azure Load Balancer distribui as solicitações entre as três máquinas virtuais no conjunto de balanceamento de carga. Você pode obter mais informações sobre o algoritmo do balanceador de carga na página [Visão geral do balanceador de carga](load-balancer-overview.md#load-balancer-features).

## Próximas etapas
Depois de aprender sobre um balanceador de carga voltado para a Internet, você também pode ler sobre [balanceadores de carga internos](load-balancer-internal-overview.md) para entender melhor qual balanceador de carga é mais adequado para sua implantação na nuvem.

Também é possível [começar a criar um balanceador de carga para a Internet](load-balancer-get-started-internet-arm-ps.md) e configurar o tipo de [modo de distribuição](load-balancer-distribution-mode.md) para um comportamento específico de tráfego de rede do balanceador de carga.

Se seu aplicativo precisar manter conexões ativas para servidores por trás de um balanceador de carga, você poderá entender mais sobre as [configurações de tempo limite de TCP ocioso para um balanceador de carga](load-balancer-tcp-idle-timeout.md). Assim, você saberá mais sobre o comportamento da conexão ociosa quando estiver usando um Balanceador de Carga do Azure.

<!---HONumber=AcomDC_0831_2016-->