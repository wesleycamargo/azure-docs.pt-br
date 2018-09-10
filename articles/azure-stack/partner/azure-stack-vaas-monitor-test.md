---
title: Monitore um teste com a validação do Azure Stack como um serviço | Microsoft Docs
description: Monitore um teste com a validação do Azure Stack como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 2dc4d3f2855864ff80648b5b9635ff28c0dacbb7
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163311"
---
# <a name="monitor-a-test-with-azure-stack-validation-as-a-service"></a>Monitore um teste com a validação do Azure Stack como um serviço

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

A execução de um teste pode ser monitorada por meio da exibição de **operações** página para conjuntos de testes que estão em andamento ou concluída. Esta página fornece detalhes sobre o status do teste e suas operações.

## <a name="monitor-a-test"></a>Monitore um teste

1. Selecione uma solução.

2. Selecione **gerenciar** em qualquer bloco de fluxo de trabalho.

3. Clique em um fluxo de trabalho para abrir a página de resumo do teste.

4. Expanda o menu de contexto **[...]**  para qualquer instância do conjunto de teste.

5. Selecione **Exibir operações**

![Alt text](media\image4.png)

Para testes que foram finalizados em execução, os logs podem ser baixados da página de resumo de teste clicando em **baixar logs** no menu de contexto de um teste **[...]** . Parceiros do Azure Stack podem usar esses logs para depurar problemas para testes com falha.

## <a name="open-the-test-pass-summary"></a>Abrir o resumo do teste de passagem

1. Abra o portal. 
2. Selecione o nome de uma solução existente que contém os testes de execução ou agendados anteriormente.

    ![Gerenciar aprovações de teste](media/managetestpasses.png)

3. Selecione **Manage** na **teste é aprovado** painel.
4. Selecione a aprovação do teste para abrir o resumo de aprovação do teste. Você pode examinar o nome do teste, data de criação, executar, quanto tempo levou o teste e o resultado (êxito ou falha).
5. Selecione [ **...  .** ].

### <a name="test-pass-summary"></a>Resumo do teste de passagem

| Coluna | DESCRIÇÃO |
| --- | --- |
| Nome do teste | O nome do teste. Referencia o número de validação. |
| Criado | Hora em que a aprovação do teste foi criada. |
| Iniciado | Hora da que execução de teste anterior. |
| Duration | Período de tempo, o tempo necessário para executar a passagem de teste. |
| Status | O resultado (êxito ou falha) para a passagem de rest. |
| Nome do Agente | O nome de domínio totalmente qualificado do agente. |
| Total de operações | O número total de operações de tentativa na fase de teste. |
| Operações passadas | O número de operações que são passados na fase de teste. |
|  Operações com falha | O número de operações que falharam. |

### <a name="group-columns-in-the-test-pass-summary"></a>Colunas de grupo no teste passam resumidas

Você pode selecionar e arrastar uma coluna para o cabeçalho para criar um grupo no valor da coluna.

## <a name="reschedule-a-test"></a>Reagendar um teste

1. [Abrir o resumo do teste de passagem](#open-the-test-pass-summary).
2. Selecione **reagendar** reagendar a aprovação do teste.
3. Insira a senha do administrador de nuvem para sua instância do Azure Stack.
4. Insira a cadeia de Conexão de armazenamento de diagnóstico definido quando você configura sua conta.
5. Reagende o teste.

## <a name="cancel-a-test"></a>Cancelar um teste

1. [Abrir o resumo do teste de passagem](#open-the-test-pass-summary).
2. Selecione **Cancelar**.

## <a name="get-test-information"></a>Obter informações de teste

1. [Abrir o resumo do teste de passagem](#open-the-test-pass-summary).
2. Selecione **exibir informações** reagendar a aprovação do teste.

**Informações de teste**

| NOME | DESCRIÇÃO |
| -- | -- |
| Nome do teste | O nome do teste, por exemplo, atualização de OEM no Azure Stack 1806 RC validação. |
| Versão de teste | A versão do teste, por exemplo, 5.1.4.0. |
| Publicador | O Editor de teste, como o Microsoft. |
| Categoria | A categoria de teste, tais como **funcional** ou **confiabilidade**. |
| Serviços de destino | Os serviços que está sendo testados, como VirtualMachines |
| DESCRIÇÃO | A descrição do teste. |
| Duração estimada (minutos) | O período de tempo em minutos que o teste levou para ser executado. |
| Links | Um link para o rastreador de problemas do GitHub. |

## <a name="get-test-parameters"></a>Obter parâmetros de teste

1. [Abrir o resumo do teste de passagem](#open-the-test-pass-summary).
2. Selecione **Exibir parâmetros** reagendar a aprovação do teste.

**Parâmetros**

| NOME | DESCRIÇÃO |
| -- | -- |
| Nome do teste | O nome do teste, por exemplo, oemupdate1806test. |
| Versão de teste | A versão de rest, por exemplo, 5.1.4.0. |
| ID da instância de teste | Um GUID que identifica a instância específica do teste, por exemplo, 20b20645-b400-4f0d-bf6f-1264d866ada9. |
| cloudAdminUser | O nome da conta usada como o administrador de nuvem, por exemplo, **cloudadmin**. |
| DiagnosticsContainerName | A ID do contêiner do diagnóstico, por exemplo, 04dd3815-5f35-4158-92ea-698027693080. |

## <a name="get-test-operations"></a>Obter operações de teste

1. [Abrir o resumo do teste de passagem](#open-the-test-pass-summary).
2. Selecione **Exibir operações** reagendar a aprovação do teste. Abre o painel de resumo de operações.

## <a name="get-test-logs"></a>Obter logs de teste

1. [Abrir o resumo do teste de passagem](#open-the-test-pass-summary).
2. Selecione **baixar logs** reagendar a aprovação do teste.  
    Um arquivo zip denominado ReleaseYYYY-MM-DD.zip que contém os downloads de logs.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre [validação do Azure Stack como um serviço](https://docs.microsoft.com/azure/azure-stack/partner).
