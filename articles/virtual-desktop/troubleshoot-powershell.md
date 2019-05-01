---
title: PowerShell de área de trabalho Virtual do Windows – Azure
description: Como solucionar problemas com o PowerShell quando você configurar um ambiente de locatário de área de trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: ad32f7ff883812830dbcf2ed900c4034bd90abfc
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927501"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows PowerShell de área de trabalho Virtual

Use este artigo para resolver problemas e erros ao usar o PowerShell com a área de trabalho Virtual do Windows. Para obter mais informações sobre o PowerShell remoto de serviços de área de trabalho, consulte [Windows Powershell de área de trabalho Virtual](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Fornecer comentários

Atualmente, não estamos usando casos de suporte durante a versão prévia da Área de Trabalho Virtual do Windows. Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Comandos do PowerShell usados durante a instalação da área de trabalho Virtual do Windows

Esta seção lista os comandos do PowerShell que normalmente são usados durante a configuração da área de trabalho Virtual do Windows e fornece maneiras de resolver problemas que podem ocorrer ao usá-los.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Erro: Comando RdsAppGroupUser adicionar – o UserPrincipalName especificado já está atribuído a um grupo de aplicativos do RemoteApp no Pool de Host especificado

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Causa:** O nome de usuário usado já foi atribuído a um grupo de aplicativos de um tipo diferente. Usuários não podem ser atribuídos a um grupo de aplicativo remoto e área de trabalho remoto sob o mesmo pool de host de sessão.

**Correção:** Se o usuário precisa aplicativos remotos e área de trabalho remota, crie pools de host diferente ou conceder acesso de usuário para a área de trabalho remota, o que permitirá o uso de qualquer aplicativo na VM do host de sessão.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Erro: Comando RdsAppGroupUser adicionar – o UserPrincipalName especificado não existe no Azure Active Directory associado ao locatário de área de trabalho remota

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**Causa:** O usuário especificado por - UserPrincipalName não foi encontrado no Active Directory do Azure vinculada ao locatário de área de trabalho Virtual do Windows.

**Correção:** Confirme se os itens na lista a seguir.

- O usuário está sincronizado no Azure Active Directory.
- O usuário não está vinculado aos negócios ao consumidor (B2C) ou de comércio do business-to-business (B2B).
- O locatário de área de trabalho Virtual do Windows está vinculado ao Azure Active Directory correto.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Erro: Get-RdsDiagnosticActivities – O usuário não está autorizado para consultar o serviço de gerenciamento

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Causa:** parâmetro - nome do locatário

**Correção:** Execute Get-RdsDiagnosticActivities com - TenantName <TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Erro: Get-RdsDiagnosticActivities – o usuário não está autorizado para consultar o serviço de gerenciamento

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Causa:** Usando - opção de implantação.

**Correção:** -opção de implantação pode ser usada somente por administradores de implantação. Geralmente, esses administradores são membros da equipe remota da área de trabalho Windows/serviços de área de trabalho Virtual. Substitua a - opção de implantação com o nome do locatário - <TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Erro: Novo-RdsRoleAssignment – o usuário não está autorizado para consultar o serviço de gerenciamento

**Causa 1:** A conta que está sendo usada não tem permissões de proprietário de serviços de área de trabalho remota no locatário.

**Corrigi 1:** Precisa de um usuário com permissões de proprietário de serviços de área de trabalho remota executar a atribuição de função.

**Causa 2:** A conta que está sendo usada possui permissões de proprietário de serviços de área de trabalho remota, mas não faz parte do Active Directory do Azure do locatário ou não tem permissões para consultar o Azure Active Directory em que o usuário está localizado.

**Corrigi 2:** Um usuário com permissões do Active Directory precisa executar a atribuição de função.

>[!Note]
>Novo RdsRoleAssignment não é possível conceder permissões a um usuário que não existe no Azure Active Directory (AD).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral sobre solução de área de trabalho Virtual do Windows e as faixas de escalonamento de bloqueios, consulte [solução de problemas de visão geral, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas durante a criação de um pool de locatário e o host em um ambiente de área de trabalho Virtual do Windows, consulte [criação de pool de locatário e host](troubleshoot-set-up-issues.md).
- Para solucionar problemas ao configurar uma máquina virtual (VM) na área de trabalho Virtual do Windows, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de cliente de área de trabalho Virtual do Windows, consulte [conexões de cliente de área de trabalho remota](troubleshoot-client-connection.md).
- Para saber mais sobre o serviço de visualização, consulte [ambiente de visualização de área de trabalho do Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Para percorrer um tutorial de solução de problemas, confira [Tutorial: Solucionar problemas de implantações de modelo do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).