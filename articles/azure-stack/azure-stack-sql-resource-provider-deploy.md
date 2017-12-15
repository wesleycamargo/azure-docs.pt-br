---
title: Bancos de dados SQL na pilha do Azure | Microsoft Docs
description: "Saiba como você pode implantar bancos de dados SQL como um serviço na pilha do Azure e as etapas rápidas para implantar o adaptador de provedor de recursos do SQL Server"
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
ms.date: 12/14/2017
ms.author: JeffGo
ms.openlocfilehash: 111b6274f4a3633fa4dd367866bf4e4e72d6e2df
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Usar bancos de dados SQL na pilha do Microsoft Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Usar o adaptador de provedor de recursos do SQL Server para expor bancos de dados SQL como um serviço de [Azure pilha](azure-stack-poc.md). Depois de instalar o provedor de recursos e conectá-lo para uma ou mais instâncias do SQL Server, os usuários podem criar:
- bancos de dados para aplicativos de nuvem nativo
- sites baseados em SQL
- cargas de trabalho com base em SQL você não precisam provisionar uma máquina virtual (VM) que hospeda o SQL Server cada vez.

O provedor de recursos não dá suporte a todos os recursos de gerenciamento de banco de dados do [banco de dados do SQL Azure](https://azure.microsoft.com/services/sql-database/). Por exemplo, pools de banco de dados Elástico e a capacidade de desempenho de banco de dados para cima e para baixo de discagem automaticamente não estão disponíveis. No entanto, o recurso oferece suporte ao provedor semelhante criar, ler, atualizar e excluir operações (CRUD). A API não é compatível com o banco de dados SQL.

## <a name="sql-resource-provider-adapter-architecture"></a>Arquitetura de adaptador de provedor de recursos do SQL
O provedor de recursos é composto de três componentes:

- **O adaptador de provedor de recursos do SQL VM**, que é uma máquina virtual do Windows que executa os serviços de provedor.
- **O provedor de recursos**, que processa solicitações de provisionamento e expõe recursos de banco de dados.
- **Servidores que hospedam o SQL Server**, que fornecem a capacidade para bancos de dados, chamados de servidores de hospedagem.

Você deve criar um (ou mais) servidores SQL e/ou fornecer acesso a instâncias SQL externos.

## <a name="deploy-the-resource-provider"></a>Implantar o provedor de recursos

1. Se você ainda não tiver feito isso, registre o kit de desenvolvimento e baixar a imagem do Datacenter do Windows Server 2016 Core pode ser baixada por meio do gerenciamento do Marketplace. Você deve usar uma imagem do Windows Server 2016 Core. Você também pode usar um script para criar um [imagem do Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) -Certifique-se de selecionar a opção de núcleo. O tempo de execução do .NET 3.5 não é mais necessário.

2. Entrar para um host que pode acessar a VM com privilégios de ponto de extremidade.

    a. Em instalações do Kit de desenvolvimento na pilha do Azure (ASDK), faça logon no host físico.

    b. Em sistemas com vários nós, o host deve ser um sistema que pode acessar o ponto de extremidade com privilégios. 
    
    >[!NOTE]
    > O sistema onde o script está sendo executado *deve* ser um sistema Windows 10 ou Windows Server 2016 com a versão mais recente do tempo de execução .NET instalada. Caso contrário, haverá falha na instalação. O host ASDK atende a esses critérios.


3. Baixe o provedor de recursos SQL binário e execute o Self-extractor para extrair o conteúdo para um diretório temporário.

    >[!NOTE] 
    > Corresponde a compilação do provedor de recursos para compilações de pilha do Azure. Você deve baixar o binário correto para a versão da pilha do Azure que está em execução.

    | Compilação de pilha do Azure | Instalador de SQL RP |
    | --- | --- |
    | 1.0.171122.1 | [SQL RP versão 1.1.12.0](https://aka.ms/azurestacksqlrp) |
    | 1.0.171028.1 | [SQL RP versão 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
    | 1.0.170928.3 | [SQL RP versão 1.1.3.0](https://aka.ms/azurestacksqlrp1709) |
   

4. O certificado de raiz de pilha do Azure é recuperado do ponto de extremidade com privilégios. Para ASDK, um certificado autoassinado é criado como parte desse processo. Para vários nós, você deve fornecer um certificado apropriado.

    Se você precisa fornecer seu próprio certificado, você precisará de um arquivo PFX no **DependencyFilesLocalPath** (veja abaixo) da seguinte maneira:

    - Um certificado curinga para \*.dbadapter.\< região\>.\< fqdn externo\> ou um certificado de site único com um nome comum de sqladapter.dbadapter.\< região\>.\< fqdn externo\>
    - Esse certificado deve ser confiável, como seria emitido por uma autoridade de certificação. Ou seja, a cadeia de confiança deve existir sem a necessidade de certificados intermediários.
    - Somente um arquivo de certificado único existe no DependencyFilesLocalPath.
    - O nome do arquivo não deve conter caracteres especiais.


5. Abra um **novo** console do PowerShell (administrativo) com privilégios elevados e vá para o diretório onde você extraiu os arquivos. Use uma nova janela para evitar problemas que podem surgir de módulos do PowerShell incorretos já carregados no sistema.

6. [Instale o Azure PowerShell versão 1.2.11](azure-stack-powershell-install.md).

7. Execute o script DeploySqlProvider.ps1 com os parâmetros listados abaixo.

O script executa estas etapas:

- Carregar os certificados e outros artefatos para uma conta de armazenamento na sua pilha do Azure.
- Publica pacotes de galeria para que você possa implantar bancos de dados SQL por meio da Galeria.
- Publicar um pacote de galeria para implantar servidores de hospedagem
- Implantar uma VM usando a imagem do Windows Server 2016 criada na etapa 1 e instale o provedor de recursos.
- Registre um registro DNS local que é mapeado para o provedor de recursos VM.
- Registre o provedor de recursos com o local do Azure Resource Manager (usuário e administrador).

> [!NOTE]
> Se a instalação demora mais de 90 minutos, pode falhar e você verá uma mensagem de falha na tela e no arquivo de log, mas a implantação é repetida da etapa com falha. Sistemas que não atendem às especificações recomendadas vCPU e memória podem não ser capazes de implantar o SQL RP.
>

Aqui está um exemplo que você pode executar a partir do PowerShell prompt (mas alterar as informações de conta e senhas, conforme necessário):

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack and the default prefix is AzS
# For integrated systems, the domain and the prefix will be the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>Parâmetros de DeploySqlProvider.ps1
Você pode especificar esses parâmetros na linha de comando. Se você não fizer isso, ou qualquer parâmetro de validação falha, você precisará fornecer as necessárias.

| Nome do Parâmetro | Descrição | Comentário ou valor padrão |
| --- | --- | --- |
| **CloudAdminCredential** | A credencial do administrador da nuvem, necessário para acessar o ponto de extremidade com privilégios. | _obrigatório_ |
| **AzCredential** | Forneça as credenciais para a conta de administrador de serviço de pilha do Azure. Use as mesmas credenciais que você usou para implantar a pilha do Azure). | _obrigatório_ |
| **VMLocalCredential** | Defina as credenciais para a conta de administrador local do provedor de recursos SQL VM. | _obrigatório_ |
| **PrivilegedEndpoint** | Forneça o endereço IP ou nome DNS do ponto de extremidade Privleged. |  _obrigatório_ |
| **DependencyFilesLocalPath** | O arquivo PFX de certificado deve ser colocado nesse diretório também. | _opcional_ (_obrigatório_ para vários nós) |
| **DefaultSSLCertificatePassword** | A senha para o certificado. pfx | _obrigatório_ |
| **MaxRetryCount** | Defina quantas vezes você deseja repetir a cada operação se houver uma falha.| 2 |
| **RetryDuration** | Defina o tempo limite entre as repetições, em segundos. | 120 |
| **Desinstalar** | Remover o provedor de recursos e todos os respectivos recursos (consulte as observações abaixo) | Não |
| **DebugMode** | Impede que a limpeza automática em caso de falha | Não |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Verifique se a implantação usando o portal de pilha do Azure

> [!NOTE]
>  Depois que o script de instalação for concluída, você precisará atualizar o portal para ver a folha de administrador.


1. Entre no portal de administração como o administrador do serviço.

2. Verifique se a implantação foi bem-sucedida. Procurar **grupos de recursos** &gt;, clique no **system.\< local\>.sqladapter** recursos de grupo e verifique se todas as implantações de quatro foi bem-sucedida.

      ![Verifique se a implantação do SQL RP](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="remove-the-sql-resource-provider-adapter"></a>Remova o adaptador de provedor de recursos do SQL

Para remover o provedor de recursos, é essencial para primeiro remova todas as dependências.

1. Certifique-se de que o pacote de implantação original que foi baixado para esta versão do adaptador de provedor de recursos do SQL.

2. Todos os bancos de dados de usuário devem ser excluídos do provedor de recursos (isso não exclui os dados). Isso deve ser executado pelos usuários.

3. Administrador deve excluir os servidores de hospedagem do adaptador de provedor de recursos de SQL

4. Administrador deve excluir todos os planos que referenciam o adaptador de provedor de recursos do SQL.

5. Administrador deve excluir qualquer SKUs e cotas associadas ao adaptador de provedor de recursos do SQL.

6. Executar novamente o script de implantação com-desinstalar o parâmetro, os pontos de extremidade do Azure Resource Manager, DirectoryTenantID e credenciais para a conta de administrador de serviço.


## <a name="next-steps"></a>Próximas etapas

[Adicionar servidores de hospedagem](azure-stack-sql-resource-provider-hosting-servers.md) e [criar bancos de dados](azure-stack-sql-resource-provider-databases.md).

Tente outro [serviços de PaaS](azure-stack-tools-paas-services.md) como o [provedor de recursos do servidor MySQL](azure-stack-mysql-resource-provider-deploy.md) e [provedor de recursos de serviços de aplicativos](azure-stack-app-service-overview.md).
