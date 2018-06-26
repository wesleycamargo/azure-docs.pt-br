---
title: Bancos de dados SQL na pilha do Azure | Microsoft Docs
description: Saiba como você pode implantar bancos de dados SQL como um serviço na pilha do Azure e as etapas rápidas para implantar o adaptador de provedor de recursos do SQL Server.
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
ms.openlocfilehash: e1505761a0bd1ea9dabdd0b2cbab7af902198311
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938325"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Implantar o provedor de recursos do SQL Server na pilha do Azure

Use o provedor de recursos do SQL Server do Azure pilha para expor bancos de dados SQL como um serviço de pilha do Azure. O provedor de recursos do SQL é executado como um serviço em uma máquina virtual do Server Core do Windows Server 2016 (VM).

## <a name="prerequisites"></a>Pré-requisitos

Existem vários pré-requisitos que precisam estar em vigor antes de implantar o provedor de recursos do SQL de pilha do Azure. Para atender a esses requisitos, conclua as etapas a seguir em um computador que possa acessar o ponto de extremidade privilegiado VM:

- Se você ainda não fez isso, [registrar Azure pilha](.\azure-stack-registration.md) com o Azure para o download de itens do marketplace do Azure.
- Adicionar o núcleo do Windows Server necessário VM Marketplace do Azure pilha baixando o **Windows Server 2016 Datacenter - Server Core** imagem. Você também pode usar um script para criar um [imagem do Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). Verifique se que você selecionar a opção de núcleo, quando você executa o script.

  >[!NOTE]
  >Se você precisar instalar uma atualização, você pode colocar um único pacote MSU no caminho do local de dependência. Se mais de um arquivo MSU for encontrado, a instalação do provedor de recursos SQL falhará.

- Baixe o provedor de recursos SQL binário e, em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário. O provedor de recursos possui uma pilha de Azure de correspondente mínima de compilação. Certifique-se de que baixar o binário correto para a versão da pilha do Azure que você está executando.

    |Versão da pilha do Azure|Versão de SQL RP|
    |-----|-----|
    |Versão 1804 (1.0.180513.1)|[SQL RP versão 1.1.24.0](https://aka.ms/azurestacksqlrp1804)
    |Versão 1802 (1.0.180302.1)|[SQL RP versão 1.1.18.0](https://aka.ms/azurestacksqlrp1802)|
    |Versão 1712 (1.0.180102.3, 1.0.180103.2 ou 1.0.180106.1 (sistemas integrados))|[SQL RP versão 1.1.14.0](https://aka.ms/azurestacksqlrp1712)|
    |     |     |

### <a name="certificates"></a>Certificados

Para instalações de sistemas integrados somente. Você deve fornecer o certificado PKI de PaaS SQL descrito na seção de certificados de PaaS opcional de [requisitos de PKI de implantação do Azure pilha](.\azure-stack-pki-certs.md#optional-paas-certificates). Coloque o arquivo. pfx no local especificado pelo **DependencyFilesLocalPath** parâmetro.

## <a name="deploy-the-sql-resource-provider"></a>Implantar o provedor de recursos do SQL

Depois que você tem todos os pré-requisitos instalados, execute o **DeploySqlProvider.ps1** script para implantar o provedor de recursos do SQL. O script DeploySqlProvider.ps1 é extraído como parte do binário de provedor de recursos do SQL que você baixou para a sua versão da pilha do Azure.

> [!IMPORTANT]
> O sistema que você estiver executando o script em deve ser um sistema Windows 10 ou Windows Server 2016 com a versão mais recente do tempo de execução .NET instalada.

Para implantar o provedor de recursos do SQL, abra um **novo** PowerShell elevado janela de console e altere o diretório onde você extraiu os arquivos binários do provedor de recursos SQL. É recomendável usar uma nova janela do PowerShell para evitar possíveis problemas causados por módulos do PowerShell que já estão carregados.

Execute o script DeploySqlProvider.ps1, que conclui as seguintes tarefas:

- Carrega os certificados e outros artefatos para uma conta de armazenamento na pilha do Azure.
- Publica pacotes da Galeria para que você possa implantar bancos de dados SQL usando a Galeria.
- Publica um pacote de galeria para implantar servidores de hospedagem.
- Implanta uma VM usando a imagem do Windows Server 2016 core você baixou e, em seguida, instala o provedor de recursos do SQL.
- Registra um registro DNS local que é mapeado para o provedor de recursos VM.
- Registra o provedor de recursos com o local do Azure Gerenciador de recursos para as contas de usuário e do operador.
- Opcionalmente, instala uma única atualização do Windows Server durante a instalação do provedor de recursos.

> [!NOTE]
> Quando a implantação de provedor de recursos do SQL é iniciado, o **system.local.sqladapter** grupo de recursos é criado. Ele pode levar até 75 minutos para concluir as quatro implantações necessárias para este grupo de recursos.

### <a name="deploysqlproviderps1-parameters"></a>Parâmetros de DeploySqlProvider.ps1

Você pode especificar os seguintes parâmetros de linha de comando. Se você não fizer isso, ou se nenhuma validação de parâmetro falhar, será solicitado a fornecer os parâmetros necessários.

| Nome do parâmetro | DESCRIÇÃO | Comentário ou o valor padrão |
| --- | --- | --- |
| **CloudAdminCredential** | A credencial do administrador da nuvem, necessário para acessar o ponto de extremidade com privilégios. | _Obrigatório_ |
| **AzCredential** | As credenciais para a conta de administrador de serviço de pilha do Azure. Use as mesmas credenciais que você usou para implantar a pilha do Azure. | _Obrigatório_ |
| **VMLocalCredential** | As credenciais de conta de administrador local do provedor de recursos SQL VM. | _Obrigatório_ |
| **PrivilegedEndpoint** | O endereço IP ou nome DNS do ponto de extremidade com privilégios. |  _Obrigatório_ |
| **DependencyFilesLocalPath** | O arquivo. pfx do certificado deve ser colocado nesse diretório também. | _Opcional_ (_obrigatório_ para sistemas integrados) |
| **DefaultSSLCertificatePassword** | A senha para o certificado. pfx. | _Obrigatório_ |
| **MaxRetryCount** | O número de vezes que você deseja repetir a cada operação se houver uma falha.| 2 |
| **RetryDuration** | O intervalo de tempo limite entre as repetições, em segundos. | 120 |
| **Desinstalar** | Remove o provedor de recursos e todos os respectivos recursos (consulte as observações a seguir). | Não  |
| **DebugMode** | Impede que a limpeza automática em caso de falha. | Não  |

>[!NOTE]
> SKUs podem demorar até uma hora para ser visível no portal. Você não pode criar um banco de dados até que o SKU é implantado e em execução.

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Implantar o provedor de recursos do SQL usando um script personalizado

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
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory If folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Quando o script de instalação do provedor de recursos for concluída, atualize seu navegador para se certificar de que você pode ver as atualizações mais recentes.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Verifique se a implantação usando o portal de pilha do Azure

Você pode usar os seguintes etapas, verifique se o provedor de recursos do SQL é implantado com êxito.

1. Entre no portal de administração como o administrador do serviço.
2. Selecione **grupos de recursos**.
3. Selecione o **system.\< local\>.sqladapter** grupo de recursos.
4. A mensagem em **implantações**, mostrado na captura de tela de Avançar, deve ser **êxito 4**.

      ![Verifique se a implantação do provedor de recursos do SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

5. Você pode obter informações mais detalhadas sobre a implantação do provedor de recursos em **configurações**. Selecione **implantações** para obter informações como: STATUS, TIMESTAMP e duração de cada implantação.

## <a name="next-steps"></a>Próximas etapas

[Adicionar servidores de hospedagem](azure-stack-sql-resource-provider-hosting-servers.md)
