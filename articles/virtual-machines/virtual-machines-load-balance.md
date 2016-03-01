<properties
	pageTitle="Balanceamento de carga para serviços de infraestrutura | Microsoft Azure"
	description="Descreve os dois tipos diferentes de balanceamento de carga com suporte do Azure: Balanceador de carga para serviços em nuvem e o Azure Traffic Manager para o tráfego do cliente."
	services="virtual-machines"
	documentationCenter=""
	authors="joaoma"
	manager="carmonm"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="joaoma"/>


# Balanceamento de carga para serviços de infraestrutura do Azure#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Existem dois níveis de balanceamento de carga para serviços de infraestrutura do Azure:

- **Nível DNS**: balanceamento de carga para tráfego de diferentes serviços de nuvem localizados em diferentes datacenters, para diferentes sites do Azure localizados em diferentes datacenters ou para pontos de extremidade externos. Isso é feito com o Gerenciador de Tráfego e o método de balanceamento de carga Round Robin do Azure.
- **Nível de rede**: balanceamento de carga de tráfego da Internet para diferentes máquinas virtuais de um serviço de nuvem ou balanceamento de carga de tráfego entre máquinas virtuais em um serviço de nuvem ou rede virtual. Isso é feito com balanceador de carga do Azure.

## Balanceamento de carga do Gerenciador de Tráfego para serviços de nuvem e sites##

O Gerenciador de Tráfego permite que você controle a distribuição de tráfego de usuário para os pontos de extremidade, o que pode incluir serviços de nuvem, sites, sites externos e outros perfis do Gerenciador de Tráfego. O Gerenciador de Tráfego funciona aplicando um mecanismo de políticas inteligente às consultas DNS (Domain Name System) para os nomes de domúnio de seus recursos da Internet. Seus serviços de nuvem ou sites podem ser executados em datacenters diferentes em todo o mundo.

Você deve usar tanto o REST como o Windows PowerShell para configurar os pontos de extremidade externos ou os perfis do Gerenciador de Tráfego como pontos de extremidade.

O Gerenciador de Tráfego usa três métodos de balanceamento de carga para distribuir o tráfego:

- **Failover**: use este método quando quiser usar um ponto de extremidade primário para todo o tráfego, mas fornecer backups caso o primário se torne indisponível.
- **Desempenho**: use este método quando houver pontos de extremidade em locais geográficos diferentes e você quiser solicitar aos clientes que usem o ponto de extremidade “mais próximo” em termos de menor latência.
- **Round Robin:** use este método quando quiser distribuir a carga através de um conjunto de serviços de nuvem no mesmo datacenter ou através dos serviços de nuvem ou sites em diversos datacenters.

Para obter mais informações, consulte [Sobre métodos de balanceamento de carga do Gerenciador de Tráfego](../traffic-manager/traffic-manager-load-balancing-methods.md).

O diagrama a seguir mostra um exemplo do método de balanceamento de carga Round Robin para distribuir tráfego entre diferentes serviços de nuvem.

![loadbalancing](./media/virtual-machines-load-balance/TMSummary.png)

O processo básico é o seguinte:

1.	Um cliente da Internet consulta um nome de domínio correspondente ao serviço web.
2.	O DNS encaminha a solicitação de consulta do nome para o Gerenciador de Tráfego.
3.	O Gerenciador de Tráfego escolhe o próximo serviço de nuvem na lista do Round Robin e envia de volta o nome DNS. O servidor DNS do cliente da Internet soluciona o nome para um endereço IP e o envia para o cliente da Internet.
4.	O cliente da Internet se conecta ao serviço de nuvem escolhido pelo Gerenciador de Tráfego.

Para obter mais informações, consulte [Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md).

## Balanceamento de carga do Azure para máquinas virtuais ##

As máquinas virtuais no mesmo serviço de nuvem ou rede virtual pode comunicar entre si diretamente usando seus endereços de IP privados. Os computadores e serviços fora do serviço de nuvem ou rede virtual somente podem se comunicar com máquinas virtuais em um serviço de nuvem ou rede virtual com um ponto de extremidade configurado. Um ponto de extremidade é um mapeamento de um endereço IP público e porta para esse endereço IP privado e porta de uma máquina virtual ou função web dentro de um serviço de nuvem do Azure.

O Balanceador de Carga do Azure distribui randomicamente um tipo específico de tráfego de entrada através de várias máquinas virtuais ou serviços em uma configuração conhecida como conjunto balanceado de carga. Por exemplo, você pode difundir a carga de tráfego de solicitação da web em vários servidores web ou funções web.

O diagrama a seguir mostra um ponto de extremidade com carga balanceada para o tráfego de Web padrão (não criptografado) que é compartilhado entre as três máquinas virtuais para a porta TCP pública e privada 80. Essas três máquinas virtuais estão em um conjunto de balanceamento de carga.

![loadbalancing](./media/virtual-machines-load-balance/LoadBalancing.png)

Para obter mais informações, consulte [Balanceador de carga do Azure](../load-balancer/load-balancer-overview.md). Para conhecer as etapas para criar um conjunto balanceado de carga, consulte [Configurar um conjunto balanceador de carga](../load-balancer/load-balancer-internet-getstarted.md).

O Azure também pode balancear a carga dentro de um serviço de nuvem ou rede virtual. Isso é conhecido como balanceamento de carga interno e pode ser usado das seguintes maneiras:

- Para balancear a carga entre os servidores em diferentes camadas de um aplicativo de multi-camadas (por exemplo, entre as camadas da web e do banco de dados).
- Para balancear a carga de aplicativos de LOB (linha de negócios) hospedados no Azure sem exigir hardware ou software do balanceador de carga adicional.
- Para incluir servidores locais no conjunto de computadores cujo tráfego está balanceado por carga.

Semelhante ao balanceamento de carga do Azure, o balanceamento de carga interno é facilitado pela configuração de um conjunto de carga balanceada interno.

O diagrama a seguir mostra um exemplo de um ponto de extremidade de carga balanceada interna para um aplicativo de LOB (linha de negócios), que é compartilhado entre três máquinas virtuais em uma rede virtual entre locais.

![loadbalancing](./media/virtual-machines-load-balance/LOBServers.png)

## Considerações do Balanceador de carga

Um balanceador de carga é configurado por padrão para o tempo limite de uma sessão ociosa em 4 minutos. Se o seu aplicativo por trás de um balanceador de carga deixar uma conexão ociosa por mais de 4 minutos e não tiver uma configuração de Keep-Alive, a conexão será descartada. Você pode alterar o comportamento do balanceador de carga para permitir uma [configuração de tempo limite mais longa para o balanceador de carga do Azure](../load-balancer/load-balancer-tcp-idle-timeout.md).

Outra consideração é o tipo de modo de distribuição com suporte pelo Balanceador de Carga do Azure. Você pode configurar a afinidade de IP de origem (IP de origem, IP de destino) ou o protocolo de IP de origem (IP de origem, IP de destino e protocolo). Verifique o [Modo de distribuição do balanceador de carga do Azure (afinidade de IP de origem)](../load-balancer/load-balancer-distribution-mode.md) para obter mais informações.


## Próximas etapas

Para conhecer as etapas para criar um conjunto balanceado de carga, consulte [Configurar um conjunto balanceado de carga interno](../load-balancer/load-balancer-internal-getstarted.md).

Para obter mais informações sobre o balanceador de carga, confira [Balanceamento de carga interno](../load-balancer/load-balancer-internal-overview.md).

<!---HONumber=AcomDC_0218_2016-->