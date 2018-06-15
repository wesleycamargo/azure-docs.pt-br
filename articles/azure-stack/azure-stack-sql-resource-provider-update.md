---
title: Atualizando o provedor de recursos do SQL do Azure pilha | Microsoft Docs
description: Saiba como você pode atualizar o provedor de recursos do SQL de pilha do Azure.
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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 3a7656e54181c8e8e7b6b1bd39f80ce8ed01c807
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294853"
---
# <a name="update-the-sql-resource-provider"></a>Atualizar o provedor de recursos do SQL
Um novo provedor de recursos do SQL pode ser liberado quando as compilações de pilha do Azure são atualizadas. Enquanto o adaptador existente continuará a funcionar, é recomendável atualizar para a versão mais recente assim que possível. As atualizações devem ser instaladas na ordem: não é possível ignorar versões (consulte a lista de versões em [implantar os pré-requisitos do provedor de recursos](.\azure-stack-sql-resource-provider-deploy.md#prerequisites)).

Para atualizar do provedor de recursos que você usar o *UpdateSQLProvider.ps1* script. O processo é semelhante ao processo usado para instalar um provedor de recursos, conforme descrito no [implantar o provedor de recursos](.\azure-stack-sql-resource-provider-deploy.md) artigo. O script está incluído no download do provedor de recursos.

O *UpdateSQLProvider.ps1* script cria uma nova VM com o código mais recente do provedor de recursos e migra as configurações da VM antiga para a nova VM. As configurações de migração incluem o banco de dados e informações do servidor de hospedagem e registro de DNS necessário.

O script requer o uso dos mesmos argumentos que são descritas para o script DeploySqlProvider.ps1. Forneça o certificado aqui também. 

É recomendável que você baixe a imagem do Windows Server 2016 Core mais recente do gerenciamento do Marketplace. Se você precisar instalar uma atualização, você pode colocar um único. Pacote MSU no caminho do local de dependência. Se mais de um. Arquivo MSU for encontrado, o script falhará.

A seguir está um exemplo de como o *UpdateSQLProvider.ps1* script que pode ser executado do prompt do PowerShell. Certifique-se de alterar as informações de conta e senhas, conforme necessário: 

> [!NOTE]
> O processo de atualização só se aplica a sistemas integrados.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
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
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

## <a name="updatesqlproviderps1-parameters"></a>Parâmetros de UpdateSQLProvider.ps1
Você pode especificar esses parâmetros na linha de comando. Se você não fizer isso, ou se qualquer parâmetro de validação falhar, você precisará fornecer os parâmetros necessários.

| Nome do parâmetro | DESCRIÇÃO | Comentário ou o valor padrão |
| --- | --- | --- |
| **CloudAdminCredential** | A credencial do administrador da nuvem, necessário para acessar o ponto de extremidade com privilégios. | _Obrigatório_ |
| **AzCredential** | As credenciais para a conta de administrador de serviço de pilha do Azure. Use as mesmas credenciais que você usou para implantar a pilha do Azure. | _Obrigatório_ |
| **VMLocalCredential** | As credenciais de conta de administrador local do provedor de recursos SQL VM. | _Obrigatório_ |
| **PrivilegedEndpoint** | O endereço IP ou nome DNS do ponto de extremidade com privilégios. |  _Obrigatório_ |
| **DependencyFilesLocalPath** | O arquivo. pfx do certificado deve ser colocado nesse diretório também. | _Opcional_ (_obrigatório_ para vários nós) |
| **DefaultSSLCertificatePassword** | A senha para o certificado. pfx. | _obrigatório_ |
| **MaxRetryCount** | O número de vezes que você deseja repetir a cada operação se houver uma falha.| 2 |
| **RetryDuration** |O intervalo de tempo limite entre as repetições, em segundos. | 120 |
| **Desinstalar** | Remove o provedor de recursos e todos os respectivos recursos (consulte as observações a seguir). | Não  |
| **DebugMode** | Impede que a limpeza automática em caso de falha. | Não  |


## <a name="next-steps"></a>Próximas etapas

[Manter o provedor de recursos do SQL](azure-stack-sql-resource-provider-maintain.md)
