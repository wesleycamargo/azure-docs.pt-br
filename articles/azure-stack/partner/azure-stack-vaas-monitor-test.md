---
title: Monitorar e gerenciar testes no portal do Azure Stack VaaS | Microsoft Docs
description: Monitore e gerencie testes no portal do Azure Stack VaaS.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 03efe32e7a9a29318e4f97ce5636616fad443284
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956656"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>Monitorar e gerenciar testes no portal VaaS

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Depois de agendar os testes em sua solução do Azure Stack, a validação como um serviço (VaaS) começará comunicando o status de execução de teste. Essas informações estão disponíveis no portal do VaaS junto com ações como reagendamento e cancelamento de testes.

## <a name="navigate-to-the-workflow-tests-summary-page"></a>Navegue até a página de resumo de testes de fluxo de trabalho

1. No painel de soluções, selecione uma solução existente que tenha pelo menos um fluxo de trabalho.

    ![Blocos de fluxo de trabalho](media/tile_all-workflows.png)

1. Selecione **gerenciar** no bloco de fluxo de trabalho. A próxima página lista os fluxos de trabalho criados para a solução selecionada.

1. Selecione o nome do fluxo de trabalho para abrir o resumo do teste.

## <a name="change-workflow-parameters"></a>Alterar os parâmetros de fluxo de trabalho

Cada tipo de fluxo de trabalho permite que você edite os [parâmetros de teste](azure-stack-vaas-parameters.md#test-parameters) especificados durante a criação de fluxo de trabalho.

1. Na página de resumo de testes, selecione a **editar** botão.

1. Fornecer novos valores de acordo com a [parâmetros comuns de fluxo de trabalho para validação de pilha do Azure como um serviço](azure-stack-vaas-parameters.md).

1. Selecione **enviar** para salvar os valores.

> [!NOTE]
> No **aprovação do teste** fluxo de trabalho, você precisará concluir a seleção de teste e vá para a página de revisão antes de salvar os novos valores de parâmetro.

### <a name="add-tests-test-pass-only"></a>Adicionar testes (modo de aprovação de teste)

No **aprovação do teste** fluxos de trabalho, ambos os **adicionar testes** e **editar** botões permitem que você agende novos testes no fluxo de trabalho.

> [!TIP]
> Selecione **adicionar testes** se você apenas deseja agendar novos testes e não precisa editar os parâmetros para um **aprovação do teste** fluxo de trabalho.

## <a name="managing-test-instances"></a>Gerenciar instâncias de teste

Para execuções não oficiais (ou seja, o **aprovação do teste** fluxo de trabalho), a página de resumo de testes lista os testes agendados em comparação com a solução do Azure Stack.

Para execuções oficiais (ou seja, o **validação** fluxos de trabalho), a página de resumo de testes lista os testes que são necessários para concluir a validação da solução do Azure Stack. Testes de validação são agendados a partir desta página.

Cada instância de teste agendada mostra as seguintes informações:

| Coluna | DESCRIÇÃO |
| --- | --- |
| Nome do teste | O nome e versão do teste. |
| Categoria | A finalidade do teste. |
| Criado | A hora em que o teste foi agendado. |
| Iniciado | A hora em que o teste começou execução. |
| Duration | O período de tempo que o teste foi executado. |
| Status | O estado ou o resultado do teste. Status de pré-execução ou em andamento são: `Pending`, `Running`. Status de terminal são: `Cancelled`, `Failed`, `Aborted`, `Succeeded`. |
| Nome do agente | O nome do agente que executou o teste. |
| Total de operações | O número total de operações de tentativa durante o teste. |
| Operações passadas | O número de operações que foram bem-sucedidas durante o teste. |
|  Operações com falha | O número de operações que falharam durante o teste. |

### <a name="actions"></a>Ações

Cada instância de teste lista Ações disponíveis que podem ser executadas quando você clicar no menu de contexto **[...]**  na tabela de instâncias de teste.

#### <a name="view-information-about-the-test-definition"></a>Exibir informações sobre a definição de teste

Selecione **exibir informações** no menu de contexto para exibir informações gerais sobre a definição de teste. Isso é compartilhado por cada instância de teste com o mesmo nome e versão.

| Propriedade de teste | DESCRIÇÃO |
| -- | -- |
| Nome do teste | O nome do teste. |
| Versão de teste | A versão do teste. |
| Publicador | O Editor do teste. |
| Categoria |  A finalidade do teste. |
| Serviços de destino | Os serviços do Azure Stack que está sendo testado. |
| DESCRIÇÃO | A descrição do teste. |
| Duração estimada (minutos) | O tempo de execução esperado do teste. |
| Links | Todas as informações relevantes sobre o teste ou pontos de contato. |

#### <a name="view-test-instance-parameters"></a>Exibir parâmetros de instância de teste

Selecione **Exibir parâmetros** no menu de contexto para exibir os parâmetros fornecidos para a instância de teste em horário agendado. Cadeias de caracteres confidenciais, como senhas, não são exibidas. Essa ação está disponível somente para testes que foram agendados.

Essa janela inclui os seguintes metadados para todas as instâncias de teste:

| Propriedade de instância de teste | DESCRIÇÃO |
| -- | -- |
| Nome do teste | O nome do teste. |
| Versão de teste | A versão do teste. |
| ID da instância de teste | Um GUID que identifica a instância específica do teste. |

#### <a name="view-test-instance-operations"></a>Exibir operações de instância de teste

Selecione **Exibir operações** do contexto de menu para exibir um status detalhado das operações executadas durante o teste. Essa ação está disponível somente para testes que foram agendados.

![Exibir operações](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>Baixar logs para uma instância de teste concluído

Selecione **baixar logs** no menu de contexto para baixar um `.zip` arquivo de saída de logs durante a execução de teste. Essa ação está disponível somente para testes que foram concluídos, ou seja, um teste com um status de `Cancelled`, `Failed`, `Aborted`, ou `Succeeded`.

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>Reagendar uma instância de teste ou agende um teste

Programando testes na página de gerenciamento depende do tipo de fluxo de trabalho que o teste é executado.

##### <a name="test-pass-workflow"></a>Fluxo de trabalho de aprovação do teste

No fluxo de trabalho aprovação de teste **reagendamento** uma instância de teste reutiliza o mesmo conjunto de parâmetros como a instância original do teste e *substitui* o resultado da original, incluindo seus logs. Você precisará reinserir as cadeias de caracteres confidenciais, como senhas, quando você reagendar.

1. Selecione **reagendar** no menu de contexto para abrir um prompt para a instância de teste de reagendamento.

1. Insira todos os parâmetros aplicáveis.

1. Selecione **enviar** para reagendar a instância de teste e substituir a instância existente.

##### <a name="validation-workflows"></a>Fluxos de trabalho de validação

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>Cancelar uma instância de teste

Um teste agendado pode ser cancelado se seu status será `Pending` ou `Running`.  

1. Selecione **Cancelar** no menu de contexto para abrir um prompt para cancelar a instância de teste.

1. Selecione **enviar** para cancelar a instância de teste.

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas de validação como um serviço](azure-stack-vaas-troubleshoot.md)
