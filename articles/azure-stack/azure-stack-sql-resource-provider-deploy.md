---
title: Bancos de dados SQL na pilha do Azure | Microsoft Docs
description: "Saiba como você pode implantar bancos de dados SQL como um serviço na pilha do Azure e as etapas rápidas para implantar o adaptador de provedor de recursos do SQL Server."
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: JeffGo
ms.openlocfilehash: bf52ed4986b4e0930b57721c0e38bbf748045a36
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Usar bancos de dados SQL na pilha do Microsoft Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Usar o adaptador de provedor de recursos do SQL Server para expor bancos de dados SQL como um serviço de [Azure pilha](azure-stack-poc.md). Depois de instalar o provedor de recursos e conectá-lo para uma ou mais instâncias do SQL Server, os usuários podem criar:
- Bancos de dados para aplicativos de nuvem nativo.
- Sites baseados em SQL.
- Cargas de trabalho baseados em SQL.
Você não precisa provisionar uma máquina virtual (VM) que hospeda o SQL Server cada vez.

O provedor de recursos não dá suporte a todos os recursos de gerenciamento de banco de dados do [banco de dados do SQL Azure](https://azure.microsoft.com/services/sql-database/). Por exemplo, pools de banco de dados Elástico e a capacidade de discagem o desempenho do banco de dados para cima e para baixo automaticamente não estão disponíveis. No entanto, o recurso oferece suporte ao provedor semelhante criar, ler, atualizar e excluir operações (CRUD). A API não é compatível com o banco de dados SQL.

## <a name="sql-resource-provider-adapter-architecture"></a>Arquitetura de adaptador de provedor de recursos do SQL
O provedor de recursos consiste em três componentes:

- **O adaptador de provedor de recursos do SQL VM**, que é uma máquina virtual do Windows que executa os serviços de provedor.
- **O provedor de recursos**, que processa solicitações de provisionamento e expõe recursos de banco de dados.
- **Servidores que hospedam o SQL Server**, que fornecem a capacidade para bancos de dados chamados de servidores de hospedagem.

Você deve criar uma (ou mais) de instâncias do SQL Server e/ou fornecer acesso às instâncias do SQL Server externas.

## <a name="deploy-the-resource-provider"></a>Implantar o provedor de recursos

1. Se você ainda não tiver feito isso, registre o kit de desenvolvimento e baixar a imagem do Datacenter do Windows Server 2016 Core pode ser baixada por meio do gerenciamento do Marketplace. Você deve usar uma imagem do Windows Server 2016 Core. Você também pode usar um script para criar um [imagem do Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Certifique-se de selecionar a opção core). O tempo de execução do .NET 3.5 não é mais necessário.

2. Entrar para um host que possa acessar o ponto de extremidade privilegiado VM.

    - Em instalações do Kit de desenvolvimento de pilha do Azure, conectar-se ao host físico.

    - Em sistemas com vários nós, o host deve ser um sistema que pode acessar o ponto de extremidade com privilégios.
    
    >[!NOTE]
    > O sistema onde o script está sendo executado *deve* ser um sistema Windows 10 ou Windows Server 2016 com a versão mais recente do tempo de execução .NET instalada. A instalação falhará caso contrário. O host do SDK do Azure pilha atende a esse critério.


3. Baixe o provedor de recursos SQL binário. Em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário.

    >[!NOTE] 
    > Corresponde a compilação do provedor de recursos para compilações de pilha do Azure. Certifique-se de baixar o binário correto para a versão da pilha do Azure que está em execução.

    | Compilação de pilha do Azure | Instalador de provedor de recursos do SQL |
    | --- | --- |
    |1.0.180102.3, 1.0.180103.2 ou 1.0.180106.1 (com vários nós) | [SQL RP versão 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1.0.171122.1 | [SQL RP versão 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1.0.171028.1 | [SQL RP versão 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. O certificado de raiz de pilha do Azure é recuperado do ponto de extremidade com privilégios. Para o SDK de pilha do Azure, um certificado autoassinado é criado como parte desse processo. Para vários nós, você deve fornecer um certificado apropriado.

   Para fornecer seu próprio certificado, coloque um arquivo. pfx no **DependencyFilesLocalPath** da seguinte maneira:

    - Um certificado curinga para \*.dbadapter.\< região\>.\< fqdn externo\> ou um certificado de site único com um nome comum de sqladapter.dbadapter.\< região\>.\< fqdn externo\>.

    - Esse certificado deve ser confiável. Ou seja, a cadeia de confiança deve existir sem a necessidade de certificados intermediários.

    - Somente um arquivo de certificado único existe no DependencyFilesLocalPath.

    - O nome do arquivo não deve conter caracteres especiais.


5. Abra um **novo** console do PowerShell (administrativo) com privilégios elevados e vá para o diretório onde você extraiu os arquivos. Use uma nova janela para evitar problemas que possam surgir incorretos módulos do PowerShell que já estão carregados no sistema.

6. [Instale o Azure PowerShell versão 1.2.11](azure-stack-powershell-install.md).

7. Execute o script DeploySqlProvider.ps1, que executa estas etapas:

    - Carrega os certificados e outros artefatos para uma conta de armazenamento na pilha do Azure.
    - Publica pacotes da Galeria para que você possa implantar bancos de dados SQL por meio da Galeria.
    - Publica um pacote de galeria para implantar servidores de hospedagem.
    - Implanta uma máquina virtual usando a imagem do Windows Server 2016 que foi criada na etapa 1 e, em seguida, instala o provedor de recursos.
    - Registra um registro DNS local que é mapeado para o provedor de recursos VM.
    - Registra o provedor de recursos com o local do Azure Gerenciador de recursos (usuário e administrador).

> [!NOTE]
> Se a instalação demora mais de 90 minutos, ele poderá falhar. Se ele falhar, você verá uma mensagem de falha na tela e no arquivo de log, mas a implantação é repetida da etapa com falha. Sistemas que não atendem às especificações recomendadas vCPU e memória podem não ser capazes de implantar o provedor de recurso do SQL.
>

Aqui está um exemplo que pode ser executado do prompt do PowerShell. (Certifique-se de alterar as informações de conta e senhas, conforme necessário.)

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
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

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 parameters
Você pode especificar esses parâmetros na linha de comando. Se você não fizer isso, ou se qualquer parâmetro de validação falhar, você precisará fornecer os parâmetros necessários.

| Nome do parâmetro | DESCRIÇÃO | Comentário ou o valor padrão |
| --- | --- | --- |
| **CloudAdminCredential** | A credencial do administrador da nuvem, necessário para acessar o ponto de extremidade com privilégios. | _Obrigatório_ |
| **AzCredential** | As credenciais para a conta de administrador de serviço de pilha do Azure. Use as mesmas credenciais que você usou para implantar a pilha do Azure. | _Obrigatório_ |
| **VMLocalCredential** | As credenciais de conta de administrador local do provedor de recursos SQL VM. | _Obrigatório_ |
| **PrivilegedEndpoint** | O endereço IP ou nome DNS do ponto de extremidade com privilégios. |  _Obrigatório_ |
| **DependencyFilesLocalPath** | O arquivo. pfx do certificado deve ser colocado nesse diretório também. | _Opcional_ (_obrigatório_ para vários nós) |
| **DefaultSSLCertificatePassword** | A senha para o certificado. pfx. | _Obrigatório_ |
| **MaxRetryCount** | O número de vezes que você deseja repetir a cada operação se houver uma falha.| 2 |
| **RetryDuration** | O intervalo de tempo limite entre as repetições, em segundos. | 120 |
| **Desinstalar** | Remove o provedor de recursos e todos os respectivos recursos (consulte as observações a seguir). | Não  |
| **DebugMode** | Impede que a limpeza automática em caso de falha. | Não  |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Verifique se a implantação usando o portal de pilha do Azure

> [!NOTE]
>  Depois de concluir a execução do script de instalação, você precisa atualizar o portal para ver a folha de administrador.


1. Entre no portal de administração como o administrador do serviço.

2. Verifique se a implantação foi bem-sucedida. Vá para **grupos de recursos**. Selecione o **system.\< local\>.sqladapter** grupo de recursos. Verifique se todas as implantações de quatro foi bem-sucedida.

      ![Verifique se a implantação do provedor de recursos do SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Atualizar o adaptador de provedor de recursos do SQL (com vários nós apenas, compilações 1710 e posteriores)
Sempre que a compilação de pilha do Azure é atualizada, é lançado um novo adaptador de provedor de recursos do SQL. O adaptador existente pode continuar a trabalhar. No entanto, é recomendável atualizar para a versão mais recente assim que possível após a pilha do Azure é atualizada. 

O processo de atualização é semelhante ao processo de instalação descrita anteriormente. Você pode criar uma nova VM com o código mais recente do provedor de recursos. Além disso, você migra as configurações para essa nova instância, incluindo banco de dados e informações do servidor de hospedagem. Também é possível migrar o registro DNS necessário.

Use o script de UpdateSQLProvider.ps1 com os mesmos argumentos que descrevemos anteriormente. Você deve fornecer o certificado aqui também.

> [!NOTE]
> Esse processo de atualização só é suportado em sistemas com vários nós.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

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

### <a name="updatesqlproviderps1-parameters"></a>Parâmetros de UpdateSQLProvider.ps1
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



## <a name="remove-the-sql-resource-provider-adapter"></a>Remova o adaptador de provedor de recursos do SQL

Para remover o provedor de recursos, é essencial primeiro remova todas as dependências.

1. Certifique-se de que você tenha o pacote de implantação original que foi baixado para esta versão do adaptador de provedor de recursos do SQL.

2. Todos os bancos de dados de usuário devem ser excluídos do provedor de recursos. (Excluir os bancos de dados do usuário não exclui a dados). Essa tarefa deve ser executada pelos usuários.

3. O administrador deve excluir os servidores de hospedagem do adaptador de provedor de recursos de SQL.

4. O administrador deve excluir todos os planos que referenciam o adaptador de provedor de recursos do SQL.

5. O administrador deve excluir qualquer SKUs e cotas que estão associadas com o adaptador de provedor de recursos do SQL.

6. Executar novamente o script de implantação com os seguintes elementos:
    - -Desinstalar o parâmetro
    - Os pontos de extremidade do Gerenciador de recursos do Azure
    - O DirectoryTenantID
    - As credenciais da conta de administrador de serviço


## <a name="next-steps"></a>Próximas etapas

[Adicionar servidores de hospedagem](azure-stack-sql-resource-provider-hosting-servers.md) e [criar bancos de dados](azure-stack-sql-resource-provider-databases.md).

Tente outro [serviços de PaaS](azure-stack-tools-paas-services.md) como o [provedor de recursos do servidor MySQL](azure-stack-mysql-resource-provider-deploy.md) e [provedor de recursos de serviços de aplicativos](azure-stack-app-service-overview.md).
