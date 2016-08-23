<properties
   pageTitle="Introdução ao Application Gateway | Microsoft Azure"
   description="Esta página fornece uma visão geral do serviço Application Gateway para balanceamento de carga de camada 7, incluindo tamanhos de gateway, balanceamento de carga HTTP, afinidade de sessão baseada em cookie e descarregamento de SSL."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="gwallace"/>

# Visão geral do Application Gateway

O Microsoft Azure Application Gateway fornece uma solução de balanceamento de carga HTTP gerenciada pelo Azure com base no balanceamento de carga de camada 7.

O balanceamento de carga do aplicativo permite que os desenvolvedores e administradores de TI criem regras de roteamento de tráfego de rede com base em HTTP. O serviço do Application Gateway é altamente disponível e medido. Para ver o SLA e o Preço, confira as páginas [SLA](https://azure.microsoft.com/support/legal/sla/) e [Preço](https://azure.microsoft.com/pricing/details/application-gateway/).

Atualmente, o Application Gateway dá suporte à entrega de aplicativo de camada 7 para o seguinte:

- Balanceamento de carga HTTP
- Afinidade de sessão baseada em cookie
- [Descarregamento do protocolo SSL](application-gateway-ssl-arm.md)
- [Roteamento de conteúdo baseado em URL](application-gateway-url-route-overview.md)
- [Roteamento de vários sites](application-gateway-multi-site-overview.md)

## Balanceamento de carga de camada 7 HTTP

O Azure fornece o balanceamento de carga de camada 4 por meio do Azure Load Balancer funcionando no nível de transporte (TCP/UDP) e com toda a carga do tráfego de rede de entrada balanceada para o serviço do Application Gateway. O Application Gateway aplica as regras de roteamento ao tráfego HTTP, fornecendo o balanceamento de carga de camada 7 (HTTP). Quando você cria um Application Gateway, um ponto de extremidade (VIP) é associado e usado como o IP público para o tráfego de rede de entrada.

O Application Gateway roteará o tráfego HTTP com base em sua configuração seja uma máquina virtual, serviço de nuvem, aplicativo Web ou um endereço IP externo.

O balanceamento de carga de camada 7 HTTP é útil para:

- Os aplicativos que exijam solicitações da mesma sessão de usuário/cliente para acessar a mesma máquina virtual de back-end. Exemplos desses aplicativos são os aplicativos de carrinho de compras e servidores de email na Web.
- Aplicativos que deseja liberar de farms do servidor Web da sobrecarga da terminação SSL.
- Aplicativos, como uma Rede de Distribuição de Conteúdo, que exigem que várias solicitações HTTP na mesma conexão TCP de execução longa sejam roteadas ou balanceadas por carga para diferentes servidores back-end.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]


## Instâncias e tamanhos de gateway

O Application Gateway atualmente é oferecido em 3 tamanhos: Pequeno, Médio e Grande. Os tamanhos de instância pequenos são destinados a cenários de desenvolvimento e teste.

Você pode criar até 50 Application Gateways por assinatura e cada um deles pode ter até 10 instâncias. O balanceamento de carga do Application Gateway como um serviço gerenciado pelo Azure permite o provisionamento de um balanceador de carga de camada 7 por trás do balanceador de carga de software do Azure.

A tabela a seguir mostra uma produtividade de desempenho médio para cada instância do Application Gateway:


| Resposta de página de back-end | Pequena | Média | Grande|
|---|---|---|---|
| 6 K | 7,5 Mbps | 13 Mbps | 50 Mbps |
|100 K | 35 Mbps | 100 Mbps| 200 Mbps |


>[AZURE.NOTE] Esses valores são valores aproximados para uma produtividade do Application Gateway. A produtividade real depende de diversos detalhes de ambiente, como o tamanho médio da página, a localização das instâncias de back-end e o tempo de processamento para fornecer de uma página.


## Monitoramento da integridade

O Application Gateway do Azure monitora automaticamente a integridade das instâncias de back-end. Para saber mais, consulte a [Visão geral do monitoramento de integridade do Application Gateway](application-gateway-probe-overview.md).





## Configurando e gerenciando

É possível criar e gerenciar um application gateway usando as APIs REST e os cmdlets do PowerShell.


## Próximas etapas

Depois de aprender mais sobre o Application Gateway, você pode [criar um Application Gateway](application-gateway-create-gateway-portal.md) ou [criar um descarregamento de SSL do Application Gateway](application-gateway-ssl-arm.md) para balancear a carga de conexões HTTPS.

Para saber como criar um Application Gateway usando a URL com base em roteamento de conteúdo, acesse [Criar um Application Gateway usando roteamento com base em URL](application-gateway-create-url-route-arm-ps.md) para obter mais informações.

<!---HONumber=AcomDC_0810_2016-->