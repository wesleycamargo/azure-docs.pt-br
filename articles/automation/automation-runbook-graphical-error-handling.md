---
title: "Tratamento de erros em runbooks gráficos na Automação do Azure | Microsoft Docs"
description: "Este artigo descreve como implementar a lógica de tratamento de erros em runbooks gráficos na Automação do Azure."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/26/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: f9b359691122da9e5d93e51f3085cad51e55d8f2
ms.openlocfilehash: 13c3e1693badcf4148738cb63666f34546d1696c

---

# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Tratamento de erros em runbooks gráficos na Automação do Azure

Uma entidade de design de runbook de chave que você precisa considerar é identificar diferentes problemas que um runbook pode ter, como sucesso, estados de erro esperados e condições de erro inesperadas.  Os runbooks deve incluir o tratamento de erros para realizar a detecção adequadamente.  Com runbooks gráficos, para validar a saída de uma atividade ou manipular um erro de forma apropriada, você possivelmente executaria uma atividade de código do PowerShell, definiria a lógica condicional no link de saída da atividade ou aplicaria outro método.          

Muitas vezes, quando seus runbooks são executados, se houver um erro não fatal que ocorre com uma atividade, qualquer atividade a seguir será processada mesmo assim.  Claro, ele provavelmente gerará uma exceção, mas o importante é que a próxima atividade pode ser executada. O motivo para esse comportamento é devido a como a linguagem do PowerShell foi projetada para lidar com erros.    

Os tipos de erros do PowerShell que podem ocorrer durante a execução são de finalização ou não fatais.  A diferença é o seguinte:

* Erro de finalização: um erro grave durante a execução que interrompe o comando (ou a execução do script) completamente. Exemplos podem incluir cmdlets inexistentes, erros de sintaxe que impediriam a execução de um cmdlet ou outros erros fatais.

* Erro não fatal: um erro não grave que permite que a execução continue, apesar da falha. Exemplos incluem erros operacionais como arquivo não encontrado, problemas de permissões etc.

Os runbooks gráficos da Automação do Azure foram aprimorados com a possibilidade de incluir o tratamento de erros. Assim, agora você pode ativar exceções em erros de não finalização e criar links de erro entre atividades. Isso permite que um autor de runbook capture erros e tenha um caminho para gerenciar a condição realizada ou inesperada.  

## <a name="when-to-use"></a>Quando usar

Controlar a execução de fluxos de trabalho é importante para garantir que sempre que houver uma atividade essencial que gere um erro ou exceção, você possa evitar prosseguir para a próxima atividade no runbook e trate o erro adequadamente.  Isso é particularmente necessário quando seus runbooks dão suporte a uma empresa ou a um processo de operações de serviço.

Para cada atividade que pode produzir um erro, o autor do runbook pode adicionar um link de erro apontando para qualquer outra atividade.  A atividade de destino pode ser de qualquer tipo: atividade de código, invocação de um cmdlet, chamada de outro runbook ou qualquer outro item. 

Além disso, a atividade de destino também pode ter links de saída, e eles podem ser links regulares ou de erro, para que o autor do runbook possa implementar a lógica de tratamento de erros complexa sem incluir uma atividade de código.  Embora a prática recomendada seja criar um runbook dedicado de tratamento de erros com funcionalidade comum, isso não é obrigatório, e a lógica de tratamento de erros em uma atividade de código do PowerShell não é a única alternativa.  

Por exemplo, considere um runbook que tenta iniciar uma VM e instalar um aplicativo. Se a VM não for iniciada corretamente, ele executará duas ações: 

1. Envie uma notificação sobre esse problema.
2. Inicie outro runbook que provisiona automaticamente uma nova VM em vez disso. 

Uma solução seria ter um link de erro apontando para uma atividade para lidar com a etapa 1, como o cmdlet **Write-Warning**, conectado a uma atividade para a etapa 2, como o cmdlet **Start-AzureRmAutomationRunbook**. 

Você também pode generalizar esse comportamento para uso em vários runbooks e colocar essas duas atividades em um runbook de tratamento de erros separado seguindo as orientações sugeridas anteriormente.  Antes de chamar esse runbook de tratamento de erros, você pode criar uma mensagem personalizada com base nos dados no runbook original e, em seguida, passá-la como um parâmetro para o runbook de tratamento de erros. 

## <a name="how-to-use"></a>Como usar

Cada atividade tem uma configuração para ativar exceções para erros de não finalização. Por padrão, isso está desabilitado.  Você deve habilitar isso em qualquer atividade em que deseje manipular erros.  Habilitando a configuração, você garante que erros de não finalização e finalização na atividade sejam tratados como erros de não finalização e possam ser manipulados com um link de erro.  Depois de definir essa configuração, você cria uma atividade que manipulará o erro.  Se uma atividade produzir erros, os links de saída de erro serão seguidos e os links regulares não o serão, mesmo que uma atividade também produza saída regular.<br><br> ![Exemplo de link de erro de runbook de automação](media/automation-runbook-graphical-error-handling/error-link-example.png)

No exemplo simples a seguir, temos um runbook que recupera uma variável que contém o nome do computador de uma máquina virtual e, em seguida, tenta iniciar a máquina virtual com a próxima atividade.<br><br> ![Exemplo de tratamento de erro de runbook de automação](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

A atividade **Get-AutomationVariable** e **Start-AzureRmVm** são configurados para converter exceções em erros.  Se houver problemas ao obter a variável ou iniciar a VM, serão gerados erros.<br><br> ![Configurações de atividade de tratamento de erros de runbook de automação](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Links de erro fluem dessas atividades para uma única atividade de **Gerenciamento de Erros** (uma atividade de Código), que é configurada com uma expressão simples do PowerShell usando a palavra-chave *Throw* para parar o processamento, juntamente com *$Error.Exception.Message* para obter a mensagem que descreve a exceção atual.<br><br> ![Exemplo de código de tratamento de erros de runbook de automação](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre links e compreender os tipos de link em runbooks gráficos, confira [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md#links-and-workflow).

* Para saber mais sobre a execução de runbooks, como monitorar trabalhos de runbook e outros detalhes técnicos, confira [Acompanhar um trabalho de runbook](automation-runbook-execution.md) 


<!--HONumber=Jan17_HO1-->


