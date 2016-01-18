

<properties 
   pageTitle="Visão geral do monitoramento de integridade do Application Gateway do Azure | Microsoft Azure"
   description="Saiba mais sobre os recursos de monitoramento do Application Gateway do Azure"
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# Visão geral do monitoramento de integridade do Application Gateway 


Por padrão, o Application Gateway do Azure monitora a integridade de todos os recursos em seu pool de back-end e remove automaticamente qualquer recurso do pool que não for considerado íntegro. O Application Gateway continua monitorando as instâncias não íntegras e as adiciona de volta ao pool de back-end íntegro depois que elas se tornarem disponíveis e responderem a investigações de integridade.

Além do monitoramento da investigação de integridade padrão, você também pode personalizar a investigação de integridade para atender às necessidades do seu aplicativo. Neste artigo, abordaremos as investigações de integridade padrão e personalizadas.

## Investigação de integridade padrão

Um Application Gateway configura automaticamente uma investigação de integridade padrão quando você não define nenhuma configuração de investigação personalizada. O comportamento de monitoramento funciona fazendo uma solicitação HTTP para os endereços IP configurados para o pool de back-end.

Por exemplo: configure seu Application Gateway para usar os servidores de back-end, A, B e C para receber o tráfego de rede HTTP na porta 80. O monitoramento de integridade padrão testa os três servidores a cada 30 segundos para receber uma de HTTP íntegra. Uma resposta de HTTP íntegra tem um [código de status](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 e 399.

Se a verificação de investigação padrão falhar para o servidor A, o Application Gateway o remove do seu pool de back-end e o tráfego de rede para de fluir para este servidor. A investigação padrão ainda continua a verificar o servidor A a cada 30 segundos. Quando o Servidor A responde com êxito a uma solicitação de uma investigação de integridade, ela é adicionada de volta como íntegro ao pool de back-end e o tráfego começa a fluir para esse servidor novamente.

A investigação padrão só examina http://127.0.0.1:<port> para determinar o status de integridade. Se precisar configurar a investigação de integridade para ir para uma URL personalizada ou modificar outras configurações, você deve usar investigações personalizadas, conforme descrito abaixo.

### Configurações da investigação de integridade padrão

|Propriedades da investigação | Valor | Descrição|
|---|---|---|
| URL de investigação| http://127.0.0.1/ | Caminho da URL |
| Intervalo | 30 | intervalo da investigação em segundos |
| Tempo limite | 30 | tempo limite da investigação em segundos |
| Limite não íntegro | 3 | Contagem de repetições da investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir um limite não íntegro |


## Investigação de integridade personalizada 

Investigações personalizadas permitem que você tenha um controle mais granular sobre o monitoramento de integridade. Ao usar investigações personalizadas, você pode configurar o intervalo de investigação, a URL e o caminho a testar e quantas respostas com falha devem ser aceitas antes de marcar a instância do pool de back-end como não íntegra.


### Configurações da investigação de integridade personalizada

|Propriedades da investigação| Descrição|
|---|---|
| Nome | Nome da investigação. Este é o nome usado para se referir à investigação nas configurações de Http do back-end |
| Protocolo | Protocolo usado para enviar a investigação. HTTP é o único protocolo válido |
| Host | Nome do host para enviar a investigação |
| Caminho | Caminho relativo da investigação. Um caminho válido começa com "/". A investigação é enviada para <protocol>://<host>:<port><path> |
| Intervalo | Intervalo de investigação em segundos. Este é o intervalo de tempo entre duas investigações consecutivas| 
| Tempo limite | Tempo limite da investigação em segundos. A investigação é marcada como com falha se não for recebida uma resposta válida dentro desse período de tempo limite |
| Limite não íntegro | Contagem de repetições da investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir um limite não íntegro |

## Próximas etapas

Depois de aprender sobre o monitoramento de integridade do Application Gateway, você pode configurar uma [investigação de integridade personalizada](application-gateway-create-probe-ps.md) para o Gerenciador de Recursos do Azure ou uma [investigação de integridade personalizada](application-gateway-create-probe-classic-ps.md) para o modelo de implantação clássico do Azure.

<!---HONumber=AcomDC_0107_2016-->