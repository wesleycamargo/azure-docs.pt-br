---
title: Atualizando o provedor de recursos MySQL do Azure Stack | Microsoft Docs
description: Saiba como você pode atualizar o provedor de recursos MySQL do Azure Stack.
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
ms.date: 09/13/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 5ea865a6a0f3f671d92d9ef3bf60cd8cfe90bddb
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578879"
---
# <a name="update-the-mysql-resource-provider"></a>Atualizar o provedor de recursos do MySQL 

*Aplica-se a: sistemas integrados do Azure Stack.*

Um novo adaptador de provedor de recursos do SQL pode ser liberado quando o Azure Stack compilações são atualizadas. Enquanto o adaptador existente continua a funcionar, é recomendável atualizar para a compilação mais recente assim que possível. 

>[!IMPORTANT]
>Você deve instalar as atualizações na ordem em que elas forem lançadas. Não é possível ignorar as versões. Consulte a lista de versões no [implantar os pré-requisitos do provedor de recursos](.\azure-stack-mysql-resource-provider-deploy.md#prerequisites).

## <a name="update-the-mysql-resource-provider-adapter-integrated-systems-only"></a>Atualizar o adaptador de provedor de recursos do MySQL (apenas sistemas integrados)

Um novo adaptador de provedor de recursos do SQL pode ser liberado quando o Azure Stack compilações são atualizadas. Enquanto o adaptador existente continua a funcionar, é recomendável atualizar para a compilação mais recente assim que possível.  
 
Para atualizar o provedor de recursos que você usar o **UpdateMySQLProvider.ps1** script. O processo é semelhante ao processo usado para instalar um provedor de recursos, conforme descrito na [implantar o provedor de recursos](#deploy-the-resource-provider) seção deste artigo. O script está incluído com o download do provedor de recursos. 

O **UpdateMySQLProvider.ps1** script cria uma nova VM com o código mais recente do provedor de recursos e migra as configurações da VM antiga para a nova VM. As configurações que migrar incluem o banco de dados e informações do servidor de hospedagem e de registro de DNS necessários. 

>[!NOTE]
>É recomendável que você baixe a imagem mais recente do Windows Server 2016 Core do gerenciamento do Marketplace. Se você precisar instalar uma atualização, você pode colocar uma **único** pacote MSU no caminho do local de dependência. O script falhará se não houver mais de um arquivo MSU neste local.

>[!NOTE]  
> 

O script requer o uso dos mesmos argumentos que são descritas para o script DeployMySqlProvider.ps1. Forneça o certificado aqui também.  

A seguir está um exemplo de como o *UpdateMySQLProvider.ps1* script que pode ser executado do prompt do PowerShell. Certifique-se de alterar as informações de conta e senhas, conforme necessário:  

> [!NOTE] 
> O processo de atualização é aplicável somente a sistemas integrados. 

```powershell 
# Install the AzureRM.Bootstrapper module and set the profile. 
Install-Module -Name AzureRm.BootStrapper -Force 
Use-AzureRmProfile -Profile 2017-03-09-profile 

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM. 
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access. 
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate. 
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
 
# Change directory to the folder where you extracted the installation files. 
# Then adjust the endpoints. 
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
``` 
 
### <a name="updatemysqlproviderps1-parameters"></a>Parâmetros de UpdateMySQLProvider.ps1 
Você pode especificar esses parâmetros na linha de comando. Se você não fizer isso, ou se nenhuma validação de parâmetro falhar, você precisará fornecer os parâmetros necessários. 

| Nome do Parâmetro | DESCRIÇÃO | Comentário ou o valor padrão | 
| --- | --- | --- | 
| **CloudAdminCredential** | A credencial do administrador da nuvem, necessário para acessar o ponto de extremidade com privilégios. | _Obrigatório_ | 
| **AzCredential** | As credenciais para a conta de administrador de serviço do Azure Stack. Use as mesmas credenciais que você usou para implantar o Azure Stack. | _Obrigatório_ | 
| **VMLocalCredential** |As credenciais para a conta de administrador local do provedor de recursos SQL VM. | _Obrigatório_ | 
| **PrivilegedEndpoint** | O endereço IP ou nome DNS do ponto de extremidade com privilégios. |  _Obrigatório_ | 
| **AzureEnvironment** | O ambiente do Azure da conta de administrador de serviço que você usou para implantar o Azure Stack. Necessário apenas para implantações do AD do Azure. Nomes de ambiente com suporte são **AzureCloud**, **AzureUSGovernment**, ou se usando uma AD do Azure, na China **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | O arquivo. pfx do certificado deve ser colocado neste diretório também. | _Opcional_ (_obrigatório_ para vários nós) | 
| **DefaultSSLCertificatePassword** | A senha para o certificado. pfx. | _Obrigatório_ | 
| **MaxRetryCount** | O número de vezes que você deseja repetir a cada operação se houver uma falha.| 2 | 
| **RetryDuration** | O intervalo de tempo limite entre novas tentativas, em segundos. | 120 | 
| **Desinstalar** | Remova o provedor de recursos e todos os recursos associados (consulte as observações a seguir). | Não  | 
| **DebugMode** | Impede que a limpeza automática em caso de falha. | Não  | 
| **AcceptLicense** | Ignora o prompt para aceitar a licença GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 
 

## <a name="next-steps"></a>Próximas etapas
[Manter o provedor de recursos MySQL](azure-stack-mysql-resource-provider-maintain.md)
