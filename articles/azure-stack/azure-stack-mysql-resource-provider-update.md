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
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 976c05449704b0ecbc48ee5bd4799f300fcac0aa
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651678"
---
# <a name="update-the-mysql-resource-provider"></a>Atualizar o provedor de recursos do MySQL 

*Aplica-se a: Sistemas integrados do Azure Stack.*

Um novo MySQL adaptador de provedor de recursos pode ser liberado quando o Azure Stack compilações são atualizadas. Enquanto o adaptador existente continua a funcionar, é recomendável atualizar para a compilação mais recente assim que possível. 

Começando com o lançamento de versão 1.1.33.0 do provedor de recursos MySQL, as atualizações são cumulativas e não precisam ser instalados na ordem em que elas foram lançadas; desde que você está começando a partir da versão 1.1.24.0 ou posterior. Por exemplo, se você estiver executando a versão 1.1.24.0 do provedor de recursos do MySQL, em seguida, você pode atualizar para a versão 1.1.33.0 ou posteriormente sem precisar primeiro instale a versão 1.1.30.0. Para examinar as versões do provedor de recursos disponíveis e a versão do Azure Stack, elas têm suporte no, consulte a lista de versões no [implantar os pré-requisitos do provedor de recursos](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Para atualizar o provedor de recursos que você usar o **UpdateMySQLProvider.ps1** script. O processo é semelhante ao processo usado para instalar um provedor de recursos, conforme descrito em implantar a seção de provedor de recursos deste artigo. O script está incluído com o download do provedor de recursos. 

 > [!IMPORTANT]
 > Antes de atualizar o provedor de recursos, analise as notas de versão para saber mais sobre a nova funcionalidade, correções e problemas conhecidos que podem afetar sua implantação.

## <a name="update-script-processes"></a>Processos de script de atualização

O **UpdateMySQLProvider.ps1** script cria uma nova VM com o código mais recente do provedor de recursos e migra as configurações da VM antiga para a nova VM. As configurações que migrar incluem o banco de dados e informações do servidor de hospedagem e de registro de DNS necessários. 

>[!NOTE]
>É recomendável que você baixe a imagem mais recente do Windows Server 2016 Core do gerenciamento do Marketplace. Se você precisar instalar uma atualização, você pode colocar uma **único** pacote MSU no caminho do local de dependência. O script falhará se não houver mais de um arquivo MSU neste local.

O script requer o uso dos mesmos argumentos que são descritas para o script DeployMySqlProvider.ps1. Forneça o certificado aqui também.  


## <a name="update-script-parameters"></a>Atualizar parâmetros de script 
Você pode especificar os seguintes parâmetros da linha de comando quando você executa o **UpdateMySQLProvider.ps1** script do PowerShell. Se você não fizer isso, ou se nenhuma validação de parâmetro falhar, você será solicitado a fornecer os parâmetros necessários. 

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
| **AcceptLicense** | Ignora o prompt para aceitar a licença GPL.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Exemplo de script de atualização
A seguir está um exemplo de como usar o *UpdateMySQLProvider.ps1* script que pode ser executado em um console do PowerShell com privilégios elevados. Certifique-se de alterar as senhas e informações sobre a variável conforme necessário:  

> [!NOTE] 
> O processo de atualização é aplicável somente a sistemas integrados. 

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

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

## <a name="next-steps"></a>Próximas etapas
[Manter o provedor de recursos MySQL](azure-stack-mysql-resource-provider-maintain.md)
