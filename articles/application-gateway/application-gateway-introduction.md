<properties 
   pageTitle="Introdução ao Application Gateway | Microsoft Azure"
   description="Esta página fornece uma visão geral do serviço Application Gateway para balanceamento de carga de camada 7, incluindo tamanhos de gateway, balanceamento de carga HTTP, afinidade de sessão baseada em cookie e descarregamento SSL."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/09/2015"
   ms.author="joaoma"/>

# O que é o Application Gateway?


O Microsoft Azure Application Gateway fornece uma solução de balanceamento de carga HTTP gerenciada pelo Azure com base no balanceamento de carga de camada 7.

O balanceamento de carga do aplicativo permite que os desenvolvedores e administradores de TI criem regras de roteamento de tráfego de rede com base em HTTP. O serviço do Application Gateway é altamente disponível e medido. Para ver o SLA e o Preço, consulte as página [SLA](http://azure.microsoft.com/support/legal/sla/) e [Preço](https://azure.microsoft.com/pricing/details/application-gateway/).

Atualmente, o Application Gateway oferece suporte à entrega de aplicativo de camada 7 para:

- Balanceamento de carga HTTP
- Afinidade de sessão baseada em cookie
- Descarregamento SSL

![Application Gateway](./media/application-gateway-introduction/appgateway1.png)

O balanceamento de carga de camada 7 HTTP é útil para:

- Aplicativos que exigem solicitações da mesma sessão de usuário/cliente para acessar a mesma VM back-end. Exemplos disso são os aplicativos de carrinho de compras e servidores de email na Web.
- Aplicativos que deseja liberar de farms do servidor Web da sobrecarga da terminação SSL.
- Aplicativos, como o CDN, que exigem que várias solicitações HTTP na mesma conexão TCP de execução longa sejam roteadas/balanceadas por carga para diferentes servidores back-end.


## Instâncias e tamanhos de gateway

O Application Gateway atualmente é oferecido em 3 tamanhos: Pequeno, Médio e Grande. Os tamanhos de instância pequenos são destinados a cenários de desenvolvimento e teste.

Você pode criar até 50 application gateways por assinatura e cada um deles pode ter até 10 instâncias. O balanceamento de carga do Application Gateway como um serviço gerenciado pelo Azure permite o provisionamento de um balanceador de carga de camada 7 por trás do balanceador de carga de software do Azure.

A tabela abaixo mostra uma taxa de transferência de desempenho médio para cada instância do Application Gateway:


| Resposta de página de back-end | Pequena | Média | Grande|
|---|---|---|---|
| 6 mil | 7,5 Mbps | 13 Mbps | 50 Mbps |
|100k | 35 Mbps | 100 Mbps| 200 Mbps |


>[AZURE.NOTE]Essa é uma orientação aproximada para uma taxa de transferência de application gateway. A taxa de transferência real depende de diversos detalhes de ambiente, como o tamanho médio da página, o local das instâncias de back-end, o tempo de processamento para o servidor de uma página, para citar alguns.

## Monitoramento da integridade
 

O Application Gateway do Azure monitora automaticamente a integridade das instâncias de back-end. Acesse [investigações e monitoramento de integridade do Application Gateway](application-gateway-probe-overview.md) para obter mais informações.

## Configurando e gerenciando

É possível criar e gerenciar um application gateway usando as APIs REST e os cmdlets do PowerShell.



## Próximas etapas

Criar um Application Gateway. Confira [Criar um Application Gateway](application-gateway-create-gateway.md).

Configurar o descarregamento SSL. Confira [Configurar o descarregamento de SSL com o Application Gateway](application-gateway-ssl.md).

<!---HONumber=AcomDC_0107_2016-->