<properties 
	pageTitle="Saiba mais sobre a Limitação nos Serviços BizTalk | Azure" 
	description="Saiba mais sobre os limites de limitação e comportamentos de tempo de execução resultantes para os serviços BizTalk. A limitação é baseada no uso de memória e número de mensagens. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2015" 
	ms.author="mandia"/>





# Serviços BizTalk: limitação

Os Serviços BizTalk do Azure implementam a limitação do serviço com base em duas condições: uso de memória e número de mensagens simultâneas em processamento. Este tópico lista as limitações e descreve o comportamento em tempo de execução quando ocorre uma condição de limitação.

## Limites da limitação

A tabela a seguir lista os limites e origem da limitação:

<table border="1">

<tr bgcolor="FAF9F9">
        <th> </th>
        <td><strong>Descrição</strong></td>
        <td><strong>Limite baixo</strong></td>
        <td><strong>Limite alto</strong></td>
</tr>
    <tr>
        <td>Memória</td>
        <td>% de memória total de sistema disponível/PageFileBytes. 
<br/><br/>
O total de PageFileBytes disponível é aproximadamente 2 vezes a RAM do sistema.</td>
        <td>60%</td>
        <td>70%</td>
    </tr>
    <tr>
        <td>Processamento de mensagem</td>
        <td>Número de processamento de mensagens simultaneamente</td>
        <td>40 vezes o número de núcleos</td>
        <td>100 vezes o número de núcleos</td>
    </tr>
</table>

Quando um limite alto é atingido, os Serviços BizTalk do Azure começam a ser limitados. A limitação é interrompida quando um limite baixo é atingido. Por exemplo, seu serviço estiver usando 65% da memória do sistema. Nesta situação, o serviço não sofre limitação. Seu serviço começa usando 70% da memória do sistema. Nessa situação, o serviço começa a ser limitado e continua até que o serviço use 60% da memória do sistema (limite baixo).

Os Serviços BizTalk do Azure acompanham o status da limitação (estado normal versus limitado) e a duração da limitação.


## Comportamento do tempo de execução

Quando os Serviços BizTalk do Azure entram em estado de limitação, o seguinte ocorre:

- A limitação ocorre por instância de função. Por exemplo:<br/>InstânciadeFunçãoA está limitada. A InstânciadeFunçãoB não está limitada. Nesta situação, as mensagens da InstânciadeFunçãoB são processadas conforme o esperado. As mensagens na InstânciadeFunçãoA são descartadas e ocorre uma falha com o seguinte erro:<br/><br/> **O servidor está ocupado. Tente novamente.**<br/><br/>
- Nenhuma origem de pull pode pesquisar ou baixar uma mensagem. Por exemplo:<br/> um pipeline puxa as mensagens de uma origem de FTP externa. A instância de função que faz puxa entra em estado de limitação. Nessa situação, o pipeline interrompe o download de mensagens adicionais até que a instância de função saia da limitação.
- Uma resposta é enviada ao cliente para que ele reenvie a mensagem.
- Você deve aguardar até que a limitação seja resolvida. Especificamente, você deve aguardar até que o limite baixo seja atingido.

## Observações importantes
- A limitação não pode ser desabilitada.
- Os limites de limitação não podem ser modificados.
- A limitação é implementada em todo o sistema.
- O Servidor de Banco de Dados SQL do Azure também tem uma limitação interna.

## Tópicos adicionais sobre Serviços do BizTalk do Azure

-  [Instalando o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Tutoriais: Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Como começar a usar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## Consulte também
- [Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Serviços BizTalk: gráfico do status do provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Serviços BizTalk: guias Painel, Monitor e Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Serviços BizTalk: backup e restauração](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Serviços BizTalk: nome e chave do emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
 

<!---HONumber=July15_HO4-->