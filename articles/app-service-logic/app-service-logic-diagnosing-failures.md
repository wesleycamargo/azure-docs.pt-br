---
title: Diagnosticando falhas dos aplicativos lógicos | Microsoft Docs
description: Abordagens comuns para compreender onde os aplicativos lógicos estão falhando
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/18/2016
ms.author: jehollan

---
# Diagnosticando falhas nos aplicativos lógicos
Se você tiver problemas ou falhas com o recurso de aplicativos lógicos do Serviço de Aplicativo do Azure, algumas abordagens poderão ajudá-lo a entender melhor a origem das falhas.

## Ferramentas do portal do Azure
O Portal do Azure fornece várias ferramentas para diagnosticar cada aplicativo lógico em cada etapa.

### Histórico de gatilho
Cada aplicativo lógico tem pelo menos um gatilho. Se você perceber que os aplicativos não são acionados, o primeiro lugar para obter informações adicionais será o histórico de gatilhos. Você pode acessar o histórico de gatilhos na folha principal do aplicativo lógico.

![Exibir o histórico de gatilho][1]

Isso lista todas as tentativas de gatilho feitas por seu aplicativo lógico. Você pode clicar em cada tentativa de gatilho para ir para o próximo nível de detalhe (especificamente, quaisquer entradas ou saídas geradas pela tentativa de gatilho). Se você observar quaisquer gatilhos com falha, clique na tentativa de gatilho e analise o link de **Saídas** para ver quaisquer mensagens de erro que possam estar sendo geradas (por exemplo, credenciais de FTP inválidas).

Os diferentes status que você pode ver são:

* **Ignorado**. Ele sondou o ponto de extremidade para procurar por dados e recebeu uma resposta de que nenhum dado estava disponível.
* **Êxito**. O gatilho recebeu uma resposta de que havia dados disponíveis. Pode ser de um gatilho manual, um gatilho de recorrência ou um gatilho de sondagem. Isso provavelmente estará acompanhado de um status de **Disparado**, mas talvez isso não aconteça se você tiver uma condição ou comando SplitOn na exibição de código que não foi atendida.
* **Falha**. Um erro foi gerado.

#### Iniciar um gatilho manualmente
Se você quiser que o aplicativo lógico verifique imediatamente se há um gatilho disponível (sem aguardar a próxima recorrência), você poderá clicar no botão **Selecionar Gatilho** na folha principal para forçar uma verificação. Por exemplo, clicar nesse link com um gatilho Dropbox fará com que o fluxo de trabalho sonde o Dropbox imediatamente em busca de novos arquivos.

### Histórico da execução
Cada gatilho que é acionado resulta em uma execução. Você pode acessar informações da execução da folha principal, que contém muitas informações que podem ser úteis para entender o que aconteceu durante o fluxo de trabalho.

![Localizando o histórico da execução][2]

Uma execução exibe um dos seguintes status:

* **Êxito**. Todas as ações foram bem-sucedidas ou, se tiver ocorrido uma falha, ela terá sido tratada por uma ação posterior no fluxo de trabalho. Ou seja, ela terá sido tratada por uma ação que foi definida para ser executada depois de uma ação com falha.
* **Falha**. Pelo menos uma ação teve uma falha que não foi tratada por uma ação posterior no fluxo de trabalho.
* **Cancelado**. O fluxo de trabalho estava em execução, mas recebeu uma solicitação de cancelamento.
* **Executando**. O fluxo de trabalho está em execução atualmente. Isso pode ocorrer para os fluxos que estão sendo limitados, ou devido ao plano do Serviço de Aplicativo atual. Consulte os limites da ação na [página de preços](https://azure.microsoft.com/pricing/details/app-service/plans/) para obter detalhes. Configurar o diagnóstico (os gráficos listados abaixo do histórico da execução) também podem fornecer informações sobre quaisquer eventos de restrição que estão ocorrendo.

Quando você estiver observando um histórico da execução, você pode analisar para obter mais detalhes.

#### Saídas do gatilho
As saídas do gatilho mostrarão os dados recebidos do gatilho. Isso pode ajudá-lo a determinar se todas as propriedades retornaram conforme o esperado.

> [!NOTE]
> Poderá ser útil entender como o recurso Aplicativos Lógicos [lida com os diferentes tipos de conteúdo](app-service-logic-content-type.md) se você vir qualquer conteúdo que não entender.
> 
> 

![Exemplos de saída do gatilho][3]

#### Entradas e saídas da ação
Você pode analisar as entradas e saídas que uma ação recebeu. Isso é útil para entender o tamanho e a forma das saídas, bem como ver todas as mensagens de erro que possam ter sido geradas.

![Entradas e saídas da ação][4]

## Depurando o tempo de execução do fluxo de trabalho
Além de monitorar as entradas, saídas e gatilhos de uma execução, pode ser útil adicionar algumas etapas em um fluxo de trabalho para ajudar na depuração. O [RequestBin](http://requestb.in) é uma ferramenta poderosa que você pode adicionar como uma etapa em um fluxo de trabalho. Usando o RequestBin, você pode configurar um inspetor de solicitação HTTP para determinar exatamente o tamanho, forma e formato de uma solicitação HTTP. Você pode criar um novo RequestBin e colar a URL em uma Ação HTTP POST do aplicativo lógico junto com qualquer conteúdo do corpo que você deseje testar (por exemplo, uma expressão, ou outra saída da etapa). Depois de executar o aplicativo lógico, você poderá atualizar o RequestBin para ver como a solicitação foi formada, já que ela foi gerada do mecanismo dos Aplicativos Lógicos.

<!-- image references -->
[1]: ./media/app-service-logic-diagnosing-failures/triggerHistory.PNG
[2]: ./media/app-service-logic-diagnosing-failures/runHistory.PNG
[3]: ./media/app-service-logic-diagnosing-failures/triggerOutputsLink.PNG
[4]: ./media/app-service-logic-diagnosing-failures/ActionOutputs.PNG

<!---HONumber=AcomDC_0803_2016-->