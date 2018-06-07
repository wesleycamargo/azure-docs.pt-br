---
title: Usar bancos de dados MySQL como PaaS na pilha do Azure | Microsoft Docs
description: Saiba como você pode implantar o provedor de recursos MySQL e fornecer os bancos de dados MySQL como um serviço na pilha do Azure.
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
ms.date: 05/24/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: ef4d34870cce7d2a149b2592e341956419272c92
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604192"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Usar bancos de dados MySQL na pilha do Microsoft Azure 
Você pode implantar um provedor de recursos do MySQL na pilha do Azure. Depois de implantar o provedor de recursos, você pode criar bancos de dados por meio de modelos de implantação do Gerenciador de recursos do Azure e de servidores MySQL. Você também pode fornecer os bancos de dados MySQL como um serviço.  

Bancos de dados MySQL, que são comuns em sites da web, suportam a várias plataformas de site. Por exemplo, depois de implantar o provedor de recursos, você pode criar sites de WordPress da plataforma de aplicativos Web como um complemento de serviço (PaaS) para a pilha do Azure. 
 
Para implantar o provedor do MySQL em um sistema que não tem acesso à Internet, copie o arquivo [mysql-conector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) para um compartilhamento local. Em seguida, fornece esse nome de compartilhamento quando você for solicitado. Você deve instalar os módulos do Azure e o Azure PowerShell de pilha. 

## <a name="mysql-server-resource-provider-adapter-architecture"></a>Arquitetura de adaptador de provedor de recursos do servidor MySQL 
O provedor de recursos é composto de três componentes: 
- **O adaptador de provedor de recursos MySQL VM**, que é uma máquina virtual do Windows que está executando os serviços de provedor. 
- **O provedor de recursos**, que processa solicitações de provisionamento e expõe recursos de banco de dados. 
- **Servidores que hospedam o servidor MySQL**, que fornecem a capacidade para bancos de dados que são chamados de servidores de hospedagem. Você precisa criar instâncias do MySQL por conta própria ou fornecer acesso a instâncias SQL externos. Visite o [Galeria de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) para um modelo de exemplo que pode: 
    - Crie um servidor MySQL para você. 
    - Baixe e implante um servidor MySQL no Azure Marketplace. 


> [!NOTE] 
> Servidores que estão instalados na pilha do Azure de hospedagem sistemas integrados devem ser criados de uma assinatura de locatário. Eles não não possível criar a assinatura do provedor padrão. Eles devem ser criados no portal de locatário ou uma sessão do PowerShell com uma entrada apropriada. Todos os servidores de hospedagem são passíveis de cobrança VMs e devem ter as licenças apropriadas. O administrador de serviço pode ser o proprietário da assinatura de locatário. 

### <a name="required-privileges"></a>Privilégios necessários 
A conta do sistema deve ter os seguintes privilégios: 
- Banco de dados: Criar, descartar 
- Logon: Criar, definir, drop, conceder, revogar 
 
## <a name="deploy-the-resource-provider"></a>Implantar o provedor de recursos 
1. Se você ainda não tiver feito isso, registre o kit de desenvolvimento e baixar a imagem do Datacenter do Windows Server 2016 Core pode ser baixada por meio do gerenciamento do Marketplace. Você deve usar uma imagem do Windows Server 2016 Core. Você também pode usar um script para criar um [imagem do Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Certifique-se de selecionar a opção de núcleo.) 

2. Entrar para um host que possa acessar o ponto de extremidade privilegiado VM:
    - Em instalações do SDK do Azure, conectar-se ao host físico.  
    - Em sistemas integrados, o host deve ser um sistema que pode acessar o ponto de extremidade com privilégios. 
    >[!NOTE] 
    > O sistema no qual o script está sendo executado *deve* ser um sistema Windows 10 ou Windows Server 2016 com a versão mais recente do tempo de execução .NET instalada. A instalação falhará caso contrário. O host do Azure pilha ASDK atende a esse critério. 
3. Baixe o provedor de recursos MySQL binário. Em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário. 
    >[!NOTE]  
    > O provedor de recursos possui uma pilha de Azure de correspondente mínima de compilação. Certifique-se de baixar o binário correto para a versão da pilha do Azure que está em execução.

    | Versão da pilha do Azure | Versão do MySQL RP| 
    | --- | --- | 
    | Versão 1804 (1.0.180513.1)|[RP MySQL versão 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) | 
    | Versão 1802 (1.0.180302.1) | [RP MySQL versão 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) | 
    | Versão 1712 (1.0.180102.3 ou 1.0.180106.1 (sistemas integrados)) | [RP MySQL versão 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) | 

4.  Para ASDK, um certificado autoassinado é criado como parte desse processo. Para sistemas integrados, você deve fornecer um certificado apropriado. Se você precisa fornecer seu próprio certificado, coloque um arquivo. pfx no **DependencyFilesLocalPath** que atenda aos seguintes critérios: 
    - Um certificado curinga para \*.dbadapter.\< região\>.\< fqdn externo\> ou um certificado de site único com um nome comum de mysqladapter.dbadapter.\< região\>.\< fqdn externo\>. 
    - Esse certificado deve ser confiável. Ou seja, a cadeia de confiança deve existir sem a necessidade de certificados intermediários. 
    - Somente um arquivo de certificado único existe no DependencyFilesLocalPath. 
    - O nome do arquivo não deve conter todos os caracteres especiais ou espaços. 

5. Abra um **novo** console do PowerShell (administrativo) com privilégios elevados. Em seguida, altere para o diretório onde você extraiu os arquivos. Use uma nova janela para evitar problemas que possam surgir incorretos módulos do PowerShell que já estão carregados no sistema. 

6. Execute o **DeployMySqlProvider.ps1** script. O script executa estas etapas: 
    - Baixa o binário de conector do MySQL (Isso pode ser fornecida offline). 
    - Carrega os certificados e outros artefatos para uma conta de armazenamento na pilha do Azure. 
    - Publica pacotes da Galeria para que você possa implantar bancos de dados SQL por meio da Galeria. 
    - Publica um pacote de galeria para implantar servidores de hospedagem. 
    - Implanta um usin VM uma imagem do marketplace de pilha do Windows Server 2016 do Azure e instala o provedor de recursos. 
    - Registra um registro DNS local que é mapeado para o provedor de recursos VM. 
    - Registra o provedor de recursos com o local Azure Resource Manager (administrador e locatário). 

    Você pode especificar os parâmetros necessários na linha de comando ou executa o script sem nenhum parâmetro e, em seguida, digite-os quando solicitado. 

    Aqui está um exemplo que pode ser executado do prompt do PowerShell. Certifique-se de alterar as informações de conta e senhas, conforme necessário: 

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
    # Find the ERCS01 IP address first, and make sure the certificate 
    # file is in the specified directory. 
    $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds ` 
    VMLocalCredential $vmLocalAdminCreds ` 
    CloudAdminCredential $cloudAdminCreds ` 
    PrivilegedEndpoint $privilegedEndpoint ` 
    DefaultSSLCertificatePassword $PfxPass ` 
    DependencyFilesLocalPath $tempDir\cert ` 
    AcceptLicense 
    ``` 

### <a name="deploymysqlproviderps1-parameters"></a>Parâmetros de DeployMySqlProvider.ps1 
Você pode especificar esses parâmetros na linha de comando. Se você não fizer isso, ou se qualquer parâmetro de validação falhar, você precisará fornecer os parâmetros necessários. 

| Nome do parâmetro | DESCRIÇÃO | Comentário ou o valor padrão | 
| --- | --- | --- | 
| **CloudAdminCredential** | A credencial do administrador da nuvem, necessário para acessar o ponto de extremidade com privilégios. | _Obrigatório_ | 
| **AzCredential** | As credenciais para a conta de administrador de serviço de pilha do Azure. Use as mesmas credenciais que você usou para implantar a pilha do Azure. | _Obrigatório_ | 
| **VMLocalCredential** | As credenciais de conta de administrador local do provedor de recursos MySQL VM. | _Obrigatório_ | 
| **PrivilegedEndpoint** | O endereço IP ou nome DNS do ponto de extremidade com privilégios. |  _Obrigatório_ | 
| **DependencyFilesLocalPath** | Caminho para um compartilhamento local que contém [mysql-conector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Se você fornecer um desses caminhos, o arquivo de certificado deve ser colocado nesse diretório também. | _Opcional_ (_obrigatório_ para vários nós) | 
| **DefaultSSLCertificatePassword** | A senha para o certificado. pfx. | _Obrigatório_ | 
| **MaxRetryCount** | O número de vezes que você deseja repetir a cada operação se houver uma falha.| 2 | 
| **RetryDuration** | O intervalo de tempo limite entre as repetições, em segundos. | 120 | 
| **Desinstalar** | Remove o provedor de recursos e todos os respectivos recursos (consulte as observações a seguir). | Não  | 
| **DebugMode** | Impede que a limpeza automática em caso de falha. | Não  | 
| **AcceptLicense** | Ignora o prompt para aceitar a licença GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

> [!NOTE] 
> SKUs podem demorar até uma hora para ser visível no portal. Você não pode criar um banco de dados até que o SKU é criado. 

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Verifique se a implantação usando o portal de pilha do Azure 
> [!NOTE] 
>  Depois de concluir a execução do script de instalação, você precisa atualizar o portal para ver a folha de administrador. 

1. Entre no portal de administração como o administrador do serviço. 
2. Verifique se a implantação foi bem-sucedida. Vá para **grupos de recursos**e, em seguida, selecione o **system.\< local\>.mysqladapter** grupo de recursos. Verifique se todas as implantações de quatro foi bem-sucedida:

      ![Verifique se a implantação do MySQL RP](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png) 

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Fornecem a capacidade de se conectar a um servidor de hospedagem MySQL 
1. Entre portal do Azure pilha como um administrador de serviço. 
2. Selecione **recursos ADMINISTRATIVOS** > **servidores de hospedagem MySQL** > **+ adicionar**. Sobre o **servidores de hospedagem MySQL** folha, você pode conectar o provedor de recursos do MySQL Server para instâncias do MySQL Server reais que servem como back-end do provedor de recursos. 

![Servidores de hospedagem](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png) 

3. Forneça os detalhes de conexão da sua instância do MySQL Server. Certifique-se de fornecer o nome de domínio totalmente qualificado (FQDN) ou um endereço IPv4 válido e não o nome curto de VM. Esta instalação não fornece uma instância do MySQL padrão. O tamanho que é fornecido ajuda o provedor de recursos a gerenciar a capacidade do banco de dados. Ele deverá estar perto a capacidade física do servidor de banco de dados. 

    > [!NOTE] 
    > Se a instância do MySQL pode ser acessada pelo locatário e administração do Azure Resource Manager, ele pode ser colocado sob controle do provedor de recursos. A instância do MySQL *deve* ser atribuídos exclusivamente para o provedor de recursos. 

4. Como adicionar servidores, você deve atribuí-los para um SKU de novo ou existente para permitir que a diferenciação de ofertas de serviço. Por exemplo, você pode ter uma instância do enterprise fornecendo: 
    - capacidade do banco de dados
    - Backup automático
    - reserva de servidores de alto desempenho para departamentos individuais 

    > [!IMPORTANT] 
    > Você não pode misturar servidores autônomos com instâncias de AlwaysOn no SKU do mesmo. Tentativa de combinar tipos depois de adicionar os primeiros resultados de servidor de hospedagem em um erro. 

    O nome da SKU deve refletir as propriedades para que locatários podem colocar seus bancos de dados adequadamente. Todos os servidores de hospedagem em um SKU devem ter os mesmos recursos. 

    ![Criar uma SKU do MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png) 

## <a name="test-your-deployment-by-creating-your-first-mysql-database"></a>Testar a implantação criando seu primeiro banco de dados MySQL 
1. Entre portal do Azure pilha como um administrador de serviço. 
2. Selecione **+ novo** > **dados + armazenamento** > **banco de dados MySQL**. 
3. Forneça os detalhes do banco de dados. 

    ![Criar um teste de banco de dados MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)
 
4. Selecione um SKU. 

    ![Selecione um SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png) 

5. Crie uma configuração de logon. Você pode reutilizar uma configuração de logon existente ou criar um novo. Essa configuração contém o nome de usuário e senha para o banco de dados. 

    ![Criar um novo logon de banco de dados](./media/azure-stack-mysql-rp-deploy/create-new-login.png) 

    A cadeia de caracteres de conexões inclui o nome do servidor de banco de dados reais. Copie-o a partir do portal. 

    ![Obter a cadeia de caracteres de conexão para o banco de dados MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png) 

    > [!NOTE] 
    > O comprimento dos nomes de usuário não pode exceder 32 caracteres 5.7 do MySQL. Em edições anteriores, ele não pode exceder 16 caracteres. 

## <a name="add-capacity"></a>Adicionar capacidade 
Adicione capacidade adicionando mais servidores de MySQL no portal do Azure pilha. Servidores adicionais podem ser adicionados a uma SKU de nova ou existente. Verifique se que as características de servidor são os mesmos. 
 
## <a name="make-mysql-databases-available-to-tenants"></a>Disponibilizar os bancos de dados MySQL para locatários 
Crie planos e ofertas para disponibilizar os bancos de dados MySQL para locatários. Por exemplo, adicione o serviço Microsoft.MySqlAdapter, adicionar uma cota e assim por diante. 

![Criar planos e ofertas para incluir bancos de dados](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png) 

## <a name="update-the-administrative-password"></a>Atualizar a senha administrativa 
Você pode modificar a senha primeiro alterá-la na instância do servidor MySQL. Selecione **recursos ADMINISTRATIVOS** > **servidores de hospedagem MySQL**. Em seguida, selecione o servidor de hospedagem. No **configurações** painel, selecione **senha**. 

![Atualize a senha de administrador](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png) 

## <a name="update-the-mysql-resource-provider-adapter-integrated-systems-only"></a>Atualizar o adaptador de provedor de recursos do MySQL (apenas sistemas integrados)
Um novo adaptador de provedor de recursos do SQL pode ser liberado quando as compilações de pilha do Azure são atualizadas. Enquanto o adaptador existente continuará a funcionar, é recomendável atualizar para a versão mais recente assim que possível.  
 
Para atualizar do provedor de recursos que você usar o **UpdateMySQLProvider.ps1** script. O processo é semelhante ao processo usado para instalar um provedor de recursos, conforme descrito no [implantar o provedor de recursos](#deploy-the-resource-provider) deste artigo. O script está incluído no download do provedor de recursos. 

O **UpdateMySQLProvider.ps1** script cria uma nova VM com o código mais recente do provedor de recursos e migra as configurações da VM antiga para a nova VM. As configurações de migração incluem o banco de dados e informações do servidor de hospedagem e registro de DNS necessário. 

O script requer o uso dos mesmos argumentos que são descritas para o script DeployMySqlProvider.ps1. Forneça o certificado aqui também.  

A seguir está um exemplo de como o *UpdateMySQLProvider.ps1* script que pode ser executado do prompt do PowerShell. Certifique-se de alterar as informações de conta e senhas, conforme necessário:  

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
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
``` 
 
### <a name="updatemysqlproviderps1-parameters"></a>Parâmetros de UpdateMySQLProvider.ps1 
Você pode especificar esses parâmetros na linha de comando. Se você não fizer isso, ou se nenhuma validação de parâmetro falhar, você precisará fornecer os parâmetros necessários. 

| Nome do Parâmetro | DESCRIÇÃO | Comentário ou o valor padrão | 
| --- | --- | --- | 
| **CloudAdminCredential** | A credencial do administrador da nuvem, necessário para acessar o ponto de extremidade com privilégios. | _Obrigatório_ | 
| **AzCredential** | As credenciais para a conta de administrador de serviço de pilha do Azure. Use as mesmas credenciais que você usou para implantar a pilha do Azure. | _Obrigatório_ | 
| **VMLocalCredential** |As credenciais de conta de administrador local do provedor de recursos SQL VM. | _Obrigatório_ | 
| **PrivilegedEndpoint** | O endereço IP ou nome DNS do ponto de extremidade com privilégios. |  _Obrigatório_ | 
| **DependencyFilesLocalPath** | O arquivo. pfx do certificado deve ser colocado nesse diretório também. | _Opcional_ (_obrigatório_ para vários nós) | 
| **DefaultSSLCertificatePassword** | A senha para o certificado. pfx. | _Obrigatório_ | 
| **MaxRetryCount** | O número de vezes que você deseja repetir a cada operação se houver uma falha.| 2 | 
| **RetryDuration** | O intervalo de tempo limite entre as repetições, em segundos. | 120 | 
| **Desinstalar** | Remova o provedor de recursos e todos os respectivos recursos (consulte as observações a seguir). | Não  | 
| **DebugMode** | Impede que a limpeza automática em caso de falha. | Não  | 
| **AcceptLicense** | Ignora o prompt para aceitar a licença GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 
 
## <a name="collect-diagnostic-logs"></a>Coletar logs de diagnóstico 
O provedor de recursos do MySQL é um bloqueado para a máquina virtual. Se for necessário coletar logs da máquina virtual, um ponto de extremidade do PowerShell administração JEA (Just Enough) _DBAdapterDiagnostics_ é fornecido para essa finalidade. Há dois comandos a esse ponto de extremidade: 

- **Get-AzsDBAdapterLog**. Prepara um pacote zip que contém os logs de diagnóstico do RP e o coloca na unidade de usuário de sessão. O comando pode ser chamado sem parâmetros e coletará as últimas quatro horas de logs. 

- **Remover AzsDBAdapterLog**. Limpa os pacotes existentes do log no provedor de recursos do VM 

Uma conta de usuário chamado _dbadapterdiag_ é criada durante a implantação de RP ou atualização para se conectar ao ponto de extremidade de diagnóstico para a extração de logs do RP. A senha dessa conta é o mesmo que a senha fornecida para a conta de administrador local durante a implantação/atualização. 

Para usar esses comandos, você precisa criar uma sessão remota do PowerShell para a máquina de virtual do provedor de recursos e invocar o comando. Opcionalmente, você pode fornecer parâmetros FromDate e ToDate. Se você não especificar um ou ambos, a FromDate será quatro horas antes da hora atual e o ToDate será a hora atual. 

Esse script de exemplo demonstra o uso desses comandos: 

```powershell 
# Create a new diagnostics endpoint session. 
$databaseRPMachineIP = '<RP VM IP address>' 
$diagnosticsUserName = 'dbadapterdiag' 
$diagnosticsUserPassword = '<Enter Diagnostic password>' 
$diagCreds = New-Object System.Management.Automation.PSCredential ` 
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force)) 
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds 
        -ConfigurationName DBAdapterDiagnostics 

# Sample captures logs from the previous one hour 
$fromDate = (Get-Date).AddHours(-1) 
$dateNow = Get-Date 
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2} 
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow 

# Copy the logs 
$sourcePath = "User:\{0}" -f $logs 
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs 
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage 

# Cleanup logs 
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog } 
# Close the session 
$session | Remove-PSSession 
``` 

## <a name="maintenance-operations-integrated-systems"></a>Operações de manutenção (sistemas integrados) 
O provedor de recursos do MySQL é um bloqueado para a máquina virtual. Atualização de segurança da máquina do recurso provedor virtual pode ser feito por meio do ponto de extremidade do PowerShell administração JEA (Just Enough) _DBAdapterMaintenance_. Um script é fornecido com o pacote de instalação do RP para facilitar a essas operações. 

### <a name="update-the-virtual-machine-operating-system"></a>Atualizar o sistema operacional da máquina virtual 
Há várias maneiras de atualizar a VM do Windows Server: 
- Instalar o pacote de provedor de recursos mais recente usando uma imagem do Windows Server 2016 Core corrigida no momento 
- Instalar um pacote do Windows Update durante a instalação ou atualização do RP 

### <a name="update-the-virtual-machine-windows-defender-definitions"></a>Atualize as definições do Windows Defender de máquina virtual 
Siga estas etapas para atualizar as definições de Defender: 
1. Atualizar as definições do Windows Defender a partir de download [definição do Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions).

    Nessa página, em "Baixar e instalar manualmente as definições de" download "antivírus do Windows Defender para Windows 10 e o arquivo de 64 bits do Windows 8.1".
    
    Link direto: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64. 

2. Crie uma sessão do PowerShell para o ponto de extremidade da máquina do MySQL RP adaptador virtual manutenção. 

3. Copie o arquivo de atualização de definições para a máquina de adaptador de banco de dados usando a sessão de ponto de extremidade de manutenção. 

4. O PowerShell de manutenção de sessão invocar o _DBAdapterWindowsDefenderDefinitions atualização_ comando. 

5. Após a instalação, é recomendável remover o arquivo de atualização de definições usadas. Ele pode ser removido na sessão de manutenção usando o _ItemOnUserDrive remover)_ comando. 

Aqui está um exemplo de script para atualizar as definições de Defender (substitua o endereço ou nome da máquina virtual com o valor real): 

```powershell 
# Set credentials for the RP VM local admin user 
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force 
$vmLocalAdminUser = "<local admin user name>" 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ` 
    ($vmLocalAdminUser, $vmLocalAdminPass) 

# Public IP Address of the DB adapter machine 
$databaseRPMachine  = "<RP VM IP address>" 
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe" 
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.  
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' ` 
    -Outfile $localPathToDefenderUpdate  

# Create session to the maintenance endpoint 
$session = New-PSSession -ComputerName $databaseRPMachine ` 
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance 

# Copy defender update file to the db adapter machine 
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate ` 
     -Destination "User:\" 

# Install the update file 
Invoke-Command -Session $session -ScriptBlock ` 
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\"} 

# Cleanup the definitions package file and session 
Invoke-Command -Session $session -ScriptBlock ` 
    {Remove-AzSItemOnUserDrive -ItemPath "User:\"} 
$session | Remove-PSSession  
``` 
### <a name="secrets-rotation"></a>Rotação de segredos  
*Essas instruções se aplicam somente ao Azure pilha integrado sistemas versão 1804 e mais tarde. Não tente secreta rotação em versões de pilha do pré-1804 do Azure.* 
 
Quando usar os provedores de recursos do SQL e MySQL com pilha Azure sistemas integrados, você pode girar os segredos de infraestrutura (implantação) a seguir: 
- Certificado SSL externo [fornecido durante a implantação](azure-stack-pki-certs.md). 
- A recurso provedor VM local senha da conta administrador fornecida durante a implantação. 
- Senha de usuário de diagnóstico (dbadapterdiag) do provedor de recursos. 
#### <a name="powershell-examples-for-rotating-secrets"></a>Exemplos do PowerShell para girar segredos 
 
**Alterar todos os segredos ao mesmo tempo** 
```powershell 
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds 
``` 
**Alterar a senha de usuário de diagnóstico somente** 
```powershell 
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd  
``` 

**Alterar a senha da conta de administrador local VM** 
```powershell 
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds 
``` 
**Alterar o certificado SSL** 
```powershell 
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  
``` 

### <a name="secretrotationmysqlproviderps1-parameters"></a>Parâmetros de SecretRotationMySQLProvider.ps1 
|Parâmetro|DESCRIÇÃO| 
|-----|-----| 
|AzCredential|Credencial da conta de administrador de serviço de pilha do Azure.| 
|CloudAdminCredential|Azure pilha nuvem domínio conta credenciais de administrador.| 
|PrivilegedEndpoint|Ponto de extremidade com privilégios para acessar o Get-AzureStackStampInformation.| 
|DiagnosticsUserPassword|Senha de usuário de diagnóstico.| 
|VMLocalCredential|A conta de administrador local da VM MySQLAdapter.| 
|DefaultSSLCertificatePassword|Padrão de certificado SSL (* pfx) senha.| 
|DependencyFilesLocalPath|Caminho de Local de arquivos de dependência.| 
|     |     | 

### <a name="known-issues"></a>Problemas conhecidos 
Problema: Os logs para rotação de segredos não são automaticamente coletados se o script não quando ele é executado. 
 
Solução alternativa: Use o cmdlet Get-AzsDBAdapterLogs para coletar todos os logs do provedor de recursos, incluindo AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log em c:\Logs. 

## <a name="remove-the-mysql-resource-provider-adapter"></a>Remova o adaptador de provedor de recursos MySQL 
Para remover o provedor de recursos, é essencial primeiro remova todas as dependências. 
1. Certifique-se de que você tenha o pacote de implantação original que foi baixado para esta versão do provedor de recursos. 

2. Todos os bancos de dados de locatário devem ser excluídos do provedor de recursos. (Excluir os bancos de dados de locatário não exclui a dados). Essa tarefa deve ser executada pelos próprios locatários. 

3. Locatários devem cancelar o registro do namespace. 

4. O administrador deve excluir os servidores de hospedagem do adaptador de MySQL. 

5. O administrador deve excluir todos os planos que referenciam o adaptador do MySQL. 

6. O administrador deve excluir todas as cotas que estão associadas com o adaptador do MySQL. 

7. Executar novamente o script de implantação usando os seguintes parâmetros: 
    - -Desinstalar o parâmetro 
    - Os pontos de extremidade do Gerenciador de recursos do Azure 
    - O DirectoryTenantID 
    - As credenciais da conta de administrador de serviço 

### <a name="next-steps"></a>Próximas etapas
[Oferecer serviços de aplicativos como PaaS](azure-stack-app-service-overview.md)
