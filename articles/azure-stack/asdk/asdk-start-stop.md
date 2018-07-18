---
title: Iniciar e parar o Kit de desenvolvimento de pilha do Azure (ASDK) | Microsoft Docs
description: Saiba como iniciar e encerrar para baixo o Kit de desenvolvimento do Azure pilha (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: dfb565803746ecdda9b36a4e12a3c3f2b4d9e0d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
ms.locfileid: "31427331"
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>Iniciar e parar o Kit de desenvolvimento de pilha do Azure (ASDK)
Não é recomendável reiniciar o computador host ASDK simplesmente. Em vez disso, você deve seguir os procedimentos neste artigo para desligar e reiniciar serviços ASDK corretamente. 

## <a name="stop-azure-stack"></a>Parar a pilha do Azure 
Para desligar corretamente os serviços de pilha do Azure e o computador host ASDK, use os seguintes comandos do PowerShell:

1. Faça logon como AzureStack\CloudAdmin no computador host ASDK.
2. Abra o PowerShell como administrador (não o PowerShell ISE).
3. Execute os seguintes comandos para estabelecer uma sessão de ponto de extremidade privilegiado (PEP): 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Em seguida, na sessão PEP, use o **AzureStack Stop** cmdlet para interromper os serviços do Azure pilha e desligar o computador de host ASDK:

   ```powershell
   Stop-AzureStack
   ```
5. Examine a saída do PowerShell para garantir que todos os serviços do Azure pilha estão desligados com êxito antes do computador de host ASDK é desligado. O processo de desligamento leva vários minutos.

## <a name="start-azure-stack"></a>Iniciar a pilha do Azure 
Serviços ASDK deverá iniciar automaticamente quando o computador host é iniciado. No entanto, o tempo de inicialização de serviços de infraestrutura ASDK varia com base no desempenho da configuração de hardware do computador ASDK host. Ele pode levar várias horas para todos os serviços reiniciar com êxito em alguns casos.

Independentemente de como o ASDK foi desligado, você deve usar as etapas a seguir para verificar se todos os serviços do Azure pilha são iniciadas e totalmente operacional depois que o computador host está ligado: 

1. Ligue o computador de host ASDK. 
2. Faça logon como AzureStack\CloudAdmin no computador host ASDK.
3. Abra o PowerShell como administrador (não o PowerShell ISE).
4. Execute os seguintes comandos para estabelecer uma sessão de ponto de extremidade privilegiado (PEP):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. Em seguida, na sessão PEP, execute os comandos a seguir para verificar o status de inicialização dos serviços de pilha do Azure:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Examine a saída para garantir que os serviços do Azure pilha tem reiniciado com êxito.

Para saber mais sobre os procedimentos recomendados para desligar e reiniciar os serviços do Azure pilha corretamente, consulte [iniciar e parar Azure pilha](.\.\azure-stack-start-and-stop.md). 

## <a name="troubleshoot-startup-and-shutdown"></a>Solucionar problemas de inicialização e desligamento 
Se os serviços do Azure pilha não iniciam com êxito dentro de duas horas após ligar o computador de host ASDK, execute estas etapas:

1. Faça logon como AzureStack\CloudAdmin no computador host ASDK.
2. Abra o PowerShell como administrador (não o PowerShell ISE).
3. Execute os seguintes comandos para estabelecer uma sessão de ponto de extremidade privilegiado (PEP):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Em seguida, na sessão PEP, execute os comandos a seguir para verificar o status de inicialização dos serviços de pilha do Azure:

   ```powershell
   Test-AzureStack
   ```
5. Examine a saída e resolva quaisquer erros. Para obter mais informações, consulte [executar um teste de validação da pilha do Azure](.\.\azure-stack-diagnostic-test.md).
6. Reinicie os serviços do Azure pilha de dentro da sessão PEP executando o **AzureStack início** cmdlet:

   ```powershell
   Start-AzureStack
   ```

Se executando **início AzureStack** resulta em uma falha, visite o [Fórum de suporte do Azure pilha](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) obter ASDK Solucionando problemas de suporte. 

## <a name="next-steps"></a>Próximas etapas 
Saiba mais sobre a ferramenta de diagnóstico do Azure pilha e emitir o registro em log, consulte [ferramentas de diagnóstico do Azure pilha](.\.\azure-stack-diagnostics.md).
