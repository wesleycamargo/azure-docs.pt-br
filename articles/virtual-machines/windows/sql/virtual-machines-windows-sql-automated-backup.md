---
title: "Backup Automatizado para máquinas virtuais do Azure do SQL Server 2014 | Microsoft Docs"
description: "Explica o recurso de Backup Automatizado para VMs do SQL Server 2014 em execução no Azure. Este artigo é específico para VMs que usam o Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/30/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 1b9401d7c2f756081d585b9432ceef412ab1f807
ms.lasthandoff: 03/25/2017


---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Backup Automatizado para em máquinas virtuais do SQL Server 2014 (Resource Manager)
> [!div class="op_single_selector"]
> * [Gerenciador de Recursos](virtual-machines-windows-sql-automated-backup.md)
> * [Clássico](../classic/sql-automated-backup.md)
> 
> 

O backup automatizado configura automaticamente o [Backup Gerenciado do Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os bancos de dados novos e existentes em uma VM do Azure executando o SQL Server 2014 Standard ou Enterprise. Isso permite que você configure backups regulares do banco de dados que utilizam o durável armazenamento de Blobs do Azure. O Backup Automatizado depende da [Extensão do agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para exibir a versão clássica deste artigo, consulte [Backup Automatizado para o SQL Server em Máquinas Virtuais do Azure Clássico](../classic/sql-automated-backup.md).

## <a name="prerequisites"></a>Pré-requisitos
Para usar o Backup Automatizado, considere os seguintes pré-requisitos:

**Sistema operacional**:

- Windows Server 2012
- Windows Server 2012 R2

**Versão/edição do SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> O Backup Automatizado funciona com o SQL Server 2014. Se você estiver usando o SQL Server 2016, pode usar o Backup Automatizado v2 para fazer backup de seus bancos de dados. Para saber mais, confira [Backup Automatizado v2 para máquinas virtuais do Azure no SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md).

**Configuração do banco de dados**:

- Os bancos de dados de destino devem usar o modelo de recuperação completa.

Para obter mais informações sobre o impacto do modelo de recuperação completa em backups, consulte [Backup com o modelo de recuperação completa](https://technet.microsoft.com/library/ms190217.aspx).

**Modelo de implantação do Azure**:

- Gerenciador de Recursos

**Azure PowerShell**:

- [Instalar os comandos mais recentes do Azure PowerShell](/powershell/azureps-cmdlets-docs) se você planeja configurar o Backup Automatizado com o PowerShell.

> [!NOTE]
> O Backup Automatizado conta com a Extensão do agente IaaS do SQL Server. As imagens atuais da galeria da máquina virtual do SQL adicionam essa extensão por padrão. Para obter mais informações, consulte [Extensão do agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Configurações
A tabela a seguir descreve as opções que podem ser configuradas para Backup Automatizado. As etapas de configuração reais variam dependendo de se você usar os comandos do Portal do Azure ou do Azure Windows PowerShell.

| Configuração | Intervalo (Padrão) | Descrição |
| --- | --- | --- |
| **Backup Automatizado** | Habilitar/desabilitar (Desabilitado) | Habilita ou desabilita o Backup Automatizado de uma VM do Azure que executa o SQL Server 2014 Standard ou Enterprise. |
| **Período de retenção** | Um a 30 dias (30 dias) | O número de dias para manter um backup. |
| **Conta de armazenamento** | Conta de Armazenamento do Azure | Uma conta de armazenamento do Azure a ser usada para armazenar arquivos de Backup Automatizado no armazenamento de blobs. Um contêiner é criado neste local para armazenar todos os arquivos de backup. A convenção de nomenclatura do arquivo de backup inclui a data, hora e nome do computador. |
| **Criptografia** | Habilitar/desabilitar (Desabilitado) | Habilita ou desabilita a criptografia. Quando a criptografia está habilitada, os certificados usados para restaurar o backup estão localizados na conta de armazenamento especificado no mesmo contêiner `automaticbackup` usando a mesma convenção de nomenclatura. Se a senha for alterada, um novo certificado será gerado com essa senha, mas o certificado antigo permanece para restaurar backups anteriores. |
| **Senha** | Texto da senha | Uma senha para as chaves de criptografia. Isso só é necessário se a criptografia estiver habilitada. Para restaurar um backup criptografado, você deverá ter a senha correta e o certificado relacionado que foi usado no momento em que o backup foi feito. |

## <a name="configuration-in-the-portal"></a>Configuração no Portal
Você pode usar o Portal do Azure para configurar o Backup Automatizado durante o provisionamento ou para as VMs do SQL Server 2014 existentes.

### <a name="new-vms"></a>Novas VMs
Use o Portal do Azure para configurar o Backup Automatizado quando criar uma nova Máquina Virtual do SQL Server 2014 no modelo de implantação do Resource Manager.

Na folha **Configurações do SQL Server**, selecione **Backup Automatizado**. A captura de tela do portal do Azure a seguir mostra a folha **Backup Automatizado do SQL** .

![Configuração de Backup Automatizado do SQL no portal do Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Para ter contexto, consulte o tópico completo sobre [provisionamento de uma máquina virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes
Para máquinas virtuais existentes do SQL Server, selecione sua máquina virtual do SQL Server. Selecione a seção **Configuração do SQL Server** da folha **Configurações**.

![Backup Automatizado do SQL para VMs existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Na folha **Configuração do SQL Server**, clique no botão **Editar** na seção de Backup Automatizado.

![Configurar o Backup Automatizado do SQL para VMs existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Quando terminar, clique no botão **OK** na parte inferior da folha **Configuração do SQL Server** para salvar suas alterações.

Se você for habilitar o Backup Automatizado pela primeira vez, o Azure configurará o Agente IaaS do SQL Server em segundo plano. Durante esse tempo, o portal do Azure pode não mostrar que o Backup Automatizado está configurado. Aguarde alguns minutos para que o agente seja instalado e configurado. Depois disso, o portal do Azure refletirá as novas configurações.

> [!NOTE]
> Você também pode configurar o Backup Automatizado usando um modelo. Para obter mais informações, consulte o [Modelo de início rápido do Azure para o Backup Automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).
> 
> 

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell
Depois de provisionar sua VM do SQL, use o PowerShell para configurar o Backup Automatizado. Antes de começar, faça o seguinte:

- [Baixe e instale o Azure PowerShell mais recente](http://aka.ms/webpi-azps).
- Abra o Windows PowerShell e associe-o à sua conta. Você pode fazer isso seguindo as etapas na seção [Configurar sua assinatura](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-ps-sql-create#configure-your-subscription) do tópico de provisionamento.

No exemplo do PowerShell a seguir, o Backup Automatizado é configurado para uma VM existente do SQL Server 2014. O comando **AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig** define as configurações de Backup Automatizado para armazenar backups na conta de armazenamento do Azure associada à máquina virtual. Esses backups serão mantidos por 10 dias. O comando **Set-AzureRmVMSqlServerExtension** atualiza a VM do Azure especificada com essas configurações.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Pode demorar vários minutos para instalar e configurar o Agente IaaS do SQL Server.

Para habilitar a criptografia, modifique o script anterior para passar o parâmetro **EnableEncryption** e uma senha (cadeia de caracteres segura) para o parâmetro **CertificatePassword**. O script a seguir habilita as configurações de Backup Automatizado no exemplo anterior e adiciona a criptografia.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Para desabilitar o backup automático, execute o mesmo script sem o parâmetro **-Enable** no comando **AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig**. A ausência do parâmetro **-Enable** sinaliza o comando para desabilitar o recurso. Assim como acontece com a instalação, pode demorar vários minutos para desabilitar o Backup Automatizado.

> [!NOTE]
> Remover o Agente IaaS do SQL Server não remove as configurações de Backup Automatizado previamente definidas. Você deve desabilitar o Backup Automatizado antes de desabilitar ou desinstalar o Agente IaaS do SQL Server.
> 
> 

## <a name="next-steps"></a>Próximas etapas
O Backup Automatizado configura o Backup Gerenciado em VMs do Azure. Portanto, é importante [ler a documentação do Backup Gerenciado](https://msdn.microsoft.com/library/dn449496.aspx) para entender o comportamento e suas implicações.

Você pode encontrar outras orientações de backup e de restauração para o SQL Server em VMs do Azure no seguinte tópico: [Backup e restauração do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

Para obter informações sobre outras tarefas de automação disponíveis, consulte [Extensão do agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [Visão geral do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).


