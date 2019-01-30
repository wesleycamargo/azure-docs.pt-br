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
ms.date: 12/20/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 12/20/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 57aa0146f29d45dbcb5b1a0ac2f4fbdf31cb045b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238747"
---
# <a name="validate-a-new-azure-stack-solution"></a>Validar uma nova solução do Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Saiba como usar o **validação de solução** fluxo de trabalho para certificar a novas soluções do Azure Stack.

Uma solução do Azure Stack é uma lista de hardware de materiais (BoM) que tenha sido em conjunto combinada entre a Microsoft e o parceiro depois de atender os requisitos de certificação de logotipo do Windows Server. Uma solução deve ser recertified quando houve uma alteração para o BoM de hardware. Para fazer outras perguntas sobre quando reconfirmar soluções, entre em contato com a equipe em [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

Para certificar sua solução, execute o fluxo de trabalho de validação de soluções duas vezes. Executá-lo uma vez para o *minimamente* suporte para configuração. Executá-lo uma segunda vez para o *máximo* suporte para configuração. Microsoft certifica que a solução se ambas as configurações de passarem todos os testes.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Criar um fluxo de trabalho de validação de soluções

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Selecione **iniciar** sobre o **validações de solução** lado a lado.

    ![Bloco de fluxo de trabalho de validações de solução](media/tile_validation-solution.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Selecione o **configuração da solução**.
    - **Mínimo**: a solução é configurada com o número mínimo com suporte de nós.
    - **Máximo**: a solução é configurada com o número máximo com suporte de nós.
6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Informações sobre validação de soluções](media/workflow_validation-solution_info.png)

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Parâmetros de ambiente não podem ser modificados depois de criar um fluxo de trabalho.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Você será redirecionado à página de resumo de testes.

## <a name="run-solution-validation-tests"></a>Executar testes de validação de soluções

No **resumo de testes de validação de solução** página, você verá uma lista dos testes são necessárias para concluir a validação.

Nos fluxos de trabalho validação **agendamento** um teste usa os parâmetros comuns de nível de fluxo de trabalho que você especificou durante a criação de fluxo de trabalho (consulte [parâmetros comuns de fluxo de trabalho para validação de pilha do Azure como um serviço](azure-stack-vaas-parameters.md)). Se qualquer um dos valores de parâmetro de teste se tornar inválido, deve resupply-las conforme as instruções [modifique os parâmetros de fluxo de trabalho](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Um teste de validação no agendamento sobre uma instância existente, você criará uma nova instância no lugar da instância antiga no portal. Os logs para a instância antiga serão retidos, mas não são acessíveis a partir do portal.  
Depois que um teste for concluído com êxito, o **agendamento** ação for desabilitada.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

2. Selecione os seguintes testes:
    - Mecanismo de simulação de nuvem
    - Suite operacional do SDK de computação
    - Teste de identificação de disco
    - Suite operacional do SDK de extensão do Cofre de chaves
    - Suite operacionais do SDK do KeyVault
    - Rede SDK operacional Suite
    - Conta de armazenamento SDK Suite operacional

3. Selecione **agendamento** no menu de contexto para abrir um prompt para agendar a instância de teste.

4. Revise os parâmetros de teste e, em seguida, selecione **enviar** para agendar o teste para execução.

![Teste de validação de solução de agendamento](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Próximas etapas

- [Monitorar e gerenciar testes no portal VaaS](azure-stack-vaas-monitor-test.md)