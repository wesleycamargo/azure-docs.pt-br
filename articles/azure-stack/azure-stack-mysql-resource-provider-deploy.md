---
title: Usar bancos de dados MySQL como PaaS na pilha do Azure | Microsoft Docs
description: "Saiba como você pode implantar o provedor de recursos MySQL e fornecer os bancos de dados MySQL como um serviço na pilha do Azure"
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
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: badaefb4986f573362babea81d704bf2be067d6b
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2017
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Usar bancos de dados MySQL na pilha do Microsoft Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode implantar um provedor de recursos do MySQL na pilha do Azure. Depois de implantar o provedor de recursos, você pode criar servidores MySQL e bancos de dados por meio de modelos de implantação do Gerenciador de recursos do Azure e fornecem bancos de dados MySQL como um serviço. Bancos de dados MySQL, que são comuns em sites da web, suportam a várias plataformas de site. Por exemplo, depois de implantar o provedor de recursos, você pode criar sites de WordPress da plataforma de aplicativos Web do Azure como um complemento de serviço (PaaS) para a pilha do Azure.

Para implantar o provedor do MySQL em um sistema que não tem acesso à internet, você pode copiar o arquivo [mysql-conector-net-6.9.9.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.9.9.msi) para um compartilhamento local. Em seguida, fornece esse nome de compartilhamento quando solicitado. Você também deve instalar os módulos do Azure e o Azure PowerShell de pilha.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>Arquitetura de adaptador de provedor de recursos do MySQL Server

O provedor de recursos é composto de três componentes:

- **O adaptador de provedor de recursos MySQL VM**, que é uma máquina virtual do Windows que executa os serviços de provedor.
- **O provedor de recursos**, que processa solicitações de provisionamento e expõe recursos de banco de dados.
- **Servidores que hospedam o servidor MySQL**, que fornecem a capacidade para bancos de dados, chamados de servidores de hospedagem.

Esta versão não cria uma instância do MySQL. Você deve criá-los e/ou fornecer acesso a instâncias SQL externos. Visite o [Galeria de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) para um modelo de exemplo que pode:
- criar um servidor MySQL para você
- Baixe e implante um servidor MySQL do Marketplace.

! [OBSERVAÇÃO] Instalado em uma pilha do Azure de vários nós de servidores de hospedagem devem ser criado de uma assinatura de locatário. Eles não não possível criar a assinatura de provedor padrão. Em outras palavras, eles devem ser criados no portal de locatário ou uma sessão do PowerShell com um logon adequado. Todos os servidores de hospedagem são passíveis de cobrança VMs e devem ter as licenças apropriadas. O administrador de serviço pode ser o proprietário da assinatura.

### <a name="required-privileges"></a>Privilégios necessários
A conta do sistema deve ter os seguintes privilégios:

1.  Banco de dados: Criar, descartar
2.  Logon: Criar, definir, Drop, conceder, revogar

## <a name="deploy-the-resource-provider"></a>Implantar o provedor de recursos

1. Se você ainda não tiver feito isso, registre o kit de desenvolvimento e baixar a imagem do Datacenter do Windows Server 2016 Core pode ser baixada por meio do gerenciamento do Marketplace. Você deve usar uma imagem do Windows Server 2016 Core. Você também pode usar um script para criar um [imagem do Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) -Certifique-se de selecionar a opção de núcleo. O tempo de execução do .NET 3.5 não é mais necessário.


2. Entrar para um host que pode acessar a VM com privilégios de ponto de extremidade.

    a. Em instalações do Kit de desenvolvimento na pilha do Azure (ASDK), faça logon no host físico.

    b. Em sistemas com vários nós, o host deve ser um sistema que pode acessar o ponto de extremidade com privilégios.

3. [Baixe o arquivo de binários do provedor de recursos MySQL](https://aka.ms/azurestackmysqlrp) e execute o Self-extractor para extrair o conteúdo para um diretório temporário.

4.  O certificado de raiz de pilha do Azure é recuperado do ponto de extremidade com privilégios. Para ASDK, um certificado autoassinado é criado como parte desse processo. Para vários nós, você deve fornecer um certificado apropriado.

    Se você precisa fornecer seu próprio certificado, será necessário o seguinte certificado:

    Um certificado curinga para \*.dbadapter.\< região\>.\< fqdn externo\>. Esse certificado deve ser confiável, como seria emitido por uma autoridade de certificação. Ou seja, a cadeia de confiança deve existir sem a necessidade de certificados intermediários. Um certificado de site único pode ser usado com o nome VM explícito [mysqladapter] usado durante a instalação.


5. Abra um **novo** console do PowerShell (administrativo) com privilégios elevados e vá para o diretório onde você extraiu os arquivos. Use uma nova janela para evitar problemas que podem surgir de módulos do PowerShell incorretos já carregados no sistema.

6. [Instale o Azure PowerShell versão 1.2.11](azure-stack-powershell-install.md).

7. Execute o script DeploySqlProvider.ps1.

O script executa estas etapas:

* Baixe o binário de conector do MySQL (Isso pode ser fornecida offline).
* Carregar os certificados e outros artefatos para uma conta de armazenamento na sua pilha do Azure.
* Publica pacotes de galeria para que você possa implantar bancos de dados SQL por meio da Galeria.
* Publicar um pacote de galeria para implantar servidores de hospedagem
* Implantar uma VM usando a imagem do Windows Server 2016 criada na etapa 1 e instale o provedor de recursos.
* Registre um registro DNS local que é mapeado para o provedor de recursos VM.
* Registre o provedor de recursos com o local do Azure Resource Manager (administrador e locatário).


Você pode:
- Especifique os parâmetros necessários pelo menos na linha de comando
- ou, se você executar sem parâmetros, digite-os quando solicitado.

Aqui está um exemplo que você pode executar a partir do PowerShell prompt (mas alterar as informações de conta e senhas, conforme necessário):


```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack
$domain = 'AzureStack'
# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privleged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files
# Find the ERCS01 IP address first and make sure the certificate
# file is in the specified directory
.$tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint '10.10.10.10' `
  -DefaultSSLCertificatePassword $PfxPass -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```

### <a name="deploymysqlproviderps1-parameters"></a>Parâmetros de DeployMySqlProvider.ps1

Você pode especificar esses parâmetros na linha de comando. Se você não fizer isso, ou qualquer parâmetro de validação falha, você precisará fornecer as necessárias.

| Nome do Parâmetro | Descrição | Comentário ou valor padrão |
| --- | --- | --- |
| **CloudAdminCredential** | A credencial do administrador da nuvem, necessário para acessar o ponto de extremidade Privleged. | _obrigatório_ |
| **AzCredential** | Forneça as credenciais para a conta de administrador de serviço de pilha do Azure. Use as mesmas credenciais que você usou para implantar a pilha do Azure). | _obrigatório_ |
| **VMLocalCredential** | Defina as credenciais para a conta de administrador local do provedor de recursos MySQL VM. | _obrigatório_ |
| **PrivilegedEndpoint** | Forneça o endereço IP ou nome DNS do ponto de extremidade com privilégios. |  _obrigatório_ |
| **DependencyFilesLocalPath** | Caminho para um compartilhamento local contendo [mysql-conector-net-6.9.9.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.9.9.msi). Se você fornecer um, o arquivo de certificado deve ser colocado nesse diretório também. | _opcional_ (_obrigatório_ para vários nós) |
| **DefaultSSLCertificatePassword** | A senha para o certificado. pfx | _obrigatório_ |
| **MaxRetryCount** | Defina quantas vezes você deseja repetir a cada operação se houver uma falha.| 2 |
| **RetryDuration** | Defina o tempo limite entre as repetições, em segundos. | 120 |
| **Desinstalar** | Remover o provedor de recursos e todos os respectivos recursos (consulte as observações abaixo) | Não |
| **DebugMode** | Impede que a limpeza automática em caso de falha | Não |
| **AcceptLicense** | Ignora o prompt para aceitar a licença GPL (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


As velocidades de download e o desempenho do sistema, dependendo da instalação pode levar apenas como 20 minutos ou longa como várias horas. Se a folha de MySQLAdapter não estiver disponível, atualize o portal de administração.

> [!NOTE]
> Se a instalação demora mais de 90 minutos, pode falhar e você verá uma mensagem de falha na tela e no arquivo de log. A implantação é repetida da etapa com falha. Sistemas que não atendem às especificações recomendadas de memória e núcleo podem não ser capazes de implantar o MySQL RP.



## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Verifique se a implantação usando o Portal de pilha do Azure

> [!NOTE]
>  Depois que o script de instalação for concluída, você precisará atualizar o portal para ver a folha de administrador.


1. Entre no portal de administração como o administrador do serviço.

2. Verifique se a implantação foi bem-sucedida. Procurar **grupos de recursos** &gt;, clique no **system.\< local\>.mysqladapter** recursos de grupo e verifique se todas as implantações de quatro foi bem-sucedida.

      ![Verifique se a implantação do RP MySQL](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Fornecem a capacidade de se conectar a um servidor de hospedagem MySQL

1. Entre portal do Azure pilha como um administrador de serviço.

2. Navegue até **recursos ADMINISTRATIVOS** &gt; **servidores de hospedagem MySQL** &gt; **+ adicionar**.

    O **servidores de hospedagem MySQL** folha é onde você pode conectar o provedor de recursos do MySQL Server para instâncias do MySQL Server reais que servem como back-end do provedor de recursos.

    ![Servidores de hospedagem](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Preencha o formulário com os detalhes de conexão da sua instância do MySQL Server. Forneça o nome de domínio totalmente qualificado (FQDN) ou um endereço IPv4 válido e não o nome curto de VM. Esta instalação não fornece uma instância do MySQL padrão. O tamanho fornecido ajuda o provedor de recursos a gerenciar a capacidade do banco de dados. Ele deverá estar perto a capacidade física do servidor de banco de dados.

    > [!NOTE]
    > Como a instância do MySQL pode ser acessada pelo locatário e administração do Azure Resource Manager, ele pode ser colocado sob controle do provedor de recursos. A instância do MySQL __deve__ ser atribuídos exclusivamente para RP.

4. Como adicionar servidores, você deve atribuí-los para um SKU de novo ou existente para permitir que a diferenciação de ofertas de serviço.
  Por exemplo, você pode ter uma instância do enterprise fornecendo:
    - capacidade do banco de dados
    - backup automático
    - reserva de servidores de alto desempenho para departamentos individuais
    - E assim por diante.
    O nome da SKU deve refletir as propriedades para que locatários podem colocar seus bancos de dados adequadamente. Todos os servidores de hospedagem em um SKU devem ter os mesmos recursos.

    ![Criar uma SKU do MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
SKUs podem demorar até uma hora para ser visível no portal. Você não pode criar um banco de dados até que o SKU é criado.


## <a name="to-test-your-deployment-create-your-first-mysql-database"></a>Para testar a implantação, crie seu primeiro banco de dados MySQL


1. Entre portal de pilha do Azure como administrador de serviço.

2. Clique o **+ novo** botão &gt; **dados + armazenamento** &gt; **banco de dados MySQL**.

3. Preencha o formulário com os detalhes do banco de dados.

    ![Criar um teste de banco de dados MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Selecione um SKU.

    ![Selecione um SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Crie uma configuração de logon. A configuração de logon pode ser reutilizada ou criado um novo. Essa configuração contém o nome de usuário e senha para o banco de dados.

    ![Criar um novo logon de banco de dados](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    A cadeia de caracteres de conexões inclui o nome do servidor de banco de dados reais. Copie-o a partir do portal.

    ![Obter a cadeia de caracteres de conexão para o banco de dados MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> O comprimento dos nomes de usuário não pode exceder 32 caracteres com MySQL 5.7 ou 16 caracteres de edições anteriores.


## <a name="add-capacity"></a>Adicionar capacidade

Adicione capacidade adicionando mais servidores de MySQL no portal do Azure pilha. Servidores adicionais podem ser adicionados a uma SKU de nova ou existente. Verifique se que as características de servidor são os mesmos.


## <a name="making-mysql-databases-available-to-tenants"></a>Disponibilizar os bancos de dados MySQL para locatários
Crie planos e ofertas para disponibilizar os bancos de dados MySQL para locatários. Adicione o serviço Microsoft.MySqlAdapter, adicionar uma cota, etc.

![Criar planos e ofertas para incluir bancos de dados](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="updating-the-administrative-password"></a>Atualizando a senha administrativa
Você pode modificar a senha primeiro alterá-la na instância do servidor MySQL. Navegue até **recursos ADMINISTRATIVOS** &gt; **servidores de hospedagem MySQL** &gt; e clique no servidor de hospedagem. No painel configurações, clique em senha.

![Atualize a senha de administrador](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="removing-the-mysql-adapter-resource-provider"></a>Removendo o provedor de recursos de adaptador do MySQL

Para remover o provedor de recursos, é essencial primeiro remova todas as dependências.

1. Certifique-se de que o pacote de implantação original que foi baixado para esta versão do provedor de recursos.

2. Todos os bancos de dados de locatário devem ser excluídos do provedor de recursos (isso não exclui os dados). Isso deve ser feito com os próprios locatários.

3. Locatários devem cancelar o registro do namespace.

4. Administrador deve excluir os servidores de hospedagem do adaptador de MySQL

5. Administrador deve excluir todos os planos que referenciam o adaptador do MySQL.

6. Administrador deve excluir todas as cotas associadas ao adaptador de MySQL.

7. Executar novamente o script de implantação com-desinstalar o parâmetro, os pontos de extremidade do Azure Resource Manager, DirectoryTenantID e credenciais para a conta de administrador de serviço.




## <a name="next-steps"></a>Próximas etapas


Tente outro [serviços de PaaS](azure-stack-tools-paas-services.md) como o [provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md) e [provedor de recursos de serviços de aplicativos](azure-stack-app-service-overview.md).
