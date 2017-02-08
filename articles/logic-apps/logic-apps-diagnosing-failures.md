---
title: "Diagnosticar Falhas no Aplicativo Lógico do Azure | Microsoft Docs"
description: "Abordagens comuns para compreender onde os aplicativos lógicos estão falhando"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: f2a278beaf25fcc3c790bab802efea29463074af


---
# <a name="diagnosing-logic-app-failures"></a>Diagnosticando falhas nos aplicativos lógicos
Se você tiver problemas ou falhas com os aplicativos lógicos, há algumas abordagens que poderão ajudar a entender melhor a origem das falhas.  

## <a name="azure-portal-tools"></a>Ferramentas do portal do Azure
O Portal do Azure fornece várias ferramentas para diagnosticar cada aplicativo lógico em cada etapa.

### <a name="trigger-history"></a>Histórico de gatilho
Cada aplicativo lógico tem pelo menos um gatilho. Se você perceber que os aplicativos não são acionados, o primeiro lugar para obter informações adicionais será o histórico de gatilhos. Você pode acessar o histórico de gatilhos na folha principal do aplicativo lógico.

![Exibir o histórico de gatilho][1]

Isso lista todas as tentativas de gatilho feitas por seu aplicativo lógico. Você pode clicar em cada tentativa de gatilho para ir para o próximo nível de detalhe (especificamente, quaisquer entradas ou saídas geradas pela tentativa de gatilho). Se você observar quaisquer gatilhos com falha, clique na tentativa de gatilho e analise o link de **Saídas** para ver quaisquer mensagens de erro que possam estar sendo geradas (por exemplo, credenciais de FTP inválidas).

Os diferentes status que você pode ver são:

* **Ignorado**. Ele sondou o ponto de extremidade para procurar por dados e recebeu uma resposta de que nenhum dado estava disponível.
* **Êxito**. O gatilho recebeu uma resposta de que havia dados disponíveis. Pode ser de um gatilho manual, um gatilho de recorrência ou um gatilho de sondagem. Isso provavelmente estará acompanhado de um status de **Disparado**, mas talvez isso não aconteça se você tiver uma condição ou comando SplitOn na exibição de código que não foi atendida.
* **Falha**. Um erro foi gerado.

#### <a name="starting-a-trigger-manually"></a>Iniciar um gatilho manualmente
Se você quiser que o aplicativo lógico verifique imediatamente se há um gatilho disponível (sem aguardar a próxima recorrência), você poderá clicar no botão **Selecionar Gatilho** na folha principal para forçar uma verificação. Por exemplo, clicar nesse link com um gatilho Dropbox fará com que o fluxo de trabalho sonde o Dropbox imediatamente em busca de novos arquivos.

### <a name="run-history"></a>Histórico da execução
Cada gatilho que é acionado resulta em uma execução. Você pode acessar informações da execução da folha principal, que contém muitas informações que podem ser úteis para entender o que aconteceu durante o fluxo de trabalho.

![Localizando o histórico da execução][2]

Uma execução exibe um dos seguintes status:

* **Êxito**. Todas as ações foram bem-sucedidas ou, se tiver ocorrido uma falha, ela terá sido tratada por uma ação posterior no fluxo de trabalho. Ou seja, ela terá sido tratada por uma ação que foi definida para ser executada depois de uma ação com falha.
* **Falha**. Pelo menos uma ação teve uma falha que não foi tratada por uma ação posterior no fluxo de trabalho.
* **Cancelado**. O fluxo de trabalho estava em execução, mas recebeu uma solicitação de cancelamento.
* **Executando**. O fluxo de trabalho está em execução atualmente. Isso pode ocorrer para os fluxos que estão sendo limitados, ou devido ao plano de preço atual. Consulte os limites da ação na [página de preços](https://azure.microsoft.com/pricing/details/app-service/plans/) para obter detalhes. Configurar o diagnóstico (os gráficos listados abaixo do histórico da execução) também podem fornecer informações sobre quaisquer eventos de restrição que estão ocorrendo.

Quando você estiver observando um histórico da execução, você pode analisar para obter mais detalhes.  

#### <a name="trigger-outputs"></a>Saídas do gatilho
As saídas do gatilho mostrarão os dados recebidos do gatilho. Isso pode ajudá-lo a determinar se todas as propriedades retornaram conforme o esperado.

> [!NOTE]
> Poderá ser útil entender como o recurso Aplicativos Lógicos [lida com os diferentes tipos de conteúdo](../logic-apps/logic-apps-content-type.md) se você vir qualquer conteúdo que não entender.
> 
> 

![Exemplos de saída do gatilho][3]

#### <a name="action-inputs-and-outputs"></a>Entradas e saídas da ação
Você pode analisar as entradas e saídas que uma ação recebeu. Isso é útil para entender o tamanho e a forma das saídas, bem como ver todas as mensagens de erro que possam ter sido geradas.

![Entradas e saídas da ação][4]

## <a name="debugging-workflow-runtime"></a>Depurando o tempo de execução do fluxo de trabalho
Além de monitorar as entradas, saídas e gatilhos de uma execução, pode ser útil adicionar algumas etapas em um fluxo de trabalho para ajudar na depuração. [RequestBin](http://requestb.in) é uma ferramenta poderosa que você pode adicionar como uma etapa em um fluxo de trabalho. Usando o RequestBin, você pode configurar um inspetor de solicitação HTTP para determinar exatamente o tamanho, forma e formato de uma solicitação HTTP. Você pode criar um novo RequestBin e colar a URL em uma Ação HTTP POST do aplicativo lógico junto com qualquer conteúdo do corpo que você deseje testar (por exemplo, uma expressão, ou outra saída da etapa). Depois de executar o aplicativo lógico, você poderá atualizar o RequestBin para ver como a solicitação foi formada, já que ela foi gerada do mecanismo dos Aplicativos Lógicos.

<!-- image references -->
[1]: ./media/logic-apps-diagnosing-failures/triggerhistory.png
[2]: ./media/logic-apps-diagnosing-failures/runhistory.png
[3]: ./media/logic-apps-diagnosing-failures/triggeroutputslink.png
[4]: ./media/logic-apps-diagnosing-failures/actionoutputs.png



<!--HONumber=Jan17_HO3-->


