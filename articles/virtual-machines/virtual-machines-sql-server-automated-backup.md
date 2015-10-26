<properties
   pageTitle="Backup Automatizado para Máquinas Virtuais do SQL Server | Microsoft Azure"
   description="Explica o recurso de Backup Automatizado para SQL Server em execução em Máquinas Virtuais do Azure."
   services="virtual-machines"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="08/05/2015"
   ms.author="jroth" />

# Backup automatizado para SQL Server em Máquinas Virtuais do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.



O Backup Automatizado configura automaticamente o [Backup Gerenciado do Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os bancos de dados novos e existentes em uma VM do Azure executando o SQL Server 2014 Standard ou Enterprise. Isso permite que você configure backups regulares do banco de dados que utilizam o durável armazenamento de Blobs do Azure.

>[AZURE.NOTE]O backup automatizado depende do Agente IaaS do SQL Server. Para instalar e configurar o agente, você deve ter o Agente de VM do Azure em execução na máquina virtual de destino. Imagens da galeria de máquinas virtuais mais recentes têm esta opção habilitada por padrão, porém o Agente de VM do Azure pode estar ausente em VMs existentes. Se você estiver usando sua própria imagem de VM, também será necessário instalar o Agente IaaS do SQL Server. Para obter mais informações, veja [Agente de VM e Extensões](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

## Configurações de Backup Automatizadas

A tabela a seguir descreve as opções que podem ser configuradas para Backup Automatizado. As etapas de configuração reais variam dependendo de se você usar os comandos do Portal do Azure ou do Azure Windows PowerShell.

|Configuração|Intervalo (Padrão)|Descrição|
|---|---|---|
|**Backup Automatizado**|Habilitar/desabilitar (Desabilitado)|Habilita ou desabilita o Backup Automatizado de uma VM do Azure que executa o SQL Server 2014 Standard ou Enterprise.|
|**Período de retenção**|Um a 30 dias (30 dias)|O número de dias para manter um backup.|
|**Conta de armazenamento**|Conta de armazenamento do Azure (a conta de armazenamento criada para a VM especificada)|Uma conta de armazenamento do Azure a ser usada para armazenar arquivos de Backup Automatizado no armazenamento de blobs. Um contêiner é criado neste local para armazenar todos os arquivos de backup. A convenção de nomenclatura do arquivo de backup inclui a data, hora e nome do computador.|
|**Criptografia**|Habilitar/desabilitar (Desabilitado)|Habilita ou desabilita a criptografia. Quando a criptografia está habilitada, os certificados usados para restaurar o backup estão localizados na conta de armazenamento especificado no mesmo contêiner automaticbackup usando a mesma convenção de nomenclatura. Se a senha for alterada, um novo certificado será gerado com essa senha, mas o certificado antigo permanece para restaurar backups anteriores.|
|**Senha**|Texto da senha (nenhum)|Uma senha para as chaves de criptografia. Isso só é necessário se a criptografia estiver habilitada. Para restaurar um backup criptografado, você deverá ter a senha correta e o certificado relacionado que foi usado no momento em que o backup foi feito.|

## Configurar Backup automatizado no Portal

É possível usar o [Portal de Visualização do Azure](http://go.microsoft.com/fwlink/?LinkID=525040&clcid=0x409) para configurar o Backup Automatizado ao criar uma nova Máquina Virtual do SQL Server 2014. A captura de tela a seguir mostra essas opções em **CONFIGURAÇÃO OPCIONAL** | **BACKUP AUTOMATIZADO DO SQL**.

![Configuração de Backup Automático do SQL no Portal do Azure](./media/virtual-machines-sql-server-automated-backup/IC778483.jpg)

Para as máquinas virtuais existentes do SQL Server 2014, selecione as configurações de **Backup automático** na seção **Configuração** das propriedades da máquina virtual. Na janela **Backup automatizado**, é possível habilitar o recurso, definir o período de retenção, selecionar a conta de armazenamento e definir a criptografia. Isso é mostrado na captura de tela a seguir.

![Configuração de Backup Automatizado no Portal do Azure](./media/virtual-machines-sql-server-automated-backup/IC792133.jpg)

>[AZURE.NOTE]Quando você habilita o Backup Automatizado pela primeira vez, o Azure configura o Agente IaaS do SQL Server em segundo plano. Durante esse tempo, o portal não mostrará que o Backup Automatizado está configurado. Aguarde alguns minutos para que o agente seja instalado e configurado. Depois disso, o portal refletirá as novas configurações.

## Configurar Backup Automatizado com o PowerShell

No exemplo do PowerShell a seguir, o Backup Automatizado é configurado para uma VM existente do SQL Server 2014. O comando **New-AzureVMSqlServerAutoBackupConfig** define as configurações de Backup Automatizado para armazenar backups na conta de armazenamento do Azure especificada pela variável $storageaccount. Esses backups serão mantidos por 10 dias. O comando **Set-AzureVMSqlServerExtension** atualiza a VM do Azure especificada com essas configurações.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Pode demorar vários minutos para instalar e configurar o Agente IaaS do SQL Server.

Para habilitar a criptografia, modifique o script anterior para passar o parâmetro EnableEncryption e uma senha (cadeia de caracteres segura) para o parâmetro CertificatePassword. O script a seguir habilita as configurações de Backup Automatizado no exemplo anterior e adiciona a criptografia.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Para desabilitar o backup automático, execute o mesmo script sem o parâmetro **-Enable** para **New-AzureVMSqlServerAutoBackupConfig**. Assim como acontece com a instalação, pode demorar vários minutos para desabilitar o Backup Automatizado.

## Como desabilitar e desinstalar o Agente IaaS do SQL Server

Se você quiser desabilitar o Agente IaaS do SQL Server para Backup e Aplicação de Patch Automatizados, use o seguinte comando:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

Para desinstalar o Agente IaaS do SQL Server, use a seguinte sintaxe:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

Você também pode desinstalar a extensão usando o comando **Remove-AzureVMSqlServerExtension**:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

>[AZURE.NOTE]Desabilitar e desinstalar o Agente IaaS do SQL Server não remove as configurações de Backup Gerenciado previamente configuradas. Você deve desabilitar o Backup Automatizado antes de desabilitar ou desinstalar o Agente IaaS do SQL Server.

## Compatibilidade

Os produtos a seguir são compatíveis com os recursos do Agente IaaS do SQL Server para Backup Automatizado:

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2014 Standard

- SQL Server 2014 Enterprise

## Próximas etapas

O Backup Automatizado configura o Backup Gerenciado em VMs do Azure. Por isso, é importante [ler a documentação do Backup Gerenciado](https://msdn.microsoft.com/library/dn449496.aspx) para entender o comportamento e suas implicações.

Você pode encontrar outras orientações de backup e restauração para o SQL Server em VMs do Azure no seguinte tópico: [Backup e restauração do SQL Server em Máquinas Virtuais do Azure](virtual-machines-sql-server-backup-and-restore.md).

Um recurso relacionado às VMs do SQL Server no Azure é a [Aplicação de patch automatizada para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-sql-server-automated-patching.md).

Examine outros [recursos para executar o SQL Server em Máquinas Virtuais do Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=Oct15_HO3-->