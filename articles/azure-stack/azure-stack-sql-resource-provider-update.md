---
title: Atualizando o provedor de recursos do SQL do Azure Stack | Microsoft Docs
description: Saiba como você pode atualizar o provedor de recursos do SQL do Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 01815de68552e30ac5158c68d09cbb6157d03034
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362956"
---
# <a name="update-the-sql-resource-provider"></a>Atualizar o provedor de recursos do SQL

*Aplica-se a: sistemas integrados do Azure Stack.*

Um novo provedor de recursos do SQL pode ser liberado quando o Azure Stack é atualizado para uma nova compilação. Embora o adaptador existente continua a funcionar, é recomendável atualizar para a compilação mais recente assim que possível.

>[!IMPORTANT]
>Você deve instalar as atualizações na ordem em que elas forem lançadas. Não é possível ignorar as versões. Consulte a lista de versões no [implantar os pré-requisitos do provedor de recursos](.\azure-stack-sql-resource-provider-deploy.md#prerequisites).

## <a name="overview"></a>Visão geral

Para atualizar o provedor de recursos, use o *UpdateSQLProvider.ps1* script. Esse script está incluído com o download do novo provedor de recursos do SQL. O processo de atualização é semelhante ao processo que costumava [implantar o provedor de recursos](.\azure-stack-sql-resource-provider-deploy.md). O script de atualização usa os mesmos argumentos que o script DeploySqlProvider.ps1, e você precisará fornecer informações de certificado.

### <a name="update-script-processes"></a>Processos de script de atualização

O *UpdateSQLProvider.ps1* script cria uma nova máquina virtual (VM) com o código mais recente do provedor de recursos.

> [!NOTE]
> É recomendável que você baixe a imagem mais recente do Windows Server 2016 Core do gerenciamento do Marketplace. Se você precisar instalar uma atualização, você pode colocar uma **único** pacote MSU no caminho do local de dependência. O script falhará se não houver mais de um arquivo MSU neste local.

Após o *UpdateSQLProvider.ps1* script cria uma nova VM, o script migra as seguintes configurações do provedor antigo de VM:

* informações de banco de dados
* informações do servidor de hospedagem
* registro DNS necessário

### <a name="update-script-powershell-example"></a>Atualizar exemplo de script do PowerShell

Você pode editar e executar o script a seguir de um PowerShell ISE elevado. 

Lembre-se de alterar as informações de conta e senhas, conforme necessário para o seu ambiente.

> [!NOTE]
> Esse processo de atualização se aplica somente a sistemas integrados do Azure Stack.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `

 ```

## <a name="updatesqlproviderps1-parameters"></a>Parâmetros de UpdateSQLProvider.ps1

Você pode especificar os seguintes parâmetros da linha de comando quando você executa o script. Se você não fizer isso, ou se nenhuma validação de parâmetro falhar, você será solicitado a fornecer os parâmetros necessários.

| Nome do parâmetro | DESCRIÇÃO | Comentário ou o valor padrão |
| --- | --- | --- |
| **CloudAdminCredential** | A credencial do administrador da nuvem, necessário para acessar o ponto de extremidade com privilégios. | _Obrigatório_ |
| **AzCredential** | As credenciais para a conta de administrador de serviço do Azure Stack. Use as mesmas credenciais que você usou para implantar o Azure Stack. | _Obrigatório_ |
| **VMLocalCredential** | As credenciais para a conta de administrador local do provedor de recursos SQL VM. | _Obrigatório_ |
| **PrivilegedEndpoint** | O endereço IP ou nome DNS do ponto de extremidade com privilégios. |  _Obrigatório_ |
| **AzureEnvironment** | O ambiente do Azure da conta de administrador de serviço que você usou para implantar o Azure Stack. Necessário apenas para implantações do AD do Azure. Nomes de ambiente com suporte são **AzureCloud**, **AzureUSGovernment**, ou se usando uma AD do Azure, na China **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Você também deve colocar o arquivo. pfx de certificado neste diretório. | _Opcional para um único nó, mas obrigatório para vários nós_ |
| **DefaultSSLCertificatePassword** | A senha para o certificado. pfx. | _Obrigatório_ |
| **MaxRetryCount** | O número de vezes que você deseja repetir a cada operação se houver uma falha.| 2 |
| **RetryDuration** |O intervalo de tempo limite entre novas tentativas, em segundos. | 120 |
| **Desinstalar** | Remove o provedor de recursos e todos os recursos associados. | Não  |
| **DebugMode** | Impede que a limpeza automática em caso de falha. | Não  |

## <a name="next-steps"></a>Próximas etapas

[Manter o provedor de recursos do SQL](azure-stack-sql-resource-provider-maintain.md)
