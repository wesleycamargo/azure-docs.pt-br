---
title: Usando bancos de dados MySQL no Azure pilha | Microsoft Docs
description: Saiba como você pode implantar bancos de dados MySQL como um serviço na pilha do Azure e as etapas rápidas para implantar o adaptador de provedor de recursos do servidor MySQL.
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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e4c3eb1d7dfd4894576d5fbed52cf4de5fed9e44
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938104"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Implantar o provedor de recursos do MySQL na pilha do Azure

Use o provedor de recursos do MySQL Server para expor bancos de dados MySQL como um serviço de pilha do Azure. O provedor de recursos do MySQL é executado como um serviço em uma máquina virtual do Server Core do Windows Server 2016 (VM).

## <a name="prerequisites"></a>Pré-requisitos

Existem vários pré-requisitos que devem estar em vigor antes de implantar o provedor de recursos MySQL de pilha do Azure. Para atender a esses requisitos, conclua as etapas neste artigo em um computador que possa acessar o ponto de extremidade privilegiado VM.

* Se você ainda não fez isso, [registrar Azure pilha](.\azure-stack-registration.md) com o Azure para o download de itens do marketplace do Azure.
* Adicionar o núcleo do Windows Server necessário VM Marketplace do Azure pilha baixando o **Windows Server 2016 Datacenter - Server Core** imagem. Você também pode usar um script para criar um [imagem do Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). Verifique se que você selecionar a opção de núcleo, quando você executa o script.

  >[!NOTE]
  >Se você precisar instalar uma atualização, você pode colocar um único. Pacote MSU no caminho do local de dependência. Se mais de um. Arquivo MSU for encontrado, a instalação do provedor de recursos MySQL falhará.

* Baixe o provedor de recursos MySQL binário e, em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário.

  >[!NOTE]
  >Para implantar o provedor do MySQL em um sistema que não tem acesso à Internet, copie o [mysql-conector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) arquivo para um compartilhamento local. Forneça o nome de compartilhamento quando você for solicitado para ele. Você deve instalar os módulos do Azure e o Azure PowerShell de pilha.

* O provedor de recursos possui uma pilha de Azure de correspondente mínima de compilação. Certifique-se de que baixar o binário correto para a versão da pilha do Azure que você está executando.

    | Versão da pilha do Azure | Versão do MySQL RP|
    | --- | --- |
    | Versão 1804 (1.0.180513.1)|[RP MySQL versão 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) |
    | Versão 1802 (1.0.180302.1) | [RP MySQL versão 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | Versão 1712 (1.0.180102.3 ou 1.0.180106.1 (sistemas integrados)) | [RP MySQL versão 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |

### <a name="certificates"></a>Certificados

Para ASDK, um certificado autoassinado é criado como parte desse processo de instalação. Para um sistema de pilha do Azure integrado, você deve fornecer um certificado apropriado. Se você precisa fornecer seu próprio certificado, coloque um arquivo. pfx no **DependencyFilesLocalPath** que atenda aos seguintes critérios:

* Um certificado curinga para \*.dbadapter.\< região\>.\< fqdn externo\> ou um certificado de site único com um nome comum de mysqladapter.dbadapter.\< região\>.\< fqdn externo\>.
* O certificado deve ser confiável. A cadeia de confiança deve existir sem a necessidade de certificados intermediários.
* Somente um arquivo de certificado único existe no DependencyFilesLocalPath.
* O nome do arquivo não pode ter todos os caracteres especiais ou espaços.

## <a name="deploy-the-resource-provider"></a>Implantar o provedor de recursos

Depois que você tem todos os pré-requisitos instalados, execute o **DeployMySqlProvider.ps1** script para implantar o provedor de recursos do MYSQL. O script DeployMySqlProvider.ps1 é extraído como parte do binário de provedor de recursos do MySQL que você baixou para a sua versão da pilha do Azure.

> [!IMPORTANT]
> O sistema que você estiver executando o script em deve ser um sistema Windows 10 ou Windows Server 2016 com a versão mais recente do tempo de execução .NET instalada.

Para implantar o provedor de recursos do MySQL, abra uma janela de console novo com privilégios elevada do PowerShell e altere o diretório onde você extraiu os arquivos binários de provedor de recursos MySQL. É recomendável usar uma nova janela do PowerShell para evitar possíveis problemas causados por módulos do PowerShell que já estão carregados.

Execute o **DeployMySqlProvider.ps1** script, o que conclui as seguintes tarefas:

* Carrega os certificados e outros artefatos para uma conta de armazenamento na pilha do Azure.
* Publica pacotes da Galeria para que você possa implantar bancos de dados MySQL usando a Galeria.
* Publica um pacote de galeria para implantar servidores de hospedagem.
* Implanta uma VM usando a imagem do Windows Server 2016 core você baixou e, em seguida, instala o provedor de recursos do MySQL.
* Registra um registro DNS local que é mapeado para o provedor de recursos VM.
* Registra o provedor de recursos com o local do Azure Gerenciador de recursos para as contas de usuário e do operador.
* Opcionalmente, instala uma única atualização do Windows Server durante a instalação do provedor de recursos.

> [!NOTE]
> Quando a implantação de provedor de recursos do MySQL é iniciado, o **system.local.mysqladapter** grupo de recursos é criado. Ele pode levar até 75 minutos para concluir as quatro implantações necessárias para este grupo de recursos.

### <a name="deploymysqlproviderps1-parameters"></a>Parâmetros de DeployMySqlProvider.ps1

Você pode especificar esses parâmetros da linha de comando. Se você não fizer isso, ou se nenhuma validação de parâmetro falhar, será solicitado a fornecer os parâmetros necessários.

| Nome do parâmetro | DESCRIÇÃO | Comentário ou o valor padrão |
| --- | --- | --- |
| **CloudAdminCredential** | A credencial do administrador da nuvem, necessário para acessar o ponto de extremidade com privilégios. | _Obrigatório_ |
| **AzCredential** | As credenciais para a conta de administrador de serviço de pilha do Azure. Use as mesmas credenciais que você usou para implantar a pilha do Azure. | _Obrigatório_ |
| **VMLocalCredential** | As credenciais de conta de administrador local do provedor de recursos MySQL VM. | _Obrigatório_ |
| **PrivilegedEndpoint** | O endereço IP ou nome DNS do ponto de extremidade com privilégios. |  _Obrigatório_ |
| **DependencyFilesLocalPath** | Caminho para um compartilhamento local que contém [mysql-conector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Se você fornecer um desses caminhos, o arquivo de certificado deve ser colocado nesse diretório também. | _Opcional_ para um único nó, _obrigatório_ para vários nós. |
| **DefaultSSLCertificatePassword** | A senha para o certificado. pfx. | _Obrigatório_ |
| **MaxRetryCount** | O número de vezes que você deseja repetir a cada operação se houver uma falha.| 2 |
| **RetryDuration** | O intervalo de tempo limite entre as repetições, em segundos. | 120 |
| **Desinstalar** | Remove o provedor de recursos e todos os respectivos recursos (consulte as observações a seguir). | Não  |
| **DebugMode** | Impede que a limpeza automática em caso de falha. | Não  |
| **AcceptLicense** | Ignora o prompt para aceitar a licença GPL.  <http://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

> [!NOTE]
> SKUs podem demorar até uma hora para ser visível no portal. Você não pode criar um banco de dados até que o SKU é implantado e em execução.

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Implantar o provedor de recursos do MySQL usando um script personalizado

Para eliminar qualquer configuração manual ao implantar o provedor de recursos, você pode personalizar o script a seguir. Altere as senhas e informações de conta padrão conforme necessário para sua implantação de pilha do Azure.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"  

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

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

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files.
# Find the ERCS01 IP address first, and make sure the certificate file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Quando o script de instalação do provedor de recursos for concluída, atualize seu navegador para se certificar de que você pode ver as atualizações mais recentes.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Verifique se a implantação usando o portal de pilha do Azure

1. Entre no portal de administração como o administrador do serviço.
2. Selecione **grupos de recursos**
3. Selecione o **system.\< local\>.mysqladapter** grupo de recursos.
4. Na página de resumo para o recurso grupo visão geral, a mensagem em **implantações** devem ser **êxito 3**.
5. Você pode obter informações mais detalhadas sobre a implantação do provedor de recursos em **configurações**. Selecione **implantações** para obter informações como: STATUS, TIMESTAMP e duração de cada implantação.

## <a name="next-steps"></a>Próximas etapas

[Adicionar servidores de hospedagem](azure-stack-mysql-resource-provider-hosting-servers.md)
