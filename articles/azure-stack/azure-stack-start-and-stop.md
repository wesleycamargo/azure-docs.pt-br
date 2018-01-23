---
title: Iniciar e parar a pilha do Azure | Microsoft Docs
description: Saiba como iniciar e desativar a pilha do Azure.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: mabrigg
ms.openlocfilehash: 09c22b22b49664a55b8a9e77936df3b1362d5b88
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="start-and-stop-azure-stack"></a>Iniciar e parar a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas (versão 1712 e posterior)*

## <a name="stop-azure-stack"></a>Parar a pilha do Azure 

Desligar a pilha do Azure com as seguintes etapas:

1. Abra uma sessão com privilégios de ponto de extremidade (PEP) de um computador com acesso à rede para máquinas virtuais ERCS da pilha do Azure. Para obter instruções, consulte [usando o ponto de extremidade com privilégios na pilha do Azure](azure-stack-privileged-endpoint.md).

2. PEP, execute:

    ```powershell
      Stop-AzureStack
    ```

3. Aguarde para todos os nós físicos de pilha do Azure para power off.

> [!Note]  
> Você pode verificar o status de energia de um nó físico, seguindo as instruções do fabricante de equipamento Original (OEM) que forneceu o hardware de pilha do Azure. 

## <a name="get-the-startup-status-for-azure-stack"></a>Obtém o status de inicialização para a pilha do Azure

Obter a inicialização para a rotina de inicialização de pilha do Azure com as seguintes etapas:

1. Abra uma sessão de ponto de extremidade com privilégios de um computador com acesso à rede para máquinas virtuais ERCS da pilha do Azure.

2. PEP, execute:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="start-azure-stack"></a>Iniciar a pilha do Azure 

Inicie a pilha do Azure com as etapas a seguir. Siga estas etapas, independentemente de como a pilha do Azure interrompido.

1. Alimentação em cada um de nós físicos em seu ambiente de pilha do Azure. Verifique se as instruções para os nós físicos de ligar seguindo as instruções do fabricante de equipamento Original (OEM) que forneceu o hardware para a pilha do Azure.

2. Aguarde até que inicia os serviços de infraestrutura de pilha do Azure. Serviços de infraestrutura de pilha do Azure podem exigir duas horas para concluir o processo de inicialização. Você pode verificar o status de início da pilha do Azure com o [ **Get-ActionStatus** cmdlet](#get-the-startup-status-for-azure-stack).

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Solucionar problemas de inicialização e desligamento da pilha do Azure

Se os serviços de infraestrutura e de locatário não iniciar com êxito após ligar seu ambiente de pilha do Azure, execute as seguintes etapas. 

1. Abra uma sessão de ponto de extremidade com privilégios de um computador com acesso à rede para máquinas virtuais ERCS da pilha do Azure.

2. Execute: 

    ```powershell
      Test-AzureStack
      ```

3. Examine a saída e resolva quaisquer erros de integridade. Para obter mais informações, consulte [executar um teste de validação da pilha do Azure](azure-stack-diagnostic-test.md).

4. Execute:

    ```powershell
      Start-AzureStack
    ```

5. Se executando **AzureStack início** resulta em uma falha, entre em contato com o serviços de atendimento ao cliente Microsoft. 

## <a name="next-steps"></a>Próximas etapas 

Saiba mais sobre a ferramenta de diagnóstico do Azure pilha e emitir o registro em log, consulte [ferramentas de diagnóstico do Azure pilha. Ferramentas de diagnóstico do Azure de pilha. Ferramentas de diagnóstico do Azure de pilha. Ferramentas de diagnóstico do Azure de pilha.