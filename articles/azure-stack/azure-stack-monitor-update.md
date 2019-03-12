---
title: Monitorar as atualizações no Azure Stack usando o ponto de extremidade privilegiado | Microsoft Docs
description: Saiba como usar o ponto de extremidade com privilégios para monitorar o status da atualização para os sistemas integrados do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 9d66227d131a36da420fe5d4e461e738c5ee0911
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777814"
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Monitorar as atualizações no Azure Stack usando o ponto de extremidade com privilégios

*Aplica-se a: Sistemas integrados do Azure Stack*

Você pode usar o [ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md) para monitorar o progresso de uma pilha do Azure atualização executada e retomar uma atualização com falha na execução da última etapa bem-sucedida portal do Azure Stack ficar indisponível.  Usando o portal do Azure Stack é o método recomendado para gerenciar atualizações no Azure Stack.

Os seguintes novos cmdlets de PowerShell para gerenciamento de atualizações são incluídos na atualização 1710 para sistemas integrados do Azure Stack.

| Cmdlet  | DESCRIÇÃO  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Retorna o status da atualização com falha, concluído ou em execução no momento. Fornece o status de alto nível de operação de atualização e um documento XML que descreve a etapa atual e o estado correspondente. |
| `Resume-AzureStackUpdate` | Retoma uma atualização com falha no ponto em que ele falhou. Em determinados cenários, talvez você precise concluir etapas de mitigação antes de retomar a atualização.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Verifique se que os cmdlets estão disponíveis
Como os cmdlets são novos no pacote de atualização 1710 para o Azure Stack, o processo de atualização 1710 precisa ir até um determinado ponto antes que o recurso de monitoramento está disponível. Normalmente, os cmdlets estão disponíveis se o status no portal do administrador indica que a atualização 1710 está no **reinicie o armazenamento de Hosts** etapa. Especificamente, a atualização do cmdlet ocorre durante a **etapa: Executando etapa 2.6 - lista de permissões de atualização PrivilegedEndpoint**.

Você também pode determinar se os cmdlets estão disponíveis por meio de programação, consultando a lista de comandos do ponto de extremidade com privilégios. Para fazer isso, execute os seguintes comandos do host de ciclo de vida do hardware ou de uma estação de trabalho de acesso privilegiado. Além disso, certifique-se de que o ponto de extremidade com privilégios é um host confiável. Para obter mais informações, consulte a etapa 1 [acessar o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). 

1. Criar uma sessão do PowerShell em qualquer uma das máquinas virtuais ERCS em seu ambiente do Azure Stack (*prefixar*-ERCS01, *prefixo*-ERCS02, ou *prefixo*-ERCS03). Substitua *prefixo* com a cadeia de caracteres de prefixo de máquina virtual que é específica para seu ambiente.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Quando solicitado a fornecer credenciais, use o &lt; *domínio do Azure Stack*&gt;\cloudadmin conta ou uma conta que seja membro do grupo CloudAdmins. Para a conta de CloudAdmin, digite a mesma senha que foi fornecida durante a instalação para a conta de administrador de domínio AzureStackAdmin.

2. Obter a lista completa de comandos que estão disponíveis no ponto de extremidade com privilégios. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Determine se o ponto de extremidade com privilégios foi atualizado.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Liste os comandos específicos para o módulo Microsoft.AzureStack.UpdateManagement.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   Por exemplo: 
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Use os cmdlets de gerenciamento de atualização

> [!NOTE]
> Execute os seguintes comandos do host de ciclo de vida do hardware ou de uma estação de trabalho de acesso privilegiado. Além disso, certifique-se de que o ponto de extremidade com privilégios é um host confiável. Para obter mais informações, consulte a etapa 1 [acessar o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Conecte-se ao ponto de extremidade com privilégios e atribua a variável de sessão

Execute os seguintes comandos para criar uma sessão do PowerShell em qualquer uma das máquinas virtuais ERCS em seu ambiente do Azure Stack (*prefixar*-ERCS01, *prefixo*-ERCS02, ou *prefixo*-ERCS03) e para atribuir uma variável de sessão.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Quando solicitado a fornecer credenciais, use o &lt; *domínio do Azure Stack*&gt;\cloudadmin conta ou uma conta que seja membro do grupo CloudAdmins. Para a conta de CloudAdmin, digite a mesma senha que foi fornecida durante a instalação para a conta de administrador de domínio AzureStackAdmin.

### <a name="get-high-level-status-of-the-current-update-run"></a>Obter status de alto nível de execução da atualização atual 

Para obter um status de alto nível de execução da atualização atual, execute os seguintes comandos: 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

Os valores possíveis incluem:

- Executando
- Concluído
- Com falha 
- Cancelado

Você pode executar esses comandos repetidamente para ver o status mais atualizado. Você não precisará restabelecer uma conexão para verificar novamente.

### <a name="get-the-full-update-run-status-with-details"></a>Obter a status de execução com detalhes de atualização completa 

Você pode obter a atualização executar resumida como uma cadeia de caracteres XML. Você pode gravar a cadeia de caracteres em um arquivo para exame, ou convertê-lo em um documento XML e usar o PowerShell para analisá-lo. O comando a seguir analisa o XML para obter uma lista hierárquica das etapas em execução no momento.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

No exemplo a seguir, a etapa de nível superior (atualização de nuvem) tem um plano de filho para atualizar e reiniciar os hosts de armazenamento. Ele mostra que o plano de reiniciar Hosts de armazenamento está atualizando o serviço de armazenamento de BLOBs em um dos hosts.

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

### <a name="resume-a-failed-update-operation"></a>Retomar uma operação de atualização com falha

Se a atualização falhar, você poderá retomar a execução de onde parou de atualização.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Solucionar problemas

O ponto de extremidade com privilégios está disponível em todas as máquinas virtuais ERCS no ambiente do Azure Stack. Porque a conexão é feita para um ponto de extremidade altamente disponível, você pode enfrentar interrupções ocasionais, aviso ou mensagens de erro. Essas mensagens podem indicar que a sessão foi desconectada ou que houve um erro de comunicação com o serviço ECE. O comportamento é esperado. Você pode repetir a operação em alguns minutos ou criar uma nova sessão do ponto de extremidade com privilégios em uma das outras máquinas de virtuais ERCS. 

## <a name="next-steps"></a>Próximas etapas

- [Gerenciamento de atualizações no Azure Stack](azure-stack-updates.md) 


