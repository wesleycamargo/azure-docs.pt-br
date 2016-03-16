<properties 
   pageTitle="Investigações personalizadas do balanceador de carga e monitoramento do status da integridade | Microsoft Azure"
   description="Saiba como usar testes personalizados para o balanceador de carga do Azure para monitorar instâncias atrás de um balanceador de carga"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />


# Investigações do balanceador de carga 

O Balanceador de Carga do Azure oferece a capacidade de monitorar a integridade das instâncias do servidor usando investigações. Quando uma investigação não responde, o Balanceador de Carga do Azure interrompe o envio de nova conexão para a instância não íntegra. As conexões existentes não são afetadas e novas conexões são enviadas para instâncias íntegras.

Uma investigação personalizada do TCP ou HTTP deve ser configurada quando você usa as máquinas virtuais por trás de um balanceador de carga. Funções do serviço de nuvem (funções de trabalho e da Web) são as únicas instâncias de servidor com o monitoramento de investigação de agente convidado.
 
## Noções básicas sobre contagem da investigação e tempo limite

O comportamento da investigação depende do número de investigações com êxito/falha necessárias para marcar uma instância como Ativa/Inativa. Isso é calculado como SuccessFailCount = Tempo Limite/Frequência. Para o portal, o Tempo Limite é definido como duas vezes o valor da Frequência (Tempo Limite = Frequência * 2).

A configuração de investigação de todas as instâncias com balanceamento de carga para um ponto de extremidade (conjunto de balanceamento de carga) deve ser a mesma. Isso significa que você não pode ter uma configuração de investigação diferente (ou seja, porta local, tempo limite, etc.) para cada instância de função ou máquina virtual no mesmo serviço hospedado para uma combinação de ponto de extremidade específica.


>[AZURE.IMPORTANT] A investigação de um balanceador de carga usa um endereço IP 168.63.129.16. Esse endereço IP público é usado para criar um canal de comunicação com recursos de plataforma internos para o cenário de rede de IP Virtual própria. O endereço IP público 168.63.129.16 virtual é usado em todas as regiões e não será alterado. É recomendável que o endereço IP seja permitido em todas as políticas de firewall locais. Ele não deve ser considerado um risco de segurança, já que somente a plataforma interna do Azure pode originar uma mensagem dele. Se isso não for feito, resultará em um comportamento inesperado em uma variedade de cenários, como configurar o mesmo intervalo de endereços IP como 168.63.129.16 e ter endereço IP duplicado.


## Tipos de investigações

### Investigação do Agente Convidado

Somente para serviços de nuvem. O balanceador de carga do Azure utiliza o agente convidado dentro da máquina virtual, escuta e responde com uma resposta HTTP 200 OK somente quando a instância está no estado pronto (ou seja, a instância não está nos estados Ocupado, Reciclando, Parando, etc).

Para saber mais, confira [Configurando o arquivo de definição de serviço (csdef) para investigações de integridade](https://msdn.microsoft.com/library/azure/jj151530.asp) ou [Introdução à criação do balanceador de carga da Internet para serviços de nuvem da Internet](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).
 
### O que levaria uma investigação de agente convidado marcar uma instância como não íntegra?

Se o agente convidado não responder com HTTP 200 OK, o balanceador de carga do Azure marcará a instância como sem resposta e interromperá o envio de tráfego para essa instância. O balanceador de carga do Azure continuará a executar o ping na instância e, se o agente convidado responder com um HTTP 200, o balanceador de carga do Azure enviará tráfego para essa instância novamente.

Ao usar uma função Web, o código do site geralmente executa em w3wp.exe, que não é monitorado pela malha nem pelo agente convidado do Azure; portanto, as falhas no w3wp.exe (por exemplo, respostas HTTP 500) não serão reportadas ao agente convidado e o balanceador de carga não saberá colocar essa instância fora da rotação.


### Investigação personalizada do HTTP

A investigação personalizada do balanceador de carga HTTP substitui a investigação do agente convidado padrão e permite que você crie sua própria lógica personalizada para determinar a integridade da instância de função. O balanceador de carga investiga seu ponto de extremidade (a cada 15 segundos, por padrão) e a instância é considerada na rotação do balanceador de carga se responde com um HTTP 200 dentro do período de tempo limite (padrão de 31 segundos). Isso pode ser útil para implementar sua própria lógica para remover instâncias da rotação do balanceador de carga, por exemplo, retornar um status não 200 se a instância estiver acima de 90% da CPU. Para funções Web que usam w3wp.exe, isso também significa que você obtém monitoramento automático do seu site, uma vez que as falhas no código do site retornarão um status não 200 para a investigação do balanceador de carga.

>[AZURE.NOTE] A investigação personalizada HTTP só oferece suporte aos caminhos relativos e ao protocolo HTTP. Não há suporte para HTTPS.


### O que levaria uma investigação personalizada HTTP marcar uma instância como não íntegra? 

- O aplicativo HTTP retorna um código de resposta HTTP diferente de 200 (ou seja, 403, 404, 500, etc.). Essa é uma confirmação positiva de que a instância do aplicativo quer ser retirada do serviço imediatamente.

-  Caso o servidor HTTP não responda após o período de tempo limite. Observe que dependendo do valor definido do tempo limite, pode significar que várias solicitações de investigação não serão atendidas antes de marcar a investigação como inativa (ou seja, investigações SuccessFailCount testes são enviadas).
- 	Quando o servidor fecha a conexão por meio de uma redefinição TCP.

### Investigação personalizada do TCP

As investigações de TCP estão iniciando uma conexão executando um handshake de três vias para a porta definida.

### O que levaria uma investigação personalizada do TCP a marcar uma instância como não íntegra?

- Caso o servidor TCP não responda após o período de tempo limite. Isso dependerá do número de solicitações de investigação com falha, que foram configuradas para não serem atendidas antes de marcar a investigação como inativa.
- 	Ela recebe uma redefinição de TCP da instância de função.

Confira [Introdução à criação de um balanceador de carga para a Internet para gerenciar recursos](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer) para compreender como configurar uma investigação de integridade HTTP ou investigação do TCP.

## Adicionar instâncias íntegras novamente ao balanceador de carga

As investigações de TCP e HTTP são consideradas íntegras e marcam a instância de função como íntegra quando:

- Na primeira vez em que a VM é iniciada e o balanceamento de carga obtém uma investigação positiva.
- O número SuccessFailCount (veja acima) define o valor de investigações bem-sucedidas necessárias para marcar a instância de função como íntegra. Se uma instância de função foi removida, é necessário ter SuccessFailCount com uma sequência de investigações bem-sucedidas para marcar a instância de função como ativa.

>[AZURE.NOTE] Se a integridade de uma instância de função estiver flutuando, o Balanceador de Carga do Azure aguardará antes de colocar a instância de função de volta ao estado íntegro. Isso é feito por meio de uma política para proteger o usuário e a infraestrutura.

## Análise de logs para balanceador de carga

Você pode usar a [análise de logs para balanceador de carga](load-balancer-monitor-log.md) para verificar o status da integridade da investigação e a contagem da investigação. O registro em log pode ser usado com o Power BI ou com o Operation Insights para fornecer estatísticas do status da integridade do balanceador de carga.
 

<!---HONumber=AcomDC_0302_2016-->