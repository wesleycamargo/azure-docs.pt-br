
<properties 
   pageTitle="Visão geral do balanceador de carga para a Internet | Microsoft Azure"
   description="Visão geral do balanceador de carga para a Internet e seus recursos. Como um balanceador de carga funciona no Azure usando máquinas virtuais e serviços de nuvem."
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
   ms.date="10/16/2015"
   ms.author="joaoma" />


# Balanceador de carga para a Internet entre várias Máquinas Virtuais ou serviços

Um uso de pontos de extremidade é a configuração do balanceador de carga do Azure para distribuir um tipo específico de tráfego entre várias máquinas virtuais ou serviços. Por exemplo, você pode difundir a carga de tráfego de solicitação da web em vários servidores web ou funções web.

O balanceador de carga do Azure mapeia o endereço IP público e o número da porta do tráfego de entrada até o endereço IP privado e o número da porta da máquina virtual e vice-versa no tráfego de resposta da máquina virtual.

>[AZURE.NOTE]O balanceador de carga do Azure fornecerá um tráfego de rede de distribuição de hash entre várias instâncias de máquina virtual usando as configurações padrão (para obter mais informações sobre a distribuição de hash, veja [Recursos do balanceador de carga](load-balancer-overview.md#load-balancer-features). Se você estiver procurando por afinidade de sessão, confira [modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md).

Para um serviço de nuvem que contenha instâncias de funções web ou funções de trabalho, você pode definir um ponto de extremidade público na definição do serviço (.csdef).
 
O arquivo servicedefinition.csdef contém a configuração do ponto de extremidade e quando existem várias instâncias de função para uma implantação de função de trabalho ou na Web, o balanceador de carga é instalado para ele. A maneira de adicionar instâncias à sua implantação de nuvem está alterando a contagem de instâncias no arquivo de configuração de serviço (.csfg).

A figura a seguir mostra um ponto de extremidade de balanceamento de carga para tráfego criptografado na Web, que é compartilhado entre três máquinas virtuais para a porta TCP pública e privada de 443. Essas três máquinas virtuais estão em um conjunto de balanceamento de carga.


![exemplo de balanceador de carga público](./media/load-balancer-internet-overview/IC727496.png))



Quando os clientes da Internet enviam solicitações de página da Web para o endereço IP público do serviço de nuvem e a porta TCP 443, o balanceador de carga executa um balanceamento de carga baseado em hash dessas solicitações entre as três máquinas virtuais no conjunto de balanceamento de carga. Você pode obter mais informações sobre o algoritmo do balanceador de carga na página [Visão geral do balanceador de carga](load-balancer-overview.md#load-balancer-features).


## Próximas etapas

[Visão geral do balanceador de carga interno](load-balancer-internal-overview.md)

[Introdução à configuração de um balanceador de carga para a Internet](load-balancer-internet-getstarted.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)


 

<!---HONumber=Nov15_HO1-->