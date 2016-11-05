
---
title: Visão geral do balanceador de carga interno | Microsoft Docs
description: Visão geral do balanceador de carga interno e seus recursos. Como um balanceador de carga funciona no Azure e possíveis cenários para configurar pontos de extremidade internos
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
# Visão geral do balanceador de carga interno
Ao contrário do balanceador de carga voltado para a Internet, o ILB (Balanceador de Carga Interno) usa somente os recursos dentro do serviço de nuvem ou usa VPN para acessar a infraestrutura do Azure. A infraestrutura restringe o acesso aos VIPs (endereços IP virtuais) de balanceamento de carga de um Serviço de nuvem ou uma Rede virtual, de modo que nunca será exposta diretamente a um ponto de extremidade da Internet. Isso permite que aplicativos de linha de negócios internos sejam executados no Azure e acessados na nuvem ou nos recursos locais.

## Cenários para balanceador de carga interno
O ILB (Balanceamento de Carga Interno) do Azure fornece balanceamento de carga entre máquinas virtuais que residem em um serviço de nuvem ou em uma rede virtual com escopo regional. Para obter informações sobre o uso e a configuração de redes virtuais com escopo regional, consulte [Redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) no blog do Azure. Redes virtuais existentes configuradas para um grupo de afinidade não podem usar o ILB.

O ILB permite os seguintes cenários:

* Dentro de um serviço de nuvem, de máquinas virtuais a um conjunto de máquinas virtuais que residem no mesmo serviço de nuvem (veja a Figura 1).
* Em uma rede virtual, de máquinas virtuais na rede virtual a um conjunto de máquinas virtuais que residem no mesmo serviço de nuvem da rede virtual (veja a Figura 2).
* Para uma rede virtual entre instalações, de computadores locais a um conjunto de máquinas virtuais que residem no mesmo serviço de nuvem da rede virtual (veja a Figura 3).
* Aplicativos para a Internet de diversas camadas, cujas camadas de back-end não são para a Internet, mas exigem balanceamento de carga para tráfego da camada para a Internet.
* Balanceamento de carga para aplicativos de linha de negócios (LOB) hospedados no Azure sem exigir hardware ou software do balanceador de carga adicional. Incluindo servidores locais, no conjunto de computadores, cujo tráfego é de balanceamento de carga.

## Aplicativos para a Internet de diversas camadas
A camada Web tem pontos de extremidade para a Internet para clientes de Internet e faz parte de um conjunto de balanceamento de carga. O balanceador de carga distribui o tráfego de entrada de clientes Web para a porta TCP 443 (HTTPS) até os servidores Web.

Os servidores de banco de dados estão protegidos por um ponto de extremidade ILB que os servidores Web usam para armazenamento. Esse ponto de extremidade de balanceamento de carga do serviço de banco de dados, cujo tráfego é de carga balanceada entre servidores de banco de dados do conjunto ILB.

A imagem a seguir descreve o aplicativo para a Internet de diversas camadas no mesmo serviço de nuvem.

A figura 1

![Serviço de nuvem único de balanceamento de carga interno](./media/load-balancer-internal-overview/IC736321.png)

Outro cenário possível para um aplicativo com diversas camadas é quando o ILB é implantado em um serviço de nuvem diferente daquele que consome o serviço para o ILB.

Serviços de nuvem que usam a mesma rede virtual terão acesso ao ponto de extremidade ILB.

Você pode ver na imagem abaixo que os servidores Web front-end estão em um serviço de nuvem diferente do back-end do banco de dados e aproveitando o ponto de extremidade ILB dentro da mesma rede virtual.

Figura 2

![Balanceamento de carga interno entre serviços de nuvem](./media/load-balancer-internal-overview/IC744147.png)

## Aplicativos LOB (linha de negócios)
Tráfego de clientes em redes locais tem balanceamento de carga em um conjunto de servidores LOB que usam conexão VPN com a rede do Azure.

O computador cliente terá acesso a um endereço IP do serviço de VPN do Azure, usando VPN ponto a site. Ela permitirá o uso do aplicativo LOB hospedado atrás do ponto de extremidade ILB.

A figura 3

![Balanceamento de carga interno usando VPN ponto a site](./media/load-balancer-internal-overview/IC744148.png)

Outro cenário para LOB é ter uma VPN site a site na rede virtual na qual o ponto de extremidade ILB está configurado. Isso permitirá que o tráfego da rede local seja roteado para o ponto de extremidade ILB.

Figura 4

![Balanceamento de carga interno usando VPN site a site](./media/load-balancer-internal-overview/IC744150.png)

## Próximas etapas
[Introdução à configuração de um balanceador de carga para a Internet](load-balancer-get-started-internet-arm-ps.md)

[Introdução à configuração de um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0831_2016-->