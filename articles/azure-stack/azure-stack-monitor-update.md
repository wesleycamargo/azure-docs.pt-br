---
title: "Monitorar atualizações na pilha do Azure usando o ponto de extremidade privilegiado | Microsoft Docs"
description: "Saiba como usar o ponto de extremidade com privilégios para monitorar o status de atualização para sistemas de pilha do Azure integradas."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: twooley
ms.openlocfilehash: af7387268d60dc639a39da23e040097fd0695a22
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2017
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Monitorar atualizações na pilha do Azure usando o ponto de extremidade com privilégios

*Aplica-se a: sistemas integrados de pilha do Azure*

Você pode usar o ponto de extremidade com privilégios para monitorar o progresso de uma execução de atualização da pilha do Azure e retomar uma atualização com falha, execute a partir da última etapa bem-sucedida. 

Os seguintes cmdlets do PowerShell novos para gerenciamento de atualização são incluídos na atualização 1710 para sistemas de pilha do Azure integradas.

| Cmdlet  | Descrição  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Retorna o status da atualização atualmente em execução, concluído ou com falha. Fornece o status de alto nível da operação de atualização e um documento XML que descreve a etapa atual e o estado correspondente. |
| `Get-AzureStackUpdateVerboseLog` | Retorna os logs detalhados que são gerados pela atualização. |
| `Resume-AzureStackUpdate` | Retoma uma atualização com falha no ponto em que ele falha. Em determinados cenários, você precisará concluir as etapas de mitigação antes de continuar a atualização.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Verifique se que os cmdlets estão disponíveis
Como os cmdlets são novos no pacote de atualização de 1710 pilha do Azure, o processo de atualização 1710 precisa ir até um determinado ponto antes que o recurso de monitoramento está disponível. Normalmente, os cmdlets estão disponíveis se o status no portal do administrador indica que a atualização 1710 está no **reiniciar armazenamento Hosts** etapa. Especificamente, o cmdlet update ocorre durante a **passo: executando etapa 2.6 - lista de permissões de atualização PrivilegedEndpoint**.

Você também pode determinar se os cmdlets estão disponíveis por meio de programação, consultando a lista de comandos do ponto de extremidade com privilégios. Para fazer isso, execute os seguintes comandos do host de ciclo de vida de hardware ou de uma estação de trabalho de acesso privilegiado. Além disso, certifique-se de que o ponto de extremidade privilegiado é um host confiável. Para obter mais informações, consulte a etapa 1 de [acessar o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). 

1. Criar uma sessão do PowerShell em qualquer uma das máquinas virtuais ERCS em seu ambiente de pilha do Azure (*prefixo*-ERCS01, *prefixo*-ERCS02, ou *prefixo*-ERCS03). Substituir *prefixo* com a cadeia de caracteres de prefixo de máquina virtual que é específica para seu ambiente.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Quando solicitado a fornecer credenciais, use o &lt; *domínio Azure pilha*&gt;\cloudadmin conta ou uma conta que seja membro do grupo CloudAdmins. Para a conta de CloudAdmin, digite a mesma senha que foi fornecida durante a instalação para a conta de administrador de domínio AzureStackAdmin.

2. Obter a lista completa de comandos que estão disponíveis no ponto de extremidade com privilégios. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Determine se o ponto de extremidade privilegiado foi atualizado.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Lista os comandos específicos para o módulo Microsoft.AzureStack.UpdateManagement.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   Por exemplo:
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Get-AzureStackUpdateVerboseLog                     0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Usar os cmdlets de gerenciamento de atualização

> [!NOTE]
> Execute os seguintes comandos do host de ciclo de vida de hardware ou de uma estação de trabalho de acesso privilegiado. Além disso, certifique-se de que o ponto de extremidade privilegiado é um host confiável. Para obter mais informações, consulte a etapa 1 de [acessar o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Conecte-se ao ponto de extremidade com privilégios e atribuir a variável de sessão

Execute os seguintes comandos para criar uma sessão do PowerShell em qualquer uma das máquinas virtuais ERCS em seu ambiente de pilha do Azure (*prefixo*-ERCS01, *prefixo*-ERCS02, ou *prefixo*-ERCS03) e para atribuir uma variável de sessão.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Quando solicitado a fornecer credenciais, use o &lt; *domínio Azure pilha*&gt;\cloudadmin conta ou uma conta que seja membro do grupo CloudAdmins. Para a conta de CloudAdmin, digite a mesma senha que foi fornecida durante a instalação para a conta de administrador de domínio AzureStackAdmin.

### <a name="get-high-level-status-of-the-current-update-run"></a>Obter status de alto nível da execução de atualização atual 

Para obter um status de alto nível de execução de atualização atual, execute os seguintes comandos: 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

Os valores possíveis incluem:

- Executando
- Concluído
- Com falha 
- Cancelado

Você pode executar esses comandos repetidamente para ver o status mais recente. Você não precisa restabelecer uma conexão para verificar novamente.

### <a name="get-the-full-update-run-status-with-details"></a>Obtenha a atualização completa status de execução com detalhes 

Você pode obter a atualização completa executar resumida como uma cadeia de caracteres XML. Você pode gravar a cadeia de caracteres em um arquivo para análise, ou convertê-lo em um documento XML e usar o PowerShell para analisá-lo. O comando a seguir analisa o XML para obter uma lista hierárquica das etapas em execução no momento.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

No exemplo a seguir, a etapa de nível superior (atualização de nuvem) tem um plano de filho para atualizar e reiniciar os hosts de armazenamento. Ele mostra que o plano de reiniciar Hosts de armazenamento está atualizando o serviço de armazenamento de Blob em um dos hosts.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="get-the-verbose-progress-log"></a>Obter o log detalhado de andamento

Você pode gravar o log para um arquivo para análise. Isso pode ajudá-lo a diagnosticar uma falha de atualização.

```powershell
$log = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateVerboseLog }

$log > ".\UpdateVerboseLog.txt" 
```

### <a name="actively-view-the-verbose-logging"></a>Exibir ativamente o log detalhado

Ativamente para exibir o log detalhado durante uma atualização, execute e ir para as entradas mais recentes, execute os seguintes comandos para inserir a sessão no modo interativo e para exibir o log:

```powershell
Enter-PSSession -Session $pepSession 

Get-AzureStackUpdateVerboseLog -Wait 
```
O log de atualizações a cada 60 segundos e o novo conteúdo (se disponível) é gravado no console. 

Durante os processos em segundo plano de execução longa, a saída do console não pode ser escrita para o console por algum tempo. Para cancelar a saída interativa, pressione Ctrl + C. 

### <a name="resume-a-failed-update-operation"></a>Retomar uma operação de atualização com falha

Se a atualização falhar, você pode retomar a execução de onde parou de atualização.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Solucionar problemas

O ponto de extremidade com privilégios está disponível em todas as máquinas virtuais ERCS no ambiente de pilha do Azure. Como a conexão não é feita com um ponto de extremidade altamente disponível, você pode enfrentar interrupções ocasionais, avisos ou mensagens de erro. Essas mensagens podem indicar que a sessão foi desconectada ou que houve um erro de comunicação com o serviço ECE. Esse comportamento é esperado. Você pode repetir a operação em alguns minutos ou crie uma nova sessão do ponto de extremidade com privilégios em uma das outras máquinas de virtuais ERCS. 

## <a name="next-steps"></a>Próximas etapas

- [Gerenciamento de atualizações na pilha do Azure](azure-stack-updates.md) 


