---
title: Validar uma nova solução do Azure Stack | Microsoft Docs
description: Saiba como validar uma nova solução do Azure Stack com validação como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 777609b89bc08cd61489d2c3a3669ec07ccbc372
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646994"
---
# <a name="validate-a-new-azure-stack-solution"></a>Validar uma nova solução do Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Saiba como usar o **validação de solução** fluxo de trabalho para certificar a novas soluções do Azure Stack.

Uma solução do Azure Stack é uma lista de hardware de materiais (BoM) que tenha sido em conjunto combinada entre a Microsoft e o parceiro depois de atender os requisitos de certificação de logotipo do Windows Server. Uma solução deve ser recertified quando houve uma alteração para o BoM de hardware. Para fazer outras perguntas sobre quando reconfirmar soluções, entre em contato com a equipe em [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

Para certificar sua solução, execute o fluxo de trabalho de validação de soluções duas vezes. Executá-lo uma vez para o *minimamente* suporte para configuração. Executá-lo uma segunda vez para o *máximo* suporte para configuração. Microsoft certifica que a solução se ambas as configurações de passarem todos os testes.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Criar um fluxo de trabalho de validação de soluções

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]
2. Selecione **iniciar** sobre o **validações de solução** lado a lado.

    ![Bloco de fluxo de trabalho de validações de solução](media/tile_validation-solution.png)

3. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
4. Selecione o **configuração da solução**.
    - **Mínimo**: a solução é configurada com o número mínimo com suporte de nós.
    - **Máximo**: a solução é configurada com o número máximo com suporte de nós.
5. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Informações sobre validação de soluções](media/workflow_validation-solution_info.png)

6. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Parâmetros de ambiente não podem ser modificados depois de criar um fluxo de trabalho.

7. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
8. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Você será redirecionado à página de resumo de testes.

## <a name="execute-solution-validation-tests"></a>Executar testes de validação de soluções

No **resumo de testes de validação de solução** página, você verá uma lista dos testes são necessárias para concluir a validação.

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

![Teste de validação de solução de agendamento](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Próximas etapas

- [Monitorar e gerenciar testes no portal VaaS](azure-stack-vaas-monitor-test.md)