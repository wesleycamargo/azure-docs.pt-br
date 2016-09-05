<properties
   pageTitle="Suporte do Gerenciador de Recursos do Azure para visualização do balanceador de carga | Microsoft Azure "
   description="Usando PowerShell para o balanceador de carga com o ARM (Gerenciador de Recursos do Azure) na visualização. Usando modelos de balanceador de carga"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />


# Suporte do Gerenciador de Recursos do Azure para balanceador de carga

O ARM (Gerenciador de Recursos do Azure) é a nova estrutura de gerenciamento de serviços do Azure. O balanceador de carga do Azure agora pode ser gerenciado usando ferramentas e APIs baseadas no Gerenciador de Recursos do Azure.

## Conceitos

Com ARM, o balanceador de carga do Azure contém os recursos filho a seguir:

- Configuração de IP front-end – um balanceador de carga pode incluir um ou mais endereços IP front-end, também conhecidos como VIPs (IPs virtuais). Esses endereços IP servem como entrada para o tráfego.

- Pool de endereços back-end – são endereços IP associados a NIC (Placa de Interface de Rede) de máquina virtual nos quais a carga será distribuída.

- Regras de balanceamento de carga – uma propriedade de regra mapeia determinada combinação de porta e IP front-end para um conjunto de endereços IP back-end e combinação de portas. Com uma única definição de um recurso de balanceador de carga, você pode definir várias regras de balanceamento de carga, cada regra refletindo uma combinação de um IP de front-end e porta e outra combinação de IP de back-end e porta, ambas associadas a VMs.

- Investigações – as investigações permitem que você controle a integridade das instâncias VM. Se um teste de integridade falhar, a instância VM será retirada automaticamente do rodízio.

- Regras NAT de entrada – regras NAT definem o tráfego de entrada que flui pelo IP front-end e é distribuído para o IP back-end.


![](./media/load-balancer-arm/load-balancer-arm.png)



## Modelos de início rápido

O Gerenciador de Recursos do Azure permite que você provisione seus aplicativos usando um modelo declarativo. Em um modelo único, você pode implantar vários serviços, juntamente com suas dependências. Use o mesmo modelo para implantar repetidamente seu aplicativo durante cada estágio do ciclo de vida do aplicativo

Modelos incluem máquinas virtuais, redes virtuais, conjuntos de disponibilidade, NICs (Interfaces de Rede), contas de armazenamento, balanceadores de carga, grupos de segurança de rede e IPs públicos. Com modelos, você pode criar tudo o que é necessário para um aplicativo complexo, usando um arquivo simples que você pode marcar e com o qual colaborar.

[Saiba mais sobre modelos](http://go.microsoft.com/fwlink/?LinkId=544798)

[Saiba mais sobre recursos de rede](../virtual-network/resource-groups-networking.md)

Modelos que usam o balanceador de carga do Azure podem ser encontrados em um [repositório GitHub](https://github.com/Azure/azure-quickstart-templates) que hospeda um conjunto de modelos gerados pela comunidade

Exemplos de modelos:

- [2 VMs em um balanceador de carga e regras de balanceamento de carga](http://go.microsoft.com/fwlink/?LinkId=544799)

- [2 VMs em uma VNET com as regras de um balanceador de carga interno e regras do balanceador de carga](http://go.microsoft.com/fwlink/?LinkId=544800)

- [2 VMs em um balanceador de carga e regras NAT configuradas no balanceador de carga](http://go.microsoft.com/fwlink/?LinkId=544801)


## Configurando o balanceador de carga do Azure com PowerShell ou CLI

[Cmdlets de rede do Azure](https://msdn.microsoft.com/library/azure/mt163510.aspx) podem ser usados para criar um balanceador de carga. Introdução aos cmdlets de ARM e APIs REST

- [Como criar um balanceador de carga usando o gerenciador de recursos do Azure](load-balancer-get-started-internet-arm-ps.md)

- [Usando a CLI do Azure com o gerenciamento de recursos do Azure](../xplat-cli-azure-resource-manager.md)

- [APIs REST do balanceador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## Próximas etapas

Também é possível [começar a criar um balanceador de carga para a Internet](load-balancer-get-started-internet-arm-ps.md) e configurar o tipo de [modo de distribuição](load-balancer-distribution-mode.md) para um comportamento específico de tráfego de rede do balanceador de carga.

Se seu aplicativo precisar manter conexões ativas para servidores por trás de um balanceador de carga, você poderá entender mais sobre as [configurações de tempo limite de TCP ocioso para um balanceador de carga](load-balancer-tcp-idle-timeout.md). Assim, você saberá mais sobre o comportamento da conexão ociosa quando estiver usando um Balanceador de Carga do Azure.

<!---HONumber=AcomDC_0824_2016-->