---
title: Usar a validação como um serviço para o portal do Azure Stack para agendar seu primeiro teste | Microsoft Docs
description: Use a validação como um serviço para o portal do Azure Stack para agendar seu primeiro teste.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: How to
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 1fbda6c0351287b9bc7574d100c0862b172a0aed
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651684"
---
# <a name="schedule-your-first-test"></a>Agendar seu primeiro teste

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Agende um teste na validação como um portal de serviço (VaaS) para sua solução do Azure Stack. Uma solução VaaS representa uma solução do Azure Stack com uma lista de hardware específicas de materiais (BoM). Você pode agendar um teste para verificar o hardware pode executar o Azure Stack.

Para verificar se sua solução, crie o fluxo de trabalho para um teste. Um fluxo de trabalho VaaS opera dentro do contexto de uma solução VaaS. Ele representa um conjunto de conjuntos de testes que exercitam a funcionalidade de uma implantação do Azure Stack em seu hardware. Adicione os parâmetros ambientais da sua solução e selecione um ou mais testes para execução em sua solução.

Enquanto o fluxo de trabalho de aprovação do teste pode ser usado para executar qualquer teste fornecida pelo VaaS, incluindo testes dos fluxos de trabalho de validação, os resultados do fluxo de trabalho de aprovação do teste não são considerados *oficial*. Para obter informações sobre fluxos de trabalho de validação oficial, consulte [fluxos de trabalho](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir este início rápido, você deve concluir os seguintes itens:

- [Configurar sua validação como um serviço de recursos](azure-stack-vaas-set-up-resources.md)
- [Implantar o agente local](azure-stack-vaas-local-agent.md) (recomendado)
- [Validação como um conceitos principais do serviço](azure-stack-vaas-key-concepts.md) (recomendado)

## <a name="start-a-workflow"></a>Iniciar um fluxo de trabalho

![Entre no portal do VaaS](media/vaas_portalsignin.png)

Entrar no portal, selecione ou criação de uma solução e, em seguida, selecione a solução.

1. Entrar para o [VaaS portal](https://azurestackvalidation.com).
2. Digite o nome de uma solução existente ou selecione **nova solução** para criar uma nova solução. Para obter instruções, consulte [criar uma solução no portal do VaaS](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-vaas-portal).
3. Selecione **começar** sobre o **teste é aprovado** lado a lado.

## <a name="specify-parameters"></a>Especificar parâmetros

![Texto ALT](media/vaas_test_pass_parameters.png)

Defina o fluxo de trabalho para sua solução. O fluxo de trabalho tem as etapas do processo usado para testar sua solução.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Selecione **próxima** para selecionar testes para agendar.

## <a name="select-tests-to-run"></a>Selecione os testes a serem executados

Escolha os testes que você deseja executar no seu fluxo de trabalho.

1. Selecione o (s) que você deseja executar em seu fluxo de trabalho.

    Se você quiser substituir os parâmetros comuns (ou seja, os parâmetros fornecidos na seção anterior) para qualquer teste, selecione sobre o **editar** link Avançar para especificar novos valores.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]
1. Selecione **próxima** para examinar o fluxo de trabalho.

## <a name="review-and-submit"></a>Revisar e enviar

Examinar, criar e, em seguida, você agendar fluxo de trabalho.

1. Examine as informações exibidas.

    Os serviços cria seu fluxo de trabalho com as informações fornecidas e os testes selecionados serão agendados.

    Se nada aparecer incorreto, use o **anterior** botões para ir para uma seção anterior.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>Próximas etapas

- [Monitorar e gerenciar testes no portal VaaS](azure-stack-vaas-monitor-test.md)
