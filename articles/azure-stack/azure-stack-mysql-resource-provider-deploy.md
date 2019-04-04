---
title: Usando bancos de dados MySQL no Azure Stack | Microsoft Docs
description: Saiba como você pode implantar os bancos de dados MySQL como um serviço no Azure Stack e os passos rápidos para implantar o adaptador de provedor de recursos do servidor MySQL.
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
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 9b2fb5f6ae1fef29801f7decc6adfbd7e68abd0c
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651965"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Implantar o provedor de recursos do MySQL no Azure Stack

Use o provedor de recursos do servidor MySQL para expor bancos de dados MySQL como um serviço do Azure Stack. O provedor de recursos do MySQL é executado como um serviço em uma máquina virtual do Server Core do Windows Server 2016 (VM).

> [!IMPORTANT]
> O provedor de recursos é suportado para criar itens em servidores, esse host SQL ou MySQL. Criado em um servidor de host de itens que não são criados pelo provedor de recursos podem resultar em um estado incompatível.

## <a name="prerequisites"></a>Pré-requisitos

Existem vários pré-requisitos que precisam estar em vigor antes de implantar o provedor de recursos MySQL do Azure Stack. Para atender a esses requisitos, conclua as etapas neste artigo em um computador que possa acessar o ponto de extremidade com privilégios VM.

* Se você ainda não fez isso, [registrar o Azure Stack](./azure-stack-registration.md) com o Azure para que você possa baixar itens do marketplace do Azure.
* Você deve instalar os módulos do Azure e o Azure Stack PowerShell no sistema onde você executará essa instalação. Esse sistema deve ser uma imagem do Windows 10 ou Windows Server 2016 com a versão mais recente do tempo de execução .NET. Ver [instalar o PowerShell para o Azure Stack](./azure-stack-powershell-install.md).
* Adicionar o núcleo do Windows Server necessário VM no Marketplace do Azure Stack baixando a **Windows Server 2016 Datacenter - Server Core** imagem.

* Baixe o provedor de recursos MySQL binário e, em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário.

  >[!NOTE]
  >Para implantar o provedor do MySQL em um sistema que não tem acesso à Internet, copie o [mysql-conector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) arquivo para um caminho local. Forneça o nome de caminho usando o **DependencyFilesLocalPath** parâmetro.

* O provedor de recursos tem um mínimo correspondente do Azure Stack compilar.

  |Versão mínima do Azure Stack|Versão do MySQL RP|
  |-----|-----|
  |Versão 1808 (1.1808.0.97)|[RP MySQL versão 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
  |Versão 1808 (1.1808.0.97)|[RP MySQL versão 1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
  |Versão 1804 (1.0.180513.1)|[RP MySQL versão 1.1.24.0](https://aka.ms/azurestackmysqlrp11240)
  |     |     |

* Certifique-se de datacenter integration pré-requisitos forem atendidos:

    |Pré-requisito|Referência|
    |-----|-----|
    |Encaminhamento condicional do DNS está definido corretamente.|[Integração do datacenter do Azure Stack - DNS](azure-stack-integrate-dns.md)|
    |Portas de entrada para provedores de recursos estão abertas.|[Azure Stack integration data center – publicar pontos de extremidade](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |A entidade do certificado PKI e SAN está definidas corretamente.|[Azure Stack implantação PKI pré-requisitos obrigatórios](azure-stack-pki-certs.md#mandatory-certificates)[pré-requisitos de certificado de PaaS do implantação do Azure Stack](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Certificados

_Para instalações de sistemas integrados apenas_. Você deve fornecer o certificado de PKI de PaaS do SQL descrito na seção de certificados PaaS opcional [requisitos de PKI de implantação do Azure Stack](./azure-stack-pki-certs.md#optional-paas-certificates). Coloque o arquivo. pfx no local especificado o **DependencyFilesLocalPath** parâmetro. Não fornece um certificado para sistemas ASDK.

## <a name="deploy-the-resource-provider"></a>Implantar o provedor de recursos

Depois de instalar todos os pré-requisitos, você pode executar o **DeployMySqlProvider.ps1** script para implantar o provedor de recursos do MySQL. O script DeployMySqlProvider.ps1 é extraído como parte do que os arquivos de instalação de provedor de recursos do MySQL que você baixou para a sua versão do Azure Stack.

 > [!IMPORTANT]
 > Antes de implantar o provedor de recursos, revise as notas de versão para saber mais sobre a nova funcionalidade, correções e problemas conhecidos que podem afetar sua implantação.

Para implantar o provedor de recursos do MySQL, abra uma janela do PowerShell com privilégios elevados novo (não o PowerShell ISE) e altere para o diretório onde você extraiu os arquivos binários de provedor de recursos MySQL. É recomendável usar uma nova janela do PowerShell para evitar possíveis problemas causados por módulos do PowerShell que já estão carregados.

Execute o **DeployMySqlProvider.ps1** script, o que conclui as seguintes tarefas:

* Carrega os certificados e outros artefatos em uma conta de armazenamento no Azure Stack.
* Publica pacotes da Galeria para que você possa implantar bancos de dados MySQL usando a Galeria.
* Publica um pacote da Galeria para a implantação de servidores de hospedagem.
* Implanta uma VM usando a imagem do Windows Server 2016 core baixado e, em seguida, instala o provedor de recursos do MySQL.
* Registra um registro DNS local que é mapeado para seu provedor de recursos VM.
* Registra o provedor de recursos com o local do Azure Resource Manager para a conta do operador.

> [!NOTE]
> Quando a implantação de provedor de recursos do MySQL é iniciado, o **system.local.mysqladapter** grupo de recursos é criado. Ele pode levar até 75 minutos para concluir as implantações necessárias para esse grupo de recursos.

### <a name="deploymysqlproviderps1-parameters"></a>Parâmetros de DeployMySqlProvider.ps1

Você pode especificar esses parâmetros da linha de comando. Se você não fizer isso, ou se nenhuma validação de parâmetro falhar, você será solicitado a fornecer os parâmetros necessários.

| Nome do parâmetro | DESCRIÇÃO | Comentário ou o valor padrão |
| --- | --- | --- |
| **CloudAdminCredential** | A credencial do administrador da nuvem, necessário para acessar o ponto de extremidade com privilégios. | _Obrigatório_ |
| **AzCredential** | As credenciais para a conta de administrador de serviço do Azure Stack. Use as mesmas credenciais que você usou para implantar o Azure Stack. | _Obrigatório_ |
| **VMLocalCredential** | As credenciais para a conta de administrador local do provedor de recursos MySQL VM. | _Obrigatório_ |
| **PrivilegedEndpoint** | O endereço IP ou nome DNS do ponto de extremidade com privilégios. |  _Obrigatório_ |
| **AzureEnvironment** | O ambiente do Azure da conta de administrador de serviço usada para a implantação do Azure Stack. Necessário apenas para implantações do AD do Azure. Nomes de ambiente com suporte são **AzureCloud**, **AzureUSGovernment**, ou se usando uma AD do Azure, na China **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Para sistemas integrados somente, o arquivo. pfx do certificado deve ser colocado neste diretório. Para ambientes desconectados, baixe [mysql-conector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) para esse diretório. Opcionalmente, você pode copiar um pacote de MSU do Windows Update. | _Opcional_ (_obrigatório_ para sistemas integrados ou em ambientes desconectados) |
| **DefaultSSLCertificatePassword** | A senha para o certificado. pfx. | _Obrigatório_ |
| **MaxRetryCount** | O número de vezes que você deseja repetir a cada operação se houver uma falha.| 2 |
| **RetryDuration** | O intervalo de tempo limite entre novas tentativas, em segundos. | 120 |
| **Desinstalar** | Remove o provedor de recursos e todos os recursos associados (consulte as observações a seguir). | Não  |
| **DebugMode** | Impede que a limpeza automática em caso de falha. | Não  |
| **AcceptLicense** | Ignora o prompt para aceitar a licença GPL.  <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Implantar o provedor de recursos do MySQL usando um script personalizado

Para eliminar qualquer configuração manual ao implantar o provedor de recursos, você pode personalizar o script a seguir. Altere as informações de conta padrão e as senhas, conforme necessário para sua implantação do Azure Stack.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"  

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Clear the existing login information from the Azure PowerShell context.
Clear-AzureRMContext -Scope CurrentUser -Force
Clear-AzureRMContext -Scope Process -Force

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Quando o script de instalação do provedor de recursos for concluída, atualize seu navegador para garantir que você pode ver as atualizações mais recentes.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Verificar a implantação por meio do portal do Azure Stack

1. Entre no portal de administração como o administrador de serviço.
2. Selecione **grupos de recursos**
3. Selecione o **system.\< local\>.mysqladapter** grupo de recursos.
4. Na página de resumo para visão geral do grupo de recursos, não deve haver nenhuma implantação com falha.
5. Por fim, selecione **máquinas virtuais** no portal de administração para verificar que o provedor de recursos MySQL VM com êxito criado e está em execução.

## <a name="next-steps"></a>Próximas etapas

[Adicionar servidores de hospedagem](azure-stack-mysql-resource-provider-hosting-servers.md)
