---
title: Área de trabalho Virtual do Windows locatário e o host de criação de pool – Azure
description: Como solucionar problemas e resolver o pool de locatário e host problemas durante a instalação de um ambiente de locatário de área de trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 9f52d1b949310792ffedc6e4723fbfb423fb5dfc
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928491"
---
# <a name="tenant-and-host-pool-creation"></a>Criação do pool de host e de locatário

Este artigo aborda problemas durante a configuração inicial do locatário de área de trabalho Virtual do Windows e a infraestrutura de pool de host de sessão relacionado.

## <a name="provide-feedback"></a>Fornecer comentários

Atualmente, não estamos usando casos de suporte durante a versão prévia da Área de Trabalho Virtual do Windows. Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Obter a imagem de várias sessões do Windows 10 Enterprise

Para usar a imagem de várias sessões do Windows 10 Enterprise, vá para o Azure Marketplace, selecione **começar** > **Microsoft Windows 10** > e [Windows 10 Enterprise para Visualização de áreas de trabalho virtuais, versão 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Uma captura de tela da seleção do Windows 10 Enterprise para visualização de áreas de trabalho Virtual, 1809 de versão.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Criação de locatário de área de trabalho Virtual do Windows

Esta seção aborda possíveis problemas ao criar o locatário de área de trabalho Virtual do Windows.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Erro: O usuário não está autorizado para consultar o serviço de gerenciamento

![Janela de captura de tela do PowerShell na qual um usuário não está autorizado para consultar o serviço de gerenciamento.](media/UserNotAuthorizedNewTenant.png)

Exemplo de erro bruto:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Causa:** O usuário que está conectado não foi atribuído a função TenantCreator em seu Azure Active Directory.

**Correção:** Siga as instruções em [atribuir a função de aplicativo TenantCreator a um usuário em seu locatário do Azure Active Directory](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant). Depois de seguir as instruções, você terá um usuário atribuído à função TenantCreator.

![Função de captura de tela de TenantCreator atribuída.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Criação de VMs de host de sessão de área de trabalho Virtual do Windows

Host de sessão de máquinas virtuais pode ser criado de várias maneiras, mas as equipes do Remote Desktop Services/Windows área de trabalho Virtual só há suporte para problemas relacionados ao modelo do Azure Resource Manager de provisionamento da VM. O modelo do Azure Resource Manager está disponível no [do Azure Marketplace](https://azuremarketplace.microsoft.com/) e [GitHub](https://github.com/).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemas usando a área de trabalho de Virtual do Windows – provisionar uma oferta de Marketplace do Azure do pool de host

Área de trabalho Virtual do Windows – provisionar um modelo de pool do host está disponível no Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Erro: Ao usar o link do GitHub, a mensagem "criar uma conta gratuita" é exibida

![Captura de tela para criar uma conta gratuita.](media/be615904ace9832754f0669de28abd94.png)

**Causa 1:** Não existem assinaturas ativas na conta usada para entrar no Azure ou a conta usada não tem permissões para exibir as assinaturas.

**Corrigi 1:** Entrar com uma conta que tenha acesso de Colaborador (no mínimo) para a assinatura em que as VMs de host de sessão vai ser implantado.

**Causa 2:** A assinatura que está sendo usada é parte de um locatário do provedor de serviços de nuvem da Microsoft (CSP).

**Corrigi 2:** Vá para o local do GitHub para **criar e provisionar novo Windows de área de trabalho Virtual host pool** e siga estas instruções:

1. Clique duas vezes em **implantar no Azure** e selecione **Copiar endereço de link**.
2. Abra **bloco de notas** e cole o link.
3. Antes do caractere #, insira o nome de locatário do cliente de final do CSP.
4. Abra o novo link em um navegador e o portal do Azure carregará o modelo.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Modelo do Gerenciador de recursos do Azure e erros do PowerShell Desired State Configuration (DSC)

Siga estas instruções para solucionar problemas de implantações sem êxito de modelos do Azure Resource Manager e PowerShell DSC.

1. Revise os erros na implantação usando [Exibir operações de implantação com o Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
2. Se não houver nenhum erro na implantação, examine os erros no log de atividade usando [exibir logs de atividades para auditar ações em recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
3. Depois que o erro for identificado, usar a mensagem de erro e os recursos no [solucionar erros comuns de implantação do Azure com o Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) para resolver o problema.
4. Exclua todos os recursos criados durante a implantação anterior e a repetição de implantar o modelo novamente.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Erro: Sua implantação falhou...<hostname>/joindomain

![A captura de tela Falha na implantação.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Exemplo de erro bruto:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa 1:** As credenciais fornecidas para adicionar VMs ao domínio estão incorretas.

**Corrigi 1:** Consulte o erro "As credenciais incorretas" para VMs não ingressaram no domínio em [configuração de VM do host de sessão](troubleshoot-vm-configuration.md).

**Causa 2:** Nome de domínio não for resolvido.

**Corrigi 2:** Consulte o erro "não resolve o nome de domínio" para VMs não ingressaram no domínio em [configuração de VM do host de sessão](troubleshoot-vm-configuration.md).

### <a name="error-vmextensionprovisioningerror"></a>Erro: VMExtensionProvisioningError

![Falha na captura de tela de sua implantação falhou com o estado de provisionamento de terminal.](media/7aaf15615309c18a984673be73ac969a.png)

**Causa 1:** Erro transitório com o ambiente de área de trabalho Virtual do Windows.

**Causa 2:** Erro transitório com a conexão.

**Correção:** Confirme se o ambiente de área de trabalho Virtual do Windows está íntegro usando o PowerShell. Concluir o registro VM manualmente no [criar um pool de host com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Erro: O nome de usuário administrador especificado não é permitido

![Captura de tela da sua implantação falhou na qual um administrador especificado não é permitido.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Exemplo de erro bruto:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Causa:** A senha fornecida contém subcadeias de caracteres proibidas (admin, administrador, raiz).

**Correção:** Atualize o nome de usuário ou use usuários diferentes.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Erro: A VM reportou uma falha durante o processamento de extensão

![Captura de tela da operação de recurso que foi concluída com o estado de provisionamento de terminal na sua implantação falhou.](media/49c4a1836a55d91cd65125cf227f411f.png)

Exemplo de erro bruto:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-hostpool4-preview-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Causa:** Extensão de DSC do PowerShell não pôde obter acesso de administrador na VM.

**Correção:** Confirme se o nome de usuário e a senha tem acesso administrativo na máquina virtual e execute novamente o modelo do Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Erro: DeploymentFailed – configuração de DSC do PowerShell 'FirstSessionHost' concluída com erros

![Captura de tela da implantação falhar com a configuração de DSC do PowerShell 'FirstSessionHost' concluída com erros.](media/64870370bcbe1286906f34cf0a8646ab.png)

Exemplo de erro bruto:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list 
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Causa:** Extensão de DSC do PowerShell não pôde obter acesso de administrador na VM.

**Correção:** Confirme o nome de usuário e senha fornecidos têm acesso administrativo na máquina virtual e execute novamente o modelo do Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: DeploymentFailed – InvalidResourceReference

Exemplo de erro bruto:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa:** Parte do nome do grupo de recursos é usado para determinados recursos que estão sendo criados pelo modelo. Devido ao nome de correspondência de recursos existentes, o modelo pode selecionar um recurso existente de um grupo diferente.

**Correção:** Ao executar o modelo do Azure Resource Manager para implantar VMs de host de sessão, verifique os dois primeiros caracteres exclusivo para o nome do grupo de recursos de assinatura.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: DeploymentFailed – InvalidResourceReference

Exemplo de erro bruto:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Causa:** Esse erro ocorre porque a NIC criada com o modelo do Azure Resource Manager tem o mesmo nome que outra NIC já na rede virtual.

**Correção:** Use um prefixo de host diferente.

### <a name="error-deploymentfailed--error-downloading"></a>Erro: DeploymentFailed – erro ao baixar

Exemplo de erro bruto:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-hostpool-3-preview-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-private-preview-
1/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Causa:** Este erro ocorre devido uma rota estática, uma regra de firewall ou um NSG para bloquear o download do arquivo zip vinculado ao modelo do Azure Resource Manager.

**Correção:** Remova o bloqueio de rota estática, a regra de firewall ou NSG. Opcionalmente, abra o arquivo de modelo json do Azure Resource Manager em um editor de texto, levar o link para o arquivo zip e baixar o recurso para um local permitido.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Erro: O usuário não está autorizado para consultar o serviço de gerenciamento

Exemplo de erro bruto:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Causa:** O administrador de locatário de área de trabalho Virtual do Windows especificado não tem uma atribuição de função válido.

**Correção:** O usuário que criou o locatário de área de trabalho Virtual do Windows precisa entrar no Windows PowerShell de área de trabalho Virtual e atribuir o usuário tentado uma atribuição de função. Se você estiver executando os parâmetros de modelo do GitHub do Azure Resource Manager, siga estas instruções usando comandos do PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
Set-RdsContext -Name <Windows Virtual Desktop tenant group name>
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Erro: Usuário requer autenticação de multifator do Azure (MFA)

![Captura de tela da sua implantação falhou devido à falta de autenticação multifator (MFA)](media/MFARequiredError.png)

Exemplo de erro bruto:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Causa:** O administrador de locatário de área de trabalho Virtual do Windows especificado requer o Azure multi-factor MFA (autenticação) para entrar.

**Correção:** Criar uma entidade de serviço e atribuí-lo uma função para seu locatário de área de trabalho Virtual do Windows, seguindo as etapas em [Tutorial: Criar entidades de serviço e as atribuições de função com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell). Depois de verificar que você pode entrar área de trabalho Virtual do Windows com a entidade de serviço, execute novamente a oferta do Marketplace do Azure ou o modelo do Resource Manager do GitHub do Azure, dependendo de qual método você está usando. Siga as instruções abaixo para inserir os parâmetros corretos para seu método.

Se você estiver executando a oferta do Azure Marketplace, forneça valores para os seguintes parâmetros autenticar corretamente a área de trabalho Virtual do Windows:

- Locatário de área de trabalho Virtual do Windows RDS proprietário: Entidade de serviço
- ID do aplicativo: A identificação da entidade de serviço novo que você criou
- Senha/Confirmar senha: O segredo de senha gerado para a entidade de serviço
- ID do locatário do Azure AD: A ID de locatário do Azure AD da entidade de serviço que você criou

Se você estiver executando o modelo do Resource Manager do GitHub do Azure, forneça valores para os seguintes parâmetros autenticar corretamente a área de trabalho Virtual do Windows:

- Nome principal de usuário de administrador do locatário (UPN) ou a ID do aplicativo: A identificação da entidade de serviço novo que você criou
- Senha do administrador de locatário: O segredo de senha gerado para a entidade de serviço
- IsServicePrincipal: **true**
- AadTenantId: A ID de locatário do Azure AD da entidade de serviço que você criou

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral sobre solução de área de trabalho Virtual do Windows e as faixas de escalonamento de bloqueios, consulte [solução de problemas de visão geral, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas ao configurar uma máquina virtual (VM) na área de trabalho Virtual do Windows, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de cliente de área de trabalho Virtual do Windows, consulte [conexões de cliente de área de trabalho remota](troubleshoot-client-connection.md).
- Para solucionar problemas ao usar o PowerShell com a área de trabalho Virtual do Windows, consulte [Windows PowerShell de área de trabalho Virtual](troubleshoot-powershell.md).
- Para saber mais sobre o serviço de visualização, consulte [ambiente de visualização de área de trabalho do Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Para percorrer um tutorial de solução de problemas, confira [Tutorial: Solucionar problemas de implantações de modelo do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).