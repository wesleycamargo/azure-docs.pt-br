<properties
	pageTitle="Configurar a Integração do Cofre de Chaves do Azure para SQL Server em VMs do Azure (Clássico)"
	description="Saiba como automatizar a configuração da criptografia do SQL Server para uso com o cofre de chave do Azure. Este tópico explica como usar a integração da Chave do Cofre do Azure com máquinas virtuais do SQL Server para criação no modelo de implantação clássico."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/08/2016"
	ms.author="jroth"/>

# Configurar a Integração do Cofre de Chaves do Azure para SQL Server em VMs do Azure (Clássico)

> [AZURE.SELECTOR]
- [Gerenciador de Recursos](virtual-machines-windows-ps-sql-keyvault.md)
- [Clássico](virtual-machines-windows-classic-ps-sql-keyvault.md)

## Visão geral
Há vários recursos de criptografia SQL Server, como [transparent data encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [criptografia de nível de coluna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx), e [criptografia de backup](https://msdn.microsoft.com/library/dn449489.aspx). Essas formas de criptografia exigem o gerenciamento e armazenamento de chaves criptográficas usadas para a criptografia. O serviço Cofre da Chave do Azure (AKV) foi criado para melhorar a segurança e o gerenciamento dessas chaves em um local seguro e altamente disponível. O [SQL Server Connector](http://www.microsoft.com/download/details.aspx?id=45344) permite que o SQL Server use essas chaves do Cofre da Chave do Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Se você estiver executando o SQL Server em máquinas locais, há [etapas para acessar o Cofre da Chave do Azure da máquina local com SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Mas, para o SQL Server em VMs do Azure, você pode economizar tempo usando o recurso *Integração do Cofre da Chave do Azure*. Com alguns cmdlets do Azure PowerShell para habilitar esse recurso, você poderá automatizar a configuração necessária para que uma VM do SQL acesse seu cofre da chave.

Quando esse recurso está habilitado, ele instala automaticamente o SQL Server Connector, configura o provedor de EKM a fim de acessar o Cofre da Chave do Azure e cria a credencial para permitir que você acesse seu cofre. Ao examinar as etapas da documentação local mencionada anteriormente, é possível ver que esse recurso automatiza as etapas 2 e 3. A única coisa que você ainda precisará fazer manualmente é criar o cofre da chave e as chaves. A partir daí, toda a configuração de sua VM do SQL será automatizada. Quando esse recurso concluir a configuração, você poderá executar instruções T-SQL para começar a criptografar seus bancos de dados ou backups como faria normalmente.

[AZURE.INCLUDE [Preparação para a integração de AKV](../../includes/virtual-machines-sql-server-akv-prepare.md)]

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
		Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

A extensão do Agente IaaS do SQL atualizará a VM do SQL com essa nova configuração.

[AZURE.INCLUDE [Próximas Etapas da Integração de AKV](../../includes/virtual-machines-sql-server-akv-next-steps.md)]

<!---HONumber=AcomDC_0629_2016-->