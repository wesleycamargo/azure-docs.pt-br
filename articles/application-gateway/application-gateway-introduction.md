<properties 
   pageTitle="Introdução ao Application Gateway | Microsoft Azure"
   description="Esta página fornece uma visão geral do serviço Application Gateway para balanceamento de carga de camada 7, incluindo tamanhos de gateway, balanceamento de carga HTTP, afinidade de sessão baseada em cookie e descarregamento SSL."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/24/2015"
   ms.author="joaoma"/>

# Visão geral técnica do Application Gateway 


O Microsoft Azure Application Gateway é um serviço gerenciado pelo Azure semelhante ao Gateway de VPN do Azure. O Application Gateway fornece uma solução de balanceamento de carga HTTP gerenciada pelo Azure com base no ARR (Roteamento de Solicitação de Aplicativo) do IIS. O serviço de gateway de aplicativo é altamente disponível e medido. Para ver o SLA e o preço, consulte as página [SLA](http://azure.microsoft.com/support/legal/sla/) e [Preço](https://azure.microsoft.com/pricing/details/application-gateway/).

Atualmente, o Application Gateway oferece suporte à entrega de aplicativo de camada 7 para:

- Balanceamento de carga HTTP
- Afinidade de sessão baseada em cookie
- Descarregamento SSL

![Application Gateway](./media/application-gateway-introduction/appgateway1.png)

## Balanceamento de carga de camada 7 HTTP
O Azure fornece balanceamento de carga de camada 4 por meio do balanceador de carga de software. Isso acontece implicitamente para cada serviço de nuvem que tem um VIP (público ou interno) balanceado por carga. No entanto, há muitos aplicativos que podem usar o balanceamento de carga com base na camada 7 (HTTP).


O balanceamento de carga de camada 7 HTTP é útil para:


- Aplicativos que exigem solicitações da mesma sessão de usuário/cliente para acessar a mesma VM back-end. Exemplos disso são os aplicativos de carrinho de compras e servidores de email na Web.
- Aplicativos que deseja liberar de farms do servidor Web da sobrecarga da terminação SSL.
- Aplicativos, como o CDN, que exigem que várias solicitações HTTP na mesma conexão TCP de execução longa sejam roteadas/balanceadas por carga para diferentes servidores back-end.

## Instâncias e tamanhos de gateway

O Application Gateway atualmente é oferecido em 3 tamanhos: Pequeno, Médio e Grande. Os tamanhos de instância pequenos são destinados a cenários de desenvolvimento e teste.

Você pode criar até 10 gateways de aplicativo por assinatura e cada um deles pode ter até 10 instâncias. O balanceamento de carga do Application Gateway como um serviço gerenciado pelo Azure permite o provisionamento de um balanceador de carga de camada 7 por trás do balanceador de carga de software do Azure.

## Configurando e gerenciando

É possível criar e gerenciar o gateway de aplicativo usando APIs REST e cmdlets do PowerShell.

## Próximas etapas

Criar um Application Gateway. Consulte [Criar um Application Gateway](application-gateway-create-gateway.md).

Configurar o descarregamento SSL. Consulte [Configurar descarregamento SSL com o Application Gateway](application-gateway-ssl.md).

<!---HONumber=July15_HO4-->