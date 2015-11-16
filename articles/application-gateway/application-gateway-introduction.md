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
   ms.date="11/03/2015"
   ms.author="joaoma"/>

# O que é o Application Gateway?


O Microsoft Azure Application Gateway fornece uma solução de balanceamento de carga HTTP gerenciada pelo Azure com base no balanceamento de carga de camada 7.

O balanceamento de carga do aplicativo permite que os desenvolvedores e administradores de TI criem regras de roteamento de tráfego de rede com base em HTTP. O serviço do Application Gateway é altamente disponível e medido. Para ver o SLA e o Preço, consulte as página [SLA](http://azure.microsoft.com/support/legal/sla/) e [Preço](https://azure.microsoft.com/pricing/details/application-gateway/).

Atualmente, o Application Gateway oferece suporte à entrega de aplicativo de camada 7 para:

- Balanceamento de carga HTTP
- Afinidade de sessão baseada em cookie
- Descarregamento SSL

![Application Gateway](./media/application-gateway-introduction/appgateway1.png)

## Balanceamento de carga de camada 7 HTTP

O Azure fornece o balanceamento de carga de camada 4 por meio do balanceador de carga do Azure funcionando no nível de transporte (TCP/UDP) e com toda a carga do tráfego de rede de entrada balanceada para o serviço do Application Gateway. O Application Gateway, em seguida, aplicará as regras de roteamento ao tráfego HTTP, fornecendo o balanceamento de carga de nível 7 (HTTP). Quando você cria um Application Gateway, um ponto de extremidade (VIP) será associado e usado como o IP público para o tráfego de rede de entrada.

O Application Gateway roteará o tráfego HTTP com base em sua configuração seja uma máquina virtual, serviço de nuvem, aplicativo Web ou um endereço IP externo.

O diagrama abaixo explica como o tráfego flui para o Application Gateway:

 
![Application Gateway2](./media/application-gateway-introduction/appgateway2.png)

O balanceamento de carga de camada 7 HTTP é útil para:


- Aplicativos que exigem solicitações da mesma sessão de usuário/cliente para acessar a mesma VM back-end. Exemplos disso são os aplicativos de carrinho de compras e servidores de email na Web.
- Aplicativos que deseja liberar de farms do servidor Web da sobrecarga da terminação SSL.
- Aplicativos, como o CDN, que exigem que várias solicitações HTTP na mesma conexão TCP de execução longa sejam roteadas/balanceadas por carga para diferentes servidores back-end.

## Instâncias e tamanhos de gateway

O Application Gateway atualmente é oferecido em 3 tamanhos: Pequeno, Médio e Grande. Os tamanhos de instância pequenos são destinados a cenários de desenvolvimento e teste.

Você pode criar até 10 gateways de aplicativo por assinatura e cada um deles pode ter até 10 instâncias. O balanceamento de carga do Application Gateway como um serviço gerenciado pelo Azure permite o provisionamento de um balanceador de carga de camada 7 por trás do balanceador de carga de software do Azure.

A tabela abaixo mostra uma taxa de transferência de desempenho médio para cada instância do Application Gateway:

| Resposta de página de back-end | Pequena | Média | Grande|
|---|---|---|---|
| 6 mil | 7,5 Mbps | 13 Mbps | 50 Mbps |
|100k | 35 Mbps | 100 Mbps| 200 Mbps |


>[AZURE.NOTE]Os números de desempenho também dependem da resposta HTTP do aplicativo Web ao Application Gateway.


## Monitoramento
 
O Application Gateway monitora o status de integridade das instâncias de back-end usando portas de investigação, testando a resposta HTTP das seções HttpSettings do gateway periodicamente. A investigação espera uma resposta HTTP bem-sucedida no intervalo de códigos de resposta 200-390 e testa os endereços IP de back-end a cada 30 segundos para verificar a resposta HTTP.

Quando uma resposta HTTP bem-sucedida é recebida, o endereço IP é marcado como íntegro. Se a investigação falhar, o endereço IP é removido de um pool de back-end íntegro e o tráfego para de fluir para esse servidor. A investigação de integridade continua a cada 30 segundos para a instância da Web com falha até ela ficar online novamente. Quando a instância da Web responde com êxito à investigação de integridade, ela é adicionada de volta ao pool de back-end íntegro e o tráfego começa a fluir para essa instância novamente.

## Configurando e gerenciando

É possível criar e gerenciar um application gateway usando as APIs REST e os cmdlets do PowerShell.


## Próximas etapas

Criar um Application Gateway. Confira [Criar um Application Gateway](application-gateway-create-gateway.md).

Configurar o descarregamento SSL. Veja [Configurar o descarregamento SSL com o Application Gateway](application-gateway-ssl.md).

<!---HONumber=Nov15_HO2-->