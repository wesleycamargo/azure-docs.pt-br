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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 061d9431890622dafafa8c180027b8df735c100e
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295751"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Usar bancos de dados MySQL na pilha do Microsoft Azure
Use o provedor de recursos do Azure pilha MySQL Server para expor bancos de dados MySQL como um serviço de pilha do Azure. O serviço de provedor de recursos do MySQL é executado no provedor de recursos MySQL VM, que é uma máquina de virtual do Windows Server core.

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
    - Implanta uma VM usando uma imagem do marketplace de pilha do Windows Server 2016 do Azure e instala o provedor de recursos. 
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
        -VMLocalCredential $vmLocalAdminCreds ` 
        -CloudAdminCredential $cloudAdminCreds ` 
        -PrivilegedEndpoint $privilegedEndpoint ` 
        -DefaultSSLCertificatePassword $PfxPass ` 
        -DependencyFilesLocalPath $tempDir\cert ` 
        -AcceptLicense 
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


## <a name="next-steps"></a>Próximas etapas
[Adicionar servidores de hospedagem](azure-stack-mysql-resource-provider-hosting-servers.md)
