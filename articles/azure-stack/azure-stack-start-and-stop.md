---
title: Iniciar e parar o Azure Stack | Microsoft Docs
description: Saiba como iniciar e desligar o Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 6b14f5e8967567030ce854d05c53a8d26027e9a6
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428832"
---
# <a name="start-and-stop-azure-stack"></a>Iniciar e parar o Azure Stack
Você deve seguir os procedimentos neste artigo para desligar e reiniciar os serviços do Azure Stack corretamente. Desligamento será desligar fisicamente todo o ambiente do Azure Stack. Iniciar o powers em todas as funções de infraestrutura e retorna a recursos para o estado de energia que estavam antes do desligamento do locatário.

## <a name="stop-azure-stack"></a>Parar o Azure Stack 

Desligue o Azure Stack com as seguintes etapas:

1. Prepare todas as cargas de trabalho em execução em recursos do locatário do seu ambiente do Azure Stack para o desligamento futuro. 

2. Abra uma sessão com privilégios de ponto de extremidade (PEP) de um computador com acesso à rede para as VMs do Azure Stack ERCS. Para obter instruções, consulte [usando o ponto de extremidade privilegiado no Azure Stack](azure-stack-privileged-endpoint.md).

3. Em PEP, execute:

    ```powershell
      Stop-AzureStack
    ```

4. Aguarde até todos os nós físicos do Azure Stack para power off.

> [!Note]  
> Você pode verificar o status de energia de um nó físico, seguindo as instruções do fabricante de equipamento Original (OEM) que forneceu o hardware do Azure Stack. 

## <a name="start-azure-stack"></a>Inicie o Azure Stack 

Inicie o Azure Stack com as etapas a seguir. Siga estas etapas, independentemente de como o Azure Stack interrompido.

1. Energia em cada um de nós físicos em seu ambiente do Azure Stack. Verifique se as instruções para os nós físicos de ligar, seguindo as instruções do fabricante de equipamento Original (OEM) que forneceu o hardware para o Azure Stack.

2. Aguarde até que os serviços de infraestrutura do Azure Stack é iniciado. Serviços de infraestrutura do Azure Stack podem exigir duas horas para concluir o processo de inicialização. Você pode verificar o status de início da pilha do Azure com o [ **Get-ActionStatus** cmdlet](#get-the-startup-status-for-azure-stack).

3. Certifique-se de que todos os seus recursos de locatário tiveram retornado para o estado em que estavam antes do desligamento. Cargas de trabalho executadas nos recursos de locatário talvez precise ser reconfigurado após a inicialização pelo Gerenciador de carga de trabalho.

## <a name="get-the-startup-status-for-azure-stack"></a>Obter o status de inicialização para o Azure Stack

Obter a inicialização para a rotina de inicialização do Azure Stack com as seguintes etapas:

1. Abra uma sessão com privilégios de ponto de extremidade de um computador com acesso à rede para as VMs do Azure Stack ERCS.

2. Em PEP, execute:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Solucionar problemas de inicialização e desligamento do Azure Stack

Execute as seguintes etapas se os serviços de infraestrutura e de locatário não iniciam com êxito 2 horas após você energia em seu ambiente do Azure Stack. 

1. Abra uma sessão com privilégios de ponto de extremidade de um computador com acesso à rede para as VMs do Azure Stack ERCS.

2. Execute: 

    ```powershell
      Test-AzureStack
      ```

3. Examine a saída e resolva quaisquer erros de integridade. Para obter mais informações, consulte [executar um teste de validação do Azure Stack](azure-stack-diagnostic-test.md).

4. Execute:

    ```powershell
      Start-AzureStack
    ```

5. Se executando **AzureStack início** resulta em uma falha, entre em contato com o serviços de atendimento ao cliente Microsoft. 

## <a name="next-steps"></a>Próximas etapas 

Saiba mais sobre [ferramentas de diagnóstico do Azure Stack](azure-stack-diagnostics.md)
