<properties 
	pageTitle="Configurar a integração do Cofre da Chave do Azure para SQL Server em VMs do Azure"
	description="Saiba como automatizar a configuração da criptografia do SQL Server para uso com o cofre de chave do Azure. Este tópico explica como usar a integração de chave de cofre do Azure com máquinas virtuais do SQL Server." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="10/23/2015"
	ms.author="jroth"/>

# Configurar a integração do Cofre da Chave do Azure para SQL Server em VMs do Azure

## Visão geral
Há vários recursos de criptografia SQL Server, como [transparent data encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [criptografia de nível de coluna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx), e [criptografia de backup](https://msdn.microsoft.com/library/dn449489.aspx). Essas formas de criptografia exigem o gerenciamento e armazenamento de chaves criptográficas usadas para a criptografia. O serviço Cofre da Chave do Azure (AKV) foi criado para melhorar a segurança e o gerenciamento dessas chaves em um local seguro e altamente disponível. O [SQL Server Connector](http://www.microsoft.com/download/details.aspx?id=45344) permite que o SQL Server use essas chaves do Cofre da Chave do Azure.

Se você estiver executando o SQL Server em máquinas locais, há [etapas para acessar o Cofre da Chave do Azure da máquina local com SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Mas, para o SQL Server em VMs do Azure, você pode economizar tempo usando o recurso *Integração do Cofre da Chave do Azure*. Com alguns cmdlets do Azure PowerShell para habilitar esse recurso, você poderá automatizar a configuração necessária para que uma VM do SQL acesse seu cofre da chave.

Quando esse recurso está habilitado, ele instala automaticamente o SQL Server Connector, configura o provedor de EKM a fim de acessar o Cofre da Chave do Azure e cria a credencial para permitir que você acesse seu cofre. Ao examinar as etapas da documentação local mencionada anteriormente, é possível ver que esse recurso automatiza as etapas 2 e 3. A única coisa que você ainda precisará fazer manualmente é criar o cofre da chave e as chaves. A partir daí, toda a configuração de sua VM do SQL será automatizada. Quando esse recurso concluir a configuração, você poderá executar instruções T-SQL para começar a criptografar seus bancos de dados ou backups como faria normalmente.

## Preparar-se para a integração de AKV
Para usar a integração do Cofre da Chave do Azure e configurar a VM do SQL Server, há vários pré-requisitos:

1.	[Instalar o Azure Powershell](#install-azure-powershell)
2.	[Criar um Active Directory do Azure](#create-an-azure-active-directory)
3.	[Criar um cofre de chave](#create-a-key-vault)

As seções a seguir descrevem esses pré-requisitos e as informações que você precisa coletar para executar posteriormente os cmdlets do PowerShell.

### Instale o Azure PowerShell
Verifique se você instalou o SDK mais recente do Azure PowerShell. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md)

### Criar um Active Directory do Azure
Primeiro, você precisa ter um [Active Directory do Azure](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) em sua assinatura. Entre os diversos benefícios, isso permite que você conceda permissão de acesso ao seu cofre de chave para determinados usuários e aplicativos.

Em seguida, registre um aplicativo com o AAD Isso lhe dará uma conta de Entidade de Serviço com acesso ao cofre de chave de que sua máquina virtual precisará. No artigo Cofre da Chave do Azure, você poderá encontrar estas etapas na seção [Registrar um aplicativo com o Active Directory do Azure](../key-vault/key-vault-get-started.md#register) ou poderá ver as etapas com capturas de tela na seção **Obter uma identidade para o aplicativo** ou [esta postagem de blog](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Antes de concluir essas etapas, observe que você precisa coletar as seguintes informações durante esse registro e que serão necessárias mais tarde, quando você habilitar a integração da Chave do Cofre do Azure em sua VM do SQL.

- Após a adição do aplicativo, encontre a **ID do CLIENTE** na guia **CONFIGURAR**. ![ID de cliente do Active Directory do Azure](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-client-id.png)
	
	A ID do cliente é atribuída posteriormente ao parâmetro **$spName** (nome da Entidade de Serviço) no script do PowerShell a fim de habilitar a integração do Cofre da Chave do Azure. 
- Além disso, durante essas etapas, ao criar a chave, copie o segredo de sua chave conforme mostra a seguinte captura de tela. Esse segredo é atribuído posteriormente ao parâmetro **$spSecret** (segredo da Entidade de serviço) no script do PowerShell. ![Segredo do Active Directory do Azure](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-sp-secret.png)
- Você deve autorizar essa nova ID de cliente para ter as seguintes permissões de acesso: **criptografar**, **descriptografar**, **wrapKey**, **unwrapKey**, **assinar** e **verificar**. Isso é feito com o cmdlet [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607%28v=azure.98%29.aspx). Para saber mais, consulte [Autorizar o aplicativo a usar a chave ou o segredo](../key-vault/key-vault-get-started.md#authorize).

### Criar um cofre de chave
Para usar o Cofre da Chave do Azure a fim de armazenar as chaves que você usará para criptografia em sua VM, você precisa ter acesso em um cofre de chave. Se você ainda não tiver configurado seu cofre de chave, crie um usando as etapas no tópico [Introdução ao Cofre da Chave do Azure](../key-vault/key-vault-get-started.md). Antes de concluir essas etapas, você precisa coletar algumas informações durante esse configuração que serão necessárias mais tarde quando você habilitar a integração do Cofre da Chave do Azure em sua VM do SQL.

Quando você chegar à etapa Criar um cofre de chave, observe a propriedade **vaultUri** retornada, que é a URL do cofre de chave. No exemplo fornecido nesta etapa, mostrado abaixo, o nome do cofre de chave é ContosoKeyVault, portanto a URL do cofre de chave seria https://contosokeyvault.vault.azure.net/.

![Segredo do Active Directory do Azure](./media/virtual-machines-sql-server-azure-key-vault-integration/new-azurekeyvault.png)
 
A URL do cofre de chave será atribuída posteriormente ao parâmetro **$akvURL** no script do PowerShell a fim de habilitar a integração do Cofre da Chave do Azure.

## Configurar a integração de AKV
Use o PowerShell para configurar a integração do Cofre da Chave do Azure. As seções a seguir oferecem uma visão geral dos parâmetros necessários e um exemplo de script do PowerShell.

### Parâmetros de entrada
A tabela a seguir lista os parâmetros necessários para executar o script do PowerShell na próxima seção.

|Parâmetro|Descrição|Exemplo|
|---|---|---|
|**$akvURL**|**A URL do cofre da chave**|"https://contosokeyvault.vault.azure.net/"|
|**$spName**|**Nome da Entidade de Serviço**|"fde2b411-33d5-4e11-af04eb07b669ccf2"|
|**$spSecret**|**Segredo da Entidade de Serviço**|"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="|
|**$credName**|**Nome da credencial**: a integração AKV cria uma credencial no SQL Server, permitindo que a VM tenha acesso ao cofre da chave. Escolha um nome para essa credencial.|"mycred1"|
|**$vmName**|**Nome da máquina virtual**: o nome de uma VM SQL criada anteriormente.|"myvmname"|
|**$serviceName**|**Nome do serviço**: nome do Serviço de Nuvem associado à VM SQL.|"mycloudservicename"|

### Habilitar a integração de AKV com o PowerShell
O cmdlet **New-AzureVMSqlServerKeyVaultCredentialConfig** cria um objeto de configuração para o recurso de integração do Cofre da Chave do Azure. O cmdlet **Set-AzureVMSqlServerExtension** configura essa integração com o parâmetro **KeyVaultCredentialSettings**. As etapas a seguir mostram como usar esses comandos.

1. No Azure PowerShell, primeiro configure os parâmetros de entrada com valores específicos conforme descrito nas seções anteriores deste tópico. Veja a seguir um exemplo de script.
	
		$akvURL = "https://contosokeyvault.vault.azure.net/"
		$spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
		$spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
		$credName = "mycred1"
		$vmName = "myvmname"
		$serviceName = "mycloudservicename"
2.	Em seguida, use o script abaixo para configurar e habilitar a integração de AKV.
	
		$secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
		$akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
		Get-AzureVM –ServiceName $serviceName –Name $vmName | Set-AzureVMSqlServerExtension –KeyVaultCredentialSettings $akvs | Update-AzureVM

A extensão do Agente IaaS do SQL atualizará a VM do SQL com essa nova configuração.

## Próximas etapas
Depois de habilitar a integração do Cofre da Chave do Azure, você poderá habilitar a criptografia do SQL Server em sua VM do SQL. Primeiro, você precisará criar uma chave assimétrica dentro de seu cofre de chave e uma chave simétrica no SQL Server em sua VM. Em seguida, você poderá executar instruções T-SQL para habilitar a criptografia para seus bancos de dados e backups.

Há várias formas de criptografia das quais você pode tirar proveito:

- [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
- [Backups criptografados](https://msdn.microsoft.com/library/dn449489.aspx)
- [Criptografia de nível de coluna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Os scripts Transact-SQL a seguir fornecem exemplos para cada uma dessas áreas.

>[AZURE.NOTE]Cada exemplo tem base em dois pré-requisitos: uma chave assimétrica de seu cofre de chave chamado **CONTOSO\_KEY** e uma credencial criada pelo recurso de Integração de AKV chamada **Azure\_EKM\_TDE\_cred**.

### Transparent Data Encryption (TDE)
1. Crie um logon do SQL Server que será usado pelo Mecanismo de banco de dados para TDE e adicione a credencial a ele.
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it loads a database 
		-- encrypted by TDE.
		CREATE LOGIN TDE_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the TDE Login to add the credential for use by the 
		-- Database Engine to access the key vault
		ALTER LOGIN TDE_Login 
		ADD CREDENTIAL Azure_EKM_TDE_cred;
		GO
	
2. Crie a chave de criptografia do banco de dados que será usada para a TDE.
	
		USE ContosoDatabase;
		GO
		
		CREATE DATABASE ENCRYPTION KEY 
		WITH ALGORITHM = AES_128 
		ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the database to enable transparent data encryption.
		ALTER DATABASE ContosoDatabase 
		SET ENCRYPTION ON;
		GO

### Backups criptografados
1. Crie um logon do SQL Server que será usado pelo Mecanismo de banco de dados para criptografia de backups e adicione a credencial a ele.
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it is encrypting the backup.
		CREATE LOGIN Backup_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO 
		
		-- Alter the Encrypted Backup Login to add the credential for use by 
		-- the Database Engine to access the key vault
		ALTER LOGIN Backup_Login 
		ADD CREDENTIAL Azure_EKM_Backup_cred ;
		GO
	
2. Faça o backup do banco de dados especificando a criptografia com a chave assimétrica armazenada no cofre de chave.
	
		USE master;
		BACKUP DATABASE [DATABASE_TO_BACKUP]
		TO DISK = N'[PATH TO BACKUP FILE]' 
		WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD, 
		ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
		GO

### Criptografia de nível de coluna (CLE)
Esse script cria uma chave simétrica protegida pela chave assimétrica no cofre de chave e usa a chave simétrica para criptografar dados no banco de dados.

	CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
	WITH ALGORITHM=AES_256
	ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	DECLARE @DATA VARBINARY(MAX);
	
	--Open the symmetric key for use in this session
	OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY 
	DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	--Encrypt syntax
	SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));
	
	-- Decrypt syntax
	SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));
	
	--Close the symmetric key
	CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;

## Recursos adicionais
Para saber mais sobre como usar esses recursos de criptografia, consulte [Usando EKM com recursos de criptografia do SQL Server](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Observe que as etapas neste artigo presumem que o SQL Server já está em execução em uma máquina virtual do Azure. Se não estiver, consulte [Provisionar uma máquina virtual do SQL Server no Azure](virtual-machines-provision-sql-server.md). Para obter orientação sobre como executar o SQL Server em VMs do Azure, consulte [Visão geral sobre SQL Server em máquinas virtuais do Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=Nov15_HO1-->