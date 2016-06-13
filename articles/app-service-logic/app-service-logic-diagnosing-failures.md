<properties
   pageTitle="Aplicativos para Diagnosticar os Aplicativos Lógicos | Microsoft Azure"
   description="Entender as abordagens comuns para compreender onde os aplicativos estão falhando"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/18/2016"
   ms.author="jehollan"/>
   
# Diagnosticando os Aplicativos Lógicos

Se houver quaisquer problemas ou falhas com um Aplicativo Lógico, haverá algumas abordagens que você pode adotar para melhor entender de onde vêm as falhas.

## Ferramentas de Portal de Gerenciamento

O Portal do Azure fornece várias ferramentas para diagnosticar cada Aplicativo Lógico em cada etapa.

### Histórico de gatilho

Cada Aplicativo Lógico tem pelo menos um gatilho. Se você perceber que os aplicativos não são acionados, o primeiro lugar para começar é o histórico de gatilhos. Você pode acessar o histórico de gatilhos na folha principal dos Aplicativos Lógicos aqui:

![][1]

Isso listará todas as tentativas feitas pelo Aplicativo Lógico para disparar. Cada uma dessas entradas pode ser clicada para ir para o próximo nível de detalhe (especificamente, quaisquer entradas ou saídas que foram geradas pela tentativa do gatilho). Se você observar disparadores “Failed”, deverá clicar na tentativa do gatilho e analisar o Link de Saídas para entender as mensagens de erro que podem estar sendo geradas (por exemplo: credenciais inválidas do FTP).

Os diferentes status que você pode ver são:

* Ignorado - sondou o ponto de extremidade para verificar se os dados estavam disponíveis e obteve uma resposta de que não havia dados disponíveis.
* Êxito - o gatilho recebeu uma resposta de que os dados estavam disponíveis. Pode ser de um gatilho manual, gatilho de recorrência ou gatilho de sondagem. Provavelmente será acompanhado de “Disparado”, mas talvez não se você tiver uma condição ou aplicar splitOn na visualização do código que não foi atendida.
* Falha - um erro foi gerado.

#### Iniciar um gatilho manualmente

Se você quiser que o Aplicativo Lógico verifique se há um gatilho disponível imediatamente (sem aguardar a próxima recorrência), sempre poderá clicar no botão **Selecionar Gatilho** na folha principal para forçar uma verificação. Por exemplo, clicar nisso com um gatilho Dropbox fará com que o fluxo de trabalho sonde o Dropbox imediatamente para obter os novos arquivos.

### Histórico de Execuções

Sempre que um gatilho é disparado, resulta em uma execução. As execuções podem ser acessadas na folha principal e contêm muitas informações úteis para compreender o que aconteceu durante o fluxo.

![][2]

Uma execução pode ter qualquer um dos seguintes status:

* Êxito - todas as ações foram bem-sucedidas ou se houve uma falha, ela foi tratada por uma ação posterior no fluxo de trabalho (isto é, uma ação foi definida para ser executada após uma ação “Falha”).
* Falha - pelo menos uma ação teve uma falha que não foi tratada por uma ação posterior no fluxo de trabalho.
* Cancelado - o fluxo de trabalho estava em execução, mas recebeu uma solicitação de cancelamento.
* Executando - se um fluxo de trabalho está em execução atualmente. Isso pode ocorrer para os fluxos que estão sendo limitados também com o atual Plano do Serviço de Aplicativo. Consulte os limites da ação na [página de preços](https://azure.microsoft.com/pricing/details/app-service/plans/) para obter detalhes. Configurando o Diagnóstico (os gráficos abaixo do histórico de execuções) também permitirá que você conheça os eventos de restrição que estão ocorrendo.

Quando estiver em uma execução, poderá analisar para obter os detalhes.

#### Saídas do Gatilho

As Saídas do Gatilho mostrarão os dados recebidos a partir do gatilho. Isso pode ser útil para entender se todas as propriedades estão sendo retornadas conforme o esperado.

>[AZURE.NOTE] Poderá ser útil entender como os Aplicativos Lógicos [lidam com os diferentes tipos de conteúdo](app-service-logic-content-type.md) se você vir qualquer conteúdo que não entende.

![][3]

#### Entradas e Saídas da Ação

Você pode analisar as entradas e saídas que uma ação recebeu. Isso é útil para entender o tamanho e a forma das saídas, bem como ver todas as mensagens de erro que possam ter sido geradas.

![][4]

## Depurando a Execução do Fluxo de Trabalho

Além de monitorar as entradas, saídas e gatilhos de uma execução, pode ser útil adicionar algumas etapas em um fluxo de trabalho para ajudar a depurar. O [RequestBin](http://requestb.in) é uma ferramenta poderosa que você pode adicionar como uma etapa em um fluxo de trabalho. O RequestBin permite configurar um inspetor de Solicitação HTTP para compreender exatamente o tamanho, forma e formato de uma Solicitação HTTP. Você pode criar um novo RequestBin e colar a URL em uma Ação HTTP POST do Aplicativo Lógico, com qualquer conteúdo do corpo que deseja testar (uma expressão, outra saída da etapa, etc.). Depois de executar o Aplicativo Lógico, você poderá atualizar o RequestBin para ver como a solicitação foi formada quando foi gerada a partir do mecanismo do Aplicativo Lógico.




<!-- image references -->
[1]: ./media/app-service-logic-diagnosing-failures/triggerHistory.PNG
[2]: ./media/app-service-logic-diagnosing-failures/runHistory.PNG
[3]: ./media/app-service-logic-diagnosing-failures/triggerOutputsLink.PNG
[4]: ./media/app-service-logic-diagnosing-failures/ActionOutputs.PNG

<!---HONumber=AcomDC_0601_2016-->