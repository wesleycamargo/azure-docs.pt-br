---
title: Iniciar e parar o Azure Stack desenvolvimento ASDK (Kit) | Microsoft Docs
description: Saiba como iniciar e desligar para baixo o Azure Stack desenvolvimento ASDK (Kit).
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
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 0073427028d20ab969b8d0135b1c831d7fdf8c73
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191957"
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>Iniciar e parar o Azure Stack desenvolvimento ASDK (Kit de)
Não é recomendável simplesmente reiniciar o computador de host ASDK. Em vez disso, você deve seguir os procedimentos neste artigo para desligar e reiniciar serviços ASDK corretamente. 

## <a name="stop-azure-stack"></a>Parar o Azure Stack 
Para desligar corretamente os serviços do Azure Stack e o computador de host ASDK, use os seguintes comandos do PowerShell:

1. Faça logon como AzureStack\CloudAdmin no computador host ASDK.
2. Abra o PowerShell como administrador (não o PowerShell ISE).
3. Execute os seguintes comandos para estabelecer uma sessão do ponto de extremidade com privilégios (PEP): 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Em seguida, na sessão PEP, use o **Stop-AzureStack** cmdlet para parar os serviços do Azure Stack e desligar o computador de host ASDK:

   ```powershell
   Stop-AzureStack
   ```
5. Examine a saída do PowerShell para garantir que todos os serviços do Azure Stack estão desligados com êxito antes do computador host ASDK é desligado. O processo de desligamento leva vários minutos.

## <a name="start-azure-stack"></a>Inicie o Azure Stack 
Serviços ASDK devem ser iniciada automaticamente quando o computador host é iniciado. No entanto, o tempo de inicialização de serviços de infraestrutura ASDK varia de acordo com o desempenho da configuração de hardware do computador ASDK host. Pode levar várias horas para todos os serviços reiniciar com êxito em alguns casos.

Independentemente de como o ASDK tiver sido desligado, você deve usar as etapas a seguir para verificar se todos os serviços do Azure Stack são iniciados e operacionais totalmente depois que o computador host está ligado: 

1. Ligue o computador host ASDK. 
2. Faça logon como AzureStack\CloudAdmin no computador host ASDK.
3. Abra o PowerShell como administrador (não o PowerShell ISE).
4. Execute os seguintes comandos para estabelecer uma sessão do ponto de extremidade com privilégios (PEP):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. Em seguida, na sessão PEP, execute os comandos a seguir para verificar o status de inicialização dos serviços do Azure Stack:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Examine a saída para garantir que os serviços do Azure Stack foram reinicializados com êxito.

Para saber mais sobre os procedimentos recomendados para desligar e reiniciar os serviços do Azure Stack corretamente, consulte [iniciar e parar o Azure Stack](../azure-stack-start-and-stop.md). 

## <a name="troubleshoot-startup-and-shutdown"></a>Solucionar problemas de inicialização e desligamento 
Se os serviços do Azure Stack não iniciam com êxito dentro de duas horas após você ligar seu computador de host ASDK, execute estas etapas:

1. Faça logon como AzureStack\CloudAdmin no computador host ASDK.
2. Abra o PowerShell como administrador (não o PowerShell ISE).
3. Execute os seguintes comandos para estabelecer uma sessão do ponto de extremidade com privilégios (PEP):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Em seguida, na sessão PEP, execute os comandos a seguir para verificar o status de inicialização dos serviços do Azure Stack:

   ```powershell
   Test-AzureStack
   ```
5. Examine a saída e resolva quaisquer erros. Para obter mais informações, consulte [executar um teste de validação do Azure Stack](../azure-stack-diagnostic-test.md).
6. Reinicie os serviços do Azure Stack de dentro da sessão PEP executando o **AzureStack início** cmdlet:

   ```powershell
   Start-AzureStack
   ```

Se executando **Start-AzureStack** resulta em uma falha, visite o [Fórum de suporte do Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) obter ASDK Solucionando problemas de suporte. 

## <a name="next-steps"></a>Próximas etapas 
Saiba mais sobre a ferramenta de diagnóstico do Azure Stack e emitir o registro em log, consulte [ferramentas de diagnóstico do Azure Stack](../azure-stack-diagnostics.md).
