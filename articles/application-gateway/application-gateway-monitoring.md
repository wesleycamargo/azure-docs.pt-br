<properties 
   pageTitle="Configurar um Application Gateway para investigações personalizadas usando o Gerenciador de Recursos do Azure | Microsoft Azure"
   description="Esta página fornece instruções para configurar investigações personalizadas no Application Gateway usando o Gerenciador de Recursos do Azure"
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
   ms.date="11/24/2015"
   ms.author="joaoma"/>

# Testes personalizados e monitoramento de integridade 


O Application Gateway do Azure monitora a integridade de todos os recursos em seu pool de back-end e remove automaticamente qualquer recurso do pool que não for considerado íntegro.

Configure o monitoramento de integridade usando dois tipos de testes: investigação de integridade padrão e a investigação personalizada.

## Investigação de integridade padrão

Um Application Gateway configura automaticamente uma investigação de integridade padrão quando você não define nenhuma configuração de investigação personalizada. O monitoramento do comportamento funciona fazendo uma solicitação HTTP para os endereços IP configurados para o pool de back-end e para a porta configurada na configuração HTTP de back-end do Application Gateway.

Por exemplo: configure seu Application Gateway para usar os servidores de back-end, A, B e C para receber o tráfego de rede HTTP na porta 80. O monitoramento de integridade padrão testa os três servidores a cada 30 segundos para receber uma de HTTP íntegra. Uma resposta de HTTP íntegra tem um [código de status](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 e 399.

Se a verificação de investigação padrão falhar para o servidor A, o Application Gateway o removerá de seu pool de back-end, e o tráfego de rede deixará de fluir para este servidor. A investigação padrão ainda continua a verificar o servidor A a cada 30 segundos. Quando o Servidor A responde com êxito a uma solicitação de uma investigação de integridade, ela é adicionada de volta como íntegro ao pool de back-end e o tráfego começa a fluir para esse servidor novamente.

A investigação padrão usa somente os endereços IP para verificar o status. Se você quiser verificar a integridade testando a conectividade com uma URL, você deve usar a investigação personalizada.


## Investigação personalizada 

Investigações personalizadas permitem que você tenha um controle mais granular sobre o monitoramento de integridade. Ao usar investigações personalizadas, você pode configurar o intervalo de investigação, a URL e o caminho do teste e quantas respostas com falha devem ser aceitas antes de marcar a instância do pool de back-end como não íntegra.


Configurações da investigação personalizada:

- **Intervalo de investigação**: configura os intervalos das verificações da investigação.
- **Tempo limite da investigação**: define o tempo limite da investigação para uma verificação de solicitação HTTP
- **Limite não íntegro**: especifica a quantidade de solicitações com falha necessária para sinalizar a instância como não íntegra.  
- **Nome e caminho do host**: se o site da Web ou web farm não tiver uma resposta HTTP apenas para o endereço IP, será necessário configurar um nome de host para a investigação, e o caminho para uma resposta HTTP íntegra válida. Por exemplo: você tem um site da Web http://contoso.com/, mas ele não gera uma resposta HTTP válida. É necessário configurar um nome de host e um caminho para fornecer a resposta HTTP íntegra válida a fim de validar a instância do servidor Web como íntegra. Nesse caso, a investigação personalizada pode ser configurada para "http://contoso.com/path/custompath.htm" para verificações de investigação com resposta HTTP bem-sucedida. 



>[AZURE.NOTE] Investigações personalizadas só podem ser configuradas usando o PowerShell

<!---HONumber=AcomDC_0128_2016-->