<properties linkid="manage-services-biztalk-services-throttling" urlDisplayName="Throttling" pageTitle="Throttling thresholds in BizTalk Services | Azure" metaKeywords="BizTalk Services, throttling, Azure" description="Learn about throttling thresholds and resulting runtime behaviors for BizTalk Services. Throttling is based on memory usage and number of simultaneous messages." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Throttling" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />

# Serviços do BizTalk: Limitação

Os Serviços do BizTalk do Azure implementam a limitação de serviço com base em duas condições: uso da memória e o número de processamento simultâneo de mensagens. Este tópico lista as limitações e descreve o comportamento em tempo de execução quando ocorre uma condição de limitação.

## Limites da limitação

A tabela a seguir lista os limites e origem da limitação:

<table border="1">
<tr bgcolor="FAF9F9">
<th>
</th>
<td>
**Descrição**

</td>
<td>
**Limite baixo**

</td>
<td>
**Limite alto**

</td>
</tr>
<tr>
<td>
Memória

</td>
<td>
% de memória total de sistema disponível/PageFileBytes.
 O total de PageFileBytes disponível é aproximadamente 2 vezes a RAM do sistema.

</td>
<td>
60%

</td>
<td>
70%

</td>
</tr>
<tr>
<td>
Processamento de mensagem

</td>
<td>
Número de processamento de mensagens simultaneamente

</td>
<td>
40 vezes o número de núcleos

</td>
<td>
100 vezes o número de núcleos

</td>
</tr>
</table>
Quando um limite alto é atingido, os Serviços BizTalk do Azure começam a ser limitados. A limitação é interrompida quando um limite baixo é atingido. Por exemplo, seu serviço estiver usando 65% da memória do sistema. Nesta situação, o serviço não sofre limitação. Seu serviço começa usando 70% da memória do sistema. Nessa situação, o serviço começa a ser limitado e continua até que o serviço use 60% da memória do sistema (limite baixo).

Os Serviços BizTalk do Azure acompanham o status da limitação (estado normal versus limitado) e a duração da limitação.

## Comportamento do tempo de execução

Quando os Serviços BizTalk do Azure entram em estado de limitação, o seguinte ocorre:

-   A limitação ocorre por instância de função. Por exemplo:
    RoleInstanceA está limitada. A InstânciadeFunçãoB não está limitada. Nesta situação, as mensagens da InstânciadeFunçãoB são processadas conforme o esperado. As mensagens na RoleInstanceA são descartadas e ocorre uma falha com o seguinte erro:
    O servidor está ocupado. Tente novamente.
-   Nenhuma origem de pull pode pesquisar ou baixar uma mensagem. Por exemplo:
    Um pipeline puxa as mensagens de uma origem de FTP externa. A instância de função que faz puxa entra em estado de limitação. Nessa situação, o pipeline interrompe o download de mensagens adicionais até que a instância de função saia da limitação.
-   Uma resposta é enviada ao cliente para que ele reenvie a mensagem.
-   Você deve aguardar até que a limitação seja resolvida. Especificamente, você deve aguardar até que o limite baixo seja atingido.

## Observações importantes

-   A limitação não pode ser desabilitada.
-   Os limites de limitação não podem ser modificados.
-   A limitação é implementada em todo o sistema.
-   O Servidor de Banco de Dados SQL do Azure também tem uma limitação interna.

## Tópicos adicionais sobre Serviços do BizTalk do Azure

-   [Instalando o SDK dos Serviços BizTalk do Azure][Instalando o SDK dos Serviços BizTalk do Azure]
-   [Tutoriais: Serviços do BizTalk do Azure][Tutoriais: Serviços do BizTalk do Azure]
-   [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)][Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)]
-   [Serviços BizTalk do Azure][Serviços BizTalk do Azure]

## Consulte também

-   [Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium][Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium]
-   [Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure][Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure]
-   [Serviços do BizTalk: Gráfico de status do provisionamento][Serviços do BizTalk: Gráfico de status do provisionamento]
-   [Serviços do BizTalk: guias Painel, Monitoramento e Escala][Serviços do BizTalk: guias Painel, Monitoramento e Escala]
-   [Serviços do BizTalk: Backup e restauração][Serviços do BizTalk: Backup e restauração]
-   [Serviços do BizTalk: nome e chave do emissor][Serviços do BizTalk: nome e chave do emissor]

  [Instalando o SDK dos Serviços BizTalk do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=241589
  [Tutoriais: Serviços do BizTalk do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=236944
  [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/p/?LinkID=302335
  [Serviços BizTalk do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=303664
  [Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Serviços do BizTalk: Gráfico de status do provisionamento]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Serviços do BizTalk: guias Painel, Monitoramento e Escala]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [Serviços do BizTalk: Backup e restauração]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [Serviços do BizTalk: nome e chave do emissor]: http://go.microsoft.com/fwlink/p/?LinkID=303941
