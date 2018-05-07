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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 70b07cae9a1dc8b45e27f95e19fbc84f06a0b6d3
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Usar bancos de dados SQL na pilha do Microsoft Azure
Use o provedor de recursos do SQL Server do Azure pilha para expor bancos de dados SQL como um serviço de pilha do Azure. O serviço de provedor de recursos do SQL é executado no provedor de recursos SQL VM, que é uma máquina de virtual do Windows Server core.

## <a name="prerequisites"></a>Pré-requisitos
Existem vários pré-requisitos que precisam estar em vigor antes de implantar o provedor de recursos do SQL de pilha do Azure. Execute as seguintes etapas em um computador que possa acessar o ponto de extremidade privilegiado VM:

- Se você ainda não tiver feito isso, [registrar Azure pilha](.\azure-stack-registration.md) com o Azure para que você pode fazer o download de itens do marketplace do Azure.
- Adicionar o núcleo do Windows Server necessário VM Marketplace do Azure pilha baixando o **core do Windows Server 2016** imagem. Se você precisar instalar uma atualização, você pode colocar um único. Pacote MSU no caminho do local de dependência. Se mais de um. Arquivo MSU for encontrado, haverá falha na instalação de provedor de recursos do SQL.
- Baixar o provedor de recursos do SQL binário e, em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário. O provedor de recursos possui uma pilha de Azure de correspondente mínima de compilação. Certifique-se de baixar o binário correto para a versão da pilha do Azure que você está executando:
    - A pilha do Azure versão 1802 (1.0.180302.1): [SQL RP versão 1.1.18.0](https://aka.ms/azurestacksqlrp1802).
    - A pilha do Azure versão 1712 (1.0.180102.3, 1.0.180103.2 ou 1.0.180106.1 (sistemas integrados)): [SQL RP versão 1.1.14.0](https://aka.ms/azurestacksqlrp1712).
- Para instalações de sistemas integrados somente, você deve fornecer o certificado PKI de PaaS SQL conforme descrito na seção de certificados de PaaS opcional de [requisitos de PKI de implantação do Azure pilha](.\azure-stack-pki-certs.md#optional-paas-certificates), colocando o arquivo. pfx no local especificado pelo **DependencyFilesLocalPath** parâmetro.
- Certifique-se de que você tenha o [versão mais recente do PowerShell do Azure pilha](.\azure-stack-powershell-install.md) (v1.2.11) instalado. 

## <a name="deploy-the-sql-resource-provider"></a>Implantar o provedor de recursos do SQL
Após ter preparado com êxito para instalar o provedor de recursos do SQL, todos os pré-requisitos de reunião, agora você pode executar o **DeploySqlProvider.ps1** script para implantar o provedor de recursos do SQL. O script DeploySqlProvider.ps1 é extraído como parte do binário do provedor de recursos do SQL que você baixou correspondente à sua versão de pilha do Azure. 

> [!IMPORTANT]
> O sistema onde o script está sendo executado deve ser um sistema Windows 10 ou Windows Server 2016 com a versão mais recente do tempo de execução .NET instalada.


Para implantar o provedor de recursos do SQL, abra um console do PowerShell (administrativo) novos com privilégios elevado e altere o diretório onde você extraiu os arquivos binários do provedor de recursos SQL.

> [!NOTE]
> Use uma nova janela do console do PowerShell para evitar problemas que possam surgir incorretos módulos do PowerShell que já estão carregados no sistema.

Execute o script DeploySqlProvider.ps1, que executa as seguintes etapas:
- Carrega os certificados e outros artefatos para uma conta de armazenamento na pilha do Azure.
- Publica pacotes da Galeria para que você possa implantar bancos de dados SQL por meio da Galeria.
- Publica um pacote de galeria para implantar servidores de hospedagem.
- Implanta uma máquina virtual usando a imagem do Windows Server 2016 que foi criada na etapa 1 e, em seguida, instala o provedor de recursos.
- Registra um registro DNS local que é mapeado para o provedor de recursos VM.
- Registra o provedor de recursos com o local do Azure Gerenciador de recursos (usuário e administrador).
- Opcionalmente, instala uma única atualização do Windows durante a instalação do RP.

Implantação do provedor de recursos SQL começa e cria o grupo de recursos system.local.sqladapter. Ele pode levar até 75 minutos para concluir as quatro implantações necessárias para este grupo de recursos.

### <a name="deploysqlproviderps1-parameters"></a>Parâmetros de DeploySqlProvider.ps1
Você pode especificar esses parâmetros na linha de comando. Se você não fizer isso, ou se qualquer parâmetro de validação falhar, você precisará fornecer os parâmetros necessários.

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



## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Implantar o provedor de recursos do SQL usando um script personalizado
Para evitar inserir manualmente as informações necessárias quando o script DeploySqlProvider.ps1 é executado, você pode personalizar o exemplo de script a seguir alterando as informações de conta padrão e as senhas conforme necessário:

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Verifique se a implantação usando o portal de pilha do Azure
As etapas nesta seção podem ser usadas para garantir que o provedor de recursos do SQL foi implantado com êxito.

> [!NOTE]
>  Depois de concluir a execução do script de instalação, você precisa atualizar o portal para ver os itens de folha e a Galeria de administrador.

1. Entre no portal de administração como o administrador do serviço.

2. Verifique se a implantação foi bem-sucedida. Vá para **grupos de recursos**. Selecione o **system.\< local\>.sqladapter** grupo de recursos. Verifique se todas as implantações de quatro foi bem-sucedida.

      ![Verifique se a implantação do provedor de recursos do SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="next-steps"></a>Próximas etapas

[Adicionar servidores de hospedagem](azure-stack-sql-resource-provider-hosting-servers.md)
